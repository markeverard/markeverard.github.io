---
title: Impersonating Visitor Groups from Code
date: 2011-05-09T09:00:24+00:00
author: Mark Everard
layout: post
permalink: /2011/05/09/impersonating-visitor-groups-from-code/
dsq_thread_id:
  - "1092010729"
categories:
  - Episerver
---
If you require that a particular HttpRequest impersonates one or more Visitor Groups, then you can do so by adding one of two keys to the current HttpContext.

Before calling the logic to check each criteria in the VisitorGroup; and determining whether the current User is matched to the group, the VisitorGroupRole class will first check if the current HttpContext contains an item called &#8220;ImpersonatedVisitorGroupsById&#8221; or an item called &#8220;ImpersonatedVisitorGroupsByName&#8221;. If these items contain either a valid VisitorGroupRole.Id, or VisitorGroupRole.Name respectively, then the current user will be matched to that Visitor Group regardless of whether they match the Criteria rules.

This is used by EPiServer in edit mode, to allow editors to preview pages as viewed by particular Visitor Groups.

Here is a method that will ensure that users are always evaluated as being in the Visitor Groups specified by id the method&#8217;s arguments. (Note, this method uses the MVC ControllerContext to access the current HttpContext. If you are using this method in a WebForms page you&#8217;ll need to substitute this for HttpContext.Current

~~~csharp
protected void ImpersonateVisitorGroupsById(string[] visitorGroupIds)
{
     var impersonatedGroupsById =
               ControllerContext.HttpContext.Items["ImpersonatedVisitorGroupsById"] as List&lt;string&gt;
                                                                      ?? new List&lt;string&gt;();
     impersonatedGroupsById.AddRange(visitorGroupIds);
     ControllerContext.HttpContext.Items["ImpersonatedVisitorGroupsById"] = visitorGroupIds;
}
~~~