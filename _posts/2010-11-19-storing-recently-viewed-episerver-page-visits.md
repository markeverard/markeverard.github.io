---
title: Storing recently-viewed EPiServer page visits
date: 2010-11-19T03:41:08+00:00
author: Mark Everard
color: rgb(0,0,0)
layout: post
permalink: /2010/11/19/storing-recently-viewed-episerver-page-visits/
dsq_thread_id:
  - "1079377345"
categories:
  - Episerver
---
User tracking and delivering a personalised web experience is now a default part of any modern web-build. If you&#8217;re Amazon or another on-line retailer then there is real business value in storing and analysing as much user data as you can (hence the advent of cloud based storage solutions such as <a href="http://aws.amazon.com/s3/" target="_blank">Amazon S3</a> which was conceived firstly for use by Amazon itself before being opened up as a service). This data allows Amazon to calculate what you actually want for Christmas before you even <a href="http://www.amazon.co.uk/bigtrak-Bigtrak/dp/B0035IZ85G/" target="_blank">know yourself</a>!

In the more simple cases where there is no commercial requirement and the only use for this data is to provide a visual reminder to the user about the pages / content they&#8217;ve visited on your site, then storing this information in a browser-based cookie is a sensible approach.

The data we will need to store in the browser cookie is just a list of EPiServer PageReference Id&#8217;s for each of the recently visited pages (I&#8217;m not going to take explicit account of any separate language version visits).

I&#8217;ve wrapped this int value type in a more meaningful domain object (RecentPageHistory) and overridden the Equals method so we can accurately compare two RecentPageHistory objects (which we&#8217;ll need to do so we don&#8217;t get two instances of the same page in our page history list).

~~~csharp
public class RecentPageHistory
{
	public int PageReferenceId { get; set; }

	public override bool Equals(object obj)
	{
		if (obj == null)
			return false;

		if (obj is RecentPageHistory)
		{
			var assetHistory = obj as RecentPageHistory;
			return assetHistory.PageReferenceId == PageReferenceId;
		}

		return false;
	}
}
~~~

Recording a single user visit is best abstracted down to a base class inherited by all .aspx pages that you wish to record user visits for.

~~~csharp
public class RecordUserVisitPageBase : TemplatePage
{
        protected override void OnLoad(EventArgs e)
        {
            base.OnLoad(e);
            AddPageToUserHistory();
        }

        protected void AddPageToUserHistory()
        {
            var recentPageHistoryService = new RecentPageHistoryService();
            RecentPageHistory historyItem = RecentPageHistoryFactory.Create(CurrentPageLink.ID);
            HttpCookie updatedHistoryCookie = recentPageHistoryService.AddPageHistoryItemToCookie(GetUserHistoryCookie(), historyItem);
            AddCookieToResponse(updatedHistoryCookie);
        }

        private void AddCookieToResponse(HttpCookie updatedHistoryCookie)
        {
            Response.Cookies.Add(updatedHistoryCookie);
        }

        private HttpCookie GetUserHistoryCookie()
        {
            return Request.Cookies[RecentPageHistoryHelper.CookieName] ??
                   new HttpCookie(RecentPageHistoryHelper.CookieName);
        }
    }
}~~~

Finally there is the service level class which is responsible for the CRUD operations against the data storage mechanism (in this case a cookie). Although most of the functionality is not EPiServer specific, there is one method (GetPagesFromHistoryList) that deals with the EPiServer API. This method will populate a List of PageData objects and will also explicitly remove any PageReferenceID&#8217;s from the cookie that refer to pages that can no longer be found in EPiServer. Note that this method passes the cookie as a reference parameter so any consuming user interface class (page/user control) should also ensure that this cookie is added to the response stream so that the missing EPiServer page&#8217;s are removed from the cookie cache.

~~~csharp
public class RecentPageHistoryService
{
	protected const int MaximumHistoryItems = 4;
	protected HttpCookie AssetHistoryCookie { get; set; }

	private List&lt;RecentPageHistory&gt; GetPageHistoryListFromCookie(HttpCookie cookie)
	{
		var list = new List&lt;RecentPageHistory&gt;(MaximumHistoryItems);
		for (int i=0; i &lt; cookie.Values.Count; i++)
		{
			int cookiePageId;
			if (!int.TryParse(cookie.Values[i], out cookiePageId))
				continue;

			RecentPageHistory userHistory = RecentPageHistoryFactory.Create(cookiePageId);
			list.Add(userHistory);
		}
		return list;
	}

	public HttpCookie AddPageHistoryItemToCookie(HttpCookie existingCookie, RecentPageHistory pageHistoryItem)
	{
		List&lt;RecentPageHistory&gt; historyList = GetPageHistoryListFromCookie(existingCookie);

		if (historyList.Contains(pageHistoryItem))
			historyList.Remove(pageHistoryItem);

		historyList.Insert(0, pageHistoryItem);

		if (historyList.Count &gt; MaximumHistoryItems)
			historyList.RemoveAt(MaximumHistoryItems);

		return PopulateCookieFromHistoryList(historyList);
	}

	private static HttpCookie PopulateCookieFromHistoryList(IEnumerable&lt;RecentPageHistory&gt; pageHistoryList)
	{
		var newCookie = new HttpCookie(RecentPageHistoryHelper.CookieName);

		foreach (RecentPageHistory pageHistoryItem in pageHistoryList)
			newCookie.Values.Add(pageHistoryItem.PageReferenceId.ToString(), pageHistoryItem.PageReferenceId.ToString());

		return SetCookieExpiry(newCookie);
	}

	private static HttpCookie SetCookieExpiry(HttpCookie cookie)
	{
		cookie.Expires = DateTime.Now.AddMonths(1);
		return cookie;
	}

	private HttpCookie RemoveHistoryItemFromCookie(HttpCookie existingCookie, RecentPageHistory pageHistoryItem)
	{
		List&lt;RecentPageHistory&gt; historyList = GetPageHistoryListFromCookie(existingCookie);

		if (historyList.Contains(pageHistoryItem))
			historyList.Remove(pageHistoryItem);

		return PopulateCookieFromHistoryList(historyList);
	}

	public IEnumerable&lt;PageData&gt; GetPagesFromHistoryList(ref HttpCookie cookie)
	{
		var pagesInHistoryList = new List&lt;PageData&gt;();
		var pageHistoryList = GetPageHistoryListFromCookie(cookie);

 		foreach (var pageHistoryItem in pageHistoryList)
		{
			var assetReferenceId = new PageReference(pageHistoryItem.PageReferenceId);
			try
			{
				 pagesInHistoryList.Add(DataFactory.Instance.GetPage(assetReferenceId));
			}
			catch (PageNotFoundException e)
			{
 				cookie = RemoveHistoryItemFromCookie(cookie, pageHistoryItem);
			}
		}
		return pagesInHistoryList;
	}
}
~~~

I&#8217;ve not shown the code for a few of the small helper methods I&#8217;ve used. All that&#8217;s left is to write a user interface element that displays the pages (and updates the cookie). I won&#8217;t show that here as its even more straight forward than the rest of this walk through!

All the code needed to run this has been uploaded to the <a title="EPiServer World Code" href="http://world.episerver.com/Code/Mark-Everard/Storing-recently-viewed-EPiServer-page-visits/" target="_blank">EPiServer World Code section</a>, including the necessary User Interface elements to display the data on an EPiServer CMS6 Public Templates project home page, where (as long as you remember to add the RecordUserVisitPageBase to the set of page templates) you should end up with something like (in the bottom right)&#8230;&#8230;.

![Recently Viewed](/assets/uploads/2010/11/recently-viewed.jpg)