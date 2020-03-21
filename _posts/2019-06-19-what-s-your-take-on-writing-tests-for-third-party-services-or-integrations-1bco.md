---
title: What's your take on writing tests for third-party services or integrations?
date: '2019-06-19T01:08:33.818Z'
excerpt: >-
  Let's say you have an application that uses Stripe.  Stripe gives you a dev
  environment- own API key,...
thumb_img_path: null
comments_count: 7
positive_reactions_count: 8
tags:
  - discuss
  - testing
canonical_url: >-
  https://dev.to/david_ojeda/what-s-your-take-on-writing-tests-for-third-party-services-or-integrations-1bco
layout: post
---
Let's say you have an application that uses Stripe.

Stripe gives you a dev environment- own API key, own dashboard, own data, all separated from your Stripe's live data. 

**Do you write tests that interact with this dev environment or do you mock the responses or something alike?**


I work on an app that has many integration tests communicating with test/dev/sandbox environments, and they sometimes fail due to errors on their services, not on our own. Now we're discussing if should we mock the responses instead.

*[This post is also available on DEV.](https://dev.to/david_ojeda/what-s-your-take-on-writing-tests-for-third-party-services-or-integrations-1bco)*


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
