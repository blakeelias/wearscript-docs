GDK Cards
=========
.. raw:: html

  <div style="position: relative;margin-bottom: 30px;padding-bottom: 56.25%;padding-top: 30px; height: 0; overflow: hidden;">
    <iframe style="position: absolute;top: 0;left: 0;width: 100%;height: 100%;" src="http://www.youtube.com/embed/ohiSWhag-jE" frameborder="0"></iframe>
  </div>


WearScript uses an abstraction called a CardTree that allows for a hierarchy of cards where a node in the tree can optionally have either a menu or another set of cards beneath it and every card can have a tap/select callback.  The syntax is overloaded to make common functionality concise.

Examples
--------
The following displays a GDK card (consists of a body and footer)

.. code-block:: javascript

    var tree = new WS.Cards();
    tree.add('Body text', 'Footer text');
    WS.cardTree(tree);
    WS.displayCardTree();


Lets add another card

.. code-block:: javascript

    var tree = new WS.Cards();
    tree.add('Body 0', 'Footer 0');
    tree.add('Body 1', 'Footer 1');
    WS.cardTree(tree);
    WS.displayCardTree();

Select and tap callbacks are optional arguments.  If a card doesn't have any action on tap (i.e., a tap callback, subtree, or menu) it will use the "disallowed" sound.

.. code-block:: javascript

    var tree = new WS.Cards();
    tree.add('Body 0', 'Footer 0', function () {WS.say('Selected')});
    tree.add('Body 1', 'Footer 1', undefined, function () {WS.say('Tapped')});
    WS.cardTree(tree);
    WS.displayCardTree();


A menu is added with alternating title and callback arguments at the end of the parameter list.  Tap/select parameters (if present) precede them.

.. code-block:: javascript

    var tree = new WS.Cards();
    tree.add('Body 0', 'Footer 0', 'Say 0', function () {WS.say('Say 0')}, 'Say 1', function () {WS.say('Say 1')});
    tree.add('Body 1', 'Footer 1', function () {WS.say('Selected')}, 'Say 0', function () {WS.say('Say 0')}, 'Say 1', function () {WS.say('Say 1')});
    tree.add('Body 2', 'Footer 2', function () {WS.say('Selected')}, function () {WS.say('Tapped')}, 'Say 0', function () {WS.say('Say 0')}, 'Say 1', function () {WS.say('Say 1')});
    WS.cardTree(tree);
    WS.displayCardTree();

A card's tap callback can cause the menu to exit by calling WS.displayWebView().
.. code-block:: javascript

    var tree = new WS.Cards();
    tree.add('Body 0', undefined, undefined, function () {
        WS.say('selected 0');
        WS.displayWebView();
    });
    tree.add('Body 1', 'Footer 1', undefined, function() {
        WS.say('selected 1');
        WS.displayWebView();
    });
    WS.cardTree(tree);
    WS.displayCardTree();

A subtree of cards is added by creating another set of cards and placing it as the last parameter (may only have a menu or a subtree for a card).  There is no depth limit for subtrees.

.. code-block:: javascript

    var tree = new WS.Cards();
    tree.add('Body 0', 'Footer 0');
    var subtree = new WS.Cards();
    subtree.add('Sub Body 0', 'Sub Footer 0');
    subtree.add('Sub Body 1', 'Sub Footer 1');
    tree.add('Body 1', 'Footer 1', subtree);
    WS.cardTree(tree);
    WS.displayCardTree();

The GDK cards are limited in the layouts provided and the Mirror API provides more formatting options with HTML.  We've ported much of this functionality without using Mirror so that more complex layouts (e.g., lists) can be used.  Currently unsupported features include auto-paginate and auto-size.  The syntax is that you create a <script> tag with the content (same format as Mirror accepts), then pass the ID of that tag to tree.addHTML(id) which can take all of the same options previously described (e.g., callbacks, menus, subtrees).  Both card types can co-exist in the CardTree as illustrated below.  For several examples of HTML cards checkout https://api.wearscript.com/#/gist/9477514/glass.html

First put the html in a script tag

.. code-block:: html

    <script type="text/html" id="tpl_card0">
	<article>
	  <section>
	    <ul class="text-x-small">
	      <li>Gingerbread</li>
	      <li>Chocolate Chip Cookies</li>
	      <li>Tiramisu</li>
	      <li>Donuts</li>
	      <li>Sugar Plum Gummies</li>
	    </ul>
	  </section>
	  <footer>
	    <p>Grocery list</p>
	  </footer>
	</article>
    </script>

Then refer to it in javascript using WS.addHTML

.. code-block:: javascript

    var tree = new WS.Cards();
    tree.addHTML('tpl_card0');
    tree.add('Body 1', 'Footer 1');
    WS.cardTree(tree);
    WS.displayCardTree();

API
----

WS.Cards : Tree
  Empty card tree configuration, create with "new WS.Cards()".

Tree.add(String body, String footer, [Function selected, Function tapped], [String menuTitle, Function menuAction]..) : Tree
  Add a card to a tree. Can be chained for fluency

Tree.addHtml(String htmlID, [Function selected, Function tapped], [String menuTitle, Function menuAction]..) : Tree
  Add a full HTML card to the tree. Can be chained for fluency.

WS.cardTree(tree) : void
  Generates the full card tree in native memory.

WS.displayCardTree() : void
  Shows the CardScrollView

WS.displayWebView() : void
  Display the WebView activity (this is the default, reserved for future use when we may have alternate views).
