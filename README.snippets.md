## World Table Universal Script Snippets

### Quickstart

##### Minimum For Commenting - Main Commenting Widget

    <script id="twt-script" src="https://app.worldtable.co/the-world-table.js" twt-site-id="0000000000000000_0000000" async></script>
    <div id="twt-comments"></div>

Where *0000000000000000_0000000* is replaced with your World Table Site Id. (Note: Site ID will be created and provided by World Table support team)

##### Add a Comment Count

    <a href="#twt-comments" style="display: none;"><span twt-comment-count>0</span> comments</a>

##### Add a Top Comments Widget

    <div id="twt-top-comments-list"></div>

##### Add surveys or page ratings on a different page or area of articles

    <div id="twt-engagement-tool-widget></div>

##### Notes:

`twt-comments` and `twt-engagement-tool-widget` require article information, e.g. if your site uses `twt-article-id` then this must be
specified in  `<meta>` tag or an attribute on these elements. The following properties are needed to fully identify pages:

* `twt-article-id` - globally unique & durable id for article (if your site uses them)
* `twt-article-url` or `twt-permalink` - canonical url for article
* `twt-published-at-ms` or `twt-published-at`- original article publish timestamp in ms, seconds or ISO8601 datetime
* `twt-author-name` - unique ID for author (usually sites use email)
* `twt-page-title` - article title (mostly used for notification emails)

Several of these items can be specified in multiple ways, see section below about each item.

In addition, a `site-id` is always required for the `the-world-table.js` script in `<meta>` elements or a property on the script tag.

Ideally, these properties are specified explicitly with a `<meta>` tag or attribute but when unspecified the plugin will try to parse them from other metadata or data on the page.

##### Add a page for Community Table

    <div id="twt-community-table"></div>

##### Notes:

The Community Table is designed to be on it's own page and not on the same page as the main commenting
widget which is intended to go on article pages. The Top Comments Widget can be used with either and one
or more comment counts can be on any page. Comment counts will show the count for article pages where
they appear unless they have a `twt-comment-count` attribute value (article URL) or `twt-article-id` with 
an article id if your site uses those.

### The Universal Script

The snippet system has a single integration script and supports simplified HTML-only integration.

For any of The World Table commenting system features, the following script tag should be included on the page(s):

    <script id="twt-script" src="https://app.worldtable.co/the-world-table.js" twt-site-id="0000000000000000_0000000" async></script>

Of course, the all zeros example `twt-site-id` attribute value should be replaced with the site's actual 
value. The admin can visit the *Sites* section of their profile page at: https://app.worldtable.co/#!/sites

##### World Table Site Id

If it is inconvenient to place the site's `twt-site-id` on the script tag, a meta tag anywhere in the document above or
before the script tag may be used:

    <meta name="twt-site-id" content="0000000000000000_0000000"> 

The World Table features use intelligent delays for loading scripts and data optimized for comment counts to appear
quickly and the comment area to be loaded after a much longer delay since it is usually much further down on the page
and often not initially visible. In unusual cases, sites can override the delays used with the following attributes
on the script tag above. Units are milliseconds after the the-world-table.js begins executing.

   `twt-top-comments-list-delay` - delay for loading top comments list (sidebar widget)
   `twt-comments-delay`          - delay for main comment area 
   `twt-community-table-delay`   - delay main content area of community table page 

The comment counts do not require any additional scripts and are executed asynchronously so they are able to fill in
comment counts with extremely low impact to other page load activity.

### Comment Counts

A page's own comment count can be displayed with the following markup:

    <a href="#twt-comments" style="display: none;"><span twt-comment-count>0</span> comments</a>

This streamlined markup will display a link for uses to scroll down to the comment area. The page is identified in the
same flexible way the comment area does, described later in this document. A simplified single tag canbe used if no 
link is desired:

    <span twt-comment-count>0</span>

In addition, comment counts for _other_ pages can be displayed with a permalink value in the `twt-comment-count`
attribute like:

    <a href="{permalink}#twt-comments" style="display: none;"><span twt-comment-count="{permalink}">0</span> comments</a>

or for a non-link display only:

    <span twt-comment-count="{permalink}">0</span>

Where `{permalink}` is the pages permalink. If the site chooses to use *ArticleIds* (described later in this document),
an analogous `twt-article-id` attribute with value should be used.

### Comment Area

In its simplest form, the main comment area can be displayed using a single, simple tag like:

    <div id="twt-comments"></div>

##### Page URL or Permalink

To uniquely identify a page, The World Table uses a page's url (without parameters) or permalink unless a more explicit
url or permalink is specified. The more explicit values come from meta tags, either `name` or `property` may be used 
for the meta tag. The following search order is used:

1. `twt-article-url` meta tag 
1. `twt-permalink` meta tag
1. `og:url` meta tag
1. the document's href as reported by HTML DOM's `document.location.href` with the url parameters removed

##### Page Title

To ascertain the page's title, a similar heuristic is used with the following search order:

1. `twt-page-title` meta tag
1. `og:title` meta tag
1. the document's title property (HTML DOM's `document.title`), if the title contains one or more `|` (vertical bar) only the part before the first bar is used.
1. the document's url if no title is found

##### Page Author

The World Table allows pages to have an associated *author*, the author name is ascertained from:

1. `twt-author-name` meta tag
1. `author` meta tag
1. `twitter:creator` meta tag
1. the default value `unknown author`

