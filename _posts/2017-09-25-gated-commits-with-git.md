---
title: Gated Commits with Git
date: '2017-09-25T13:20:57.464Z'
excerpt: Using Git hooks to run tests before each commit.
thumb_img_path: >-
  https://res.cloudinary.com/practicaldev/image/fetch/s--OL8tZY_T--/c_imagga_scale,f_auto,fl_progressive,h_420,q_auto,w_1000/https://thepracticaldev.s3.amazonaws.com/i/k7kh73gxuasnrsig9wiu.png
comments_count: 3
positive_reactions_count: 15
tags:
  - git
  - ci
canonical_url: 'https://dev.to/david_ojeda/gated-commits-with-git'
layout: post
---


<p>A gated commit, also called a <strong>pre-tested</strong> commit, is an integration pattern in which a commit is not approved until a set of tests are ran against the code being commited. In other words, the commit does not go through if the test suite fails.</p>

<p><strong>Why do you want this?</strong> It makes your application more resilient to change since now you are running a set or sub-set of your tests even before that code is available to anyone else.</p>

<p>I am going to show you how to implement a gated commit pattern with Git. In this example, our <strong>unit test suite</strong> will be the <em>gate</em> to allow our commits to make it to the codebase.</p>

___

<h2>What do you need?</h2>

<ul>
    <li>Git</li>
    <li>Your application's Git repo</li>
    <li>A test suite</li>
</ul>

<h3>Starting off: Git Hooks</h3>

<p>We want our tests to run before a commit goes through. Git allows us to run custom commands just before that event happens thanks to Git hooks. I am not going to go into the details on how they work, but conveniently for us, there is a hook called <strong>pre-commit</strong>. This hook is executed just before the commit happens. Perfect spot for our test suite to run.</p>

<h3>Setting up a pre-commit hook</h3>

<p>In your Git repo, there is a folder named .git in which the hooks are stored. If you have never modified any hook, your .git directory structure will look like this:</p>

![.git directory structure](https://thepracticaldev.s3.amazonaws.com/i/ldij14knuw0xko7a2dcq.png)

<p>To create our hook, we need to have a file called <strong>pre-commit</strong> (no extension required) inside our <strong>hooks</strong> directory. Let's create it. The only thing the file needs to have is the command you use to run your tests. Also, don't forget to make the file executable (chmod +x).</p>

<p>If your application is, let's say, a Ruby application, you probably run your tests using <strong>rake</strong>. If that's the case, your <strong>pre-commit</strong> file will look like this:</p>

<code>rake test:units</code>

<br><br>

<p>Or if you are into the JS hype, you can probably have this in your file:</p>

<code>npm tests</code>

<p>Independently of the language/framework you are using, your pre-commit hook needs to have the command to run your unit test suite. And, as long as the code inside the hook returns a <strong>zero exit code</strong>, the hook will allow the code to be commited. Otherwise, the commit will be rejected.</p>

<h3>Testing</h3>

<p>At this point you can go ahead and make a commit and see how our tests are run (and hopefully pass), thus opening the gate and letting the commit pass uncontested.</p>

<p>In the following example I am using a Grails application, and the pre-commit hook contains the following code:</p>

<code>grails test-app -unit</code>

![Successful gated commit](https://thepracticaldev.s3.amazonaws.com/i/0oemd9h0kt5luq5ag79g.png)

<h4>
    <strong>Success!</strong> 🥳
</h4>

<p>In the case that the test suite fails:</p>

![Unsuccessful gated commit](https://thepracticaldev.s3.amazonaws.com/i/4g9jrtcxh85z1av658vq.png)

<h4>Sad face 🙁</h4>

<h3>Wrapping up</h3>

<p>We have just created a Git pre-commit hook that contains specific commands to execute our app's unit test suite. <strong>Whenever a commit is issued</strong>, our tests run. If tests pass, we have a successful commit, if not, commit is rejected.</p>

<p>You can extend your tests of the pre-commit hook and build something <strong>as complex as you need</strong>. You can, for example, run a linter tool to make sure style guidelines are being followed. Or take it to the next level and integrate it with your Continuous Integration flow using additional hooks.</p>

<p>Hope this helps you build more resilience into your codebase and, ultimately, deliver more value to your customers in a <strong>safe and rapid</strong> fashion!</p>

*[This post is also available on DEV.](https://dev.to/david_ojeda/gated-commits-with-git)*


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
