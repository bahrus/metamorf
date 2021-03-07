# meta-morf [TODO]

Import maps for HTML.

```html
<meta-morf from=cater-pillar to=butter-fly></meta-morf>
```

## Why?

### Use Case I:

"Bootstrap" with simple, low cost element, shapeshift into more elaborate component after finished downloading.

#### Use Case IA:

Web components have built-in support for [progressive enhancement](https://www.google.com/search?q=progressive+enhancement+with+web+components&rlz=1C1CHBF_enUS875US875&oq=progressive+enhancement+with+web+components&aqs=chrome..69i57j0i22i30j0i390.7785j0j7&sourceid=chrome&ie=UTF-8), soon to be taken to the next level with declarative ShadowDOM.  The light children / declarative ShadowDOM form the original content, which then morphs into the web component after the web component dependencies are downloaded.  But what if we added one more progressive enhancement step into the mix? 

Create a base web component that uses the native DOM (form) elements / CSS effects (almost) exclusively as far as UI, with the help of light children.  Maybe the functionality is limited to the most crucial aspects, with no bells and whistles.  So this web component should be quite small.

Then, as much as time permits, create a vast catalog of (extending?) additional web components with (nearly) identical public property / method / light children signatures, each extending the base web component, but making use of a particular design library web component library -- UI5, Material, Fast, HAX, Vaadin, Lion, Shoelace.Style, Ionic, Patternfly, etc.  On top of which full, robust functionality is added.

**NB:** Fast components take an [intriguing alternative approach to this](https://www.fast.design/docs/design-systems/overview#the-designsystemprovider). 

Now suppose we want the web server to not have to cater to the whims of the moment.   The HTML the server generates (with the exception of the declarative ShadowDOM, perhaps) can act as a "permanent" design-library-neutral HTML stream, only consisting of "business" related tags. This will load quite quickly due to the expected small footprint of the base web component:

```html
<acme-filter-selections>
  <acme-list>
    <template shadowroot="open">
      Original Shadow Content
    </template>
    <div slot=title>Products</div>
    <acme-product-selection>Hen Grenade</acme-product-selection>
    <acme-product-selection>Rocket-Powered Roller Skates</acme-product-selection>
    <acme-product-selection>Triple-Strength Fortified Leg Muscle Vitamins</acme-product-selection>
  </acme-list>
</acme-filter-selections>
```

Now suppose the front-end team is much more interested in having the web page don the latest look, adopting the same "design language" other groups are using.  Rather than burdening the web api team with this, the front-end team can "centralize the pain" and create a mapping rule:

```html
<meta-morf from=acme-list to=acme-ui5-list></meta-morf>
<meta-morf from=acme-product-selection to=acme-ui5-li></meta-morf>
```

meta-morf waits for the "to" value web component to be registered before acting.

meta-morf makes one "hopeful" assumption about the components it upgrades to -- that the web component has a method called "morfMerge" that accepts the original component as an argument (e.g. acme-production-selection), and which extracts the information it needs so as not to lose the "state" of the component.  If no such method is present, then the transform still takes place, but state could be lost.

**Disadvantages:**

1.  Added complexity, abstraction layer
2.  Additional CPU / bandwidth from downloading / loading two libraries, especially if the two libraries duplicate functionality

**Advantages:**

1.  Markup can be more "pure" to the business meaning.
2.  Potentially faster metrics (Time for Interactivity, etc).

So maybe use a bit judiciously?

#### Use Case IB:

Kind of same idea, but taken a little further:

Suppose you need to display a large amount of tabular data with Excel like functionality?  

In the past, this required a heavy duty component like Revo grid, Vaadin grid, or Ag-grid, so nothing could display until the component is downloaded.

Of course, it's quite natural to say "why not show a simple table of (some of the) data" while that's happening?

Likewise with charts -- use a [pure CSS](https://lea.verou.me/tag/pie-charts/) solution, then morph into a small but robust library like frappe charts, finally landing on eCharts or something equivalently powerful. 

So that's Use Case IB

### Use Case II:  

Weak, probably not a valid use case -- Pure xml web api

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

Could we just start with:

```html
<ul>
  <li>Item 0</mwc-list-item>
  <li>Item 1</mwc-list-item>
  <li>Item 2</li>
  <li>Item 3</li>
</ul>
```

... and switch to mwc when the dependency is loaded?  

Of course we *could*, but does it really make sense to do this?

This issue is not specific to web components -- the focus here is on a cultural bias that has developed over time in many circles, where the established culture is that the server only provides api's, no SSR or any kind of HTML views.  Everything in the browser, then,  is derived from JSON data + JavaScript.  This cultural shift occurred especially hard in corporate intranets, where bandwidth is generally aplenty, and providing mobile-friendly experiences with slow network connections is regarded as an off-the-beaten-track edge case.  

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

Perhaps the "correct" view towards this paradigm shift is to say "It's a new dawn, it's a new day, embrace PHP already" (joke), and realign development accordingly.  Part of what makes (me) less enthusiastic about investing too much in server-side technologies, is that, like front-end frameworks, it is another (vendor-based) tie-in.  Admittedly, having a vendor out there "watching your back" is a comfort for many, and I suppose is for me as well.  

XSLT is an appealing solution, as it provides a nice "mapping" mechanism that works both on the server and (more or less) in the browser.  This would require a medium-level paradigm shift where api's provide options where the output can be JSON or XML, and consumers choose whichever is more convenient.

Another option is to make the API continue to work exclusively with JSON output.  Use a language that is recognized (with minimal modification) in the browser, as well as a (JS-based) server  -- tagged template literals, for example, or JSX/[E4X](https://en.wikipedia.org/wiki/ECMAScript_for_XML), especially if the latter were standardized.

Popular as these are, they have some limitations, in my mind.  They are inspired from server-side rendering engines, where the emphasis is on generating the markup from a "state" or "model" object.  These make total sense on the server, where each new request means a new document must be created.  But using the syntax both on the server, and possibly on the client during initial rendering, and on the client during updates, means that the syntax ends up cramming multiple aspects of rendering into one crowded syntax, and then requires some sort of mental-model training to understand which aspects are relevant during each of these events.  For example, adding event handlers doesn't make sense on the server, nor during updates, so the developer needs to understand these quirks when it becomes necessary.  And libraries then bear the burden of "decompiling" the compact notation into optimized instruction sets, adding to the complexity and (potentially) run-time footprint.  

How to account for other differences between client and server, such as streaming the output on the server, use of attributes vs properties, etc, compatibility with all web servers leads me to believe, despite current trends, that the more that can be turned into data formats (HTML, JSON-ish, etc), the more easily constructs can be "shared" across environments (server / build / client crossed with multilingual server-side technologies). 

**NB:** React's server components looks like an intriguing approach to this issue.

</details>






