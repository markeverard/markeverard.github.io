---
title: 'Developing a custom workflow in EPiServer : Part three'
date: 2010-11-10T10:19:32+00:00
author: Mark Everard
layout: post
dsq_thread_id:
  - "1073095671"
categories:
  - Episerver
  - Technical
tags: [Episerver-CMS, Workflows]
---
This is the third post, in a series of five about developing a custom workflow in EPiServer.

* <a title="Part One: Overview and Requirements" href="/2010/09/29/developing-a-custom-workflow-in-episerver-part-one/">Part One &#8211; Overview and Requirements</a>
* <a title="Part Two: Windows Workflow Foundation in EPiServer" href="/2010/09/30/developing-a-custom-workflow-in-episerver-part-two/">Part Two &#8211; Windows Workflow Foundation in EPiServer</a>
* **Part Three &#8211; Designing the Workflow**
* <a title="EPiServer tasks and working with the user interface" href="/2011/01/24/developing-a-custom-workflow-in-episerver-part-four-2/" >Part Four &#8211; EPiServer tasks and working with the user interface</a>
* Part Five &#8211; Access control and finishing touches

## Designing the Workflow
Now we&#8217;ve defined the communication pathway between the application (EPiServer) and the Workflow runtime, it&#8217;s time to use the designer surface to piece together pre-existing Windows workflow activities, the EPiServer specific activities and the bespoke EventDriven Activities that are raised by the host application.

This is done using a Visual Studio design surface which auto-magically generates code files descibing your workflow based on what you drag-and-drop onto the surface. Personally I hate developing in this manner, the one redeeming feature is that it does make for some easily created diagrams demonstrating whats going on!

![workflow designer](/assets/uploads/2010/11/workflow-design-surface.gif)


Here you can see the Two-stage workflow laid out on the designer surface. Each box represents a unique State within this StateMachine workflow, with the arrows indicating the possible transitions between states. Initally the workflow process is created in the InitialState (which is set by right-clicking and selecting the relevant option from the dropdown).

Each State contains a list of Activities, for example the PageApproverState contains four activities; three bespoke EventListener activites and a StateInitialization Activity. You&#8217;ll notice I&#8217;ve used an StateInitialization Activity as a common pattern across all of the States  (barring the FinalizedState &#8211; which has no additional work to do). This will initialise / modify any data within the workflow to be persisted (which in our case is the data encapsulated by our TwoStageEventArgs).

### StateInitializationActivity
StateInitialization Activities, available by dragging and dropping from the Visual Studio Toolbox ð are performed by the workflow runtime as soon as the StateMachine workflow is moved into that particular state. They can contain any number of other Activities, so as to make up your exact Workflow requirements.

We&#8217;ll be using them to perform the following common tasks:

1. Email the workflow creator to inform them of the change of workflow state.  
2. Create an EPiServer Task (which are designed for informing users or groups about workflow tasks), using the information provided in the passed TwoStageEventArgs.  
3. Moving the workflow into a sequential state when there is no input expected from an external user (for example in the case of moving from PageApprovedState to FinalisedState)

The code for each of these three types of Activity is already implemented for us (either within the Windows Workflow library or the EPiServer Workflow library), so all we have to do is drag them from the Visual Studio toolbox onto our workflow surface and bind the relevant data to them. Sending emails is handled by the SendEmailActivity available within Windows Workflow Foundation library, as is the SetStateActivity &#8211; which moves the StateMachine into a defined State.

The CreateTaskActivity (defined in EPiServer.WorkflowFoundation.dll &#8211; make sure this assembly in added to the Visual Studio toolbox) creates a Task associated with a User or Group and also sends an Email notification to them. EPiServer Tasks nicely bind the workflow to the EPiServer user interface, so the User/Group will see them and be able to interact as soon as they log in to EPiServer.

Additionally there are some activities that must occur in particular workflow states, such as initially associating the workflow with an EPiServer page so that the EPiServer UI is aware that the current workflow instance is asscociated with a page and can populate the Workflow tab in Edit mode with the workflow instance details. We&#8217;ll look at these in the next post

### EventDrivenActivity
EventDriven activities are the most important part of a StateMachine workflow as they are the activities which are execute when a pre-defined user event is raised from the host application. Microsoft have helpfully provided a tool for creating the bespoke EventDriven Activites based on the events and methods that are exposed from any class marked with the [ExternalDataExchange()] attribute.

The (wca.exe) tool lives in:

~~~
/Program Files/Microsoft SDKs/Windowsv6.0Abin
~~~

and when run it needs to be passed arguments informing it of the path of the assembly to scan and also the namespace and location in which to create the bespoke EventDriven activities. In our solution it will find the ITwoStageService and create EventDriven activities based against the four events defined in that service, PageApproved, PageDeclined, PagePassedToApprover and PagePassedToPublisher.

Each of these bespoke event-activities needs to persist the current state of the workflow, and also move the StateMachine to a subsequent State. The first of these responsibilities is handled by a custom piece of code with the workflow code file, which must be bound to the Invoked handler on each custom Event activity.

~~~csharp
private void OnExternalUiEventHandled(object sender, ExternalDataEventArgs e)
ject sender, ExternalDataEventArgs e)
{
   var currentArgs = e as TwoStageEventArgs;
   if (currentArgs == null)
      throw new ArgumentException("Workflow event args were not of type TwoStageEventArgs");
   WorkflowStateArgs = currentArgs;
}
~~~

This code stores the passed TwoStageEventArgs locally in the workflow so that when the workflow runtime persists the current state, the TwoStageEventArgs are serialised and stored too.

### After Structure &#8211; comes Databinding&#8230;.

After you&#8217;ve set up your structure you will need to bind specific data to your Activities within each State. This is done using the Visual Studio Properties window, where you will need to hook up and bind a code property to the Activity property. In most cases these properties will be encompassed in your TwoStageEventArgs (which effectively represent the current Workflow State), however sometimes there are specific pieces of data needed, such as the SMTPsettings or simple strings. In these cases, add additional code properties (with getters) within you workflow class to get the data and then bind your Activity property to these properties.

Next post, we&#8217;ll cover EPiServer Tasks and also how to present Workflow User Interfaces in Edit and Admin mode.