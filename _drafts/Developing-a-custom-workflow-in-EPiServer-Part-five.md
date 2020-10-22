---
id: 427
title: 'Developing a custom workflow in EPiServer : Part five'
date: 2017-07-11T16:33:55+00:00
author: Mark Everard
layout: post
guid: http://www.markeverard.com/blog/?p=427
permalink: /?p=427
categories:
  - Episerver
---
Finishing touches

Access control

Only one instance

How requirements are met

There are three user roles who interact with this process:

  * Creators, who initially create the content and kick off the workflow.
  * Approvers, who can decide to approve or decline the content, or pass on the approval decision.
  * Publishers, who can approve or decline the content, and publish the content.

This is only slightly different than the out-of-the-box Sequential Approval workflow, but the following differences necessitate a custom solution:

  1. There are two stages of approval
  2. Approvers can opt to ‘pass-the-buck’ and pass the approval stage to a fellow Approver.
  3. The workflow creator is informed at every decision point.

There are some additional requirements that can’t be shown on the diagram:

  1. Pages can only be published using the workflow
  2. Only one instance of the workflow can be in progress for a page at any one time.