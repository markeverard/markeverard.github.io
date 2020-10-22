---
id: 4441
title: Adding GA tracking to Emails delivered using Adobe Campaign
date: 2016-08-06T19:05:38+00:00
author: Mark Everard
layout: revision
guid: http://www.markeverard.com/2016/08/06/4241-autosave-v1/
permalink: /2016/08/06/4241-autosave-v1/
---
Adobe Campaign contains a formula that is executed in the delivery preparation phase which parses a delivery template and modifies all links, so that they are passed through a tracking server. This allows links to be individually tracked to a both an email recipient and a specific delivery.

**Example**  
A link to the BBC news site, http://www.bbc.co.uk/news is changed to https://t.adobe-campaign.server/r/?id=hcc970,436a07,436a08. The latter url eventually resolves to BBC news, but first is passed through the tracking server, that issues a redirect and records that the link has been followed.

## Google Analytics Tracking

Google Analytics can track inbound links to a site and attribute them to a source and an associated campaign. It does this in a number of ways, either via cookies, or for cross-domain links by checking querystring parameters. The following parameters are recognised

  * utm_content
  * utm_source
  * utm_medium

See the following link for further information about each parameter and the ‘meaning’ – <https://support.google.com/analytics/answer/1033867?hl=en>

## Modifying the Click Tracking Formula

The click tracking formula in Adobe Campaign can be modified to include additional query string parameters within the forwarding record. This is achieved by modifying the NmsTracking_ClickFormula contained in **Administration > Platform > Options**

**Warning**  
You should very carefully test the click tracking formula after making any modifications. Be very sure that the record you enter doesn’t include any spurious white space or line-breaks. Adobe Campaign is very ‘fussy’ about character encoding! As an example, when I first modified the below we discovered that modified outbound links were rendering incorrectly in Hotmail / Outlook.com email clients.

Links within the rendered email body had a line break before and after the URL which Hotmail interpreted as a link without a protocol and so added an additional protocol to the start of the url, so our links looked like https://https://t.adobe-campaign.server/r/?id=hcc970,436a07,436a08 . I’m sure this type of functionality is intentional from Hotmail / Outlook so they can turn www.bbc.co.uk entries in text into a fully-formed hyperlink. Regardless, it does show the complexity and why its vital to test your deliveries in a tool like <a href="https://litmus.com/" target="_blank">Litmus – test email rendering</a>.

_Be very careful about cutting and pasting this as I’ve deliberately added line-breaks and white space to make it readable!_  
?d[0]:&#8221; %>&ap_deliveryId=<%=d[1]?parseInt(d[1],16):0%><%  
} %>  
<%  
if( typeof proposition != &#8220;undefined&#8221; && proposition.length == undefined )  
{ %>&ap_oid=<%= proposition.offer.id %>  
<% } %> </pre> 

The utm\_medium and utm\_source variables are hardcoded to be ’email’ and ‘adobecampaign’. These parameters are used by marketing analysts to understand the channel and source system that a visit can be attributed to.

## Adding a variable to a delivery template

The above tracking formula depends on the delivery template having a variable named utm_content added into its context. If a template does not have this variable defined then the delivery will fail at the **html compilation** stage, and that’s always a pain to debug!

A good practise is to have the variable’s value taken from the internal delivery Id. This means your Google Analytics will be able to easily identify inbound links coming from specific emails sent from Adobe Campaign. It’s straightforward to set this within the delivery template as shown below.

&nbsp;

<img class="aligncenter wp-image-5072 size-full" src="http://www.markeverard.com/wp-content/uploads/2015/10/add-delivery-template-e1444053693708.png" alt="add-delivery-template" width="650" height="360" />