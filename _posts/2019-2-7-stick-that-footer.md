---
layout: post
title: Stick that footer!
title_suffix: <span>:pencil::straight_ruler::triangular_ruler:</span>
---

<script type="text/javascript">
    function toggle_content() {
      var ids = ['post_body', 'disqus_thread'];
      for (var id in ids) {
        var e = document.getElementById(ids[id]);
        if(e.style.display == 'none')
            e.style.display = 'block';
        else
            e.style.display = 'none';
      }
      e = document.getElementById('toggle_p');
      if(e.style.display == 'none')
          e.style.display = 'block';
      else
          e.style.display = 'none';
    }
</script>

Based on my experience with web development over the years, there is (almost) exclusively one simple thing (among many others) that I can point to which I believe is something most web developers/designers/hobbyists, have had to tackle at some point at a fundamental level outside of a pre-configured theme or a front-end framework, its the<!-- more --> good ol' <a href="#" onclick="toggle_content()">'sticky footer'</a>.

<p id="toggle_p" style="display: none; font-weight: bold;">See how the footer below 'sticks' to the bottom of the page, even with whitespace in between! (Click the same link to toggle the content)</p> 

<div id="post_body" markdown="1">

After, mucking around with several different css 'hacks', I have finally settled on a simple and elegant solution that's based on flexbox layout, it's the same that I use on this blog too! Hence, I thought it might be useful to share it here.

The idea is pretty simple once you understand **CSS flexbox layout**. The 'flexible box' layout provides a simple, dynamic and direction-agnostic solution to laying out, aligning and distributing space among elements within a container. And the good thing here, is that the size of those elements need not be known beforehand. Hence, the container has the ability to alter the elements' dimensions (which themselves are dynamic) to fill up the container space as efficiently as possible, by either expanding or shrinking the footprints of the elements in question.

Now when it comes to flexbox layout, it works in both horizontal and vertical directions. And it provides you the ability to specify which elements can 'flex' and which can't. Now this, is perfect for our situation, where we basically want the body of our web-page to expand to fill up the maximum space available, after the header and footer have been accommodated. Note that the solution also works in cases where the footer itself has dynamic height. Considering the following markup for our web page:
<h id="tldr"></h>
```html
<!DOCTYPE html>
<html>
  <head>
    <title>This is a title</title>
  </head>
  <body>
  	<header>This is the header</header>
    <main>Hello world!</main>
    <footer>This is the footer</footer>
  </body>
</html>
```    
One of the solution (and the simplest in my opinion) would be:

```css
body {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}
main {
  flex: 1;
} 
```

Now as always, not all browsers (yes! I specially am looking at you IE) are up to the official flexbox (and other!) specifications and hence a more verbose way (thanks to **[Philip Walton's Flex bug documentation](https://github.com/philipwalton/flexbugs)**) to make sure this works across most clients would be as follows:

```css
body {
  display: flex;
  flex-direction: column;
  /* Flex bug # 3 */
  height: 100%; 
}
main {
  /* Flex bug # 1 */		
  flex: 1 0 auto;
}
header, footer {
  /* Flex bug # 1 */
  flex: none;
}
```

Some other solutions that have _sometimes_ worked for me in the past are as follows.

This one had issues with Safari and IE for me. 

```css
body {
  display: flex;
  flex-direction: column;
  min-height: 100vh; 
}
footer {		
  margin: auto auto 0 auto;
}
```

Another one, where again I had issues with Safari and IE, also Chrome was having issues too here.  

```css
body {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  margin: 0; 
}
footer {		
  margin-top: auto;
}
```

**Now go and make that footer sticky!**

#### TL;DR
Wrap the parent in a flex column container with full height, and make the main content of the container flex, relative to the header and the footer. [Use this CSS!](#tldr)

</div> 