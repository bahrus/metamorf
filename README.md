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

Making the web server tightly coupled to a specific design library may be a hard sell.  Plus how to elegantly show the initial list without fancy, throwaway css tied to showing the markup above as a list?

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
