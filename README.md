# metamorf

Import maps for HTML.

```html
<meta-morf from=cater-pillar to=butter-fly when-defined copy-props='["prop1", "prop2"]' copy-attrs='["attr1", "attr2"]'></meta-morf>
```

## Why

Use case 1

SSR

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



Perhaps the "correct" view towards this paradigm shift is to say "It's a new dawn, it's a new day, embrace PhP already" (joke), and realign development accordingly.  Part of what makes 

So alternative:

Server renders:

```html
<ul>
    <li>Item 0</li>
    <li>Item 1</li>
    <li>Item 2</li>
    <li>Item 2</li>
</ul>
```

Just as "index.html" can have an import map json, it could contain an html to web component map:

```html
<meta-morf from='["ul", "li"]' to='["mwc-list", "mwc-list-item"]' when-defined></meta-morf>
```

## Is this ideal?

The biggest downside I see to this approach is the cost of transforming the DOM in such a radical way.

This approach would certainly not be ideal for post-ssr html queries, after the mwc (for example) library has loaded.

So maybe this isn't right.

If the "requirement" that the backend not be tightly coupled to a design library, perhaps a better performing way of accommodating this would be as follows:

1,  The server generates:

```html
<ul>
    <li>Item 0</li>
    <li>Item 1</li>
    <li>Item 2</li>
    <li>Item 2</li>
</ul>
```

A web component can take the list as its input via slotting:

```html
<standard-to-mwclist>
    <ul slot=standardList>
        <li>Item 0</li>
        <li>Item 1</li>
        <li>Item 2</li>
        <li>Item 2</li>
    </ul>
</standard-to-mwclist>
```

standard-to-mwclist has a slot inside the shadow dom, which is hidden.  A slot change event will fire, and standard-to-mwclist can treat the ul/li markup as data and transform it into mwc-list within its shadowdom.

So then in this scenario where metamorf might help is create a "HTML import mapping" that says:  "When I encounter a ul/li list, add attribute 'slot=standardList' and wrap it inside a standard-to-mwclist tag."



What about extra properties?

mwc-list has attributes not applicable to ul, but still semantically meaningful:  multi, for example


Server renders:

```html
<ul data-multi>
    <li>Item 0</li>
    <li>Item 1</li>
    <li>Item 2</li>
    <li>Item 2</li>
</ul>
```

```html
<meta-morf from='["ul", "li"]' to='["mwc-list", "mwc-list-item"]' when-defined merge-dataset></meta-morf>
```

Maybe we don't want to turn *all* ul/li's into mwc-list/mwc-list-item. 

```html
<meta-morf from='["ul", "li"]' to='["mwc-list", "mwc-list-item"]' when-defined merge-dataset>
    <script >
        document.currentScript.parentElement.where = el => el.matches('[some css criteria]')
    </script>
</meta-morf>
```

Use case 2

"bootstrap" with simple, low cost element, shapeshift into more elaborate component after finished downloading.
