---
title: 'On handling outages: a case study'
date: '2018-11-09T22:50:06.397Z'
excerpt: >-
  Ramblings about ways to communicate outages using latest Basecamp's outage as
  an example
thumb_img_path: null
comments_count: 0
positive_reactions_count: 34
tags:
  - outage
  - communication
canonical_url: 'https://dev.to/david_ojeda/on-handling-outages-a-case-study-59cb'
layout: post
---


If you happen to use Basecamp 3 to manage your projects, you might have noticed a huge outage on November 8th, 2018; **it lasted almost 5 hours**.

The issue was that they failed to use bigint for the primary keys of their tables so they ran out of IDs. The *TLDR* solution, taken from David Heinemeier- DHH, creator of Ruby on Rails and Founder and CTO at Basecamp: 

> We took half of our replicas offline, did the 3h migration, put them back online, will now be converting the other half of the fleet.

And I'm not writing this to expose and/or throw **** at them. 

I'm writing this to **applaud their communication and openness** about the whole outage.

I'm writing this to expose how **over-communication, honesty, humbleness and clarity DO make a difference**, specially on difficult situations.

---

To give you some context, the first notice on their Twitter account about something going wrong was at 5:40 AM on November, 8th:


<iframe class="liquidTag" src="https://dev.to/embed/tweet?args=1060527469361537024" style="border: 0; width: 100%;"></iframe>


From that tweet and until everything was working again, there were 15 more tweets with constant updates! With the last one being at 10:47 AM, November 8th, signed by DHH himself:


<iframe class="liquidTag" src="https://dev.to/embed/tweet?args=1060604787819827201" style="border: 0; width: 100%;"></iframe>


All that information is a **huge deal**. You know they are working really hard to get everything up and going, and you might also know that outages can get really messy. 

Despite all the chaos that was probably happening, they kept posting updates with specific details of the cause and solution being taken- on their Twitter account, status page and on their blog. And not only that, DHH was also posting some more technical details about the outage to the point where **he links to the pull request that could have saved everything**:


<iframe class="liquidTag" src="https://dev.to/embed/tweet?args=1060565296048562177" style="border: 0; width: 100%;"></iframe>


---

I find all this **incredibly valuable and relieving**. Even though it was a really long outage, they handled each and every customer interaction gracefully. I could not get upset with them with so many information about the problem/solution being provided. 

Hell, that morning **I was even more productive because Basecamp remained read-only**; I could check on what was pending on my side and just get to it with no distractions.

I've been part, and cause, of outages at my company and it's really stressful. And we don't even handle the amount of traffic Basecamp 3 does.

So, as DHH states it, **this is a reminder to stay humble**. We could be the next ones involved in a situation like this. **We all make mistakes, that's inevitable**, but knowing how to properly communicate them is what matters in the long run.

--- 

Hope you have enjoyed this short rambling ❤️

*[This post is also available on DEV.](https://dev.to/david_ojeda/on-handling-outages-a-case-study-59cb)*


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
