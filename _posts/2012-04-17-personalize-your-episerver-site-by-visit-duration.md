---
title: Personalize your EPiServer site by visit duration
date: 2012-04-17T10:00:46+00:00
author: Mark Everard
layout: post
dsq_thread_id:
  - "1083585435"
categories:
  - Episerver
  - Technical
tags: [Episerver-CMS, Personalization]
---
Want to show a personalized message of gratitude to those users who spend a long time on your site? Now you can with the TimeOnSiteCriterion for the EPiServer Personalization/Visitor Group framework.

Technically &#8211; this is a straightforward implementation of a Visitor Group criteria, using some of the native Personalization API to record when the user starts their session. This is then compared to the current DateTime along with the editor selected time period to calculate a match.

![timeonsitecriterion](/assets/uploads/2012/04/timeonsitecriterion.png)

The TimeOnSiteCriterion is available as part of Criteria Pack version 1.3, which is available <a title="Criteria pack for EPiServer" href="http://criteriapack.codeplex.com/" target="_blank">on Codeplex</a> (along with the source code) and of course there is an individual Nuget package on <a title="EPiServer Nuget feed" href="http://nuget.episerver.com/" target="_blank">Nuget.episerver.com</a>

## An Extended example
One of the clients I&#8217;m working with at the minute had a requirement to display a modal pop-up on their site to user&#8217;s who had either visited a set number of pages, or had visited for more than a defined duration. Using the Visitor Group functionality sounded like a good match as it allows the editor&#8217;s to specify the exact criteria and mix and match with other matching criteria moving forwards.

The TimeOnSite criterion obviously forms part of this requirement, but fully meeting the requirement involves not waiting for a user to request a new page from your site and instead delegating responsibility to the browser to continue the countdown and respond (display the popup) when the criteria is matched. This is a requirement that the Visitor Group API doesn&#8217;t handle completely, as a Visitor Group is resolved as either a match or not a match when the page is delivered to the browser. There is no straightforward way of working out how &#8216;much&#8217; of a match the current request may be.

Never the less, we still have the flexibility to achieve this:

1. We need to link the site to a specific Visitor Group that will provide our criteria for when to display the popup (using a custom property like <a title="Visitor Group Custom Property" href="http://world.episerver.com/Blogs/Thomas-Krantz-/Dates/2011/4/Custom-property-for-selecting-Visitor-Groups/" target="_blank">this</a> will do the job &#8211; possibly on a site wide settings page)
2. We need a method of working out how close we are to a match being made &#8211; in this case how many seconds are left. This data needs to be delivered to the browser.
3. We need some client side script that will continue the countdown and trigger the popup when the countdown reaches zero.

The following public class is a part of the TimeOnSiteCriterion assembly, and contains the SecondsToMatch method to which can be used to work out how many seconds remain. The method will search a single Visitor Group &#8211; which is instantiated by Id (which is why we need a custom property to store this information), to find the first instance of a TimeOnSite model. This model contains the editor selected TimeOnSite criterion &#8211; which is then used with the user&#8217;s session start variable to calculate how long remains.

~~~csharp
using System;
using System.Linq;
using System.Web;
using EPiServer.Personalization.VisitorGroups;

namespace CriteriaPack.TimeOnSiteCriterion
{
    public class TimeOnSiteService
    {
        public TimeOnSiteService()
        {
            Store = new VisitorGroupStore();
        }

        public TimeOnSiteService(IVisitorGroupRepository store)
        {
            Store = store;
        }

        public IVisitorGroupRepository Store { get; set; }

        private double CurrentValueInSeconds(Guid visitorGroupId)
        {
            //load visitor group by providing id
            var group = Store.Load(visitorGroupId);
            if (group == null)
                return 0;

            //return first instance of TimeOnSite Criterion
            var timeOnSiteCriteria = group.Criteria.FirstOrDefault(a => a.TypeName == VisitorGroupCriterionRepository.GetTypeName(typeof(TimeOnSiteCriterion)));
            if (timeOnSiteCriteria == null)
                return 0;

            var model = timeOnSiteCriteria.Model as TimeOnSiteModel;
            if (model == null)
                return 0;

            if (model.DurationUnit == DurationUnit.Minutes)
                return model.TimeOnSite * 60;

            if (model.DurationUnit == DurationUnit.Hours)
                return model.TimeOnSite * 60 * 60;

            return model.TimeOnSite;
        }

        private DateTime SessionStart(HttpContextBase httpContext)
        {
            return Convert.ToDateTime(httpContext.Session[TimeOnSiteCriterion.SessionStartTimeKey]);
        }

        /// <summary>
        /// Returns the number of seconds remaining until a match.
        /// </summary>
        /// <param name="httpContext">The HTTP context.</param>
        /// <param name="visitorGroupId">The visitor group id.</param>
        /// <returns></returns>
        public double SecondsToMatch(HttpContextBase httpContext, Guid visitorGroupId)
        {
            var matchTime = SessionStart(httpContext);
            matchTime = matchTime.AddSeconds(CurrentValueInSeconds(visitorGroupId));

            double timeToMatch = (matchTime - DateTime.Now).TotalSeconds;
            return timeToMatch < 0
                ? 0
                : timeToMatch;
        }
    }
}
~~~

The final part is to use this in a page:

~~~csharp
protected void Page_Load(object sender, EventArgs e)
   {
      var service = new TimeOnSiteService();

      //retrieve this from a page / sitewide property - allowing match from the page to the time tracking visitor group
      var visitorGroupId = new Guid("4c3bb8b2-4e8e-4fbc-bc24-5018e8c935af");

      //use the TimeOnSiteService to return the number of seconds to match
      double timeToLive = service.SecondsToMatch(new HttpContextWrapper(HttpContext.Current), visitorGroupId);
      //convert to milliseconds and write to the page
      TimeToLive.Text = (timeToLive *1000).ToString();
   }
~~~
and then write some javascript to handle this (also perhaps write it slightly better than below. Hey I&#8217;m no javascript developer &#8211; yet!)

~~~javascript
<script type="text/javascript">
    function setAlertTimeOut() {
        var timeToLive = '<asp:Literal id="TimeToLive" runat="server" />';
        if (timeToLive > 0) {
            setTimeout('AlertMe()', timeToLive);
        }
    }

    function AlertMe() {
        alert("Time is Up");
    }
</script>
~~~

and also don&#8217;t forget to call the method setAlertTimeOut() when the browser loads your page. I&#8217;d also imagine that your front end will need to do something a little more fancy than pop up an alert&#8230;&#8230;&#8230;.