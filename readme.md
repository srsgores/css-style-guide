# CSS Style Guide
### By Sean Goresht

# Contents

* Layout
* Colours
* Sizing and Proportions
* Type

## Layout

### Floats
Avoid using floats in most cases.  Floats should be sparingly used.

Good:

```
article > figure.captioned-image {
	float: right;
}
```

Good: 

```
nav.sharing-tools > a {
	display: inline-block;
}
```

Bad:

```
nav.sharing-tools > a {
	float: left;
}
```

For example, **do not** use floats for horizontally stacking items; instead use ``display: inline-block``.  Remember, using ``float`` takes that element **out of document flow**.  This often leads to many issues with content *flowing* over.  ``inline-block``, on the other hand, keeps content in document flow.

Also remember that floats have caused problems with ``clear``.  Try to avoid floats at all costs.  If your document doesn't have floats, it's not a bad thing.  Remember what floats were initially intended for.  Try not to stray from that.

## Clearfixes
Clearfixes were very common a couple years ago, and are still used on some major websites.  However, this does not mean you should use them.  In fact, **you should never have to use clearfixes** if you followed this guide on floats.

Avoid using clearfixes.  You should **not** have to use them.

Horrible:

```
.clearfix {
	clear: both;
	zoom: 1;
}
```

Even worse:

```
.container .clearfix {
	clear: both;
	zoom: 1;
}
```

Better:

```
.row {
	zoom: 1;
}
.row::before, .row::after {
	content: "";
	display: table;
}
.row::after {
	clear: both;
}
```

Good:

```
.row .item {
	display: inline-block;
}
```

## Widths

1. Widths should **always** be specified in percentages when possible
2. If a width must have a specified, *fixed* width, then specify width using ``min-width`` and ``max-width`` properties
3. If an element should take the entire width of its container, then do not assign ``width``, but either do not specify a width (defaulting to ``block``), or assign ``display: block`` to allow the element to take the entire width of its parent
4. If you must specify width, **do not** use ``px`` (except when styling media such as images or videos); instead, use ``em``, ``vw``, ``rem``, or ``%``
5. When dealing with images, **do not** style the ``<img>`` tag; instead, specify the width of the image *in-line* in your HTML, and allow the browser to optimize the image loading.

### Examples

### Basic Elements

Bad:

```

```

#### Images/Videos

Bad:

```
header[role="banner"] .logo {
	width: 15em;
}
```

Terrible:

```
header[role="banner"] .logo {
	max-width: 15em;
}
```

Sensible:

```
figure img {
	max-width: 100%;
}
```

Good:

(Do nothing)

The browser can decide the width of the media


## Selectors

Avoid overly complex or long selectors.  Usually 3 levels is enough for most selectors:

Selector types should take the following priorities (decreasing in priority):

1. Elements: ``h1``, ``p``, ``header``, ``article``
2. Classes: ``.myclass``
3. CSS3 Pseudo-selectors: ``::nth-child(even)``, ``::nth-of-type(2)``, ``::first-child``, ``::last-child``, 
``::empty``, etc.
4. Attribute selectors: ``footer[role="contentinfo"]``, ``main[role="main"]``

Selector choice should match CSS specifity when possible.

### ``id``s

You should **NEVER** have to style by ``id`` (``#``).  If you do, then create a class.  But, for some reason, if you must style one, **and only one** element **EVER**, then you may opt to use the ``#`` selector.

Bad:

```
.contact-information #email-info {
	text-decoration: underline;
}
```

Acceptable:

```
.contact-information .email-address {
	text-decoration: underline;
}
```

Better:

```
.contact-information a {
	text-decoration: underline;
}
```

Best:

```
.contact-information a[mailto] {
	text-decoration: underline;
}
```

### Special Characters

1. Do not use numbers in your selectors: ex. ``1``, ``2``, ``.myClass35``, ``.span1``, etc.; not only is prefixing selectors with numbers *illegal* in CSS, but it is also **unsemantic**.  What does ``.span1`` mean?  How am I supposed to use it?  What does it do?
2. Avoid using underscores ``_`` unless using BEM styling.  If doing BEM, then use SASS, and use the ``@at_root`` to generate bulk-prefixed selectors
3. Do not capitalize your selectors.  Do not use camelCase capitalization.
4. Use a dash to separate your class names, where spaces would normally exist.  Do not use double dashes (``--``) unless a selector convention such as BEM mandates it.

## Descendant Selector ``>``

Use the descendant selector wherever possible, and wherever markup is not subject to change.  The descendant selector outperforms all class-based selectors in performance.

Bad:

```
header > span.callout {
	font-size: 1.5em;
}
```

Better:

```
header .callout {
	font-size: 1.5em;
}
```

Best:

```
.callout {
	font-size: 1.5em;
}
```