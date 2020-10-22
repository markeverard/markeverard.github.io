---
id: 4321
title: Using WebHooks in an EPiServer solution
date: 2016-08-06T15:01:22+00:00
author: Mark Everard
layout: revision
guid: http://markeverardcom.azurewebsites.net/?p=4321
permalink: /2016/08/06/4311-revision-v1/
---
<a href="https://en.wikipedia.org/wiki/Webhook" target="_blank">WebHooks</a> are a way of connecting internet / cloud services together. They allow websites to communicate with each other via HTTP callbacks. Services can subscribe (via HTTP) to receive notifications from publishers about a specific event. Publishers, manage these subscriptions and then on each event push notifcations via an HTTP Post to each receiver, at an endpoint defined during subscription.

<img class="aligncenter wp-image-5252 size-medium" src="http://www.markeverard.com/wp-content/uploads/2015/11/web-hooks-258x300.png" sizes="(max-width: 258px) 100vw, 258px" srcset="http://www.markeverard.com/wp-content/uploads/2015/11/web-hooks-258x300.png 258w, http://www.markeverard.com/wp-content/uploads/2015/11/web-hooks.png 349w" alt="web-hooks" width="258" height="300" /> 

### ASP.NET WebHooks

Microsoft have recently released a [WebHook framework for ASP.NET](https://github.com/aspnet/WebHooks) that gives you a pattern for:

  * Handling subscriptions from interested subscribers
  * Sending subscriptions to publishers
  * Sending published messages to subscribers
  * Handling publisher messages from subscribed services (via Receivers)

Along with the basic framework, they have also provided implementations for some very common services like [Dropbox](http://dropbox.com/), [GitHub](http://www.github.com/), [Instagram](http://www.instagram.com), [PayPal](http://www.paypal.com/), [Pusher](http://www.pusher.com/), [Salesforce](http://www.salesforce.com/), [Slack](http://www.slack.com/), [Stripe](http://www.stripe.com/), [Trello](http://www.trello.com/), and [WordPress](http://www.wordpress.com/)

### Webhooks in EPiServer

What would an EPiServer implementation / usage of WebHooks look like?

  * Publish content events to subscribers (system to system integration)
  * Publish Form data inputs to external systems
  * Publish / Subscribe to Catalog events and changes from integrated commerce systems (Stock control and pricing)
  * Subscribe to events from external systems (Payments)
  * Subscribe to external content events – Instagram / social

### Ascend London

I was invited to talk (along with fellow EMVP <a href="http://world.episerver.com/Blogs/K-Khan-/" target="_blank">Khurram Khan</a>) at the <a href="http://www.episerver.com/about-us/event-listing/london-ascend-2015/ascend-2015/ascend-london-technical-track-agenda-2015/" target="_blank">technical track</a> at <a href="http://www.episerver.com/about-us/event-listing/london-ascend-2015/ascend-2015/" target="_blank">EPiServer Ascend London 2015</a>. You can download the slides from <a href="http://www.slideshare.net/ev2000/publisher-subscriber-integrations-using-episerver" target="_blank">Slideshare</a>.

<img class="aligncenter" title="Look Mum, its me :" src="http://www.markeverard.com/wp-content/uploads/2015/11/IMG_4942.jpg" alt="" width="400" height="300" />  
As part of a presentation I put together a simple solution demonstrating an EPiServer site with an <a href="http://blogs.msdn.com/b/webdev/archive/2015/09/21/integrating-with-instagram-using-asp-net-webhooks-preview.aspx" target="_blank">Instagram receiver</a> that provided a solution to the below user story.

> “As a content editor, I want images that are uploaded on a social channel (Instagram) and tagged with ‘ascend15’ to be available in my content management system so I can use them on my awesome website”

The solution contained the following elements:

  * A receiver accepting notifications from Instagram when a image with a tag of ‘ascend15’ was added (using the ASP.NET WebHook framework)
  * A Dynamic Data Store implementation to store the number of notifications received
  * A scheduled job: to request, download and import images into EPiServer as MediaData / IContent items

The solution and even the scenario was a little contrived, so I’m not going to show the code (though it you really want it just drop me a line). It did however work on the day, which when you’re trying a tech demo that relies on the cloud and external services and also your own code; is always nice<img class="wp-smiley" style="height: 1em; max-height: 1em;" src="http://www.markeverard.com/wp-includes/images/smilies/simple-smile.png" alt=":)" />