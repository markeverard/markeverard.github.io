---
title: The Great EPiServer Informed Response
color: rgb(0,0,0)
date: 2010-04-20T14:30:26+00:00
author: Mark Everard
layout: post
permalink: /2010/04/20/the-great-episerver-informed-response/
dsq_thread_id:
  - "1073095635"
categories:
  - Episerver
  - Opinion
---
Some of you may have picked up on a blog post entitled the &#8216;[Great EPiServer Rant](http://ugh.cc/the-great-episerver-rant/ "The Great EPiServer Rant")&#8216; posted by Fredrik Holmström. I&#8217;d encourage you to read it for yourself and see what you think.

If you find yourself agreeing with Fredrik, then please read on, as the EPiServer developer community have made great strides in the past year improving the same aspects that Fredrik finds annoying.

I&#8217;ve listed some direct responses to the points Fredrik made in his post. I&#8217;d welcome your comments.

## The development process
Any EPiServer developer will recognise and remember the frustrations that came from having to manually add and update PageTypes in the database especially when working across several environments. It was a royal pain, but not any more. The EPiServer development process has moved on.

I just cannot imagine starting any new EPiServer project without using [PageTypeBuilder](http://pagetypebuilder.codeplex.com/ "PageTypeBuilder") (by [Joel Abrahamsson](http://joelabrahamsson.com/ "Joel Abrahamsson")), which decouples your code from the EPiServer database, and in one fell swoop does away with any need for workarounds and central &#8216;development databases&#8217;. This tool has been actively developed and blogged about since June 2009. Its an invaluable tool for any new EPiServer project. Look into it and if you choose to ignore it&#8230; well, good luck.

## Everything isn’t a page
I couldn&#8217;t agree more, accurately distilling requirements down into a sensible domain model is a non-trivial problem during any software design phase. It brings to mind the following quote:

> _Remember that all models are wrong; the practical question is how wrong do they have to be to not be useful_

[George E. P. Box](http://en.wikipedia.org/wiki/George_E._P._Box) from Box and Draper, Empirical Model-Building, p. 74

Surely a content management system using a domain model based on the concept of pages is reasonable? It has its limitations sure, but there are sensible ways available to extend this model. The latest version of EPiServer (CMS 6) has introduced the [dynamic data store](http://world.episerver.com/Blogs/Paul-Smith/Dates1/2009/10/Introducing-The-Dynamic-Data-Store/ "Dynamic Data Store"), an API that allows the storage and retrieval of arbitrary data structures. Further still, these can be linked directly with the overriding page model via the _Page Objects_ extension API.

If you still feel like you&#8217;re having to push a 50&#8243; square through a 2&#8243; round hole then frankly you&#8217;re doing it wrong. Perhaps EPiServer wasn&#8217;t the right CMS system for your project? Perhaps you are trying too hard to utilise a CMS to store alternative business data that shouldn&#8217;t fall under the remit of a CMS?

Is it really fair to judge a piece of software by what it can&#8217;t do rather than what it can? EPiServer won&#8217;t meet your requirements as a business accounting and reporting system but as a Web Content Management system it certainly can.

## There is no spoon (or documentation)

The available EPiServer documentation has improved immeasurably over the past two years, but thats not to say it can&#8217;t improve further. Much of the improvement has come from the developer community who are invited to allow their content to be aggregated into the [EPiServer World](http://world.episerver.com/ "EPiServer World") site. If you follow this then you&#8217;d surely have seen many great posts by interested EPiServer developers and in particular you couldn&#8217;t have failed to notice [Frederik Vig&#8217;s](http://www.frederikvig.com/ "Frederik Vig") excellent series from December 2009 entitled &#8216;[Creating an EPiServer Site from Scratch](http://www.frederikvig.com/2009/12/introduction-create-an-episerver-site-from-scratch/ "Creating an EPiServer site from scratch")&#8216;, which should be considered the starting point for any developer new to the EPiServer platform.

## Agile – is that a cactus or something?

TDD is an extremely valuable approach and certainly isn&#8217;t impossible to do with EPiServer. The [EPiAbstractions](http://epiabstractions.codeplex.com/ "EPiAbstractions") framework (credit [Joel Abrahamsson](http://joelabrahamsson.com/ "Joel Abrahamsson")) is a library of facades that wrap the core EPiServer classes and exposes virtual methods so you can mock, isolate and test to your heart&#8217;s content.

However the difficulties in using TDD within an EPiServer project aren&#8217;t limited to the EPiServer API. They are passed down from the ASP.NET WebForms framework itself, which is incredibly difficult to unit test because of the strict dependency that the codebehind file has on the HttpContext.

Again however there are viable solutions available. One such is the [WebForms MVP](http://webformsmvp.com/ "WebForms MVP") framework (of which EPiServer already has its own version &#8211; [EPiMVP](http://joelabrahamsson.com/entry/introducing-epimvp-a-framework-for-using-web-forms-mvp-with-episerver-cms "EPiMVP") &#8211; thanks again Joel), which facilitates a better separation of concerns between UI and domain level logic. This enables you to say hello to niceties such as Dependency Injection and other methodologies that facilitate code testing.

## Conclusions

In the two years I&#8217;ve been an EPiServer developer, there has been a noticeable shift from EPiServer towards fostering a strong developer community. This has paid dividends, as the community has provided a great number of excellent blog posts/solutions that have truly improved the EPiServer development experience.

The continued interest in EPiServer WCM products from potential customers demonstrates that a great many developers are successfully delivering high-quality WCM solutions using the EPiServer platform. With the continued input from the community, I&#8217;m sure this will continue.

### Put simply:

Happy developers = Successful implementations = Happy customers

*(and yes, you can put me down as a happy developer!)