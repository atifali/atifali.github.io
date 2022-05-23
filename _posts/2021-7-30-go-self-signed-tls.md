---
layout: post
title: Creating self-signed TLS certificates in Go 🗂️📜🆔
---

So a while back while I was playing around with some golang codebase and we came across the need for implementing a simple authentication feature, which was based on an external API and used a TLS based authentication mechanism, to secure<!-- more --> the exchange of data between the server and a client over subsequent API calls. We needed to make sure that the user could present the PEM-encoded TLS certificate that was provided beforehand by the service provider to authenticate and subsequently use the service securely with the access keys provided by the server in the response during the aforementioned intial TLS based authentication.

We went on and implemented the authentication feature by using the typical TLS settings on the HTTP golang client object and based on the return status of the authentication HTTP API call we would determine if the authentication was successful or not, and whether or not to proceed with looking for and using the access keys in the response of that initial call. Here is a code snippet for that:

<br>

```
...
...
...
// 'eClient' is the service client, setup with the appropriate settings
// and property values from previous routines and calls
...
...
...
// create a cert pool
pool, _ := x509.SystemCertPool()
if pool == nil {
    pool = x509.NewCertPool()
}

// check the client cert for validity
if eClient.Cert == "" {
    return fmt.Errorf("certificate cannot be null.")
}

// decode the cert and add to the pool
block, _ := pem.Decode([]byte(eClient.Cert))
if block == nil || block.Type != "CERTIFICATE" || len(block.Headers) != 0 {
    return fmt.Errorf("decode certificate failed: %s.", eClient.Cert)
}

cert, err := x509.ParseCertificate(block.Bytes)
if err != nil {
    return err
}
pool.AddCert(cert)

// configure the transport layer for the http client with the cert
tr := &http.Transport{
    TLSClientConfig: &tls.Config{
        InsecureSkipVerify: true, //Not actually skipping, check the cert in VerifyPeerCertificate
        RootCAs: pool,
        VerifyPeerCertificate: func(rawCerts [][]byte, verifiedChains [][]*x509.Certificate) error {
            // If this is the first handshake on a connection, process and
            // (optionally) verify the server's certificates.
            certs := make([]*x509.Certificate, len(rawCerts))
            for i, asn1Data := range rawCerts {
                cert, err := x509.ParseCertificate(asn1Data)
                if err != nil {
                    return fmt.Errorf("tls: failed to parse certificate from server: " + err.Error())
                }
                certs[i] = cert
            }
            // see: https://github.com/golang/go/issues/21971
            opts := x509.VerifyOptions{
                Roots:         pool,
                CurrentTime:   time.Now(),
                DNSName:       "", // <- skip hostname verification
                Intermediates: x509.NewCertPool(),
            }
            for i, cert := range certs {
                if i == 0 {
                    continue
                }
                opts.Intermediates.AddCert(cert)
            }
            _, err := certs[0].Verify(opts)
            return err
        },
    },
}

// create a http client with the above tls transport layer
client := &http.Client{Transport: tr, Timeout: time.Second * 5}

// the authentication end-point
authEndpoint := eClient.authEndpoint.String()

// authentication POST request
resp, err := client.Post(authEndpoint, "application/json", bytes NewBuffer(reqBodyJson))

if err != nil {
    return err
}

// check for the response status for authentication success
if resp.StatusCode != 200 {
    log.DefaultLogger.Error("auth response not ok:", "response code:", strconv.Itoa(resp.StatusCode))
    return fmt.Errorf("request not ok. returned code: %v", resp.StatusCode)
}

log.DefaultLogger.Debug("auth response ok.") 

// continue with extracting the access keys and other details
// from the response to continue with the rest of the workflow
...
...
...
```

Now, once we had this all incorporated in the codebase we needed to make sure that we had a way to unit test this functionality somehow. This is where we needed to create a self-signed certificate along with a dummy server that could present that to the HTTP test client and subsequently be useful in making sure the above functionality that we implemented actually works.

Here are the code snippets for that, feel free to adapt and use them where useful, these in turn were copied and adapted from somewhere else on the interwebs too! (Yay open web!!)

<br>

