---
id: 853
title: Building a Hybrid MVC / WebForms site with EPiServer CMS6R2
date: 2012-10-17T11:30:47+00:00
author: Mark Everard
layout: post
guid: http://www.markeverard.com/blog/?p=853
permalink: /2012/10/17/building-a-hybrid-mvc-webforms-site-with-episerver-cms6r2/
dsq_thread_id:
  - "1073079551"
  - "1073079551"
categories:
  - Episerver
---
We have recently delivered an EPiServer CMS6R2 site where some parts of the application were delivered using the ASP.NET MVC framework, and some using the standard WebForm technology. Although this is currently not a supported or recommended way of building an EPiServer site, I thought I&#8217;d share some of the solutions we had to put in place to create our monster&#8230;&#8230;..

### Hybrid applications

ASP.NET allows you to create <a title="ASP.NET Hybrid applications" href="http://www.hanselman.com/blog/PlugInHybridsASPNETWebFormsAndASPMVCAndASPNETDynamicDataSideBySide.aspx" target="_blank">hybrid applications</a> that combine all parts of the ASP.NET stack, from WebForms, MVC to Dynamic Data and WCF.Â EPiServer itself has been a hybrid application since the release of EPiServer CMS 6. The <a title="Develping EPiServer Gadgets - Tutorial" href="http://world.episerver.com/Documentation/Items/Tech-Notes/EPiServer-CMS-6/EPiServer-CMS-60/Gadgets---Tutorial/" target="_blank">Dashboard feature and Gadgets</a> use the MVC framework and it is possible to integrate your own <a title="Creating EPiServer Plugins using MVC" href="http://world.episerver.com/Blogs/Paul-Smith/Dates1/2011/8/Creating-EPiServer-AdminEdit-Plug-ins-using-MVC/" target="_blank">MVC plugins</a>.

### MVC templates in EPiServer

Despite being a hybrid application, EPiServer 6 does not support delivering CMS managed MVC templates (though <a title="How to set up an empty CMS site with MVC support" href="http://world.episerver.com/Blogs/Per-Gunsarfs/Dates/2012/8/How-to-set-up-an-empty-CMS-site-with-MVC-support/" target="_blank">EPiServer 7 does have this feature</a>). Joel Abrahamsson went some way to allowing this with his <a title="EPiMVC - EPiServer CMS with ASP.NET MVC" href="http://epimvc.codeplex.com/" target="_blank">EPiMVC</a> project. Although the project delivered the basic functionality well, it was <a title="The future of EPiMVC" href="http://joelabrahamsson.com/entry/the-future-of-epimvc" target="_blank">difficult to implement some of the more advanced</a> EPiServer features, such as XForms.

## Why would you do this?<figure id="attachment_857" style="width: 750px" class="wp-caption aligncenter">

<img class="size-full wp-image-857" title="Frankenstein" src="http://markeverard.azurewebsites.net/wp-content/uploads/2012/10/Frankenstein.jpg" alt="" width="750" height="199" srcset="https://www.markeverard.com/wp-content/uploads/2012/10/Frankenstein.jpg 750w, https://www.markeverard.com/wp-content/uploads/2012/10/Frankenstein-300x80.jpg 300w, https://www.markeverard.com/wp-content/uploads/2012/10/Frankenstein-720x191.jpg 720w, https://www.markeverard.com/wp-content/uploads/2012/10/Frankenstein-580x154.jpg 580w, https://www.markeverard.com/wp-content/uploads/2012/10/Frankenstein-320x85.jpg 320w" sizes="(max-width: 750px) 100vw, 750px" /> <figcaption class="wp-caption-text">The beauty of the dream vanished, and breathless horror and disgust filled my heart.</figcaption></figure> 

Good question. Very good question! Our requirements were to build a community-based application rather than a typical brochure-ware CMS site. There was still need for content managed &#8216;article&#8217; type pages but the majority of data presented to a user would come from a set ofÂ 3rd party SOAP web-services. This data composed only a small part of the wider business domain (which is why we didn&#8217;t consider EPiServer Relate as a suitable solution fit).

