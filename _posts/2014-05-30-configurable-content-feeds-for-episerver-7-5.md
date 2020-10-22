---
id: 3732
title: Configurable Content Feeds for EPiServer 7.5
date: 2014-05-30T13:30:09+00:00
author: Mark Everard
layout: post
guid: http://www.markeverard.com/?p=3732
permalink: /2014/05/30/configurable-content-feeds-for-episerver-7-5/
dsq_thread_id:
  - "2723836092"
  - "2723836092"
categories:
  - Episerver
---
AKA &#8211; another (and better!) RSS/ATOM feed for EPiServer. Developing an RSS feed for an EPiServer CMS system is actually quite a simple task and there is already a good amount of <a title="RSS Feed in EPiServer 7" href="http://world.episerver.com/Forum/Developer-forum/EPiServer-7-CMS/Thread-Container/2013/7/RSS-Feed-in-Episerver-7/" target="_blank">information</a> and community options that you can <a title="OpenWaves.Templates.RssFeed for EPiServer" href="http://www.nuget.org/packages/OpenWaves.Templates.RssFeed/" target="_blank">download</a> and use.

However, the particular problem I was presented with required something a little more than what was already out there, so I took the opportunity to build a fuller solution based on CMS 7.5. This may be the last RSS feed module for EPiServer that you&#8217;ll ever need!

This effort started out with a requirement to integrate content from EPiServer CMS to an IBM WebSphere Commerce system (WCS). WCS contains out-of-the-box functionality to <a title="WebSphere Commerce - Integrating with a Content Management System to use the web feed utility" href="http://pic.dhe.ibm.com/infocenter/wchelp/v7r0m0/index.jsp?topic=%2Fcom.ibm.commerce.data.doc%2Ftasks%2Ftml_wcmconfig.htm" target="_blank">consume external content</a> from a publishing feed presented in an ATOM format so this seemed like a suitable integration method.

The design of the pages and content area to be shared required that we shared only specifc HTML snippets from the CMS to the WCS system. This fitted in perfectly with the new Content <a title="Blocks, Block Types and Block Templates in EPiServer" href="http://world.episerver.com/Documentation/Items/Developers-Guide/EPiServer-CMS/7/Content/Pages-and-Blocks/Blocks-Block-Types-and-Block-Templates/" target="_blank">Blocks</a> in EPiServer 7; meaning we needed a content feed that could share not only pages but also content blocks. This additionallyÂ meant that content blocks needed to be routed so the individual output could be consumed by the external WCS solution.

Also the new Media system in EPiServer 7.5 allows the content feeds to handle and output media items in a simpler way. Its all _IContent_ man!

### Editor functionality

  * Editors can create multiple feeds (a feed is a Page Type)
  * Feeds contain a date ordered (most recently published first) list of content items
  * Editors can specify whether a feed is delivered in RSS or ATOM format
  * Editors can specify how many items appear in the feed.
  * Editors have granular control over what content items are shared. They can include any of the following in a single feed. 
      1. Descendents of a specified page in the page tree
      2. Child blocks contained within a specified content folder
      3. Child media items within a specified media folder
      4. Any number of individually selected pages, blocks or media items, via a ContentArea property
  * Blocks included in a feed are externally routed so their HTML output can be consumed by external systems.
  * Feed pages have a partial renderer meaning they can be dragged into Content Areas to display an RSS feed logo and link.

&nbsp;

<img class="aligncenter wp-image-3832 size-full" src="http://www.markeverard.com/wp-content/uploads/2014/05/event-feed.jpg" alt="event-feed" width="500" height="606" srcset="https://www.markeverard.com/wp-content/uploads/2014/05/event-feed.jpg 500w, https://www.markeverard.com/wp-content/uploads/2014/05/event-feed-248x300.jpg 248w, https://www.markeverard.com/wp-content/uploads/2014/05/event-feed-320x388.jpg 320w" sizes="(max-width: 500px) 100vw, 500px" /> 

### Extending and modifying

There isn&#8217;t much to do as a developer beyond installing the package. Note. I&#8217;ve only tested this against an MVC Alloy solution. It should play nicely with WebForms but I&#8217;ve not tested!

  * You may want to change the partial renderer view for a Feed Page. This can be found in /modules/Chief2moro.SyndicationFeeds/Views/Partial.cshtml

<img class="aligncenter size-full wp-image-3852" src="http://www.markeverard.com/wp-content/uploads/2014/05/event-feed-partial.jpg" alt="event-feed-partial" width="400" height="190" srcset="https://www.markeverard.com/wp-content/uploads/2014/05/event-feed-partial.jpg 400w, https://www.markeverard.com/wp-content/uploads/2014/05/event-feed-partial-300x143.jpg 300w, https://www.markeverard.com/wp-content/uploads/2014/05/event-feed-partial-320x152.jpg 320w" sizes="(max-width: 400px) 100vw, 400px" /> 

  * You can override the description that is shown along with each item in the feed by providing a method for the SetItemDescription delegate. By default this is of the form &#8216;An src link to content with id = {content.ContentLink.ID} and name = {content.Name}&#8217;

### Other integration considerations

There is one item not included in the solution which you may need to consider . Whilst the feed presents absolute urls from the feed to pages, blocks and media.Â Any content within a page or block that contains editor set hyperlinks, such as an XHtml property will most likely contain relative urls. If these are consumed and presented on an external website you&#8217;ll get a serious case of the 404&#8217;s.

You can get around this by rewriting any outbound urls in the content to be absolute. The solution I used was to set up a rule in my favourite tool IIS UrlRewrite. This intecepted all outbound html from a particular path and rewrites all links to be absolute. This isn&#8217;t the most flexible solution as it relies on outbound scanning on specific paths. It may be better to add something into this solution, but this isn&#8217;t something I&#8217;ve looked at yet.

### It&#8217;s on Nuget

The source code is available at <a title="Chief2moro.SyndicationFeeds by markeverard on GitHub" href="https://github.com/markeverard/Chief2moro.SyndicationFeeds" target="_blank">https://github.com/markeverard/Chief2moro.SyndicationFeeds</a> &#8211; I&#8217;m happy to accept Pull requests

A package (currently v1.0.0.1)Â is (soon to be &#8211; when its approved) available in the EPiServer Nuget Feed &#8211;Â [http://nuget.episerver.com/](http://nuget.episerver.com/en/?search=possible&sort=MostDownloads&page=1&pageSize=10)Â &#8211; search for CHIEF2MORO.SyndicationFeeds

Happy Feeding!