---
id: 5451
title: Creating a custom ModelBinder allowing validation of injected composite models
date: 2017-07-11T16:51:00+00:00
author: Mark Everard
layout: revision
guid: http://www.markeverard.com/2017/07/11/581-revision-v1/
permalink: /2017/07/11/581-revision-v1/
---
<a title="ASP.NET MVC Model Binding" href="http://weblogs.asp.net/nmarun/archive/2010/02/25/asp-net-mvc-model-binding.aspx" target="_blank">Model Binding</a> &#8211; is the &#8216;auto-magic&#8217; step performed by the ASP.NET MVC framework to convert user submitted data (either http post values, querystring values or url route values) into a strongly typed model, used in your controller actions.

Out of the box, the MVC framework also allows you to set <a title="Models and Validation in ASP.NET MVC" href="http://msdn.microsoft.com/en-us/library/dd410405.aspx" target="_blank">validation attributes</a> on your models which are inspected at the model binding stage, meaning that your controller actions can inspect the ModelState.IsValid property to assess whether the user submitted data meet expectations. This attribute based approach to validation provides a clean way to handle validation (a cross-cutting concern) without introducing additional code into your controller action.

One of the features needed when putting together the [Fortune Cookie Personalization Engine for EPiServer](http://personalization.codeplex.com/ "Fortune Cookie Personalization Engine for EPiServer ") was to perform validation on a user submitted criteria value. As a reminder, in the context of the Personalization Engine, a criteria is an editor submitted string which is persisted and used by a ContentProvider to allow for a more granular method of content retrieval. For further background and explanation, check out one of my earlier posts &#8211; <a title="Personalization Engine – ContentProvider Criteria Models" href="http://www.markeverard.com/blog/2011/06/06/personalization-engine-contentprovider-criteria-models/" target="_blank">Personalization Engine &#8211; ContentProvider Criteria Models</a>

In the full Personalization Engine domain model, criteria properties belong to IContentModel objects which are used to specify the user interface displayed to an editor to allow them to enter the criteria. Below is an example of a TextBoxCriteriaModel which renders as a textbox in the Admin interface.

[<img class="aligncenter size-full wp-image-586" title="criteriamodel-validation" src="http://markeverard.azurewebsites.net/wp-content/uploads/2011/07/criteriamodel-validation.jpg" alt="criteriamodel-validation-interface" width="571" height="294" srcset="https://www.markeverard.com/wp-content/uploads/2011/07/criteriamodel-validation.jpg 571w, https://www.markeverard.com/wp-content/uploads/2011/07/criteriamodel-validation-300x154.jpg 300w, https://www.markeverard.com/wp-content/uploads/2011/07/criteriamodel-validation-320x165.jpg 320w" sizes="(max-width: 571px) 100vw, 571px" />](http://markeverard.azurewebsites.net/wp-content/uploads/2011/07/criteriamodel-validation.jpg)

The string value from this criteria input is posted to the controller action.� However as the type of IContentModel depends on the value of the ContentProvider dropdown, validation attributes cannot be set directly on the model passed to/from this view as different concrete IContentModel types need to be able to specify different validation rules.

To provide validation of the composite IContentModel using validation attributes, we have to hook in to one of the extension points of the ASP.NET MVC framework and create a custom model binder.

### Validating composite models

Our [custom ModelBinder](http://odetocode.com/blogs/scott/archive/2009/04/27/6-tips-for-asp-net-mvc-model-binding.aspx "ASP.NET Model Binder") needs to perform the following tasks:

  * Bind the incoming data against an AdminViewModel (the model passed from the user interface shown above).
  * Obtain an instance of the specified ICriteriaModel and update the ICriteriaModel&#8217;s criteria property with the value posted by the form.
  * Validate the composite (and now populated) ICriteriaModel
  * Update the ModelState with the validation results from the composite model, along with the original parent model validation results

As the custom ModelBinder needs to perform all of the existing validation and binding that the DefaultModelBinder would, I&#8217;ve chosen to inherit from it and add the additional composite model validation logic into the overriden BindModel method. An instantiated IContentModel is obtained from the AdminViewModel, and the <a title="ModelValidator class" href="http://msdn.microsoft.com/en-us/library/system.web.mvc.modelvalidator.aspx" target="_blank">ModelValidator</a> framework class allows us to validate the composite IContentModel, before updating the bindingContext.ModelState with an validation errors.

<pre class="brush: csharp; title: ; notranslate" title="">public class CriteriaValidationModelBinder : DefaultModelBinder
{
   const string ValidationPropertyName = &quot;Criteria&quot;;

   public override object BindModel(ControllerContext controllerContext, ModelBindingContext bindingContext)
   {
      if (bindingContext.Model != null)
         return bindingContext.Model;

      var model = base.BindModel(controllerContext, bindingContext);

      var adminViewModel = model as AdminViewModel;
      if (adminViewModel == null)
         return model;

      var criteriaValue = bindingContext.ValueProvider.GetValue(ValidationPropertyName);
      adminViewModel.CriteriaModel.Criteria = criteriaValue != null ? criteriaValue.AttemptedValue : string.Empty;

      ModelMetadata modelMetadata = ModelMetadataProviders.Current.GetMetadataForType(() =&gt; adminViewModel.CriteriaModel, typeof(ICriteriaModel));
      ModelValidator compositeValidator = ModelValidator.GetModelValidator(modelMetadata, controllerContext);

      foreach (ModelValidationResult result in compositeValidator.Validate(null))
         bindingContext.ModelState.AddModelError(ValidationPropertyName, result.Message);

      return model;
   }
}

</pre>

Our custom ModelBinder can be hooked into our application in Global.asax, or in an <a title="EPiServer 6 Initialization" href="http://world.episerver.com/Documentation/Items/Tech-Notes/EPiServer-CMS-6/EPiServer-CMS-60/Initialization/" target="_blank">EPiServer IInitializableModule</a> using the following method.

<pre class="brush: csharp; title: ; notranslate" title="">private void AddCriteriaValidationModelBinder()
{
    ModelBinders.Binders.Add(typeof(AdminViewModel), new CriteriaValidationModelBinder());
}

</pre>

And that&#8217;s it&#8230;. ModelBinders are an important piece of the MVC framework, and in the majority of scenarios you can rely on the DefaultModeBinder to handle all of your requirements. However creating your own ModelBinder for more advanced requirements is pretty straightforward, depending on your requirements for your binder 🙂