The community aspects of this project were required to have a &#8216;modern interface&#8217;, i.e. something clean,Â accessible and with support for browsers ranging from no javascript to those allowing for partial page updates using AJAX.Â This isn&#8217;t impossible to achieve with ASP.NET WebForms but it&#8217;s architecture increases the complexity significantly. Also &#8211; higher complexity means you should consider your UI testing strategy &#8211; again something difficult to achieve with WebForms (lookup <a title="WebForms Model View Presenter Framework" href="http://webformsmvp.com/" target="_blank">WebForms MVP</a> if you&#8217;re interested in testable WebForms).

## Our solution

<img class="alignright size-medium wp-image-865" title="EPiServerMVCHybrid" src="http://markeverard.azurewebsites.net/wp-content/uploads/2012/10/EPiServerMVCHybrid-300x235.png" alt="EPiServer MVC Hybrid Logical separation" width="300" height="235" srcset="https://www.markeverard.com/wp-content/uploads/2012/10/EPiServerMVCHybrid-300x235.png 300w, https://www.markeverard.com/wp-content/uploads/2012/10/EPiServerMVCHybrid-320x251.png 320w, https://www.markeverard.com/wp-content/uploads/2012/10/EPiServerMVCHybrid.png 416w" sizes="(max-width: 300px) 100vw, 300px" /> Well &#8211; the title of this piece probably gives this away, but mainly because of the clear separation between the CMS pages and the community application &#8211; we decided to build a hybrid, which would utilise EPiServer as the CMS platform to take advantage of it&#8217;s multi-lingual / personalisationÂ features, but deliver all of the community based use-cases as MVC views supplemented with EPiServer data.

Largely these two approaches were isolated, however there were some custom pieces we had to extend and overrideÂ and build to make a coherent whole.

### A Route aware UrlRewriteProvider

EPiServer has its own UrlRouting/Rewriting feature that allows friendly extension-less URLs to be resolved to WebForm templates. MVC by default has its own routing system that allows requests to be routed to the correct Controller.

We needed MVC routes to be honoured over EPiServer friendly urls; with the requests that don&#8217;t match a route being handled by EPiServer.Â This scenario also allows the MVC templates to be viewed in edit mode by creating a simple container at the correct place in the content tree.

Standard MVC applications define a greedy approach to routing requests &#8211; that is the RouteTable contains a wildcard approach and would in most cases match. We had to change this implementation so that we defined only the routes that we needed to resolve. Along with this, we created our own implementation of a EPiServer FriendlyUrlRewriter that checked each request to see if it was satisfied by the RouteTable and if so deferred the request to the MVC routing system.

Additionally &#8211; we had used the <a title="MVC Routing - IRouteConstraint" href="http://msdn.microsoft.com/en-us/library/system.web.routing.irouteconstraint.aspx" target="_blank">IRouteConstraint interface</a> to allow us to handle EPiServer language segment slugs within the MVC routes. Our custom constraint used the EPiServer LanguageBranch API to ensure that only enabled language slugs were matched by the MVC routing system.

### Delivering PageData to the MVC views

EPiServer&#8217;s API already provides a rich method for content retrieval. As each of the community MVC pages would only ever have a single instance, passing this data to each controller was relatively straight-forward. We usedthe standard EPiServer pattern of creating a settings-page that defines page references to each page instance we require. This gives simple API access to the correct PageData object.

### Reuse of common components

Creating a site using two different display technologies can lead to a duplication of effort when creating consistent/static page elements such as header / footers and navigation. We were keen to avoid this where possible. We used MVC2 and the WebFormViewEngine to create our MVC views. The WebFormViewEngine is built on top of the existing WebForm display technology meaning we could reuse common ASP.NET web controls directly in our MVC views (note this only works for data display &#8211; the PostBack mechanism doesn&#8217;t work). We also built a mechanism for rendering MVC partial views in a WebForm context following a similar method to this [StackOverflow post](http://stackoverflow.com/questions/702746/how-to-include-a-partial-view-inside-a-webform "Include a Partial view inside a WebForm").

### Exposing the EPiServer Resource Provider

We wanted to expose the EPiServer XML resource provider implementation to our MVC views so that we could have a common repository of language resources in use across the site &#8211; so we created a few HTML helper methods that allowed us to hook right in.

### Should I do this?

This is just a top-level overview of the main pieces we had to think about to use MVC and EPiServer in this way. The take-away point here is to be very sure that there is real value in this approach before building anything like this. For us &#8211; it worked (and is still working) really well&#8230;&#8230;&#8230;&#8230;&#8230;..