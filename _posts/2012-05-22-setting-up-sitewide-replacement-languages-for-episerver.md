---
title: Setting up sitewide replacement languages for EPiServer
date: 2012-05-22T19:58:00+00:00
author: Mark Everard
layout: post
color: rgb(0,0,0)
permalink: /2012/05/22/setting-up-sitewide-replacement-languages-for-episerver/
dsq_thread_id:
  - "1079319524"
categories:
  - Episerver
---
Fallback languages allow editors to specify inheritance chains for content at the page property level, meaning that if content isn&#8217;t available in the user selected language, the fallback language is served. This can be useful when working with a large site that requires a translation effort &#8211; meaning that you can translate parts of pages at a time.

I recently dealt with a requirement from a client who wanted to start over with their translation effort which had occurred at different rates across large areas of their site. They wanted just the English language version to be served. There are many possible solutions to this &#8211; such as putting in top-level redirects for all language variants through IISÂ &#8211; however the editors wanted to retain the control of when they could re-enable a language for a particular page.

This meant removing all of their existing fallback and replacement languages settings for each and every page and then ensure that English was served as a replacement language for every page on every language variant.

Obviously there are SEO impacts of serving <a title="How to handle multiple urls serving the same content" href="http://webmasters.stackexchange.com/questions/24922/multiple-urls-serving-same-content-how-to-handle-betterhttp://" target="_blank">identical contents on different urls,</a> our solution also included a technical piece that handled this &#8211; perhaps I&#8217;ll share that next time if anybody is interested.

### Under the hood
EPiServer stores language replacement and fallback in the sql server table **tblPageLanguageSetting**

![tblPagelanguageSetting](/assets/uploads/2012/05/tblPagelanguageSetting.gif)  

If no data is found for a page &#8211; then the EPiServer code assumes that the fallback/replacement language data is inherited from a parent page. This behaviour is chained all the way up the tree to the site&#8217;s start page.

If you add data through the UI then EPiServer will add an entry to the table for each enabled language branch (in the above image I have three site languages enabled). When it does this it adds a null entry for each enabled language that doesn&#8217;t have a fallback or replacement language defined. The presence of this entry is enough for EPiServer to know that the language data isn&#8217;t inherited.

So achieving our requirement is actually pretty straightforward. Ensure that the language settings are correct on the homepage and then remove all entries in this table apart from entries with _fkPageID_ equal to the start page ID. (Note this wasn&#8217;t an enterprise scenario &#8211; you may have to be a little more granular with what you delete in that instance)

Obviously running direct SQL against the EPiServer schema isn&#8217;t recommended (or supported by EPiServer) &#8211; so **BE CAREFUL** &#8211; with great power comes great responsibility. However this is a huge time-saver over using the UI to achieve the same result.