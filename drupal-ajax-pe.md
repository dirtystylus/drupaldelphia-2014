# Progressive Enhancement in Drupal 7
## Using Ajax-Include Patterns

---

![inline](http://cl.ly/image/1E1R0v2T0o3I/bc-logo.jpg)
## Mark Llobrera
### @dirtystylus
### @bluecadet

^ This talk is broken into three parts:
* progressive enhancement overview
* a case study
* a short and simple demo

---

# Some groundwork.

---

# What is Progressive Enhancement?

^ Progressive Enhancement is more of a philosophy than a technique.

---

# Content First

---

# “Understanding Progressive Enhancement”

\- by Aaron Gustafson

http://alistapart.com/article/understandingprogressiveenhancement


---

> “Progressive enhancement is a philosophy aimed at crafting experiences that serve your users

---

> by giving them access to content without technological restrictions.”

---

# Design and build for everybody.

---

# The same content for everybody.
## (Not necessarily the same experience.)

---

* Semantic HTML Markup

---

* Semantic HTML Markup
* CSS

^ HTML/CSS are grouped together, because they are fault-tolerant.

---

# HTML and CSS
## are fault-tolerant

---

* Semantic HTML Markup
* CSS
* JavaScript

---

# JavaScript is *not* fault-tolerant

---

![fit](images/fwa-nojs.jpg)

---

* giant expanse of nothing
* menus don’t work

---

![170%](images/m-m.jpg)
<br>
(*Image © A List Apart*)

---

![100%](http://www.mariowiki.com/images/thumb/6/6b/SMB3_Smallmario.svg/96px-SMB3_Smallmario.svg.png)

---

![700%](http://www.mariowiki.com/images/archive/0/0b/20101006100050%21Super_Mario_SMB3.PNG)

---

![600%](http://www.mariowiki.com/images/archive/3/3f/20110223082817%21Tanooki_Mario_SMB3.PNG)

---

![100%](https://encrypted-tbn1.gstatic.com/images?q=tbn:ANd9GcRMeRmwYKCq6vUZBSzjgT_ojsRwNJLw7U9QiF-7YRj5_ZW8yEHllQ)

---

![140%](https://33.media.tumblr.com/157b3a4c13f0b4c167b1bdb05d8ef315/tumblr_mopu2fc9Ss1rkg2f4o1_250.gif)

---

# Why?

---

# “It’s good for you.”
## -Mom & Dad

---

# “It’s good for your users.”
## -Mom & Dad

---

* Accessibility

---

* Accessibility
	* Older Browsers
	
---

![90%](images/boston-globe-newton.gif)
<br>
Photo by Grant Hutchinson: https://www.flickr.com/photos/splorp/6141775264/in/set-72157624225682388

---

* Accessibility
	* Older Browsers
	* Newer “Browsers”: game consoles, wearables

---

![](images/apple-watch.jpg)

---

# Thaaanks, Apple.

^ As far as I can tell there’s no browser on this thing. So I need to figure out how my content can get on this.

---

* Accessibility
	* Older Browsers
	* Newer “Browsers”: game consoles, wearables
	* Users of assistive devices: browsers for sight/mobility impaired users

---

* Accessibility
	* Older Browsers
	* Newer “Browsers”: game consoles, wearables
	* Users of assistive devices: browsers for sight/mobility impaired users
* Performance

---

# Some Scenarios

---

* A list of headlines

---

* A list of headlines + thumbnails loaded after inital load

---

* A list of headlines + thumbnails loaded after inital load
* A list of topics, each topic loaded on click.

---

* A list of headlines + thumbnails loaded after inital load
* A list of topics, each topic loaded on click.
* An image gallery, loaded on click

---

# Techniques

---

# Ajax-Include Patterns

“An Ajax-Include Pattern for Modular Content”
*by The Filament Group*

![inline](images/ajax-include-github.jpg)

---

* Lazy-load non-essential content: create hooks for content to target based on certain rules
	* Hyperlinks to content can be enhanced to become excerpts
* Load can be done in a staggered fashion, or on demand (click)

---

# Pattern
```html
<h2><a href="/path/to/content">Related Articles</a></h2>
```

---

# Pattern

* data-before
* data-after
* data-replace
* data-interaction

---

# Pattern

* data-before
* data-after
* data-replace
* data-interaction

```html
<h2><a data-after="/path/to/content?ajax=1"
href="/path/to/content">Related Articles</a></h2>
```

---

# Example:
## Temple Computer Services

---

# Service catalog:

* Over *100* articles covering topics such as Internet/Phone Access, Security, Email.
* Each article can contain multiple sub-sections.

---

# Wireless Access Article

* Instructions for numerous OSes
* Two issues: bandwidth and accessibility. Wide range of devices with many capabilities
* Loading every subsection would be wasteful in terms of time/bandwidth
* Loading every subsection would be problematic for devices without JavaScript, like some screenreaders

---

![30%](images/tucs-article.jpg)

---

![](images/tucs-article.jpg)

---

# Solution

* Hyperlinks to subsections, progressively enhanced with JavaScript
* Users without JavaScript can click on the hyperlink and go to the subsection, loaded as a separate node page
* Users *with* JavaScript would have the subsection loaded via Ajax
 
---

# Fulfills accessibility and performance goals:

* Users of devices that don’t use JavaScript can get to content
* Users on slower networks don’t wait for entire page to load

---

# How?
## URL Pattern (node template)

```html
<a data-interaction 
data-after="<?php print $node_url . '?is_ajax=1' ?>" 
href="<?php print $node_url; ?>"
rel="bookmark">
<h3<?php print $title_attributes; ?>>
<?php print (render($content['field_display_title'])); ?>
</h3></a>
```

---

```html
<a 
data-interaction
data-after="/topic/applicant?is_ajax=1"
href="/topic/applicant">
<h3>Applicant</h3>
</a>
```

---

# How?
## JavaScript

* Include *ajaxinclude.js*
 
https://github.com/filamentgroup/Ajax-Include-Pattern

* **On click** make request to URL in **data-after** attribute

---

```javascript
Drupal.behaviors.computer_servicesLoadInstructionSet = {
    attach: function (context, settings) {
      $( "a[data-interaction]" ).once( "instructionajax", function() {
        $( "a[data-interaction]" ).unbind().bind( "click", function() {
          $( this ).removeAttr( "data-interaction" ).ajaxInclude();
          $( this ).on( "ajaxInclude", function () {
            // … do some things after load here
          });
          return false;
        });
      });
    }
  };
  ```

---

# How?
## Drupal *theme\_preprocess\_html* hook (template.php)

```php
function computer_services_preprocess_html(&$variables) {
  if (!empty($_REQUEST['is_ajax'])) {
    print render($variables['page']['content']);
    die();
  }
}
```

---

# Result

---

![](images/TUCS.mov)

---

# To recap:

* A simple unordered List is progressively enhanced to an image slideshow
* List is perfectly understandable content without JavaScript

---

# Image Gallery

---

![80%](images/tucs-image-gallery.jpg)

---

* We worked backwards here, which is harder
* Like mobile-first RWD, Progressive Enhancement is easier when you’re adding complexity gradually

---

# Progressive Enhancement
## is easier when you’re adding complexity *gradually*

---

# DEMO

---

# Conclusion

* We want our content to reach the widest possible audience
* We want to be able to scale the user’s experience to their device’s capabilities

---

# Conclusion 

* Think of layers: HTML can be rendered/understood by almost everything
* Layer on CSS for presentation
* Layer on JavaScript as an enhancement
* If you rely on JavaScript pause and ask why

---

## This is hard work, and it takes time to get it right.

---

# Thank you.

---

![inline](http://cl.ly/image/1E1R0v2T0o3I/bc-logo.jpg)
## Mark Llobrera
### @dirtystylus
### @bluecadet

---


# Example:
## Lapham’s Quarterly

* Literary journal
* “Sophisticated whimsy”

---

# Random Artwork

---

![fit](images/laphams-article.jpg)

---

![fit](images/laphams-article-zoom.jpg)

---

* Random artwork is not tied to essay (whimsy!) so it is not essential
* If user does not have JavaScript, we do not display the image(s)
* Image content is the progressive enhancement

---

# Solution

* Theme functions to run query for the set of available artwork
* Query results written out as JavaScript arrays
* JavaScript runs and looks for markup patterns to replace with artwork
* Custom node templates used if an ajax param is present in the URL request. 

^ If the ajax param is not present, the full page is rendered. If not, then only the core artwork of the node is sent back.

---

# How?
## Drupal 

```php
function laphams_theme_get_quote_nodes_by_term_id($tid) {
  $query = db_select('taxonomy_index', 't');
  $query->addTag('node_access');
  $query->join('node', 'n', 'n.nid = t.nid');

  $query->condition('tid', $tid);
  $query->condition('type', 'quote');

  $query->addField('t', 'nid');
  $query->addField('t', 'tid');
  $nids = $query->execute()->fetchCol();

  laphams_theme_add_ajax_vars($nids, "quote_nids");
}
```

---

```php
function laphams_theme_add_ajax_vars($nids, $array_name) {
  $node_urls = array();
  $options = array('absolute' => TRUE);
  foreach ($nids as $nid) {
      $node_urls[] = url('node/' . $nid, $options);
  }
  $js_string = 'var ' . $array_name . ' = ' . json_encode($node_urls) . ';';
  $inline_options = array(
  	'type' => 'inline', 
  	'group' => JS_THEME, 
  	'defer' => TRUE, );

  drupal_add_js($js_string, $inline_options);
}
```
result:

```javascript
var widescreen_artwork_nids = 
["http://laphams.local/time/art/time-lord-byrons-dream",
"http://laphams.local/time/art/time-acropolis-photograph"…];
```

---

# How?
## WYSIWYG Shortcode Insert

* Shortcode inserts HTML pattern:

```html
<a href="" data-replace-widescreen="true"></a>
```

---

# How?
## JavaScript

```javascript
$("a[data-replace-widescreen]").each(function(index) {
  if (widescreen_artwork_nids.length > 0) {
    loadArtworkFragment(
    	getRandomUniqueNodeId(widescreen_artwork_nids), $(this)
    );
  }
});

function loadArtworkFragment(nodeUrl, target) {
  window.art_bool = true;
  console.log("nodeURL: ", nodeUrl);
  $.ajax({
    url: nodeUrl,
    type: 'GET',
    data: {'is_ajax': 1},
    cache: false,
    success: function (response) {
      $(target).children().remove();
      $(target).append(response);
    },
    complete: function () {
      console.log('ajax done');
      if(window.apropo){
        renderApropos();
        aproposFade();
      };
    },
    error: function() {
      console.log('ajax error');
    }
  });

}
```
^ If we have values in the widescreen\_artwork\_nids array, then load a piece of random artwork

---

# How?
## Drupal

```php
function laphams_theme_preprocess_html(&$variables, $hook) {
  if (isset($_GET['is_ajax']) && $_GET['is_ajax'] == 1) {
      $variables['theme_hook_suggestions'][] = 'html__embed';
    }
}

function laphams_theme_preprocess_page(&$variables) {
	if (isset($_GET['is_ajax']) && $_GET['is_ajax'] == 1) {
  	$variables['theme_hook_suggestions'][] = 'page__embed';
	}
}
```

---

### html--embed.tpl.php

```php
<?php print $page; ?>
```

### page--embed.tpl.php

```php
<?php print render($page['content']); ?>
```

---

# Example
## Loading Views via Ajax

* Views have a handy “Use Ajax” option
* View can be accessed via direct hyperlink, or loaded via Ajax

---

[image of View, with Ajax option]

---

[image of Views page]

---

# How?
## JavaScript

```javascript
function loadEssaysView() {
 $.ajax({
   url: 'http://laphams.local/views/ajax',
   type: 'post',
   data: {
     view_name: 'essay_issue_content',
     view_display_id: 'test', //your display id
   },
   dataType: 'json',
   success: function (response) {
     if (response[1] !== undefined) {
       var viewHtml = response[1].data;
       $('#essaysDestination').html(viewHtml);
       //Drupal.attachBehaviors(); //check if you need this.
     }
   }
 });
}
```
^ In this situation we target the URL /views/ajax
We pass in the view name, and the display id. Our success handler tells us where to put the response.



