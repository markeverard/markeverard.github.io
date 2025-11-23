---
id: 673
title: Missing Personalization Containers
date: 2012-01-06T19:00:36+00:00
author: Mark Everard
layout: post
dsq_thread_id:
  - "1080433837"
categories:
  - Episerver
  - Technical
tags: [Episerver-CMS, Composer, Personalization]
---
Combining Composer with Visitor groups on your EPiServer CMS6 R2 site gives your editors a powerful new way of working with &#8216;personalized&#8217; content, via the built-in Personalization Container Composer element.

This function allows an editor to create a Composer area that will display a unique list of other Composer functions; depending on which visitor group a user is matched to in your site. This means that editors can personalise more than just page content, and can also adjust the page layout and functionality offered to each visitor, by dragging different Composer functions into each Personalized &#8216;slide&#8217; within the Personalization Container function.

## Upgrading a site to 6 R2

After upgrading a few sites from CMS6 to CMS6R2, if you don&#8217;t check the option to import PageTypes during the upgrade process (which if you&#8217;re using PageTypeBuilder you might think you can skip) then  the Personalization container isn&#8217;t created, and you&#8217;ll be missing this piece of functionality.

<p style="text-align: center;">
  <a href="/assets/uploads/2012/01/personalizationcontainer11.png"><img class="aligncenter wp-image-681 size-full" title="personalization container" src="/assets/uploads/2012/01/personalizationcontainer11.png" alt="personalization container" width="650" height="388"  /></a>
</p>

The easiest way to re-create this function is to export the composer PageType information from a &#8216;fresh&#8217; install of the Composer / R2 Alloy Tech site, and then import this into your site.

To save you the hassle of setting up a fresh demo site. I&#8217;ve included an exported Composer Personalization function as a download below (.xml format).

<a title="Personalization Container" href="/uploads/2012/01/PersonalizationContainer1.zip" target="_blank" rel="noopener noreferrer">PersonalizationContainer.zip</a> &#8211; Import using EPiServer Composer Import / Export page.

You just need to import this using the Composer import feature in EPiServer Admin mode, and hey presto!