Author names may be associated with specific World Table accounts in the site admin's sites section: https://app.worldtable.co/#!/sites

##### Publish Date

Publish dates control some functionality that only appears on recently published pages. A explicitly
provided publish date is best using a meta tag like:

    <meta name="twt-published-at-ms" content="12345678">

It normally takes a number of milliseconds like the javascript `Date.now()` function provides. Seconds values are also supported.

As a less reliable backup, an attempt is made to parse the date in a `datePublished` meta tag that looks like:

    <meta itemprop="datePublished" content="2018-01-01T03:20:00-06:00">

or World Table specific meta tag with ISO8601 format date:

    <meta name="twt-published-at" content="2018-01-01T03:20:00-06:00">

##### Page Article Ids

In cases where a page's url or permalink might change over time, sites can choose to use a separate string value to
uniquely identify a page, otherwise existing comments will become disassociated with the page. An example is newspaper 
site where a page's url can change when a headline is revised or corrected. In order to allow the comment thread started 
on one revision to _travel with_ the article and it's new url, an *ArticleId* can be used. This value can be any 
reasonable length string that will be unique within the site. 

This mechanism can also be used to tie comment threads to multiple pages that should share the thread like some slide 
shows or `listicles` that have multiple urls/pages.

In addition, there are cases where a site may want a single comment thread across multiple pages. Care must be 
taken since comments might write comments that assume the context of the page they see while commenting and readers
on a page with different content might be confused.

Unlike permalinks, *ArticleIds* don't have to be navigable urls, so a url or permalink is used in addition to allow users
link to a page in user interfaces such as the *Top Comments List* or notification emails. 

To specify an *ArticleId*, a meta tag is used with the name `twt-article-id` like:

    <meta name="twt-article-id" content="{your-content-id}">

*ArticleIds* are case sensitive and a strict equality comparison is used. Though not necessary, it is recommended that 
leading and trailing whitespace be trimmed from values used because of the difficulty debugging invisible differences.
Javascript's unicode implementation and string comparison rules are in effect. 

### Demo Pages

* [Example 0](https://test.worldtable.co/demo/demo-0.html) - minimal markup
* [Example 1](https://test.worldtable.co/demo/demo-1.html) - simple, typical article page using page url 
* [Example 2](https://test.worldtable.co/demo/demo-2.html) - simple, typical article page using article id

### Special Cases

##### HTML Classes Preferred Over Ids

If the special World Table HTML ids are an issue for a site, alternate classes may be used:

The class `twt-comments-container` may be used instead of the id `twt-comments`
The class `twt-top-comments-list-container` may be used instead of the id `twt-top-comments-list`
The class `twt-community-table-container` may be used instead of the id `twt-community-table`

##### User Triggered Comment Thread Loading

Sites can implement user triggered comment thread loading by using the *Async API*

Example:

    <div id="comments-area">
      <a style="display: none;"><span twt-comment-count>0</span> comments</a>
      <button id="load-comments" type="button">View Comments</button>
    </div>
    <script>
      var button = document.getElementById('load-comments');
      button.addEventListener('click', function (evt) {
        var container = document.getElementById('comments-area');
        container.classList.add('twt-comments-container');
        container.innerHTML = '';
        window.twtAsyncAPI('load', 'twt-comments'); // use 'load' so redundant calls are ignored
      });
    </script>

##### Single Page App

If a site uses AJAX or similar techniques to avoid full page reloads, the *Async API* can be used to load comments
for the new logical page at the same time the content is loaded. A trivial example of virtual navigation comment
handling. The example code assumes there is an element with the id twt-comments in the DOM already
    
    function loadCommentsForLogicalPage(logicalPageDeepLinkUrl, title, author, articleId) {
      var options = {
        permalink: logicalPageDeepLinkUrl,
        pageTitle: title,
        authorId: author
      };
      if (articleId) {
        options.articleId = articleId;
      }
      // use 'reload' to force refresh even if already loaded with previous logical page
      window.twtAsyncAPI('reload', 'twt-comments', options); 
      window.twtAsyncAPI('reload', 'twt-comment-count', options);
    }

In order to logically switch pages, the general recipe is:

* Set the `twt-article-url` meta tag with the (deep link / link back / permalink) URL to the page
* If the site uses *Article Ids*, set the `twt-article-url` meta tag
* Set the `twt-page-title` meta tag (or the HTML DOM's `document.title`) to the logical page title
* Set the `twt-author-name` meta tag
* Call `reload` on `twt-comments` using the *Async API*

### Async API

Explicit Loading of Features

    window.twtAsyncAPI(<action>, <feature>, <options>)

* 'load' action - explicitly load a feature that hasn't loaded yet
  * 'twt-comments' - main comment area for page
  * 'twt-top-comments-list' - top comments (sidebar) widget
  * 'twt-community-table' - main content area for community table page
  * 'twt-comment-count' - load any comment counts that have not yet loaded

Note: the `load` action does nothing if the feature is already loaded
  
* 'reload' action - force a reload of a feature even if it was previously loaded, avoid calling needlessly or it will cause flash
  * 'twt-comments' - main comment area for page
  * 'twt-top-comments-list' - top comments (sidebar) widget
  * 'twt-community-table' - main content area for community table page
  * 'twt-comment-count' - comment counts that have not yet loaded