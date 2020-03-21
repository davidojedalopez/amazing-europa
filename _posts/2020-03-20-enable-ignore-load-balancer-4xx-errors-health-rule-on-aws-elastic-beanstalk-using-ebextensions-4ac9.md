---
title: >-
  Enable "Ignore load balancer 4xx errors" health rule on AWS Elastic Beanstalk
  using .ebextensions
date: '2020-03-20T00:50:17.803Z'
excerpt: >-
  Enable "Ignore load balancer 4xx errors" health rule on AWS Elastic Beanstalk
  using .ebextensions
thumb_img_path: null
comments_count: 0
positive_reactions_count: 7
tags:
  - aws
  - elasticbeanstalk
canonical_url: >-
  https://dev.to/david_ojeda/enable-ignore-load-balancer-4xx-errors-health-rule-on-aws-elastic-beanstalk-using-ebextensions-4ac9
layout: post
---


If you are an **Elastic Beanstalk (EB)** user, you are probably aware of a frequently [requested feature that was released on July 25, 2018](https://aws.amazon.com/releasenotes/release-aws-elastic-beanstalk-support-for-enhanced-health-rule-customization-on-july-25-2018/?tag=releasenotes%23keywords%23aws-elastic-beanstalk): To ignore application 4xx errors when determining your environment's health. It's was a new EB health rule that ignores 400-499 HTTP status codes when alerting if your environment instances are having trouble 🏥.  
  
It is common for applications to receive many 4xx errors, for example, due to:  
  

*   Client's API integrations using invalid credentials.
*   Client-side test tools.
*   Broken links that create 404 responses.

  
I started to use this feature since the moment it was released.  
  

Today I logged in into my EB console and switched the design to the latest version of the console. Everything okay. Then, as I was exploring the new console, I noticed a new configuration that was not previously available: **To ignore load balancer 4xx errors**.  
  
In this post, I will show you, through a story, how to enable this feature using [_.ebextensions_](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/ebextensions.html), because, infrastructure-as-code, you know 🤓. The funny part, I can't seem to find the documentation anywhere on AWS. Never happened before, right? 🙃.   
  
Follow along!  
  

# Digging through the docs 🗂

  
The first thing I do when trying to configure this new feature is: go to the [docs page where the almost identical feature is documented](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/health-enhanced-rules.html), that is, the ignore **application** 4xx errors. I found that is the same page as it was before, with no extra information about a load balancer health rule. Citing the docs:  
  

> Currently, this is the only available enhanced heath rule customization. You can't configure enhanced health to ignore HTTP errors returned by an environment's load balancer, or other HTTP errors in addition to 4xx.

  
Liars! Just kidding 😝   
  
Without luck on this doc page, I proceeded to look somewhere else:  
  

*   Went through the [EB release notes](https://aws.amazon.com/releasenotes/?tag=releasenotes%23keywords%23aws-elastic-beanstalk) to see if I missed the announcement. No luck.
*   Went to the [EB public roadmap](https://github.com/aws/elastic-beanstalk-roadmap/projects/1) and found nothing there either.
*   [Asked on Twitter](https://twitter.com/DavidOjedaL/status/1240705686343798784), but since no one follows all I got was a response from AWS support to look at their forum. Go [follow me](https://twitter.com/DavidOjedaL) on Twitter! 👀.
*   Went to the EB forums and only found people asking how to work around the fact that there was no feature to ignore load balancer 4xx errors 🤦🏻‍♂️.
*   Asked a [question in StackOverflow](https://stackoverflow.com/questions/60764891/how-to-enable-aws-elastic-beanstalk-health-rule-ignore-load-balancer-4xx-throu?noredirect=1# comment107510426_60764891) and got a comment saying that I should create a support ticket.

  

# What next❓

  
I had already searched through a lot of docs without any progress. I posted the question on Twitter and StackOverflow, and I wasn't expecting any response soon. I thought about trying to guess the new field names and do a try-error session.  
  
My current configuration document looks like this:  
  

{% raw %}```json
{
  "Rules": {
    "Environment": {
      "Application": {
        "ApplicationRequests4xx": {
          "Enabled": false
        }
      }
    }
  },
  "Version": 1
}
```{% endraw %}

  
If I were the software developer that created this feature, how would I call the new field? Some options:  
  

*   LoadBalancerRequests4xx
*   ALBRequests4xx
*   ELBRequests4xx

  
But, should they be nested under "Application" or should it be another high-level field? By this time I was already hungry, so I went to cook and eat 👨🏻‍🍳🍲  
  

# **Enlightenment**! 🔮

  
**Taking a break helps**, you should consider it in your daily routine. Back to the topic...   
  
I realized that even though I can't find a way to configure this feature through code, I can do it directly in the console. This would only be temporary because the environments re-create themselves on each deploy- any new environment would have this feature disabled.  
  
And here is where I got an idea💡 What if I:  
  

1.  Enable the feature on the console.
2.  Save the environment configuration as an [EB Saved Configuration](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/environment-configuration-savedconfig.html).
3.  Retrieve it with the EB CLI to see how the field is called at an API level.

  
That's exactly what I did. Once the configuration was saved, I retrieved it:  
  

{% raw %}```bash
$ eb config get NAME_OF_MY_CONFIGURATION
```{% endraw %}

  
and 💥, the configuration showed itself:  
  

{% raw %}```json
{
  "Rules": {
    "Environment": {
      "ELB": {
        "ELBRequests4xx": {
          "Enabled": false
        }
      },
      "Application": {
        "ApplicationRequests4xx": {
          "Enabled": false
        }
      }
    }
  }
}
```{% endraw %}

  
There was a high-level field called "_ELB"_, and a property "_ELBRequests4XX_"; I was not that erred 👨🏻‍💻.  
  
I added those new fields to my .config file on the _.ebextensions_ folder and everything worked as expected 👏🏼 Here is the final .config file I used:  
  

{% raw %}```yaml
option_settings:
  - namespace: aws:elasticbeanstalk:healthreporting:system
    option_name: ConfigDocument
    value: {
"Rules": {
  "Environment": {
    "ELB": {
      "ELBRequests4xx": {
        "Enabled": false
      }
    },
    "Application": {
      "ApplicationRequests4xx": {
        "Enabled": false
      }
    }
  }
},
"Version": 1
}
```{% endraw %}

  

# Wrap up 🔄

  
I can't imagine how complex is to add a feature to a system as big as AWS while maintaining all the docs updated. I don't blame them.  
  
Nonetheless, we figured out at the end 🙌🏼. In short, to configure your EB environments to ignore load balancer 4xx errors, you need to add the previous .config file to your .ebextensions folder and deploy a new version.  
  
Maybe the docs are updated by the time you read this, and the story will not be that fun 🙃 Anyhow, it was a blast to write.  
  
I hope you enjoyed it. Thanks for reading me 💙

*[This post is also available on DEV.](https://dev.to/david_ojeda/enable-ignore-load-balancer-4xx-errors-health-rule-on-aws-elastic-beanstalk-using-ebextensions-4ac9)*


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
