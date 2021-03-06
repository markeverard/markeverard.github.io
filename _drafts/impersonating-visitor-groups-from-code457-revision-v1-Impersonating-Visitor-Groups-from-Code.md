---
id: 5011
title: Impersonating Visitor Groups from Code
date: 2017-07-11T16:33:54+00:00
author: Mark Everard
layout: revision
guid: http://www.markeverard.com/2017/07/11/457-revision-v1/
permalink: /2017/07/11/457-revision-v1/
---
If you require that a particular HttpRequest impersonates one or more Visitor Groups,� then you can do so by adding one of two keys to the current HttpContext.

Before calling the logic to check each criteria in the VisitorGroup; and determining whether the current User is matched to the group, the VisitorGroupRole class will first check if the current HttpContext contains an item called &#8220;ImpersonatedVisitorGroupsById&#8221; or an item called &#8220;ImpersonatedVisitorGroupsByName&#8221;. If these items contain either a valid VisitorGroupRole.Id, or VisitorGroupRole.Name respectively, then the current user will be matched to that Visitor Group regardless of whether they match the Criteria rules.

This is used by EPiServer in edit mode, to allow editors to preview pages as viewed by particular Visitor Groups.

Here� is an method that will ensure that users are always evaluated as being in the Visitor Groups specified by id the method&#8217;s arguments. (Note, this method uses the MVC ControllerContext to access the current HttpContext. If you are using this method in a WebForms page you&#8217;ll need to substitute this for HttpContext.Current

<pre class="brush: csharp; title: ; notranslate" title="">protected void ImpersonateVisitorGroupsById(string[] visitorGroupIds)
{
     var impersonatedGroupsById =
               ControllerContext.HttpContext.Items[&quot;ImpersonatedVisitorGroupsById&quot;] as List&lt;string&gt;
                                                                      ?? new List&lt;string&gt;();
     impersonatedGroupsById.AddRange(visitorGroupIds);
     ControllerContext.HttpContext.Items[&quot;ImpersonatedVisitorGroupsById&quot;] = visitorGroupIds;
}

</pre>