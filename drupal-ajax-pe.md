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

# Philosophy, not technique

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

^ Components

---

# Components

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

![fit](http://cl.ly/image/183f340A4343/fwa-nojs.jpg)

---

* giant expanse of nothing
* menus don’t work

---

![170%](http://cl.ly/image/1X1r3i1U160A/m-m.jpg)
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

![90%](http://cl.ly/image/1U3d0F451J20/boston-globe-newton.gif)
<br>
Photo by Grant Hutchinson: https://www.flickr.com/photos/splorp/6141775264/in/set-72157624225682388

---

##	BostonGlobe.com on 
## *Apple Newton*

---

* Accessibility
	* Older Browsers
	* Newer “Browsers”: game consoles, wearables

---

![](http://cl.ly/image/3n1a2X3l0e30/apple-watch.jpg)

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
	* mobile (cell) networks

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

[“An Ajax-Include Pattern for Modular Content”](http://filamentgroup.com/lab/ajax-includes-modular-content.html)
*by The Filament Group*

![inline](http://cl.ly/image/0g3G1n3E283V/ajax-include-github.jpg)

---

* Lazy-load non-essential content: create **data attribute patterns** for JavaScript to target based on certain rules
	* Hyperlinks to content can be enhanced
* Load can be done in a staggered fashion, or on demand (click)

---

# No Pattern
```html
<h2><a href="/path/to/content">Related Articles</a></h2>
```

---

# Data Attribute Pattern

* data-before
* data-after
* data-replace
* data-interaction

---

# Data Attribute Pattern

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

![30%](http://cl.ly/image/3B0t111Y1t1R/tucs-article.jpg)

---

![](http://cl.ly/image/3B0t111Y1t1R/tucs-article.jpg)

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

* Include *ajaxinclude.js* in theme **.info** file
 
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
  
^ ajaxinclude.js has a callback event that can be used to do things after the load is complete, like animate a section

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

# 1. Basic site

---

# 2. Site with ajaxinclude.js

^ You can see we are loading the content but the header and footer are included

---

# 3. Site with ajaxinclude.js and template overrides

^ But what about other things? How about a view?

---

# 4. Loading a view via Ajax

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

# Don’t break the URL.

---

## This is hard work, and it takes time to get it right.

---

# Thank you.

---

# Github

* Presentation: https://github.com/dirtystylus/drupaldelphia-2014
* Demo Site: https://github.com/dirtystylus/drupaldelphia-2014-demo

---

![inline](http://cl.ly/image/1E1R0v2T0o3I/bc-logo.jpg)
## Mark Llobrera
### @dirtystylus
### @bluecadet