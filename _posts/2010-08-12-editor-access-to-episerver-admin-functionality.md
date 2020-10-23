---
id: 124
title: Editor access to EPiServer Admin functionality
date: 2010-08-12T13:11:57+00:00
author: Mark Everard
layout: post
guid: http://www.markeverard.com:/blog/?p=124
permalink: /2010/08/12/editor-access-to-episerver-admin-functionality/
aktt_notify_twitter:
  - 'yes'
  - 'yes'
  - 'yes'
  - 'yes'
  - 'yes'
  - 'yes'
dsq_thread_id:
  - "1073095655"
  - "1073095655"
  - "1073095655"
categories:
  - Episerver
---
EPiServer separates the functionality it offers website editors and site administrators by using two distinct user interfaces (edit and admin mode). Edit mode offer further granular control by allowing you to set access rights over content such as files, pages (and also page types in CMS6).

However there are times when you may wish to provide editor access to a particular admin functionality, without providing full access to the admin interface. Out-of-the-box EPiServer doesn&#8217;t support this. Access to the admin mode is defined via a location path configuration set in web.config, which by default only gives access to users in the WebAdmins and the Administrators role. There is no functionality to provide further granular access to any particular function within the admin interface.

_However there are a few routes available to allow us to meet this requirement._

  1. If using CMS6, create a dashboard gadget to provide the required admin functionality.
  2. Use Reflector to inspect the EPiServer assemblies so you can recreate the required functionality in a page that lives on a different path and so will not be subject to the predefined admin interface&#8217;s location security configuration (following the <a title="Customising the EPiServer UI" href="http://world.episerver.com/Blogs/David-Knipe/Dates/2010/5/Customising-the-EPiServer-UI/" target="_blank">technique</a> shown by David Knipe).

Whilst both of the above will allow us to meet our needs, neither are particular elegant as they both involve &#8216;reinventing the wheel&#8217; and recreating the necessary admin functionality at another position in our codebase. This ultimately leads to a solution that will be harder to maintain.

Ideally what we want is a way of using the existing EPiServer functionality and interface but providing access to a specific admin function to a user in a non-Admin role.

### Enter the VirtualPathMappedProvider&#8230;&#8230;

