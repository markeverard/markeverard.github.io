---
id: 5001
title: EPiServer Personalization Engine
date: 2017-07-11T16:33:54+00:00
author: Mark Everard
layout: revision
guid: http://www.markeverard.com/2017/07/11/474-revision-v1/
permalink: /2017/07/11/474-revision-v1/
---
By now you&#8217;ll have heard a lot about the new <a title="New features in EPiServer CMS 6 R2" href="http://world.episerver.com/Blogs/Allan-Thran/Dates/2010/11/EPiServer-CMS-6-Release-2/" target="_blank">Personalization / Visitor Group functionality in EPiServer CMS 6 R2</a>, and with good reason. Not only is there an extensible framework for developers to add their own Visitor Group matching criteria, there is a understandable user interface for editors and the whole concept definitely fits in with current expectations of &#8220;today&#8217;s web&#8221;.

Setting up an area of personalized content is straightforward. Editors create Visitor Groups to categorise users using pre-defined rules and then, using the TinyMCE editor provide personalised content for each group; on every page where they require it.

However, on large scale sites working purely through the rich text editor on a page-by-page could quickly turn into a very large administrative effort.

We discussed this at [Fortune Cookie](http://www.fortunecookie.co.uk "Fortune Cookie"), as we wanted to allow our clients to benefit from the personalization features without the administrative load of setting personalized content through TinyMCE. What was needed was a more [automated way of working with Visitor Groups](http://world.episerver.com/Blogs/Paul-Smith/Dates1/2011/4/Checking-if-a-request-matches-a-Visitor-Group-from-code/ "Checking for a Visitor Group match from code"), one which could provide new pathways to users (beyond regular navigation and search), and promote content deemed as relevant to them.

## Introducing the FortuneCookie.PersonalizationEngine

The open source FortuneCookie PersonalizationEngine for EPiServer is composed of :

  * An API to return a list of &#8216;recommended content pages&#8217; that are personalized to the current user.
  * An EPiServer UI Plugin to allow editors to manage the Personalization Engine rules and output.
  * An extensible framework making it simple for developers to plugin additional Personalization Engine rules.

The overriding idea is to match up a Visitor Group with a means of providing EPiServer content (PageData) that is relevant to that group. Editors can create Visitor Groups and then match them with pre-defined ContentProviders which are configured to provide content from a particular taxonomy. For example &#8211; you may match first-time visitors to your site to all pages which have a PageName property containing the term &#8220;Introduction&#8221; .

[<img class="aligncenter size-full wp-image-477" title="PersonalizationEngineOverview" src="http://markeverard.azurewebsites.net/wp-content/uploads/2011/04/PersonalizationEngineOverview.png" alt="" width="650" height="279" srcset="https://www.markeverard.com/wp-content/uploads/2011/04/PersonalizationEngineOverview.png 650w, https://www.markeverard.com/wp-content/uploads/2011/04/PersonalizationEngineOverview-300x129.png 300w, https://www.markeverard.com/wp-content/uploads/2011/04/PersonalizationEngineOverview-580x249.png 580w, https://www.markeverard.com/wp-content/uploads/2011/04/PersonalizationEngineOverview-320x137.png 320w" sizes="(max-width: 650px) 100vw, 650px" />](http://markeverard.azurewebsites.net/wp-content/uploads/2011/04/PersonalizationEngineOverview.png)

The Personalization engine will iterate through all of the Engine Rules that have been defined by an editor and for each rule

  1. Determine whether the current user is a part of the defined Visitor Group, if so it will ask the specified ContentProvider to return its list of pages.
  2. Collate of all the distinct content provider pages together (maintaining their order) and return them to the calling method.

The main public API to consume the output of the Personalization Engine is listed below. This method would be used by any Page / Control that wished to retrieve a list of &#8216;recommended content&#8217;.

<pre class="brush: csharp; title: ; notranslate" title="">public class PersonalizationEngine
{
     public IEnumerable&lt;PageData&gt; GetRecommendedContent(IPrincipal principal, string languageBranch, int pageCount)
     {
          ....
     }
}
</pre>

ContentProviders allow fine-grained searching and granular content provision by allowing an editor (or through code) to specify a Critieria which is can be used within the GetContent method. For example &#8211; you could define multiple rules using a PageNameContentProvider which would return Pages with names, containing &#8220;Introduction&#8221;, and then one that would return Pages with names containing &#8220;Alloy&#8221;.

Hopefully this concept will become a little more obvious once I&#8217;ve described the ContentProviders that are included with the initial release.

  * **Page search content provider** &#8211; Performs a full-text page property search against the editor defined criteria
  * **Pages in category content provider** &#8211; Performs a FindPagesWithCriteria search to find pages tagged with a Category matching the defined criteria
  * **Pages with pagename content provider** &#8211; Performs a FindPagesWithCriteria search to find pages that have a PageName property matching the defined criteria
  * **Recently changed pages content provider** &#8211; Performs a [RecentlyChangedPagesFinder](http://world.episerver.com/Blogs/Marthin-Freij/Dates/2010/5/Find-the-most-recently-changed-page/ "Recently Changed Pages Finder") search to return the sites recently changed pages
  * **Search referrer page search content provider** &#8211; Performs a full-text page property search against the search term found in the user&#8217;s http header. If you were to combine this with a Visitor Group that looks for referrals from Google, Bing, other Search engine then you can easily replicate the referrer search functionality Allan Thraen blogged about <a title="Referrer-Search-Dynamic-Content" href="http://labs.episerver.com/en/Blogs/Allan/Dates/2009/7/Referrer-Search-Dynamic-Content/" target="_blank">here</a>

Personally I think that the Pages in Category Content Provider is the most flexible, as this allows editors to easily define their own taxonomy using EPiServer categories and have the Personalization Engine push this content to specific Visitor Groups. It would be very straight forward on a commerce site, to use the Personalization Engine to push a list of related products to users who have purchased a particular item.

I&#8217;m sure there are a lot more ideas for ContentProviders, which is why I&#8217;ve spent some time trying to make the API simple to work with (I&#8217;d be interested to hear whether I&#8217;ve achieved that). I&#8217;ll blog about that in future posts.

## Try it now

The Personalization Engine combined with the Visitor Group functionality gives EPiServer editors an additional degree of freedom to provide their site&#8217;s visitors with personalized content.

The PersonalizationEngine requires EPiServer CMS 6 R2 and runs on .Net 3.5 / 4.0. The source code and further developer information is available on <a title="Personalization Engine at CodePlex" href="http://personalization.codeplex.com/" target="_blank">http://personalization.codeplex.com/</a>

The **BEST** way to try this out is to use the **AWESOME** <a title="EPiServer Nuget Feed" href="http://nuget.episerver.com" target="_blank">EPiServer Nuget Feed</a>, and install the package directly into your CMS 6 R2 projects.

And for those of you wondering, Workflows part five is coming soon&#8230;&#8230;&#8230;.