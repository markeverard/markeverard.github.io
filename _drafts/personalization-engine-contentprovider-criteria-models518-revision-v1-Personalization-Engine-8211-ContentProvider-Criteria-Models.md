---
id: 4981
title: 'Personalization Engine &#8211; ContentProvider Criteria Models'
date: 2017-07-11T16:33:53+00:00
author: Mark Everard
layout: revision
guid: http://www.markeverard.com/2017/07/11/518-revision-v1/
permalink: /2017/07/11/518-revision-v1/
---
This post explains how to build a custom user interface element for the <a title="Personalization Engine for EPiServer" href="http://personalization.codeplex.com/" target="_blank">FortuneCookie PersonaliztionEngine for EPiServer</a> which will describe a criteria to be used within a ContentProvider.

Each <a title="Creating an IContentProvider" href="http://www.markeverard.com/2011/05/17/personalization-engine-creating-an-icontentprovider/" target="_blank">ContentProvider</a> possesses a Criteria string property which is set by an editor via the PersonalizationEngine UI and can be used within the GetContent method to specify a search term or more granular content method of retrieval. For example, the PagesInCategoryContentProvider uses the Criteria property to specify a drop down list populated withÂ the full list of site categories. The selected value of this drop down list is then used as a criteria within the FindPagesWithCriteria method.

[<img class="aligncenter size-full wp-image-520" title="CriteriaModel" alt="" src="http://markeverard.azurewebsites.net/wp-content/uploads/2011/06/CriteriaModel.jpg" width="464" height="189" srcset="https://www.markeverard.com/wp-content/uploads/2011/06/CriteriaModel.jpg 464w, https://www.markeverard.com/wp-content/uploads/2011/06/CriteriaModel-300x122.jpg 300w, https://www.markeverard.com/wp-content/uploads/2011/06/CriteriaModel-320x130.jpg 320w" sizes="(max-width: 464px) 100vw, 464px" />](http://markeverard.azurewebsites.net/wp-content/uploads/2011/06/CriteriaModel.jpg)

This partial view containing the CriteriaModel interface is loaded by an AJAX call made by the PersonalizationEngine Admin user interface when the ContentProvider dropdown is changed.

## Building your own CriteriaModel user interface element

The ICriteriaModel interface defines a way for developers to specify the user interface element that should be used by the editor to input the Criteria for each ContentProvider. Ultimately the persisted criteria value will be a string, but it could be entered via any type of HTML element, such as a dropdown list or simple input textbox. The exact CriteriaModel used can be specified in the VisitorGroupContentProvider attribute &#8211; if none is specified (or needed) a default CriteriaModel which displays a simple text message is used.

Building your own CriteriaModel involves creating a simple model and a partial view. This utilises a feature of the ASP.NET MVC 2 framework called Display and Editor Template Helpers. This feature provides a way to specify a custom view for any type within your solution (see <a title="ASP.NET MVC Template Helpers" href="http://blogs.msdn.com/b/nunos/archive/2010/02/08/quick-tips-about-asp-net-mvc-ui-helpers-and-templates.aspx" target="_blank">http://blogs.msdn.com/b/nunos/archive/2010/02/08/quick-tips-about-asp-net-mvc-ui-helpers-and-templates.aspx</a>). These partial views are placed in a specific location within your solution and the framework will then use those views to display your type any time it is rendered in a view using the <%= Html.DisplayForModel() %> or <%= Html.EditorForModel() %> html helpers.

Here is an example of the previously mentioned CategoryListCriteriaModel which is used by the PagesInCategoryContentProvider. The model contains the Criteria string property as defined by the ICriteriaModel interface. It also specifies a property that uses the EPiServer API to return the full list of categories. This property is used directly by the partial view.

Standard MVC <a title="Validation Attributes" href="http://www.asp.net/mvc/tutorials/validation-with-the-data-annotation-validators-cs" target="_blank">validation attributes</a> can be marked on the Criteria property which will be honoured by the Personalization Engine (by means of a custom Model Binder).

<pre class="brush: csharp; title: ; notranslate" title="">using System.Collections.Generic;
using System.Linq;
using System.Web.Mvc;
using EPiServer.DataAbstraction;

namespace FortuneCookie.PersonalizationEngine.EditorModels
{
   public class CategoryListCriteriaModel : ICriteriaModel
   {
      public string Criteria { get; set; }

      public IEnumerable&lt;SelectListItem&gt;; CategorySelectListItems
      {
         get
            {
               var rootCategory = Category.GetRoot();
               return from Category category in rootCategory.Categories
                        select new SelectListItem() { Text = category.LocalizedDescription, Value = category.Name };
            }
      }
   }
}

</pre>

The view itself is very simple, with just a label and the populated dropdown list.

<pre class="brush: csharp; title: ; notranslate" title="">&lt;%@ Control Language=&quot;C#&quot; Inherits=&quot;System.Web.Mvc.ViewUserControl&lt;FortuneCookie.PersonalizationEngine.EditorModels.CategoryListCriteriaModel&gt;&quot; %&gt;
   &lt;%= Html.LabelFor(m =&gt; m.Criteria)%&gt;
   &lt;%= Html.DropDownListFor(m =&gt; m.Criteria, Model.CategorySelectListItems)%&gt;
</pre>

The Personalization Engine project contains four ICriteriaModels that you can use in your own implementations

  * CategoryListCriteriaModel &#8211; very specific model displaying a dropdown list of EPiServer site categories
  * SearchReferrerCriteriaModel &#8211; a simple model displaying a text message. The SearchReferrerContentProvider in this case overrides the Criteria value to pull the criteria value used from the current request&#8217;s header.
  * DefaultCriteriaModel &#8211; displays a simple message stating &#8220;No criteria value necessary&#8221;
  * TextBoxCriteriaModel &#8211; displays a input textbox and also validates that a value must be entered.