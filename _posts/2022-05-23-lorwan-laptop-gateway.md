---
layout: post
title: My laptop = LoRaWAN Gateway...wait what!! 🐱‍💻🗼📶
---

A while back I was looking around for a couple sensors online for a quick hack and came across some wireless sensors that were using a protocol named LoRaWAN. Upon further investigation, I stumbled upon the fascinating world of low-power long-range wireless networks for IoT (and other things too imo!). So turns out that essentially LoRa is a<!-- more --> proprietary modulation technique for radio signals that was derived from chirp-based signal modulation techniques (really just a bunch of variable frequency sine wave) to encode information. A company called Semtech now owns the rights to that. And LoRaWAN is really the software protocol that is built on top of the physical LoRa layer to provide a standard low-power low-bitrate wide-area wireless networking framework to connect battery-powered (intended use!) _"Things"_ to the internet.

There is a whole body of literature and standard specifications to make sure that the sensors and other components that form the network can work in harmony and operate in various conditions. I wont really go into that here, but if you are interested head over to the [Lora Alliance](https://lora-alliance.org/) website for all the nitty-gritty details.

However, if I were to summarize in short what a typical LoRaWAN setup looks like, it would generally consist of a bunch of sensors _("LoRaWAN Sensors")_ and a gateway _("LoRaWAN Gateway")_ embedded with the physical LoRa radio chip and the fimware to _'talk'_ the LoRaWAN protocol, a network server _("LoRaWAN Network Server - LNS")_ that would connect the whole radio network to other networks usually via high-bandwidth back-haul connections (like EtherNet and WiFi) and Application Servers which provide the interfaces for the actual use of the data from the rest of the network. 

Now one thing I missed there is that the gateway really also has a specialized packet forwarding software, which is really how it transmits the LoRa radio signals from the sensors to the network server. So in short the gateway is the middlemen between a LoRaWAN Network and other types of networks. Here is what a typical setup looks like:

![_config.yml]({{ site.baseurl }}/images/lorawan-laptop-gateway1.png "Figure 1: ")
<figcaption><i>Figure 1: A typical LoRaWAN based network topology <a href="https://pbs.twimg.com/media/Eer56wqWAAIX7NL.png">(source)</a></i></figcaption> 

Note that the above description is over-simplified but it overall describes the topology of the network pretty well.

Now, I have been really wanting to play with this technology myself for quite a while now. So I ended up deciding to get some sensors and my own gateway to experiment with. I wanted a compact and somewhat cheap development setup to begin with, so I could not really afford to go with a full-blown super-duper nice LoRaWAN gateway that many vendors sell ranging from some 100s to even 1000s of $$$ in some cases. Here is how I turned my laptop into a LoRaWAN gateway, with the power of some affordable radio hardware and open-source community software.

I got my hands on a nice [RAK7371 WisGate Developer Base](https://store.rakwireless.com/products/wisgate-developer-base?variant=39942858703046) from [RAKwireless](https://www.rakwireless.com/en-us). It is essentially a pre-packaged LoRa radio-to-usb converter based on the [RAK5146 LoRaWAN concentrator](https://store.rakwireless.com/collections/wislink-lpwan/products/wislink-lpwan-concentrator-rak5146?variant=39677269409990), which is in turn a mini-PCIe form factor convertor module (requires a mini-PCIe slot to SPI/USB connection) for the [Semtech SX1303](https://www.semtech.com/products/wireless-rf/lora-core/sx1303) LoRa baseband processing chip.

![_config.yml]({{ site.baseurl }}/images/lorawan-laptop-gateway2.png "Figure 2: ")
<figcaption><i>Figure 2: RAK7371 WisGate Developer Base <a href="https://docs.rakwireless.com/assets/images/wisgate/rak7271-7371/overview/rak7271-7371.png">(source)</a></i></figcaption> 

Next, I needed a way to connect this LoRa radio module to a _"computer" ("LoRa Gateway Host")_ so that we can convert the radio signals being transmitted via USB to LNS protocol, so that we can eventually play with all the data from our LoRaWAN sensors. For this, one could very well use a Raspberry Pi or another SBC with a USB interface. However, I already had a linux-based computer with a USB connector on me, and always do (for the most part 👨‍💻), my very own laptop!!!🙌 

This is where some great open-source software came to the rescue. This combination of a _"LoRa Radio Module"_ + _"LoRa Gateway Host"_ is really what fundamentally a LoRaWAN gateway is! We just hacky lego'ed our way out of budgeting constraints!

First, I tried to use the open-source [packet_forwarder](https://github.com/Lora-net/packet_forwarder) from Semtech, which was an early proof-of-concept implementation of a LNS-compatible software stack for a LoRaWAN gateway, to connect to [The Things Network (TTN)](https://www.thethingsnetwork.org/), which is a community free-tier LNS implementation (they also have an enterprise version, [The Things Industries](https://www.thethingsindustries.com/), for hardcore industry users).

Now trying to run Semtech's packet_forwarder directly for any piece of LoRa radio module is not that straight-forward, since it is a bare-bones implementation of the super low-level specifications of the the LoRaWAN protocol itself in C. Fortunately, there are HAL libraries and tools that were also provided by Semtech which are based on the core libraries and abstract away most of the hardware level customizations needed to run the packet_forwarder and other utilities in conjunction with various LoRa radio modules.

So in fact that is eventually what I ended up using. [Here](https://docs.rakwireless.com/Product-Categories/WisGate/RAK7271-7371/Quickstart/#for-rak7371-wisgate-developer-base-with-rak5146-inside) is a nice super useful guide from RAKwireless that uses the [sx1302_hal](https://github.com/Lora-net/sx1302_hal) from Semtech, which I used to setup the packet_forwarder on my laptop to be able to interface with the RAK7371. [Here](https://github.com/atifali/rak7371-ttn/blob/main/packet_forwarder/global_config.json) is my own global configuration file that I was able to get working with the TTN console. Note that here I am using USB based COM, US915 frequency band and nam1 based TTN server. Then, I used the step 13 onwards [here](https://docs.rakwireless.com/Product-Categories/WisGate/RAK7271-7371/Quickstart/#connecting-to-the-things-network-v3-ttnv3) to connect my laptop-based LoRaWAN gateway to TTN. Lo and behold I was able to see some packets coming into the TTN console! 🎉

_Note: I dont have any sensors of my own chirping over this test dev network YET, so all the messages are just gateway status related not uplink messages from sensors!_

![_config.yml]({{ site.baseurl }}/images/lorawan-laptop-gateway3.png "Figure 3: ")
<figcaption><i>Figure 3: TTN Console Messages using a Semtech packet_forwarder based LoRaWAN Gateway</i></figcaption>

Upon further investigation though, I realized that there was a better and newer recommended implementation of a LoRaWAN packet forwarding host software from Semtech again to move the industry away from the origial POC packet_forwarder (since that was only meant to be a reference implementation). They called it the [LoRa Basics™ Station](https://github.com/lorabasics/basicstation/). You can try to build it from source and adapt it to your own LoRa radio hardware, but that would not be very straight forward from my experience. Thankfully, [Xose Perez from RAKwireless](https://news.rakwireless.com/author/xose/) with the help of some other awesome open-source contributors has created a docker wrapper around the Semtech's LoRa Basics™ Station software implementation. Here is the [repository](https://github.com/xoseperez/basicstation) where you can find all the details, and he has also written a very nice [blogpost](https://news.rakwireless.com/basics-station-on-rak-wisgate-developer-gateways/) to walk through the setup as well.

I wont really go into the details here, since I was for the most part able to follow the instructions in the repository to get up and running with the docker-based LoRa Basics™ Station software on my laptop to be able to interface with the RAK7371 and TTN console. [Here](https://github.com/atifali/rak7371-ttn/blob/main/basicstation/docker-compose.yml) is the docker compose YAML that I was succesfully able to use for this. If at any point you are having any issues connecting to the TTN console make sure to refer to this [gateway troubleshooting](https://www.thethingsindustries.com/docs/gateways/troubleshooting/) guide from TTN. 

Here is the TTN console lighting up in cheers with uplink messages (albeit empty, since no sensors yet, lol!) with the LoRa Basics™ Station connecting to the TTN LNS, using my laptop-based LoRaWAN Gateway! 🥳

![_config.yml]({{ site.baseurl }}/images/lorawan-laptop-gateway4.png "Figure 4: ")
<figcaption><i>Figure 4: TTN Console Messages using a Semtech Basics™ Station based LoRaWAN Gateway</i></figcaption>

That would be all for now. Next, I am really looking forward to playing with some of the LoRaWAN sensors that are on the way to me from half-way across the world (damn you shipping delays!!). That will really complete this test dev LoRa network with some real data that we can hopefully play around with. Until then, I hope that my experience could be of use to someone out there who is looking for simple budget-friendly ways to experiment with the LoRaWAN technology! Keep LoRaWANing folks...⚡