The solution below uses the VirtualPathMappedProvider to map an existing admin functionality (.aspx page) to a developer-defined virtual url, which is subjected to different location security configuration. This is made possible by the fact that the EPiServer admin pages (or the ones I&#8217;ve looked at) rely solely on the location path element for their security and do no further on-page access checking (which is as it should be really). Once you&#8217;ve bypassed the first security checkpoint &#8211; you&#8217;ve got a free run.

I&#8217;m going to provide access to the Category administration interface to any user in a newly-created role that I&#8217;ll call CategoryAdmins.Â Category administration is often a function that in my opinion falls more under the remit of an Editor, though that does depend on your EPiServer implementation (as categories can be used for a lot more than just <a title="Category - more than labelling" href="http://labs.episerver.com/en/Blogs/Anders-Hattestad/Dates/2009/3/Category---more-than-labeling/" target="_blank">labelling</a>).

  * Add Mapped links &#8211; create a virtual path mapping in episerver.config (or in web.config pre-CMS 6) which forwards the defined virtual url to the existing categories.aspx page. You will need to provide a mapping for every page that you want to allow access to. Category administration is handled by a single .aspx page.

<pre class="brush: xml; title: ; notranslate" title="">&lt;add name="AdminMappings" type="EPiServer.Web.Hosting.VirtualPathMappedProvider,EPiServer" /&gt;
</pre>

<pre class="brush: xml; title: ; notranslate" title="">&lt;virtualPathMappings&gt;
   &lt;add url="~/ui/CMS/CategoryAdmin/Categories.aspx" mappedUrl="~/ui/CMS/Admin/Categories.aspx" /&gt;
&lt;/virtualPathMappings&gt;
</pre>

  * Add a new location element in web.config for our new virtual path. Here I&#8217;m allowing users in the CategoryAdmins and Administrators roles access whilst denying all other users.

<pre class="brush: xml; title: ; notranslate" title="">&lt;location path="ui/CMS/categoryadmin"&gt;
   &lt;system.web&gt;
      &lt;authorization&gt;
         &lt;allow roles="CategoryAdmins, Administrators"&gt;
         &lt;deny users="*"&gt;
      &lt;/authorization&gt;
   &lt;/system.web&gt;
&lt;/location&gt;
</pre>

  * Create Action Window plugin &#8211; now that we&#8217;ve opened up access to the category admin page, we need to define a position for a CategoryAdmin user to access this page from the EPiServer edit interface. I&#8217;ve chosen to create an Action window plugin, which is achieved by marking your page class with a GuiPlugin attribute. Don&#8217;t forget also to lock down access to this page with a location security configuration.

There is a small choice/compromise here to make about the user experience. You can either use a Server.Transfer call to transfer the Plugin page directly to your virtual url. However you will see some anomalies from the EPiServerUI:SystemPrefix control which provides the page title for most pages in admin mode (this control lives in the EPiServerUI.Master page). The SystemPrefix controls creates the page title from the current url which means that as you&#8217;ve used Server.Transfer (which preserves the original request url) you will see a heading of &#8216;Action Window&#8217;.

If you can live with this you&#8217;ll get a more straight forward user experience. However I&#8217;ve decided that having the correct title is important, so I&#8217;ve just provided a simple anchor tag with the Plugin page&#8217;s markup which allows a click through to the Category page.

<pre class="brush: xml; title: ; notranslate" title="">&lt;%@ Control Language="C#" AutoEventWireup="true" CodeBehind="CategoryAdminPlugin.ascx.cs" Inherits="EPiServer.CategoryAdminPlugin" %&gt;

&lt;h2&gt;Category Admin&lt;/h2&gt;
&lt;p&gt;Users in the CategoryAdmin role can access this page.&lt;/p&gt;
&lt;asp:HyperLink runat="server" NavigateUrl="~/ui/CMS/CategoryAdmin/Categories.aspx" Text="Edit Categories" /&gt;
</pre>

<pre class="brush: csharp; title: ; notranslate" title="">using System;
using EPiServer.PlugIn;

namespace EPiServer.Plugins
{
   [GuiPlugIn(DisplayName = "Edit Categories",
   Description = "Allow CategoryAdmins to access 'Edit Categories page",
   Url = "~/Plugins/CategoryAdminPlugin.ascx",
   Area = PlugInArea.ActionWindow)]
   public partial class CategoryAdminPlugin : System.Web.UI.UserControl
   {
      protected void Page_Load(object sender, EventArgs e)
      {

      }
   }
}
</pre>

  * Add new language elements &#8211; all quite straight forward so far, but there is one final hurdle to overcome. The categories.aspx page uses the EPiServer LanguageManager to provide the correct language translation for much of the copy on the page. The LanguageManager calculates the Xpath to the specified language element using the current page&#8217;s url (*only if the path is within the EPiServer Ui). As our path is virtual, these XPath&#8217;s don&#8217;t exist, so we must add them to the ~/lang xml files.

<pre class="brush: xml; title: ; notranslate" title="">&lt;?xml version="1.0" encoding="utf-8" standalone="yes"?&gt;
&lt;languages&gt;
   &lt;language name="English" id="en"&gt;
      &lt;categoryadmin&gt;
         &lt;categories&gt;
            &lt;heading&gt;Categories&lt;/heading&gt;
            ... removed for brevity
         &lt;/categories&gt;
      &lt;/categoryadmin&gt;
   &lt;/language&gt;
&lt;/languages&gt;
</pre>

  * The proof! This is a relatively simple technique that allows you to add a small amount of flexibility into the EPiServer edit/admin interface.

[<img class="aligncenter size-full wp-image-135" title="Edit Categories in Edit Mode" src=" /assets/uploads/2010/08/categoryineditmode.gif" alt="" width="700" height="289" />]( /assets/uploads/2010/08/categoryineditmode.gif)