---
title: 'Developing a custom workflow in EPiServer : Part four'
date: 2011-01-24T09:30:07+00:00
author: Mark Everard
layout: post
permalink: /2011/01/24/developing-a-custom-workflow-in-episerver-part-four-2/
dsq_thread_id:
  - "1073095675"
categories:
  - Episerver
---
This is the fourth post, in a series of five about developing a custom workflow in EPiServer. By now you&#8217;ve probably forgotten the first three.

  *  <a title="Part One: Overview and Requirements" href="/2010/09/29/developing-a-custom-workflow-in-episerver-part-one/">Part One &#8211; Overview and Requirements</a>
  * <a title="Part Two: Windows Workflow Foundation in EPiServer" href="/2010/09/30/developing-a-custom-workflow-in-episerver-part-two/">Part Two &#8211; Windows Workflow Foundation in EPiServer</a>
  * <a href="/2010/11/10/developing-a-custom-workflow-in-episerver-part-three/">Part Three &#8211; Designing the Workflow</a>
  * **Part Four &#8211; EPiServer tasks and working with the user interface**
  * Part Five &#8211; Access control and finishing touches

An important part of our custom workflow is the decisions that are made by users in the Approvers and Publishers roles. These external choices will alter the current state of the workflow process (which if you remember is the reason we chose a StateMachineWorkflow way back in <a title="Windows Workflow Foundation in EPiServer" href="/2010/09/30/developing-a-custom-workflow-in-episerver-part-two/" target="_blank">part two</a>). In this case EPiServer is acting as the user interface for all workflow decisions.

EPiServer provides a framework to achieve this by tying together the concept of an EPiServer Task with the Workflow and an EventDrivenActivity within the workflow.

## What is an EPiServer Task?
An <a title="EPiServer.Personalization.Task" href="http://sdk.episerver.com/library/cms6/html/T_EPiServer_Personalization_Task.htm" target="_blank">EPiServer.Personalization.Task</a> describes a task assignable to users in the system. Each task can be assigned to a single user, or a group of users, and can be assigned a due date. Notification of assigned tasks are emailed to each user and are also visible on the page tree tab (and also a Dashboard gadget in CMS 6).