```
// helper function to create a certificate template with a serial number and other required fields
func certTemplate() (*x509.Certificate, error) {
	// generate a random serial number (a real cert authority would have some logic behind this)
	serialNumberLimit := new(big.Int).Lsh(big.NewInt(1), 128)
	serialNumber, err := rand.Int(rand.Reader, serialNumberLimit)
	if err != nil {
		return nil, errors.New("failed to generate serial number: " + err.Error())
	}

    // settings for the cert (usually these would also have some sense and logic behind them)
	tmpl := x509.Certificate{
		SerialNumber:          serialNumber,
		Subject:               pkix.Name{Organization: []string{"Test Inc."}},
		SignatureAlgorithm:    x509.SHA256WithRSA,
		NotBefore:             time.Now(),
		NotAfter:              time.Now().Add(time.Hour), // valid for an hour
		BasicConstraintsValid: true,
	}
	return &tmpl, nil
}

//helper function to create a certificate from a template and public key plus a parent certificate and private key
func createCert(template, parent *x509.Certificate, pub interface{}, parentPriv interface{}) (
	*x509.Certificate, []byte, error) {

	certDER, err := x509.CreateCertificate(rand.Reader, template, parent, pub, parentPriv)
	if err != nil {
		return nil, nil, errors.New("failed to create a certificate: " + err.Error())
	}

	// parse the resulting certificate so we can use it again
	cert, err := x509.ParseCertificate(certDER)
	if err != nil {
		return nil, nil, errors.New("failed to parse the certificate: " + err.Error())
	}

	// PEM encode the certificate (this is a standard TLS encoding)
	b := pem.Block{Type: "CERTIFICATE", Bytes: certDER}
	certPEM := pem.EncodeToMemory(&b)

	return cert, certPEM, err
}

// helper function to create a TLS Certificate and Root Key Combination
func createTLSCert() ([]byte, tls.Certificate, error) {
	// generate a new key-pair for the test server TLS
	rootKey, err := rsa.GenerateKey(rand.Reader, 2048)
	if err != nil {
		return nil, tls.Certificate{}, fmt.Errorf("generating random key: %v", err)
	}

	// generate a certificate template for the test server TLS
	rootCertTmpl, err := certTemplate()
	if err != nil {
		return nil, tls.Certificate{}, fmt.Errorf("creating cert template: %v", err)
	}

	// set the certificate to be used for TLS handshake authentication
	rootCertTmpl.IsCA = true
	rootCertTmpl.KeyUsage = x509.KeyUsageCertSign | x509.KeyUsageDigitalSignature
	rootCertTmpl.ExtKeyUsage = []x509.ExtKeyUsage{x509.ExtKeyUsageServerAuth, x509.ExtKeyUsageClientAuth}
	rootCertTmpl.IPAddresses = []net.IP{net.ParseIP("127.0.0.1")} // set the host address here

	// create a self-signed certificate for the test server TLS
	_, rootCertPEM, err := createCert(rootCertTmpl, rootCertTmpl, &rootKey.PublicKey, rootKey)
	if err != nil {
		return nil, tls.Certificate{}, fmt.Errorf("error creating cert: %v", err)
	}

	// PEM encode the private key for TLS server handshake
	rootKeyPEM := pem.EncodeToMemory(&pem.Block{
		Type: "RSA PRIVATE KEY", Bytes: x509.MarshalPKCS1PrivateKey(rootKey),
	})

	// Create a TLS certificate using the private key and certificate
	rootTLSCert, err := tls.X509KeyPair(rootCertPEM, rootKeyPEM)
	if err != nil {
		return nil, tls.Certificate{}, fmt.Errorf("invalid key pair: %v", err)
	}

	return rootCertPEM, rootTLSCert, nil
}
```

Really the `createTLSCert()` function is the one that we need to focus on for any applications where a self-signed certificate is needed. Make sure to note the certificate settings in the `certTemplate()` function as well. Here is how we ended up using the above helper functions on a test:

<br>

```
...
...
...
// this is our test http server with a dummy handler for the
// authentication API call
ts := httptest.NewUnstartedServer(http.HandlerFunc(dummyHandler))

// https TLS cert
rootCertPEM, rootTLSCert, err := createTLSCert()
if err != nil {
    t.Fatal(fmt.Errorf("generating TLS certificate: %v", err))
}

// Configure the server to present the TLS certificate we created
ts.TLS = &tls.Config{
    Certificates: []tls.Certificate{rootTLSCert},
}

ts.StartTLS()
defer ts.Close()

// test client setup here
eClient.authEndpoint = ts.URL
eClient.Cert = ``
eClient.Cert = string(rootCertPEM)

// call the authentication logic that we implemented in the first
// code snippet after here using the dummy handler to see the 
// TLS based auth in action
...
...
...
```

Hope this could be of use to someone out there. I certainly found this interesting and learned a ton about behind-the-scenes of how TLS really works!

Note however that self-signed root certificates are not really useful apart from anchoring the trust chain of subsequent certficate issuances and hence are mostly used by CAs and trusted parties in our overall web of trust.

__Disclaimer__: I am not an expert by any means here so please exercise caution while using any of the snippets above and if you find an issue or logical fault please let me know in the comments below!
