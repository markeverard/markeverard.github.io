---
id: 209
title: 'Developing a custom workflow in EPiServer : Part two'
date: 2010-09-30T22:00:43+00:00
author: Mark Everard
layout: post
guid: http://www.markeverard.com/blog/?p=209
permalink: /2010/09/30/developing-a-custom-workflow-in-episerver-part-two/
dsq_thread_id:
  - "1073080540"
  - "1073080540"
  - "1073080540"
categories:
  - Episerver
---
This is the second post, in a series of five about developing a custom workflow in EPiServer.

  * <a title="Part One - Overview and Requirements" href="http://www.markeverard.com/blog/2010/09/29/developing-a-custom-workflow-in-episerver-part-one/" target="_blank">Part One &#8211; Overview and Requirements</a>
  * **Part Two &#8211; Windows Workflow Foundation in EPiServer**
  * <a title="Windows Workflow Foundation in EPiServer" href="http://www.markeverard.com/blog/2010/11/10/developing-a-custom-workflow-in-episerver-part-three/" target="_blank">Part Three &#8211; Designing the Workflow</a>
  * <a title="EPiServer tasks and working with the user interface" href="http://www.markeverard.com/blog/2011/01/24/developing-a-custom-workflow-in-episerver-part-four-2/" target="_blank">Part Four &#8211; EPiServer tasks and working with the user interface</a>
  * Part Five &#8211; Access control and finishing touches

## Windows Workflow Foundation in EPiServer

Windows workflow is a full runtime that can be hosted by any application running on the Microsoft ASP.NET stack. It defines all of the services that are necessary (persistence, threading and transactions) to manage the steps, rules and decisions that make up any arbitrary workflow task.

EPiServer first implemented Windows Workflow Foundation in CMS 5. The EPiServer application acts as the host for the workflow runtime, telling the workflow runtime the type of workflows to create, and communicating any events to the runtime. The runtime itself manages the workflow lifecycle, including persistence and will notify the host of any important workflow events such as completion or termination.

You can see the configuration for the workflow foundation within the EPiServer web.config file, where the runtime itself is configured and the persistence mechanism is setup, which is either a SQL Server or Oracle database (state is serialised into one of two workflow related tables called InstanceState and CompletedScope).

There are two main types of workflows that can be developed using Windows workflow foundation

  1. StateMachineWorkflows
  2. SequentialWorkflows

A SequentialWorkflow is predictable and can only execute in the given order which is defined within the workflow code itself. In this sense you can describe the workflow itself as being in control of its execution pathway. A StateMachineWorkflow relies on external events to drive it to completion and the external decisions define which state the workflow transitions to, meaning that it is the external events that are in control.

Our <a title="Part One - Overview and Requirements" href="http://www.markeverard.com/blog/2010/09/29/developing-a-custom-workflow-in-episerver-part-one/" target="_blank">requirements</a> can be satisifed by a StateMachineWorkflow as the external decisions that are made by Approvers or Publishers define the workflow completion order.

You can select a new StateMachineWorkflow directly from the Visual Studio &#8216;Add item&#8217; command, you&#8217;ll see that it simply inherits from StateMachineWorkflowActivity:

<pre class="brush: csharp; title: ; notranslate" title="">public sealed partial class TwoStagePageWorkflow : StateMachineWorkflowActivity
{
   //implementation
}
</pre>

### Communication

The most important element to define is the code that specifies how your host application communicates with the workflow runtime.

<p style="text-align: center;">
  <a href="/assets/uploads/2010/12/WorkflowFoundationDiagram.jpg"><img class="aligncenter size-full wp-image-226" title="WorkflowFoundationDiagram" src="/assets/uploads/2010/12/WorkflowFoundationDiagram.jpg" alt="Workflow foundation diagram" width="491" height="284" srcset="/assets/uploads/2010/12/WorkflowFoundationDiagram.jpg 795w, /assets/uploads/2010/12/WorkflowFoundationDiagram-300x174.jpg 300w, /assets/uploads/2010/12/WorkflowFoundationDiagram-768x446.jpg 768w, /assets/uploads/2010/12/WorkflowFoundationDiagram-720x418.jpg 720w, /assets/uploads/2010/12/WorkflowFoundationDiagram-580x337.jpg 580w, /assets/uploads/2010/12/WorkflowFoundationDiagram-320x186.jpg 320w" sizes="(max-width: 491px) 100vw, 491px" /></a>
