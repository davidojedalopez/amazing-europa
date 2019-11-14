---
title: Extend nginx/Apache Proxy Configurations on AWS ElasticBeanstalk
date: '2018-01-26T20:31:21.284Z'
excerpt: >-
  Use .ebextensions feature of AWS ElasticBeanstalk to define custom
  configurations for your proxy server
thumb_img_path: >-
  https://res.cloudinary.com/practicaldev/image/fetch/s--m9Atdxe4--/c_imagga_scale,f_auto,fl_progressive,h_420,q_auto,w_1000/https://thepracticaldev.s3.amazonaws.com/i/8ndsgtt91bqudopf8m7o.png
comments_count: 0
positive_reactions_count: 11
tags: []
canonical_url: >-
  https://davidojedalopez.github.io/blog/2018/01/11/extend-nginx-config-on-aws-elasticbeanstalk
layout: post
---


_[This post](https://davidojeda.mx/blog/2018/01/11/extend-nginx-config-on-aws-elasticbeanstalk) is a cross-post from my blog: [https://davidojeda.mx](https://davidojeda.mx/blog/)._

<p>AWS ElasticBeanstalk applications use either an nginx or Apache proxy to relay requests. Using the .ebextensions feature of ElasticBeanstalk we can extend the configuration of these proxies. If you don't know how .ebextensions work you can read more <a href="https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/ebextensions.html">here.</a></p>

<p>I'm going to extend the default nginx proxy configurations using .ebextensions. The same procedure can be used to extend an Apache proxy.</p>

<h1>
    Create a .conf file
</h1>

<p>First we need to create a .conf file with the desired directives. A list of nginx directives can be found <a href="http://nginx.org/en/docs/dirindex.html"> here.</a> My conf file- named proxy.conf -increases some timeouts of the proxy:</p>


<iframe class="liquidTag" src="https://dev.to/embed/gist?args=https%3A%2F%2Fgist.github.com%2Fdavidojedalopez%2Fb3735a658fbd645b38a13405f9eae8fa" style="border: 0; width: 100%;"></iframe>


<h1>Create nginx conf.d directory</h1>

<p>Now we need the directory where our configuration file will be. Under .ebextensions, create a directory named 'nginx', and inside it another named 'conf.d'. Then add the file you just created. Your dir structure should look like this:</p>

- .ebextensions
    - nginx
        - conf.d
            - proxy.conf

Now, when you deploy a new version of your application, ElasticBeanstalk will automatically copy your files on the {% raw %}`.ebextensions/nginx/conf.d/`{% endraw %} directory to the {% raw %}`/etc/nginx/conf.d/`{% endraw %} directory of your instances.

<p>This all works because the default nginx.conf file- on line 21 -specifies to include all .conf files under the conf.d directory:</p>


<iframe class="liquidTag" src="https://dev.to/embed/gist?args=https%3A%2F%2Fgist.github.com%2Fdavidojedalopez%2F680ae751eb2a3fd46c3bca04a33c5a4c" style="border: 0; width: 100%;"></iframe>


<p>The directives from the .conf file will be added to the <em>http</em> block of the default configuration.</p>

<p>If you need to add directives to the <em>server</em> block you will need to add .conf files to the elasticbeanstalk folder (see line 39 of previous Gist). That dir structure would look:</p>

- .ebextensions
    - nginx
        - conf.d
            - proxy.conf
        - elasticbeanstalk
            - my_other_conf.conf

<p>Same can be done for an Apache proxy. The difference is on the directory structure. For Apache your structure should be this:</p>

- .ebextensions
    - httpd
        - conf
            - proxy.conf

<h1>Wrap-up</h1>

<p>Using .ebextensions is by far the simplest method to add custom configurations to your nginx or Apache proxy. Create as many configuration files as you need and add them to the corresponding directory under .ebextensions and you are done.</p>

*[This post is also available on DEV.](https://dev.to/david_ojeda/extend-nginxapache-proxy-configurations-on-aws-elasticbeanstalk-3mjg)*


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
