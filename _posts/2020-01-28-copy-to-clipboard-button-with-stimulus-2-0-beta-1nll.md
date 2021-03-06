---
title: Copy to clipboard button with Stimulus 2.0 (Beta)
date: '2020-01-28T00:46:41.565Z'
excerpt: >-
  Stimulus is a JavaScript framework developed by a team at Basecamp, and it
  aims to augment your exist...
thumb_img_path: null
comments_count: 0
positive_reactions_count: 19
tags:
  - stimulus
  - javascript
  - webdev
canonical_url: >-
  https://dev.to/david_ojeda/copy-to-clipboard-button-with-stimulus-2-0-beta-1nll
layout: post
---
[**Stimulus**](https://stimulusjs.org/handbook/introduction) is a JavaScript framework developed by a team at [Basecamp](https://basecamp.com/), and it aims to augment your existing HTML so things work without too much "connecting" code.

Contrary to other frameworks, Stimulus doesn't take over your front-end, so you can add it without too much hassle to your already running app.  

**Its documentation is very clear and digestible**. Included in its handbook is an [example of building a clipboard functionality](https://stimulusjs.org/handbook/building-something-real), which I recommend you go through if you are trying Stimulus for the first time.

Right now we are **replicating** that functionality and adding a couple more things **using a development build** specified in this Pull Request (PR)


<iframe class="liquidTag" src="https://dev.to/embed/github?args=https%3A%2F%2Fgithub.com%2Fstimulusjs%2Fstimulus%2Fpull%2F202" style="border: 0; width: 100%;"></iframe>


It **includes new APIs that will be released with version 2.0** of the framework, so they are not yet available with the current stable production release.

# What are we building?

A one-time password "copy to clipboard" button what wraps the DOM Clipboard API.

You can access the final working version on [Glitch](https://glitch.com/edit/# !/trapezoidal-seer):


<iframe class="liquidTag" src="https://dev.to/embed/glitch?args=trapezoidal-seer" style="border: 0; width: 100%;"></iframe>


# Starting off

First, we are creating our base HTML where the one-time password will be and the actual button to copy it:

{% raw %}```html
<div>
  <label>
    One-time password:
    <input type="text" value="fbbb5593-1885-4164-afbe-aba1b87ea748" readonly="readonly">
  </label>

  <button>
    Copy to clipboard
  </button>
</div>
```{% endraw %}

![Text input with "copy to clipboard button" rendered HTML](https://thepracticaldev.s3.amazonaws.com/i/bu8kact7stjzee0flm5a.png)


This doesn't do anything by itself; we need to add our Stimulus controller.

# The controller definition

In Stimulus, **a controller is a JavaScript object that automatically connects to DOM elements that have certain identifiers**.

Let's define our clipboard controller. The main thing it needs to do? Grab the text on the input field and copy it to the clipboard:

{% raw %}```javascript

(() => {
  const application = Stimulus.Application.start();

  application.register("clipboard", class extends Stimulus.Controller {
    // We'll get to this below
    static get targets() {
      return ['source']
    }

    copy() {
      // Here goes the copy logic 
    }
  });

})();
```{% endraw %}

Now, this is a valid controller that doesn't do anything because it's not connected to any DOM element yet.

# Connecting the controller

Adding a {% raw %}`data-controller`{% endraw %} attribute to our {% raw %}`div`{% endraw %} will enable the connection:

{% raw %}```html
<div data-controller="clipboard">

[...]
```{% endraw %}

Remember the {% raw %}`static get targets()`{% endraw %} from above? That allows us to **access DOM elements as properties in the controller**. 

Since there is already a {% raw %}`source`{% endraw %} target, we can now access any DOM element with the attribute {% raw %}`data-clipboard-target="source"`{% endraw %}:

{% raw %}```html
[...]

<input data-clipboard-target="source" type="text" value="fbbb5593-1885-4164-afbe-aba1b87ea748" readonly="readonly">

[...]
```{% endraw %}

Also, we need the button to actually do something. We can link the "Copy to clipboard" button to the {% raw %}`copy`{% endraw %} action in our controller with another identifier: {% raw %}`data-action="clipboard# copy"`{% endraw %}. The HTML now looks like this:

{% raw %}```html
<div data-controller="clipboard">
  <label>
    One-time password:
    <input data-clipboard-target="source" type="text" value="fbbb5593-1885-4164-afbe-aba1b87ea748" readonly="readonly">
  </label>

  <button data-action="clipboard# copy">
    Copy to clipboard
  </button>
</div>
```{% endraw %}

Our controller is now automatically connected to the DOM, and clicking the copy button will invoke the {% raw %}`copy`{% endraw %} function; let's proceed to write it.

# The copy function

This function is essentially a **wrapper of the DOM Clipboard API**. The logic goes like this:

{% raw %}```javascript
[...]

copy() {
  this.sourceTarget.select();
  document.execCommand('copy');
}

[...]
```{% endraw %}

We take the {% raw %}`source`{% endraw %} target we defined earlier, our text input that is, select its content, and use the Clipboard API to copy it to our clipboard.

At this point, **the functionality is practically done!** You can press the button and the one-time password is now available for you on your clipboard.

# Moving further

The copy button works now, but we can go further. **What if the browser doesn't support the Clipboard API or JavaScript is disabled?**

If that's the case, we are going to hide the copy button entirely.

# Checking API availability

We can check if the {% raw %}`copy`{% endraw %} command is available to us by doing this:

{% raw %}```javascript
document.queryCommandSupported("copy")
```{% endraw %}

One of the best places to check this is when the Stimulus controller connects to the DOM. Stimulus gives us some nice **lifecycle callbacks** so we can know when this happens. 

We can create a {% raw %}`connect`{% endraw %} function on our controller and it will be invoked whenever this controller connects to the DOM:

{% raw %}```javascript
[...]

connect() {
  if (document.queryCommandSupported("copy")) 
    // Proceed normally
  }
} 

[...]
```{% endraw %}

One way to hide/show the copy button depending on the API availability is to initially load the page with the button hidden, and then displaying it if the API is available. 

To achieve this we can rely on CSS:

{% raw %}```css
.clipboard-button {
  display: none;
}

/* Match all elements with .clipboard-button class inside the element with .clipboard--supported class */
.clipboard--supported .clipboard-button {
  display: initial;
}
```{% endraw %}

Our button is now hidden from the beginning, and will only be visible when we add the {% raw %}`.clipboard--supported`{% endraw %} class to our {% raw %}`div`{% endraw %}.

To do it, we modify the connect lifecycle callback. 

Here is where we can start to see major differences from this latest development version. With the actual production version you would need to specify the CSS class in the controller, effectively doing this:

{% raw %}```javascript
[...]

connect() {
  if (document.queryCommandSupported("copy")) 
    this.element.classList.add('clipboard--supported');
  }
} 

[...]
```{% endraw %}

**There is a new, better way to achieve it.**

# Classes API

Now, **CSS classes can be actual properties of the controller**. To do so, we need to add some identifiers to our HTML and add a new array to our controller:

{% raw %}```html
<div data-controller="clipboard" data-clipboard-supported-class="clipboard--supported" class="clipboard">

[...]
```{% endraw %}

{% raw %}```javascript
[...]

application.register("clipboard", class extends Stimulus.Controller {

[...]

  static classes = ['supported']

  connect() {
    if (document.queryCommandSupported("copy")) 
      this.element.classList.add(this.supportedClass);
    }
  } 
[...]
```{% endraw %}

Great! Now we can access our supported class string from our controller with {% raw %}`this.supportedClass`{% endraw %}. **This will help keep things loosely coupled.**

The clipboard real-life example from Stimulus' handbook ends here. Now, to show the other newest additions and use the *Classes API* once more, we're adding the following functionality:

- A new style to the "Copy to clipboard" button once it has been clicked
- A refresh interval for the one-time password. This will generate a new password every 2.5 seconds
- A data attribute to keep track of how many times the password has been generated

# Values API

This, along with the *Classes API*, is one of the new additions to Stimulus. Before this API you would need to add arbitrary values to your controller with the Data Map API, that is, adding {% raw %}`data-[identifier]-[variable-name]`{% endraw %} to your DOM element, and then parsing that value in your controller. 

This created boilerplate such as getters and setters with calls to {% raw %}`parseFloat()`{% endraw %}, {% raw %}`parseInt()`{% endraw %}, {% raw %}`JSON.stringify()`{% endraw %}, etc. This is how it will work with the *Values API*:

{% raw %}```html
<div data-controller="clipboard" data-clipboard-supporte-class="clipboard--supported" data-clipboard-refresh-interval-value="2500" class="clipboard">

[...]
```{% endraw %}

{% raw %}```javascript
[...]

application.register("clipboard", class extends Stimulus.Controller {

[...]

  static values = {
    refreshInterval: Number
  }

  connect() {
    if (document.queryCommandSupported("copy")) 
      this.element.classList.add(this.supportedClass);
    }
    // Access refreshInterval value directly
    this.refreshIntervalValue; // 2500
  } 
[...]
```{% endraw %}

**Accessing your controller values is now cleaner since you don't need to write your getters and setters, nor do you need to parse from String to the type you need.**

Moving forward, let's write the one-time password refresh.

# Implementing password generation

We're going to define a new function to create a new random password. [I grabbed this random UUID generator snippet from the internet](https://www.arungudelli.com/tutorial/javascript/how-to-create-uuid-guid-in-javascript-with-examples/):

{% raw %}```javascript
([1e7]+-1e3+-4e3+-8e3+-1e11).replace(/[018]/g, c =>
                (c ^ crypto.getRandomValues(new Uint8Array(1))[0] & 15 >> c / 4).toString(16));
```{% endraw %}

Adding it to our Stimulus controller:

{% raw %}```javascript
  connect() {
    if (document.queryCommandSupported("copy")) 
      this.element.classList.add(this.supportedClass);
    }
    if(this.hasRefreshIntervalValue) {
          setInterval(() => this.generateNewPassword(), this.refreshIntervalValue)  
    } 
  } 

  // copy function

  generateNewPassword() {
    this.sourceTarget.value = ([1e7]+-1e3+-4e3+-8e3+-1e11).replace(/[018]/g, c =>
                (c ^ crypto.getRandomValues(new Uint8Array(1))[0] & 15 >> c / 4).toString(16));
  }
[...]
```{% endraw %}

We use {% raw %}`setInterval`{% endraw %} to refresh our password text field each 2500ms since that's the value we defined in the DOM. 

**Our refresh feature is now working!** Some things still missing:
- Add new style when copy button is clicked
- Keep track of how many times a password is generated

Giving all we have learned so far, this is what's need to be done:
- Add a new CSS class to the stylesheet, DOM element, and controller
- Add this new class when the button is clicked, and remove it when the password is refreshed
- Add to a counter when the password refreshes

This is how it will look at the end:

{% raw %}```css 
/* CSS */

.clipboard-button {
 display: none;
}

.clipboard--supported .clipboard-button {
  display: initial;
}
      
.clipboard--success .clipboard-button {
  background-color: palegreen;
}
```{% endraw %}

{% raw %}```html
<!-- HTML -->

<div data-controller="clipboard" 
     data-clipboard-refresh-interval-value="2500"
     data-clipboard-supported-class="clipboard--supported" 
     data-clipboard-success-class="clipboard--success"      
     data-clipboard-times-generated-value="1" 
     >
      
      <label>
        One-time password: <input data-clipboard-target="source" type="text" value="fbbb5593-1885-4164-afbe-aba1b87ea748" readonly="readonly">
      </label>
      
      <button data-action="clipboard# copy"               
              class="clipboard-button" >
        Copy to Clipboard
      </button>
            
    </div>
```{% endraw %}

{% raw %}```javascript
 // JavaScript

 (() => {
    const application = Stimulus.Application.start()

    application.register("clipboard", class extends Stimulus.Controller {

      static get targets() {
        return ['source']
      }

      static values = {              
        refreshInterval: Number,
        timesGenerated: Number
      }

      static classes = ['supported', 'success'];

      connect() {                 
        if (document.queryCommandSupported("copy")) {
          this.element.classList.add(this.supportedClass);                
        }                            
        if(this.hasRefreshIntervalValue) {
          setInterval(() => this.generateNewPassword(), this.refreshIntervalValue)  
        } 
      }


      copy() {              
        this.sourceTarget.select();
        document.execCommand('copy');
        this.element.classList.add(this.successClass);
      }

      generateNewPassword() {              
        this.sourceTarget.value = ([1e7]+-1e3+-4e3+-8e3+-1e11).replace(/[018]/g, c =>
          (c ^ crypto.getRandomValues(new Uint8Array(1))[0] & 15 >> c / 4).toString(16));     
        this.element.classList.remove(this.successClass);
        this.timesGeneratedValue++;
      }                  

      // NEW! Read about it below
      timesGeneratedValueChanged() {              
        if(this.timesGeneratedValue !== 0 && this.timesGeneratedValue % 3 === 0) {
          console.info('You still there?');
        }
      }

    });

 })();
```{% endraw %}

Apart from what we've already discussed about the *Values API*, there is also something new: **Value changed callbacks**.

These callbacks are called whenever a value changes, and also once when the controller is initialized. They are connected automatically given we follow the naming convention of {% raw %}`[valueName]ValueChanged()`{% endraw %}. 

We use it to log a message each time the password has been refreshed three times, but they can help with state management in a more complex use case. 


# Wrapping up

I've created multiple Stimulus controllers for my daily job, and I must say that I always end up pleased with the results. Stimulus encourages you to keep related code together and, combined with the additional HTML markup required, ends up making your code much more readable.

If you haven't tried it yet, I highly recommend going for it! It offers a different perspective, one of magic 🧙🏻‍♂️. 


Thanks for reading me 👋🏼.





*[This post is also available on DEV.](https://dev.to/david_ojeda/copy-to-clipboard-button-with-stimulus-2-0-beta-1nll)*


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