</p>

The workflow can either handle external events or invoke external methods. This is defined by the contents of a Service interface that is marked with the [ExternalDataExchange()] attribute.

<pre class="brush: csharp; title: ; notranslate" title="">using System;
using System.Workflow.Activities;

namespace TwoStage.Workflow.Communication
{
	[ExternalDataExchange()]
	public interface ITwoStageService
	{
		event EventHandler&lt;TwoStageEventArgs&gt; PageApproved;
                event EventHandler&lt;TwoStageEventArgs&gt; PageDeclined;
                event EventHandler&lt;TwoStageEventArgs&gt; PagePassedToApprover;
                event EventHandler&lt;TwoStageEventArgs&gt; PagePassedToPublisher;
	}
}
</pre>

For our two-stage workflow, we need to handle the following external events, which correspond to decisions that a Creator, Approver or Publisher will make within the EPiServer Ui, which will then be raised by a concrete implementation of the above ITwoStageService interface. These events map to our StateMachine moving between the following four states.

  1. WithApprover
  2. WithPublisher
  3. PageDeclined
  4. PageApproved

Also you must define the event arguments that are passed when any of the external events are raised and then handled.

<pre class="brush: csharp; title: ; notranslate" title="">using System;
using System.Workflow.Activities;
using EPiServer.Core;
using TwoStage.Workflow.Domain.TaskOwner;

namespace TwoStage.Workflow.Communication
{
[Serializable]
public class TwoStageEventArgs : ExternalDataEventArgs
{
   public TwoStageEventArgs(Guid instanceId, PageReference pageLink, TaskOwner assignedTo, TaskOwner wasAssignedTo, string message) : base(instanceId)
   {
      PageLink = pageLink;
      IsAssignedTo = assignedTo;
      WasAssignedTo = wasAssignedTo;
      Message = message.Replace(Environment.NewLine, "&lt;br /&gt;");
   }

   public PageReference PageLink { get; internal set; }

   public TaskOwner IsAssignedTo { get; internal set; }
   public TaskOwner WasAssignedTo { get; internal set; }

   public string Message { get; internal set; }
   }
}
</pre>

Our custom EventArgs will hold the following properties, a PageReference so we know which EPiServer page the workflow task has been created against. A custom type allowing us to know who the current actioning user or role is, and also the same type to define who previously was responsible. It also defines a string based message.

Any custom EventArgs must inbherit from ExternalDataEventArgs and must be marked as serializable (as must all types defined within the EventArgs class).

If you look at the default EPiServer web.config file / EPiServer.config, you will see that there are three communication services defined for the out-of-the-box workflows. Any new service should also be registered here.

<pre class="brush: xml; title: ; notranslate" title="">&lt;externalServices&gt;
   &lt;!-- externalService:      Custom services that is to be registered with workflow runtime--&gt;
   &lt;externalService type= "EPiServer.WorkflowFoundation.Workflows.ApprovalService,EPiServer.WorkflowFoundation" /&gt;
   &lt;externalService type= "EPiServer.WorkflowFoundation.Workflows.ReadyForTranslationService,EPiServer.WorkflowFoundation" /&gt;
   &lt;externalService type= "EPiServer.WorkflowFoundation.Workflows.RequestForFeedbackService,EPiServer.WorkflowFoundation" /&gt;
&lt;/externalServices&gt;
</pre>

Now we&#8217;ve defined the communication pathway between the workflow runtime and the EPiServer host application, we can design the workflow itself and create the activities that the workflow must perform.

See you for part 3 😉