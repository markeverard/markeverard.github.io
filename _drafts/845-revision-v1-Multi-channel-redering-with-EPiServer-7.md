---
id: 5301
title: Multi-channel redering with EPiServer 7
date: 2017-07-11T16:47:46+00:00
author: Mark Everard
layout: revision
guid: http://www.markeverard.com/2017/07/11/845-revision-v1/
permalink: /2017/07/11/845-revision-v1/
---
Content management systems and whilst EPiServer has always exceled at Web Content Management,

Prior to EPiServer 7, EPiServer&#8217;s architecture tightly coupled the display of content with the content itself. That is; each content model (PageType) defined a one-to-one mapping with a template (WebForm / .aspx) which was used to present the content to the user. Although this architecture gave rise to editor friendly age-based model, it limited the ability for true multi-channel content re-use.

[<img class="aligncenter size-full wp-image-849" title="EPiServer6-PresentationCoupling" src="http://markeverard.azurewebsites.net/wp-content/uploads/2012/10/EPiServer6-PresentationCoupling.png" alt="" width="600" height="222" srcset="https://www.markeverard.com/wp-content/uploads/2012/10/EPiServer6-PresentationCoupling.png 600w, https://www.markeverard.com/wp-content/uploads/2012/10/EPiServer6-PresentationCoupling-300x111.png 300w, https://www.markeverard.com/wp-content/uploads/2012/10/EPiServer6-PresentationCoupling-580x215.png 580w, https://www.markeverard.com/wp-content/uploads/2012/10/EPiServer6-PresentationCoupling-320x118.png 320w" sizes="(max-width: 600px) 100vw, 600px" />](http://markeverard.azurewebsites.net/wp-content/uploads/2012/10/EPiServer6-PresentationCoupling.png)

Add-on packages such as Composer and MobilePack provided solutions, and there were other extensibility points within the EPiServer API where you could develop you&#8217;re own solution but ultimately this coupling

EPiServer 7 provides new features moving it away from beingÂ a WCM (web content management system) to being a true multi-channel. The removal of the decoupling

[<img class="aligncenter size-full wp-image-850" title="EPiServer7-TemplateResolution" src="http://markeverard.azurewebsites.net/wp-content/uploads/2012/10/EPiServer7-TemplateResolution.png" alt="" width="700" height="273" srcset="https://www.markeverard.com/wp-content/uploads/2012/10/EPiServer7-TemplateResolution.png 700w, https://www.markeverard.com/wp-content/uploads/2012/10/EPiServer7-TemplateResolution-300x117.png 300w, https://www.markeverard.com/wp-content/uploads/2012/10/EPiServer7-TemplateResolution-580x226.png 580w, https://www.markeverard.com/wp-content/uploads/2012/10/EPiServer7-TemplateResolution-320x125.png 320w" sizes="(max-width: 700px) 100vw, 700px" />](http://markeverard.azurewebsites.net/wp-content/uploads/2012/10/EPiServer7-TemplateResolution.png)

Johan Bjorn has already blogged about the ITemplateResolver

### Display Channels

Display channels allow a way to determine how to present data based on the incoming request or some other logic defined by a developer. The most obvious use of this is to present specific mobile views for each page. This type of feature is also now built into the ASP.NET MVC frameworkÂ but EPiServer have also provided a similar function for.

### Blocks and Tagging

You can also specify multiple renderers for Blocks. This enables you to have different presentations depending on which content area a block is dragged to. This is incredibly powerful and allows you to very easily design specific views for each area that a block mena be used in

MVC and

It is now possibile

One of the (many) key enchancements in EPiServer 7 is the decoupling that data from it&#8217;s display.

http://www.ben-morris.com/multi-channel-episerver-de-coupling-content-and-presentation

http://world.episerver.com/Blogs/Johan-Bjornfot/Dates1/2012/9/EPiServer-7&#8211;Rendering-of-content/

Previously when working with Composer you had two choices, design your display (HTML /css) to be flexible enough

### Separation of Data and Rendering

Built-in support for different renderings of the same content depending on context. Ideal for multi-channel pages and shared blocks.

ITemplateResolver &#8211;