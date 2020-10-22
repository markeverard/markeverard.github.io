---
id: 5281
title: Configuring TinyMCE using EPiServer custom property settings
date: 2017-07-11T16:47:45+00:00
author: Mark Everard
layout: revision
guid: http://www.markeverard.com/2017/07/11/2092-revision-v1/
permalink: /2017/07/11/2092-revision-v1/
---
The TinyMCE rich-text editor provides great flexibility for editors to create and mark-up content within EPiServer. TinyMCE is a seemingly infinitely configurable tool and can be configured to show / hide many of its available features.Â One of the ways EPiServer Administrators / Developers / Integrators can help is by configuring TinyMCE to only show features that are necessary in a given context.

This posts demonstrates how to modify TinyMCE either globally or on a property by property basis using custom property settings.

### Property Settings

Property Settings are not a new feature. They were <a title="EPiServer - Settings for properties" href="http://labs.episerver.com/en/Blogs/Linus-Ekstrom1/Dates/2009/10/Settings-for-properties/" target="_blank">introduced in CMS 6</a> and are used to store settings for page properties. They are editable from within the Admin mode. One of the primary uses of this feature is to configure settings and buttons that are shown to editors when using the TinyMCE text-editor. Property settings can also usefully be used to provideÂ <a title="New property types in EPiServer 6 CMS R2 Beta " href="http://world.episerver.com/Blogs/David-Knipe/Dates/2010/12/New-property-types-in-EPiServer-6-CMS-R2-Beta/" target="_blank">editor managed drop-down lists</a>.

### TinyMCE custom property settings

You can specify which buttons to use for each XHTML property instance on a defined page type. This can be managed in Admin mode in the following way.

  1. Go into EPiServer Admin mode
  2. Navigate to the Page Type tab and to the specific Page Type you want to modify.
  3. Pick the XHTML property you wish to add custom settings
  4. Click the Custom Settings tab

You should now see the following interface:

<p style="text-align: center;">
  <img class="size-full wp-image-2112 aligncenter" alt="property settings interface" src="http://www.markeverard.com/wp-content/uploads/2013/04/propertysettings.png" width="650" height="376" srcset="https://www.markeverard.com/wp-content/uploads/2013/04/propertysettings.png 650w, https://www.markeverard.com/wp-content/uploads/2013/04/propertysettings-300x174.png 300w, https://www.markeverard.com/wp-content/uploads/2013/04/propertysettings-580x336.png 580w, https://www.markeverard.com/wp-content/uploads/2013/04/propertysettings-320x185.png 320w" sizes="(max-width: 650px) 100vw, 650px" />
</p>

You can use this to either define a custom setting that will be applied to this instance, or define a new global setting that will be used by default.

The TinyMCE toolbar can be edited by dragging and dropping buttons from the inactive tools to the editor toolbar area. You can adjust the position of existing feature buttons and the overall layout of your toolbar.

<p style="text-align: center;">
  <img class="size-full wp-image-2122 aligncenter" alt="CustomPropertySettingEditorToolbar" src="http://www.markeverard.com/wp-content/uploads/2013/04/CustomPropertySettingEditorToolbar.png" width="650" height="322" srcset="https://www.markeverard.com/wp-content/uploads/2013/04/CustomPropertySettingEditorToolbar.png 650w, https://www.markeverard.com/wp-content/uploads/2013/04/CustomPropertySettingEditorToolbar-300x149.png 300w, https://www.markeverard.com/wp-content/uploads/2013/04/CustomPropertySettingEditorToolbar-580x287.png 580w, https://www.markeverard.com/wp-content/uploads/2013/04/CustomPropertySettingEditorToolbar-320x159.png 320w" sizes="(max-width: 650px) 100vw, 650px" />
</p>

### Global settings

Defining custom settings for each XHTML property on every page type will; for any reasonably sized EPiServer installation be a very time-consuming task. It is likely that you will have a set of common TinyMCE configurations that you wish to apply and share between different properties across different page types. These can be managed by creating them as a global property setting, and adding some additional meta data such as a name and description. Global settings can then be selected on each property that you wish to use them for.

### Default settings

Any XHTML property that does not have aÂ custom property setting will use the default EPiServer XHTML property setting, meaning the TinyMCE toolbar will display will a pretty full list of feature buttons and settings. The default option can be changed to any one of your defined global property settings. This allows you to apply settings across the board. This is useful for modifying any TinyMCE instances that appear in <a title="EPiServer Dynamic Content" href="http://world.episerver.com/Documentation/Items/Tech-Notes/EPiServer-CMS-6/EPiServer-CMS-6-R2/Dynamic-Content/" target="_blank">DynamicContent</a> controls.

<p style="text-align: center;">
  <img class="size-full wp-image-2102 aligncenter" alt="propertysetting-globaldefault" src="http://www.markeverard.com/wp-content/uploads/2013/04/propertysetting-globaldefault.png" width="700" height="377" srcset="https://www.markeverard.com/wp-content/uploads/2013/04/propertysetting-globaldefault.png 700w, https://www.markeverard.com/wp-content/uploads/2013/04/propertysetting-globaldefault-300x162.png 300w, https://www.markeverard.com/wp-content/uploads/2013/04/propertysetting-globaldefault-580x312.png 580w, https://www.markeverard.com/wp-content/uploads/2013/04/propertysetting-globaldefault-320x172.png 320w" sizes="(max-width: 700px) 100vw, 700px" />
</p>