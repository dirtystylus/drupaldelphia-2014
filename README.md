drupaldelphia-2014
==================

Ajax-Include Patterns in Drupal 7

You’ll have to create your own Article nodes. I used one basic page and just pasted in a few URL patterns to test ajaxinclude.js.

## URL patterns:

<ul>
<li><a data-interaction data-after="/node/1?is_ajax=1" href="/node/1">Append</a></li>
<li><a data-interaction data-replace="/node/3?is_ajax=1" href="/node/3">Replace</a></li>
<li><a data-after="/node/1?is_ajax=1" href="/node/1">Append Automatically</a></li>
<li><a data-views href="/articles">View</a></li>
</ul>

## Order of Git Tags

* show site with no ajax includes

git checkout v1.0-startingpoint

* add ajaxinclude.js and node load script

git checkout v1.1-ajaxinclude

* add template override files and preprocess hooks

git checkout v1.2-tploverrides

* add view module
* add view behavior script

git checkout v1.3-ajaxview

## Extra Credit – Load a node automatically

Drupal.behaviors.load_article_auto = {
  attach: function(context, settings) {
    $( "a[data-after]" ).once( "nodeajaxauto", function() {
      $( this ).ajaxInclude();
      $( this ).on( "ajaxInclude", function () {
        console.log('done');
      });
    });
  }
};
