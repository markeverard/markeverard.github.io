---
id: 208
title: 'Developing a custom workflow in EPiServer : Part one'
date: 2010-09-29T13:30:38+00:00
author: Mark Everard
layout: post
guid: http://www.markeverard.com/blog/?p=208
permalink: /2010/09/29/developing-a-custom-workflow-in-episerver-part-one/
dsq_thread_id:
  - "1073785256"
  - "1073785256"
  - "1073785256"
categories:
  - Episerver
---
This in a first of a series of posts about how to build a custom EPiServer workflow. There is a fair amount of information about EPiServer workflows (most of which are nicely summarised by Frederik Vig in his amazingly useful <a title="EPiServer Developer Resources" href="http://www.frederikvig.com/2010/05/episerver-developer-resources/#toc-workflows" target="_blank">EPiServer Developer Resources</a>). Hopeful these posts will be able to add to that and also provide further context. Comments are most welcome!

Over the course of five posts I&#8217;ll cover the following:

  * **Part One &#8211; Overview and Requirements**
  * <a title="Windows Workflow Foundation in EPiServer" href="http://www.markeverard.com/2010/09/30/developing-a-custom-workflow-in-episerver-part-two/" target="_blank">Part Two &#8211; Windows Workflow Foundation in EPiServer</a>
  * <a title="Windows Workflow Foundation in EPiServer" href="http://www.markeverard.com/2010/11/10/developing-a-custom-workflow-in-episerver-part-three/" target="_blank">Part Three &#8211; Designing the Workflow</a>
  * <a title="EPiServer tasks and working with the user interface" href="http://www.markeverard.com/2011/01/24/developing-a-custom-workflow-in-episerver-part-four-2/" target="_blank">Part Four &#8211; EPiServer tasks and working with the user interface</a>
  * Part Five &#8211; Access control and finishing touches

## Overview

The out-of-the box workflows in EPiServer cover off some common editor based scenarios such as approval and feedback. However the real world always throws up more exacting requirements that can&#8217;t be satisfied by the pre-defined workflows (or any combination of them &#8211; remember that you can chain them together).

In these situations you&#8217;ll have to build your own custom workflow piece utilising Windows Workflow Foundation and EPiServer&#8217;s own library of workflow elements and UI integration.

## Requirements

First up we must start with requirements gathering. This is often a difficult process with workflows as you have to map and consolidate real business processes into a sensible technical solution.

Workflow requirements are always best distilled by a Visio flow diagram. Fact! So here&#8217;s ours _(click to enlarge)_.

<p style="text-align: center;">
  <a href=" /assets/uploads/2010/09/2StageWorkflowDiagram.jpg"><img class="size-medium wp-image-217 aligncenter" title="2StageWorkflowDiagram" alt="2 Stage Workflow Diagram" src=" /assets/uploads/2010/09/2StageWorkflowDiagram-300x218.jpg" width="300" height="218" /></a>
</p>

Looking at the required process, you can see that it&#8217;s a 2-stage approval process, where a page is created, sent for approval, and then finally sent through a further approval step, where upon the page is approved one final time before being published.

There are three user roles who interact with this process:

  * Creators, who initially create the content and kick off the workflow.
  * Approvers, who can decide to approve or decline the content, or pass on the approval decision.
  * Publishers, who can approve or decline the content, and publish the content.

This is only slightly different than the out-of-the-box Sequential Approval workflow, but the following differences necessitate a custom solution:

  1. There are two stages of approval
  2. Approvers can opt to &#8216;pass-the-buck&#8217; and pass the approval stage to a fellow Approver.
  3. The workflow creator is informed at every decision point.

There are some additional requirements that can&#8217;t be shown on the diagram:

  1. Pages can only be published using the workflow
  2. Only one instance of the workflow can be in progress for a page at any one time.

Over the course of the next four posts I&#8217;ll piece together a workflow that meets the above requirements, covering the (very) basics of Windows Workflow Foundation and how you can use them and the integration methods that the EPiServer API exposes to allow any custom workflow to be implemented against EPiServer managed content.