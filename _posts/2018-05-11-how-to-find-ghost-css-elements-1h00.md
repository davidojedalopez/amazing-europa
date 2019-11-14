---
title: How to Find Ghost CSS Elements
date: '2018-05-11T16:14:30.939Z'
excerpt: CSS styles to help you find ghost or invisible spacing on your HTML
thumb_img_path: null
comments_count: 2
positive_reactions_count: 62
tags: []
canonical_url: 'https://davidojeda.mx/blog/2018/05/11/find-ghost-css-elements'
layout: post
---


_[This post](https://davidojeda.mx/blog/2018/05/11/find-ghost-css-elements) is a cross-post from my blog: [davidojeda.mx](https://davidojeda.mx/blog/)._


I recently came across a bug on our landing page which caused a weird blank space overflow on the right side:

![Landing page with extra white space on right side][landing-page-bug]

I looked for a couple of hours trying to find any CSS spacing causing it, or some wrong element on my HTML, but couldn't find anything out of place. The blank space wasn't even inside the &lt;html&gt; element of the page 🧐

I then [stumbled upon this post](http://wernull.com/2013/04/debug-ghost-css-elements-causing-unwanted-scrolling/) and rapidly found the problem. This blog post suggests some CSS styles to make ghost elements visible 👻:

{% raw %}```css
* {
  background: # 000 !important;
  color: # 0f0 !important;
  outline: solid # f00 1px !important;
}
```{% endraw %}

Now, I could find the section that was causing the problem:

![Landing page with ghost elements visible][landing-page-ghost]

In the end, it was a matter of fixing some mismatching HTML elements.

Would've had this CSS styles helping me debug from the beginning, could've saved me a couple hours of work 🤦🏻‍♂️


[landing-page-bug]: https://thepracticaldev.s3.amazonaws.com/i/o1q1hlen9lqdy7dsc7zz.png "Landing page with extra white space on right side"

[landing-page-ghost]: https://thepracticaldev.s3.amazonaws.com/i/909z7bing8w3g1u0ssmf.png "Landing page with ghost elements visible"

*[This post is also available on DEV.](https://dev.to/david_ojeda/how-to-find-ghost-css-elements-1h00)*


<script>
const parent = document.getElementsByTagName('head')[0];
const script = document.createElement('script');
script.type = 'text/javascript';
script.src = 'https://cdnjs.cloudflare.com/ajax/libs/iframe-resizer/4.1.1/iframeResizer.min.js';
script.charset = 'utf-8';
script.onload = function() {
    window.iFrameResize({}, '.liquidTag');
};
parent.appendChild(script);
</script>    