Tasks can be created <a title="Create EPiServer Tasks Programatically" href="http://krompaco.nu/episerver-task-by-code/" target="_blank">programatically</a>, though they are mainly used internally by EPiServer to provide a means of notifying editors of any actions that they are required to take in the CMS. Tasks can also be created programatically from the Workflow designer surface by dragging and dropping an [EPiServer.WorkflowFoundation.Activities.CreateTask](http://sdk.episerver.com/library/cms5/html/T_EPiServer_WorkflowFoundation_Activities_CreateTask.htm) activity which is included in the EPiServer workflow library. This Activity allows you to bind all of the Task properties using the designer interface. This allows your workflow to create Tasks, and so notify users of any necessary actions.

### Using tasks to tie the workflow to the user interface
The <a title="EPiServer.Personalization.Task" href="http://sdk.episerver.com/library/cms6/html/T_EPiServer_Personalization_Task.htm" target="_blank">EPiServer.Personalization.Task</a> type has a property called [AssociatedActivity](http://sdk.episerver.com/library/cms5/html/P_EPiServer_WorkflowFoundation_Activities_CreateTask_AssociatedActivity.htm) which allows you to associate a workflow Activity with a given Task. EPiServer then provides a way Activities themselves can be associated with UI elements (such as a standard ASP.NET User control) by decorating your custom Activity with an ActivityPlugin attribute.

This is where the ability to define partial classes and have the c# compiler combine them into one comes in extremely handy. In part three we used the external wca.exe tool to generate four custom EventDriven activities based against the four events defined in our custom communication service (ITwoStageService). These were PageApproved, PageDeclined, PagePassedToApprover and PagePassedToPublisher.

Rather than adding these attributes to the auto-generated code, we can create some partial stubs with the same signature and add the ActivityPlugin attribute to those. These prevents you having to touch the auto-generated code. For example:

~~~csharp
using EPiServer.WorkflowFoundation.UI;

namespace TwoStage.Workflow.Activities
{
     [ActivityPlugIn(Area = EPiServer.PlugIn.PlugInArea.None, Url = "~/Templates/Plugins/Workflows/ApproverDecisionControl.ascx")]
     public partial class PagePassedToApprover
     {
            //no implementation - just a partial stub
     }
}
~~~

This Attribute specifies a User Control and so forms the final link between a Task (created in the workflow using the CreateTask Activity), a custom Activity and a user interface element.

If the AssociatedActivity is not set for a Task, the corresponding workflow instance will be searched to find any waiting (event-based) activities when the task link is followed by the user. The User Control specified by the attribute will be loaded if any of the waiting activities has a valid ActivityPlugIn attribute set. If no valid attribute is found the default edit task control will be loaded. If this isn&#8217;t found then you&#8217;ll end up with strange errors of the form &#8216;Cannot redirect HTTP headers already sent&#8221;. What this basically means is that you haven&#8217;t specified a User Control and so EPiServer doesn&#8217;t know which UI to load.

Your custom User Control must adhere to the IWorkflowTaskControl interface which contains the following methods ContextData, InvokeEvent, PageHasChanged and the property InvokeButtonText.

* **ContextData** &#8211; is called when the user control is first loaded and as its name suggest, it is used to pass the &#8216;context&#8217; of the workflow to the control. This obviously depends on the exact functionality defined in your custom workflow and what data you wish to present to the user. An example is presented below, which loads the current task and binds the task subject, description and associated workflow pageLink to controls on the page.

~~~csharp
public void ContextData(Guid workflowInstanceId, int taskId, string eventQualifiedName, PageReference pageLink)
{
      Task currentTask = new TaskDB().Load(taskId);
      ltrTaskSubject.Text = currentTask.Subject;
      ltrMessageFromCreator.Text = currentTask.Description;
      pageRoot.PageLink = pageLink;
 }
~~~

* **InvokeEvent** &#8211; when your User Control is presented in the Action Window, EPiServer adds two buttons beneath your control. One is a cancel button and the other is a submit button which you can name by overriding the **InvokeButtonText** property. This button also fires an event which you must hook into using the InvokeEvent method. It is in this method that you can put code which can raise events in the workflow. In our case we want to raise one of our custom events that will be handled by an EventDrivenActivity in the workflow. This will kick the workflow into a new state and run any of the StateInitializationActivities that we bound using the designer surface.

~~~csharp
public bool InvokeEvent(Guid workflowInstanceId, int taskId, string eventQualifiedName)
{
         //get current task information
	var task = Task.Load(taskId);

	//get workflow communication service
	TwoStageService service = WorkflowSystem.WorkflowManager.GetService&lt;TwoStageService&gt;();

       var entityName = GetApproverGroupOrUserName();
       var taskOwner = GetTaskOwner(entityName);

       //create the eventArgs from the current control context and raise the necessary event in the workflow communication service
       var passOnEventArgs = new EmailWorkflowEventArgs(workflowInstanceId, pageRoot.PageLink, taskOwner,
	                                           actioningUser, tbxMessageApprover.Text);
       service.RaiseEmailPassedToApprover(passOnEventArgs);

       //Deletes current task if true is returned
       return true;
}
~~~

* **PageHasChanged** &#8211; this method will be called prior to **InvokeEvent** if the page viewed in the edit tab has changed since **ContextData** was invoked. Here you can handle what should happen in this scenario, for example whether you want the Workflow to act against the new page shown in the edit window.

### Additional User Interface elements

EPiServer also allows you to associate a user control with the act of creating a workflow instance. This control should use the IWorkflowStartParameterHandler instance. This control will be seen in either Admin mode or the Action window. It allows you to specify start parameters for the workflow instance using the LoadStartParameters and SaveStartParameter methods. For example:

~~~csharp
public void LoadStartParameters(Guid definitionId, PageReference pageLink, bool definitionMode, IDictionary&lt;string, object&gt; parameters)
{
      //if workflow already contains state then set the current pagelink
      if (parameters.ContainsKey("WorkflowState") && parameters["WorkflowState"].GetType() == typeof(EmailWorkflowEventArgs))
            pageRoot.PageLink = ((EmailWorkflowEventArgs)parameters["WorkflowState"]).PageLink;

      //if loaded from edit tab, set pagelink if not already set
      if (PageReference.IsNullOrEmpty(pageRoot.PageLink) && Page.Request["currentPage"] != null)
            pageRoot.PageLink = pageLink;

      //perform any other necessary interface actions here...
  }

public IDictionary&lt;string, object&gt; SaveStartParameters()
{
      var startParameters = new Dictionary&lt;string, object&gt;();

      //custom class to represent task ownership
      var newTaskOwner = new GroupTaskOwner(ddlUsers.SelectedValue);
      var actioningOwner = new ActioningUserTaskOwner();

      //set initial state from user interface data
      startParameters["WorkflowState"] = new EmailWorkflowEventArgs(Guid.Empty, pageRoot.PageLink,
                                                              newTaskOwner, actioningOwner, tbxDescription.Text);
      startParameters["WorkflowCreator"] = tbxOwner.Text;
      return startParameters;
}
~~~

That&#8217;s covered off the basics of hooking up external user interface events to an EPiServer workflow. Additional detailed information can be found in the <a title="Developing Workflows" href="http://sdk.episerver.com/library/cms5/Developers%20Guide/Workflows/Developing%20Workflows.htm" target="_blank">EPiServer SDK</a>. Hopefully the combination of these two posts will provide enough information for you to create some intricate EPiServer workflows. In the final part of this series we&#8217;ll cover off some finishing touches that you may want to add, such as only allowing one instance of a particular workflow and some elements of Access control. 