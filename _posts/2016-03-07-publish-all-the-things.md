---
title: Publish all the things!
date: 2016-03-07T11:00:01+00:00
author: Mark Everard
layout: post
color: rgb(0,0,0)
permalink: /2016/03/07/publish-all-the-things/
dsq_thread_id:
  - "4639511504"
categories:
  - Episerver
  - Technology
---
If you’ve seen some of my recent posts you’ll know I’ve been playing around with <a href="http://www.markeverard.com/2015/11/11/using-webhooks-in-an-episerver-solution/" target="_blank">WebHooks</a>. These are simple HTTP requests to endpoints allowing services to exchange data and events.

## Integration Platforms as a Service

Those of you cool kids will have known about <a href="http://ifttt.com/" target="_blank">IFTTT</a> (If This Then That) for some time. It’s a online tool that acts as a way to glue a huuuge number of online services together into simple workflow pairs called ‘recipes’. Each service can provide a number of triggers to the platform, which are then linked to actions provided by other services.

I’ve got a number of personal recipes set up, some useful some not. but all of them are good fun!

* If somebody tags me in a picture on Facebook, then upload the picture to my OneDrive account
* If one of my team creates a new GitHub repository then email me
* If I arrive or leave my office then log the time in a Google Doc.
* If an astronaut is launched into space then send me a notification

Whilst IFTTT targets individual users, other <a href="http://www.gartner.com/it-glossary/information-platform-as-a-service-ipaas" target="_blank">iPaaS</a> platforms focus more towards business and enterprise. These will become increasingly useful and popular as a cheap means of system integration and data exchange. For example – if somebody submits a payment via the organisation’s payment gateway, log the entry directly into the cloud-based finance system. You won’t need development teams to set these up, just someone to configure the data exchange / webhook endpoints.

## Publish your home!

I’ve been playing around with smart home devices (<a href="https://www.tado.com" target="_blank">tado</a> smart heating and <a href="http://www.wemo.com/" target="_blank">WeMo </a>lights and switches). I wanted to experiment a little with the API’s they offered.

One of the channels IFTTT offer is called the <a href="https://ifttt.com/maker" target="_blank">Maker</a> channel. This is a user specific channel that will accept http triggers to an endpoint and can also make requests to a specified endpoint. It allows you pass up to 4 data points from a trigger or to an action.

![Publish…… and there was light, and slippers](http://www.markeverard.com/wp-content/uploads/2016/03/publish-lights-on.gif)
  
*Publish…… and there was light, and slippers*

As I’ve demonstrated <a href="http://www.markeverard.com/2015/11/11/using-webhooks-in-an-episerver-solution/" target="_blank">before</a>, adding webhooks to an existing ASP.NET app is straightforward. I decided I’d hook an Episerver solution up to an IFTTT maker channel, meaning when I clicked the publish button I could make all manner of amazing things happen, like turning my lights on.

The code to do this is straightforward (see below). I’m using an Episerver initialisation module to hook a handler for the Content Published event. I’ve also built a simple helper method to call the Maker channel API. To use this you’d just have to add in your own API key that you get when you configure the Maker channel on IFTTT.