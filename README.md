# metamorf

Import maps for HTML.

```html
<meta-morf from=cater-pillar to=butter-fly when-defined copy-props='["prop1", "prop2"]' copy-attrs='["attr1", "attr2"]'></meta-morf>
```

## Why

Weak, probably not a valid use case -- SSR

<details>
<summary>A perpetual critic's view of the current UI Syntax landscape</summary>


Consider this markup:

```html
<mwc-list>
  <mwc-list-item>Item 0</mwc-list-item>
  <mwc-list-item>Item 1</mwc-list-item>
  <mwc-list-item>Item 2</mwc-list-item>
  <mwc-list-item>Item 3</mwc-list-item>
</mwc-list>
```

Web servers have been moving away from tightly coupling their output to the UI -- first with AJAX (where X stands for XML), then with JSON.

Making the web server tightly coupled to a specific design library may be a hard sell in some cases.  Plus how to elegantly show the initial list without fancy, throwaway css tied to showing the markup above as a list?

This issue is not specific to web components -- the focus here is on a cultural bias that has developed over time in many circles, where the established culture is that the server only provides api's, no SSR or any kind of HTML views.  Everything in the browser, then,  is derived from JSON data + JavaScript.  This cultural shift occurred especially hard in corporate intranets, where bandwidth is generally aplenty, and providing mobile-friendly experiences with slow network connections is regarded as off-the-beaten-track edge case.  

In such an environment, switching to an HTML api would raise similar concerns generating this:

```html
<mwc-list>
  <mwc-list-item>Item 0</mwc-list-item>
  <mwc-list-item>Item 1</mwc-list-item>
  <mwc-list-item>Item 2</mwc-list-item>
  <mwc-list-item>Item 3</mwc-list-item>
</mwc-list>
```

as generating this:

```html
<ul class="list-group">
  <li class="list-group-item">Cras justo odio</li>
  <li class="list-group-item">Dapibus ac facilisis in</li>
  <li class="list-group-item">Morbi leo risus</li>
  <li class="list-group-item">Porta ac consectetur ac</li>
  <li class="list-group-item">Vestibulum at eros</li>
</ul>
```

Perhaps the "correct" view towards this paradigm shift is to say "It's a new dawn, it's a new day, embrace PhP already" (joke), and realign development accordingly.  Part of what makes (me) less enthusiastic about investing too much in server-side technologies, is that, like front-end frameworks, it is another (vendor-based) tie-in.  Admittedly, having a vendor out there "watching your back" is a comfort for many, and I suppose is for me as well.  

XSLT is an appealing solution, as it provides a nice "mapping" mechanism that works both on the server and (more or less) in the browser.  This would require a medium-level paradigm shift where api's provide options where the output can be JSON or XML, and consumers choose whichever is more convenient.

Another option is to make the API continue to work exclusively with JSON output.  Use a language that is recognized (with minimal modification) in the browser, as well as a (JS-based) server  -- tagged template literals, for example, or JSX/[E4X(https://en.wikipedia.org/wiki/ECMAScript_for_XML)], especially if the latter were standardized.

Popular as these are, they have some limitations, in my mind.  They are inspired from server-side rendering engines, where the emphasis is on generating the markup from a "state" or "model" object.  These makes total sense on the server, where each new request means a new document must be created.  But using the syntax both on the server, and possibly on the client during initial rendering, and on the client during updates, means that the syntax ends up cramming multiple aspects of rendering into one crowded syntax, and then requires some sort of mental-model training to understand which aspects are relevant during each of these events.  For example, adding event handlers doesn't make sense on the server, nor during updates, so the developer needs to understand these quirks when it becomes necessary.  And libraries then bear the burden of "decompiling" the compact notation into optimized instruction sets, adding to the complexity and (potentially) run-time footprint.  

How to account for other differences between client and server, such as streaming the output on the server, use of attributes vs properties, etc, compatibility with all web servers leads me to believe, despite current trends, that the more that can be turned into data formats (HTML, JSON-ish, etc), the more easily constructs can be "shared" across environments (server / build / client crossed with multilingual server-side technologies). 


</details>

Use case 2

"bootstrap" with simple, low cost element, shapeshift into more elaborate component after finished downloading.
