---
id: 5411
title: Role and user based property security for EPiServer
date: 2017-07-11T16:50:07+00:00
author: Mark Everard
layout: revision
guid: http://www.markeverard.com/2017/07/11/701-revision-v1/
permalink: /2017/07/11/701-revision-v1/
---
One of the requirements I&#8217;ve often seen from some of our bigger (more enterprise) EPiServer clients &#8211; is the ability to add more granular levels of security to the page editing process. By default EPiServer allows the following security to be set up for page editing:

  * **Specify edit / admin rights for each page** &#8211; this allows you to define parts of the page tree that editors have permissions over. These permissions allow you to use the to specify one of the following access levels for each page (Read / Create / Change/ Delete / Publish / Administer) which dictates an editor&#8217;s ability to create / view and publish a page.
  * **Specify access levels required to view each edit mode tab** &#8211; this allows you to group particular page properties onto particular tabs and show or hide those based on the access levels defined above.

However &#8211; what is missing here is a more granular approach for individual properties on each page. For example allowing users in a &#8216;MetaEditor&#8217; role to update page meta data without being able to edit anything else.

EPiServer exposes an EditPanel.LoadedPage event which allows you to modify the loaded PageData object and modify it accordingly. One of the code properties available on an EPiServer property is DisplayEditUI which dictates whether the property is shown on the edit panel. Using the EditPanel.LoadedPage event to set property visibility in this way <a title="Hiding properties in edit mode " href="http://world.episerver.com/Modules/Forum/Pages/thread.aspx?id=41592" target="_blank">isn&#8217;t new</a>.Â However now we have strongly typed PageType classes (thanks to <a title="EPiServer CMS PageTypeBuilder" href="http://pagetypebuilder.codeplex.com/" target="_blank">PageTypeBuilder</a>) we can revisit this and treat it as a true application cross cutting concern.

What I wanted to be able to achieve was a true attribute based security system, that worked with the standard ASP.Net Membership model allowing developers to set which roles and users would be able to view and modify each property by setting an code based attribute in the TypedPageData class.

The solution relies on the following pieces:

  * An EPiServer Initialization module that hooks up a method to the EditPanel.LoadedPage event.
  * An Authorize attribute that you can place on PageTypeBuilder TypedPageData classes and properties.
  * A locator which will scan your current page type for each property and modify the DisplayEditUI property based on the attributes values.

The following rules apply:

  * A property level [Authorize] attribute will override any setting from a class level [Authorize] attribute
  * [Authorize] attributes can set either usernames or roles which will be honoured &#8211; see the example below.
  * The [Authorize] attribute allows you to specify whether you wish to apply the security rules to the built in-EPiServer properties as well as any you have defined through code.

Here is an example usage:

<pre class="brush: csharp; title: ; notranslate" title="">using System;
using FortuneCookie.PropertySecurity;
using PageTypeBuilder;
using EPiServer.Templates.AlloyTech.PageTypes.Tabs;

namespace EPiServer.Templates.AlloyTech.PageTypes.AlloyTech
{
   [PageType(&quot;74f6ef3e-407b-4132-8108-7fa831910197&quot;,
   Name = &quot;[AlloyTech] Standard page&quot;,
   Filename = &quot;/Templates/AlloyTech/Pages/Page.aspx&quot;,
   DefaultChildSortOrder = EPiServer.Filters.FilterSortOrder.None,
   Description = &quot;The standard page is the most commonly used page on the web site.&quot;,
   DefaultVisibleInMenu = true,
   AvailablePageTypes = new Type[] {Â  })]
   [Authorize(Principals = &quot;StandardPageEditorRole,mark.everard&quot;, ApplyToDefaultProperties = false)]
   public class StandardpagePageType : TypedPageData
   {
      [PageTypeProperty(EditCaption = &quot;Main body&quot;,
      HelpText = &quot;The main body will be shown in the main content area of the page&quot;,
      Tab = typeof(InformationTab),
      Type = typeof(EPiServer.SpecializedProperties.PropertyXhtmlString))]
      [Authorize(Principals = &quot;MainBodyEditorRole&quot;)]
      public virtual string MainBody { get; set; }

      [PageTypeProperty(EditCaption = &quot;Secondary body&quot;,
      HelpText = &quot;The contents of this property will be shown in the right column of the page, you can use both text and images for layout.&quot;,
      Tab = typeof(InformationTab),
      Type = typeof(EPiServer.SpecializedProperties.PropertyXhtmlString))]
      public virtual string SecondaryBody { get; set; }
   }
}
</pre>

In this case &#8211; any editor will be able to see the default EPiServer page properties (PageName, PageCategories etc). Editors in the StandardPageEditorRoles and me (user with username mark.everard) will be able to view / edit the SecondaryBody property, and only editors in the MainBodyEditorRole will be able to modify the MainBody property.

At present,Â I&#8217;m not too happy with the blanket approach to the default properties that the current version has. I&#8217;d be interested to hear any better ideas for dealing with this &#8211; or better yet send me a pull request to the <a title="FortuneCookie.PropertySecurity source code at GitHub" href="https://github.com/markeverard/FortuneCookie.PropertySecurity" target="_blank">project on GitHub</a>.

A Nuget package (FortuneCookie.PropertySecurity) built against .Net 4.0 / EPiServer 6R2 and PTB 2.0 has been uploaded for review to the <a title="EPiServer Nuget" href="http://nuget.episerver.com" target="_blank">EPiServer Nuget feed</a> (and so should be available soon). The source code is available on <a title="FortuneCookie.PropertySecurity source code at GitHub" href="https://github.com/markeverard/FortuneCookie.PropertySecurity" target="_blank">GitHub &#8211; https://github.com/markeverard/FortuneCookie.PropertySecurity</a>