## World Table WordPress Integration

### Overview

In order to integrate World Table into WordPress a few modifications will need to be made to the theme templates.
There are several items that should be added, I'll walk through the item starting with the most important:

Note that World Table will not store comment data in the WordPress database. Our system always stores the comment
data in cloud storage hosted by Google. The WordPress commenting system should be shut off when using WorldTable.

In settings/discussion: uncheck "Allow people to post comments on new articles" and press the "Save Changes" button

For reference, the following example is using the "Twenty Nineteen"

### Notes

Whereever you see *0000000000000000_0000000*, you will substitute your sites World Table "site-id" that we issue to you. 
Using an example site-id or another site's site-id will not work.

### World Table Commenting Area 

The commenting area can only appear once on a page. Pages that show multiple posts should either not show the
commenting area or only show the commenting area for one post.

#### Script Tag

The script tag loads the World Table onto you site. It is required where ever any World Table related content appears,
even comment counts on index pages:

I put it at line 56 just inside/above the bottom of the `</body>` element
in the `footer.php` template of the 2019 theme.

    <script id="twt-script" src="https://app.worldtable.co/the-world-table.js" twt-site-id="0000000000000000_0000000" async></script>

#### Commenting Area

The unmodified 2019 theme template `single.php` (Single Post) contains the following php code at line 49:

    if ( comments_open() || get_comments_number() ) {
        comments_template();
    }

This is where the WordPress commenting system is inserted in post pages. The line `comments_template();` was commented
out with `//` to prevent old (example) comments from showing up.

Immediately after that code, the following was inserted:

    if ( is_singular( 'post' ) ):
      ?>
        <meta name="twt-article-id" content="<?php echo "WP-POST-" . $post->ID; ?>">
        <meta name="twt-article-url" content="<?php echo get_permalink(); ?>">
        <meta name="twt-page-title" content="<?php echo htmlentities($post->post_title); ?>">
        <meta name="twt-published-at" content="<?php echo $post->post_date; ?>">
        <meta name="twt-author-name" content="<?php echo the_author_meta('user_email'); ?>">
        <div class="comments-area">          
          <div id="twt-comments"></div>
        </div>
      <?php
    endif;

Which then generated the following markup in the `HTML`:

    <meta name="twt-article-id" content="WP-POST-5">
    <meta name="twt-article-url" content="https://docs.worldtable.co/wpexample/2019/08/08/test-post-one/">
    <meta name="twt-page-title" content="Test Post One!">
    <meta name="twt-published-at" content="2019-08-08 12:14:59">
    <meta name="twt-author-name" content="author1@worldtable.co">
    <div class="comments-area">
      <div id="twt-comments">...</div>
    </div>    

The outer `<div class="comments-area">` is used to apply the 2019 theme's comment-area styles to the commenting area. 
In allows the World Table commenting area to match the theme's margin design. They may or may not appy to other themes.

###### Author Id/Email

In the example, the author's user email, as known by WordPress, is used for the `twt-author-name`. This has been a successful strategy for 
most sites. You may however use any string value that can be associated with an individual author. Full names can be
used or even generic numbers. To use WordPress's author ID you could use the following meta tag instead:

    <meta name="twt-author-name" content="<?php echo 'wp-author-id-' . $post->post_author; ?>">

##### Pages vs. Posts

In this example, only "posts" are getting a commenting area. It is possible and sometimes desirable 
to have commenting on "pages". You can follow the same recipe as used for posts, just make sure that
the `twt-article-id` and `twt-article-url` have unique values for each page. The `twt-author-name` 
info can be used or omitted as desired. Publish date does affect some features like surveys and page 
ratings which change behaviour on older pages/articles. You can set a specific date in the past for all
"pages" like`2018-01-01 12:00:00` to get the behaviour appropriate for pages that are not *new articles*.
 
#### Recent Comments List

This widget is used to display a list of recent and "top" comments from across the site and can be used on 
the home page, navigation pages and even article pages. You can place this widget anywhere you like but you
will probably want to constrain the width to something reasonable or it may less aesthetically pleasing. This
widget provides navigation to active articles and when used broadly across a site induces reader engagement 
and additional page views.

    <div id="twt-top-comments-list"></div>

Absent other applicable styles, you can limit the width with something like:

    <div style="max-width: 400px; margin: 0 auto;">
      <div id="twt-top-comments-list"></div>
    </div>

For the example site, `footer.php` (Theme Footer) was modified by adding the following html at line 21, 
just after the other widgets were inserted at the top of the `<footer>` element:

		<div style="max-width: 400px; margin: 0 auto;">
		  <h5>Comments of Note</h5>
      <div id="twt-top-comments-list"></div>
    </div>

#### Community Table

The Community Table is a widget that creates a new page on a site showing active conversations going on
across the site. It updates each time comments are created on the site. After creating, you can add this
page to your site's navigation and add the URL to your World Table admin site settings at 
(https://app.worldtable.co)

For WordPress you can just create a new *Page* named "Community Table" with the following `HTML` as
the entire page content:

    <div id="twt-community-table"></div>
    
#### Comment Counts

Comment counts can be inserted in "The Loop" for both single and multiple post pages. The normal case is to wrap
the count with a link to the comment section/page. Here's the recommended markup for a typical inline link: 

    <a href="<?php echo get_permalink(); ?>#twt-comments" style="display: none;">
      <span twt-comment-count twt-article-id="WP-POST-<?php the_ID(); ?>">0</span> comments</a>

For the WordPress example site, the 2019 theme's `template-parts/content/content.php` template was modified
at line 26 by adding the HTML above just inside the bottom of the `</header>` element.

In addition, for single post pages, the HTML above was also added to `template-parts/content/content.php` 
in the same relative location. In this case, the link will perform a scroll-down when click to position 
to the top of the comments section.

You can customize the link text, wrap in a block element, add classes for styling, etc.

##### Special styles for comment counts when 0

The parent of the count number is given a HTML/CSS class of `twt-count-0` when the count is zero so you can 
setup special styles to hide or make zero counts more subtle like this CSS.

    .twt-count-0 {
       color: #ddd;
    }

For the example WordPress site, this CSS was added to the `style.css` file in the 2019 theme.