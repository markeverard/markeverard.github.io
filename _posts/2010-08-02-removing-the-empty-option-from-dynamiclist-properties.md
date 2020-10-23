---
title: Removing the empty option from DynamicList properties
date: 2010-08-02T16:44:58+00:00
author: Mark Everard
layout: post
permalink: /2010/08/02/removing-the-empty-option-from-dynamiclist-properties/
dsq_thread_id:
  - "1073095645"
categories:
  - Episerver
---
Today seems to be one of those days where you pick up really simple bits of information that you&#8217;re surprised you never knew before (see Erik&#8217;s <a title="Send mail in devlopment environments" href="http://antecknat.se/blog/2010/08/01/send-mail-in-development-environment/" target="_blank">post</a> about sending mail in a development environment for example)&#8230;&#8230;

My little snippet comes from a question posted in the EPiServer <a title="Dynamic List (one option) - default value" href="http://world.episerver.com/Templates/Forum/Pages/thread.aspx?id=41476" target="_blank">forums</a> that I responded to; about setting default values on EPiServer <a title="AppSettings/Dynamic List in EPiServer 5" href="http://antecknat.se/blog/2008/04/18/new-appsettings-and-appsettingsmultiple-in-episerver-cms-5/" target="_blank">DynamicList</a> properties. I&#8217;d always wondered how to remove the blank option that&#8217;s added by default to the edit mode DropDownList, though never enough to put some code together to actually get rid of it.

This morning though, I did just that, and it turned out to be very straightforward as the EPiServer development team had already provided an extensibility point for exactly that scenario.

All that&#8217;s needed is a new Property and PropertyControl class that overrides the existing PropertyAppSettings. This class defines a property called AutoGenerateEmptyValue. Override this property to return false to remove the empty value.

<pre class="brush: csharp; title: ; notranslate" title="">using System;
using EPiServer.Core;
using EPiServer.PlugIn;
using EPiServer.SpecializedProperties;
using EPiServer.Web.PropertyControls;

namespace MySite.CustomProperties
{
   [Serializable]
   [PageDefinitionTypePlugIn(DisplayName = "Dynamic list (one option) with no empty value", Description = "Works as the out-of-the-box Dynamic list property but displays no empty option")]
   public class PropertyAppSettingsNoEmptyValue : PropertyAppSettings
   {
      public override IPropertyControl CreatePropertyControl()
      {
         return new PropertyAppSettingsNoEmptyValueControl();
      }
   }

   public class PropertyAppSettingsNoEmptyValueControl : PropertyAppSettingsControl
   {
      protected override bool AutoGenerateEmptyValue { get { return false; } }
   }
}
</pre>