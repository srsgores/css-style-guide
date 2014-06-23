# CSS Style Guide
### By Sean Goresht

# Contents

* Layout
* Colours
* Sizing and Proportions
* Type

# Layout

## Floats
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

Terrible:

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

## Widths and Heights

1. Widths and heights should **always** be specified in relative units (``%``, ``vw``, ``vh``, ``em``, ``rem``, etc.) when possible
2. If a width or height must have a specified, *fixed* width, then specify width using ``min-width``, ``min-height``, or ``max-width`` and ``max-height`` properties
3. If an element should take the entire width of its container, then do not assign ``width``, but either do not specify a width (defaulting to ``block``), or assign ``display: block`` to allow the element to take the entire width of its parent
4. If you must specify width, **do not** use ``px`` (except when styling media such as images or videos); instead, use ``em``, ``vw``, ``rem``, or ``%``
5. When dealing with images and media, **do not** style the ``width`` of the ``<img>`` tag; instead, specify the ``width`` of the image *in-line* in your HTML, and allow the browser to optimize the image loading.

Overall, you should **never** have to specify the ``width`` or the ``height`` of an element.

## Absolutely-Positioned and Fixed Elements

* Avoid using ``position: absolute`` at all costs.  Do not use this unless you must have an element **overlaying** other elements, proportional to screen size.  Follow the mantra: "*Absolute when absolutely necessary*"
* Do not specify ``z-index`` unless absolutely necessary.  Remember that ``z-index will default to higher values, according to the DOM hierarchy
* Do not use ``z-index`` values over ``10``, as you should not have more than 10 elements overlapping at any given time.
* Try using ``translate-z``, ``scale``, and other CSS3 transforms if designing for modern browsers only instead of using absolute positioning; this will allow your app to benefit from hardware acceleration.
* If using a preprocessor, assign a variable to the ``z-index``

### Examples

#### Excessive ``z-index``

Terrible:

```
.nav[role="navigation"] {
	z-index: 1;
	position: relative;
	a {
		z-index: 2;
		position: absolute;
		&.bookmark {
			border: 1px solid $bookMarkBorderColour;
			z-index: 3;
			position: relative; // means nothing!
		}
	}
}
```

(*Selector ``[role="navigation"]`` is over-specific and un-supported in some older browsers.  Overuse of ``z-index`` property adds unnecessary code.  ``z-index`` on ``a.bookmark`` does nothing.*)

Bad:

```
.nav {
	z-index: 1;
	position: relative;
	a {
		z-index: 2;
		position: absolute;
		&.bookmark {
			border: 1px solid $bookMarkBorderColour;
		}
	}
}
```

(*Improved selector, but unnecessary ``z-index`` rule on ``.nav``*)

Good:

```
.nav {
	position: relative;
	a {
		z-index: 2;
		position: absolute;
		&.bookmark {
			border: 1px solid $bookMarkBorderColour;
		}
	}
}
```

(*``z-index`` only specified when needed.*)

## Media Queries

1. Try to avoid media queries when prototyping.  Aim to create an interface that works on mobile and desktop at the same time.  This does not mean you must use strategies such as **mobile-first**, but use low-fidelity prototyping to decide on a basic layout used for mobile and desktop.
2. If you decide to use media queries, do not implement more than 2, unless your design requirements mandate it.  
Adding media queries immensely complicates the maintenance of code.
3. Avoid overly verbose or device-specific media queries.
4. Use ``em``, [**ratios**](http://drafts.csswg.org/mediaqueries3/#values), [**resolution**[(http://drafts.csswg.org/mediaqueries3/#resolution), ``dpcm``, or ``dpi`` as units.  **Do not** use ``px``
5. Do not resize or change media proportions.  If you want to load different media like images, 
use the ``<picture>`` element [with a polyfill](https://github.com/scottjehl/picturefill).

### Examples

#### Overly Verbose Media Queries

Bad:

```
@media only screen and (min-device-width: 48em) and (max-device-width: 64em) and (orientation : portrait) {
	body {
		padding: 1em;
	}
}
```

(*Lengthy, confusing, and overly complicated conditions to match media query.  What devices does this target?  Why is it so specific?  Sizes are specified in ``em``, but this could cause a problem if ``font-size`` changes.*)

Terrible:

```
@media only screen and (min-device-width: 768px) and (max-device-width: 1024px) and (orientation : portrait) {
	body {
		padding: 15px;
	}
}
```

(*Padding, **and** the media query itself are set in fixed, unpredictable ``px`` width, while retina devices may report 
different proportions in ``dpcm``.  Media query may never trigger.*)

Better:

```
@media only screen and and (orientation : portrait) {
	body {
		padding: 1rem;
	}
}
```

(*Media query is basic enough, and we understand where it applies (portrait).  Padding is specified in ``rem`` units, which **will** be supported on devices which also support portrait mode, so this scales properly.*)

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


# Selectors

Avoid overly complex or long selectors.  Usually 3 levels is enough for most selectors (does not apply to preprocessors):

Selector types should take the following priorities (decreasing in priority):

1. Classes (class rules): ``.myclass``
2. Elements (tag rules): ``h1``, ``p``, ``header``, ``article``
3. Attribute selectors (universal rules): ``footer[role="contentinfo"]``, ``main[role="main"]``
4. IDs (id rules): ``#blog-post``, ``#main-article``)
4. CSS3 Pseudo-selectors: ``::nth-child(even)``, ``::nth-of-type(2)``, ``::first-child``, ``::last-child``, 
``::empty``, etc.


Selector choice should match CSS specificity when possible.

## Examples

### Overly Complex Selectors

Terrible:

```
* {
	box-sizing: border-box; // Don't do this!! :(
}
```

Bad:

```
a {
	&[href="http://mysite.ca"] {
		box-sizing: border-box;
	}
}
```

Better:

```
a {
	&.home-link {
		box-sizing: border-box;
	}
}
```

Good:

```
.home-link {
	box-sizing: border-box;
}
```

### Overly-Nested Rules

Terrible:

```
.row {
	.column1 {
		.column6 {
			width: 100%;
			a {
				span {
					sup {
						text-overflow: ellipsis;
						vertical-align: middle;
						white-space: nowrap;
						max-width: 4rem;
					}
				}
			}
		}
	}
}
```

(*Poor class selector, with ``.row`` and ``.column`` selectors.  Overly verbose selectors with ``a``, ``span``, and ``sup``.  Markup can be dramatically simplified.  Poor selectors (probably a grid system), using integers appended to column names.*)

Bad:

```
.column6 {
	width: 100%;
	a {
		span {
			sup {
				text-overflow: ellipsis;
				vertical-align: middle;
				white-space: nowrap;
				max-width: 4rem;
			}
		}
	}
}
```

(*Still overly verbose selectors with ``a``, ``span``, and ``sup``, but could be improved in favour of class selectors.*)

Better:

```
.prices {
	@extend %#{$rowNotation};
	.pricing-column {
		@extend %one-half;
		.pricing-label {
			text-overflow: ellipsis;
			vertical-align: middle;
			white-space: nowrap;
			max-width: 4rem;
		}
	}
}
```

Good:

```
.prices {
	@extend %#{$rowNotation};
	.pricing-column {
		@extend %one-half;
	}
	.pricing-label {
		text-overflow: ellipsis;
		vertical-align: middle;
		white-space: nowrap;
		max-width: 4rem;
	}
}
```

(*``.pricing-label`` can be reused, and does not have to be within ``.pricing-column``*)

## ``id``s

You should **NEVER** have to style by ``id`` (``#``).  If you do, then create a class.  But, for some reason, if you must style one, **and only one** element **EVER**, then you may opt to use the ``#`` selector.

> **Note**: Although id selectors are the fastest kind of selectors, styling with ``id``s means less re-usable code. For this reason, using ``id``s is discouraged.

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

## Special Characters

1. Do not use numbers in your selectors: ex. ``1``, ``2``, ``.myClass35``, ``.span1`` (bootstrap), etc.; not only is prefixing selectors with numbers *illegal* in CSS, but it is also **unsemantic**.  What does ``.span1`` mean?  How am I supposed to use it?  What does it do?
2. Avoid using underscores ``_`` unless using BEM styling.  If doing BEM, then use SASS, and use the ``@at_root`` to generate bulk-prefixed selectors
3. Do not capitalize your selectors.  Do not use camelCase capitalization.
4. Use a dash to separate your class names, where spaces would normally exist.  Do not use double dashes (``--``) unless a selector convention such as BEM mandates it.

## Descendant (Child) Selector ``>``

**Avoid using the descendant selector wherever possible**.  The descendant selector is one of the slowest selectors in performance.

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

## ``*=`` (Universal) selectors

Avoid using ``*=`` selectors at all costs.  ``*=`` selectors are extremely CPU-heavy, and will result in slow page loads and overall performance.

Likewise, avoid styling by attributes.

Terrible:

```
* {
	[aria-hidden] {
		display: none;
	}
}
```

Bad:

```
nav {
	&[aria-hidden] {
		display: none;
	}
}
```

Good:

```
.hidden {
	display: none;
}
```

# Type

1. Avoid styling type, and instead, style elements such as ``p`` paragraph elements or block text like ``articles``.
2. **Always use unitless line-height**.  When possible, avoid styling line-height, or simply set a global line-height
3. Avoid setting widths to type.  Avoid using ``text-overflow: ellipsis``.  Not only is the property [not well-supported](http://caniuse.com/#feat=text-overflow) in older browsers, but if you are cutting off text, you need to re-think your solution, as you must set a width to your text in order for it to work.
4. If text must not flow over, do not set a fixed width; instead, set ``white-space: nowrap``

## Examples

### Unitless Height

Bad:

```
p {
	line-height: 1.3em;
}
```

Terrible:

```
p {
	line-height: 18px;
}
```

Good:

```
p {
	line-height: 1.4;
}
```

Best:

*Don't set ``line-height`` at all, letting it inherit from parent (usually ``body``)*

### Width on Type

Bad:

```
p {
	max-width: 50em;
}
```

(Text will scale with ``font-size``, but we should really be styling the containing element)

Terrible:

```
p {
	max-width: 500px
}
```

(Text is always a fixed size, regardless of whether the ``font-size`` increases)

Better:

```
.container {
	max-width: 75em;
	margin: 5% auto;
}
```

### Overflowing Text

Bad:

```
header[role="banner"] > .logo-text {
	max-width: 3.8em;
	overflow-x: hidden;
	text-overflow: ellipsis;
}
```

(At least text overflows, but it is constrained to a fixed width, not flexible on smaller-screen devices)

Terrible:

```
header[role="banner"] > .logo-text {
	width: 150px;
	overflow-x: hidden;
	text-overflow: ellipsis;
}
```

(*``px`` limit the flexibility, and force the element to **always** have a certain width.  This fails on mobile devices*)

Good:

```
header[role="banner"] > .logo-text {
	white-space: nowrap;
	max-width: 50%;
	overflow-x: hidden;
	text-overflow: ellipsis;
}
```

(*Element is not forced into a fixed width, but can flexibly adapt to content and layout*)

Best:

(*Do not hide text from user at all, and reconsider usability*)

# Comments

* **Avoid comments altogether when possible**.  Seek to provide comments where intentions may be unclear to a reader.  CSS and preprocessor languages are declarative enough, such that any reader should be able to see what you are doing without the need of comments.  Where information is missing, [a supplied style guide](https://github.com/bjankord/Style-Guide-Boilerplate), coupled with documentation within should provide any missing information.
* **Avoid multi-line comments**.  Turn on word-wrap in your editor to see comments.  Multi-line comments often confuse the reader, sometimes confusing them on where a sentence begins or ends.
* If your comment is more than one sentence, then **use a block comment**.  If you are using a preprocessor, use single-line comments in-line, as they are intended, and not for blocks of text.
* **Do not number your comments**, or try to break your styles down into steps.  If you have to do this, you are either nesting too much, or your intentions are not clear enough.  You may need to have another look at your semantics and class names.
* When referring to another class name or some type of code, then **use the back-tick** *`* to indicate this.
* Do not try to list your comment with dashes (``-``), or other listing methods.  Be clear and concise.  You should not need to break your comment down like that.
* **Do not create elaborate ASCII art**, extraneous dashing, or hyphenation to indicate a heading block; instead, 
use spacing in a multi-line comment.  Remember, a comment is a comment, regardless of whether it is decorated with back-ticks and asterisks.
* If you decide to use a tool like [cssDoc](https://github.com/Paratron/CSSdoc), make sure to follow *their* coding conventions.

## Examples

### Proper Block Comments

Terrible:

```
/* =============================================================================
Grid Container
========================================================================== */
.container { 
	overflow: hidden;     /* Clearfix! */ 
	zoom: 1;              /* Triggers "hasLayout" in IE */ 
	display: block;       /* Element must be a block to wrap around contents. Unnecessary if only 
	                      /* using on elements that are block-level by default. */ 
}
```

(*Comments are continued on next line, and it is unclear where a comment begins and ends.  Block comments are used where inline comments should really be used.  Comments are aligned, confusing the reader as to whether new lines are part of the last comment, or whether they are associated with the neighbouring rule.  Decorative ASCII art is used, distracting the reader from the overall message of the code.*)

Bad:

```
// Grid Container
.container { 
	overflow: hidden; // Clearfix!
	zoom: 1; // Triggers "hasLayout" in IE
	display: block; // Element must be a block to wrap around contents. Unnecessary if only using on elements that are block-level by default.
}
```

(*Some unnecessary commenting; it is clear that ``display: block`` is required in older browsers.  ``// grid container`` inline comment is not used correctly, but it is clear to which rule each comment applies.*)

Good:

```
/* Grid Container */
.container { 
	overflow: hidden;
	zoom: 1; // Triggers "hasLayout" in IE
	display: block;
}
```

### Overly Verbose Block Comments

[Terrible](http://www.sitepoint.com/css-sass-styleguide/#highlighter_342934):

```
 /**
 * Purpose of the selector or the rule set
 * 1. Hardware acceleration hack (http://davidwalsh.name/translate3d)
 * 2. Fallback for unsupported rgba
 */

.selector {
	transform: translate3d(0, 0, 0); /* 1 */
	background: black; /* 2 */
	background: rgba(0, 0, 0, .5);
}
```

(Markup uses a combination of tabs and spaces (especially in header comment).  Numbers are used to correspond with 
rules, and the reader is constantly trying to correspond comments with numbers (very confusing!).  Worst of all, 
block comments are used where inline comments should be used, where block comments are used, 
there are 3 where there should be one.)

Bad:

```
/*
	Purpose of the selector or the rule set
	Hardware acceleration hack (http://davidwalsh.name/translate3d) and fallback for unsupported rgba
*/

.selector {
	@include transform(translate3d(0, 0, 0));
	background: black;
	background: rgba(0, 0, 0, .5);
}
```

(*Comments are confusing and un-associated with rules.  Block comments are used correctly, but the comment "Purpose of the selector or the rule set" is ambiguous and can be omitted.*)

# Preprocessors

The following rules apply to **all** preprocessors, including those that may be built on top of customization frameworks such as rework:

## Variables

Variable names should follow a consistent naming convention, and not stray from that convention.  If you decide to use camelCase conventions, then follow those conventions throughout your styles.  Similar to writing a book, you wouldn't use the oxford comma occasionally, and then sometimes not use it.

* Avoid over-specific variable naming: ex. ``.joe-banner``, ``.my-company-name-banner``, etc.
* Do not use underscores (``_``), unless your naming convention (ex. BEM) mandates it
* Declare all variables in one block of the file or module in which they apply.  Clearly label the given section of variables with a comment.
* If building a large-scale application, put all variables together in a dedicated file


## ``@extends``, ``&:extends()``, and ``extends``

* Strive to use ``extends`` wherever possible
* When possible, use placeholders.  (Placeholders not yet available in LESS)
* Put all helpers in a dedicated file, choosing semantic names for each helper
* When extending multiple helpers or selectors, do so in list format
* Put all ``extends`` together, in the first line of the rules for a given selector
* When in media queries, create a placeholder class to contain common styles, and extend this style on all elements that should adapt this way in a media query
* Prefer extending placeholders over extending classes or mixins
* Avoid extending large selectors such as ``*``, or any large number of elements

## Mixins

* Avoid overly long or complicated mixin names
* Use a mixin library when one is available.  If using LESS, roll your own.
* Do not use keywords in mixins (ex. ``var-background-colour--input``)
* Prepare mixins for general use cases, and not specific ones.  Use (and assume) all mixins are loaded globally.
* When writing mixins which generate prefixes, prepare a way to change or modify browser support (ex. AutoPrefixer, compass, etc.)
* Do not import or use external files in mixins

## Media Queries

* Declare media queries in-line, nested inside other nested blocks when possible
* Assign variables to your breakpoints; otherwise, you may opt to use a framework (if one is available)
* Declare as few media queries as possible
* Do not put all media queries in a separate file; instead, allow media queries to trigger based on which components are loaded.  This can speed up the maintenance and development process, when adding/removing dependencies during debugging.

### Examples

#### Optimizing Query Blocks

Bad:

```
.toolbar {
	@media screen and (max-width: $breakpointMobile) {
		box-sizing: border-box;
		border: $toolbarMobileBorder;
	}
	list-style-type: none;
	margin: $toolbarMargin 0;
	padding: $toolbarPadding;
	a {
		display: inline-block;
		padding: $toolbarButtonPadding;
		white-space: nowrap;
		margin-right: -($toolbarButtonMargin);
		@media screen and (max-width: $breakpointMobile) {
			display: block;
			margin: -($toolbarButtonMargin) 0 0 0;
		}
	}
}
```

(*Two media queries are declared, when both can be nested together at the parent level*)

Good:

```
.toolbar {
	@media screen and (max-width: $breakpointMobile) {
		box-sizing: border-box;
		border: $toolbarMobileBorder;
		a {
			display: block;
			margin: -($toolbarButtonMargin) 0 0 0;
		}
	}
	list-style-type: none;
	margin: $toolbarMargin 0;
	padding: $toolbarPadding;
	a {
		display: inline-block;
		padding: $toolbarButtonPadding;
		white-space: nowrap;
		margin-right: -($toolbarButtonMargin);
	}
}
```

(*Media queries specified at component's root level.  Only one media query ever needs to be specified in the component.*)

## Colours

* When possible, use the preprocessor's colour functions to calculate **relative** colours to variables.
* Use relative colour functions in favour or fixed (ex. ``adjust_hue`` in favour of ``darken``)
* Limit any component's colour variables to the smallest amount possible, deriving variations from base colours (usually 3 is enough per component).
* When possible, and the preprocessor offers the functionality, use colour arithmetic over their literal functions (ex. ``+``, ``-``, ``/`` over ``darken``, ``lighten``)
* **Do not declare colours in ``rgb`` or ``hsl``, or with any transparency**; some preprocessors will throw errors when you try to perform mixins on transparency-enabled colour values.  Furthermore, results may be unexpected when performing ``fade-in`` or ``fade-out`` on already-transparent colours.
* Always declare colour variables in 6-character hexadecimal.  Do not use the short-hand 3-character format.  Do not use named colours (ex. ``gray``, ``deepseablue``, ``green``, ``hotpink``, etc.)
* If you absolutely must use ``rgb``, ``rgba``, or ``hsl`` colour values, **do not omit preceeding ``0``s**.

### Examples

#### Colour Functions

Bad:

```
button {
	&:active {
		border-color: desaturate(darken($borderColour, 25), 45);
		/* ... */
	}
}
```

Better:

```
button {
	&:active {
		border-color: desaturate($borderColour - 25, 45);
		/* ... */
	}
}
```

Good:

```
button {
	&:active {
		border-color: desaturate(adjust_hue($borderColour, 130), 45);
		/* ... */
	}
}
```

> Note: users may use [sassme.arc90](http://sassme.arc90.com/) to determine appropriate functions to generate colours.
> Note: LESS users should see functions [spin](http://lesscss.org/functions/#color-operations-spin), [mix](http://lesscss.org/functions/#color-operations-mix), 
and [contrast](http://lesscss.org/functions/#color-operations-contrast) for using proper colour functions.

#### Decimal Values

Terrible:

```
.selector {
	background: black;
	background: rgba(0, 0, 0, .5);
}
```

Better:

```
.selector {
	background: black;
	background: rgba(0, 0, 0, 0.5);
}
```

Good:

```
.selector {
	background: #000000;
	background: rgba(0, 0, 0, 0.5);
}
```

## Coding Standards

* Prefer double quotes (``"``) over single quotes ``'`` at all times
* Use the tab character for tabs.  **Do not use spaces**.
* Do not align properties and selectors.  This only makes reading code harder.
* Do not insert spaces before the after the property declaration
* Indent only once for each nested level

### Examples

#### Aligned Rules

Terrible:

```
.blog-post {
	display	:	block;
	border	:	1px solid #CCC;
	margin	:	0 auto;
}
```

Bad:

```
.blog-post {
	display:	block;
	border:		1px solid #CCC;
	margin:		0 auto;
}
```

Good:

```
.blog-post {
	display: block;
	border: 1px solid #CCC;
	margin: 0 auto;
}
```

## Nesting

* Always nest selectors when possible.  Nesting allows styles to appear as "components", rather than as separate entities.  It also helps in IDEs like PHPStorm, which can expand contract nested blocks.

### Examples

#### Optimally Nesting Selectors

Terrible:

```
.tags {
	padding: $tagContainerPadding ($tagContainerPadding / 2);
	background: $tagContainerBackground;
}

ul.tags {
	list-style: none;
	margin: 0;
}

ul.tags a {
	border-radius: $tagBorderRadius;
	background: $tagBackground;
	border: $tabContainerBorderWidth $tabContainerBorderStyle $tabContainerBorderColour;
}

.tags a {
	display: inline-block;
}
```

(*Markup is confusing and repetitive.  Selectors ``ul.tags`` and ``.tags`` are really the same and should be grouped.*)

Bad:

```
.tags {
	padding: $tagContainerPadding ($tagContainerPadding / 2);
	background: $tagContainerBackground;
	list-style: none;
	margin: 0;
}

.tags a {
	display: inline-block;
	border-radius: $tagBorderRadius;
	background: $tagBackground;
	border: $tabContainerBorderWidth $tabContainerBorderStyle $tabContainerBorderColour;
}
```

(*``.tags`` can be nested to reduce ``.tags`` selector*)

Good:

```
.tags {
	padding: $tagContainerPadding ($tagContainerPadding / 2);
	background: $tagContainerBackground;
	list-style: none;
	margin: 0;
	a {
		display: inline-block;
		border-radius: $tagBorderRadius;
		background: $tagBackground;
		border: $tabContainerBorderWidth $tabContainerBorderStyle $tabContainerBorderColour;
	}
}
```

(*``.tags`` nested selector clearly indicates both HTML markup structure as well as reduces repeated code*``)

> **hint**: try [leafo's lessify tool](http://leafo.net/lessphp/lessify/) to convert un-nested, normal CSS, and convert it to nested LESS

# Imports

* If not using a preprocessor, **avoid using ``@import`` statements**; use ``<link rel = "stylesheet" href = "...">``instead
* Place all ``@import`` statements at the beginning file.  Do not place ``@import`` statements at the end of the file, even if it is media queries, or some file of less merit
* Clearly label all imports with a comment
* If using LESS, remember to [differentiate between ``@import (reference)``, ``@import (less)``, ``@import (multiple)``, and ``@import (inline)``](http://lesscss.org/features/#import-directives-feature).  Using ``@import (reference)`` can have comprable benefits to using ``&:extends()``
* When using SASS, store all imports in a folder (ex. ``partials``), where all dependencies are stored.

# Directory Structure

* If using a preprocessor, store styles in a ``styles`` folder; you may also opt to store styles in a language-specific named folder like ``sass`` if you are using sass.
* Store all styles as close to the root level as possible.  Some IDEs and build tools for preprocessors lack support for complicated style file structures.
* Compile all styles to a ``dist`` folder when a build is required or a preprocessor is used
* Store preprocessor configuration files (``config.rb``) in the parent directory to which you have created the ``styles`` directory

# Sources

* [(MDN) - Writing Efficient CSS](https://developer.mozilla.org/en-US/docs/Web/Guide/CSS/Writing_efficient_CSS)
* [CSSTricks) - Efficiently Rendering CSS](http://css-tricks.com/efficiently-rendering-css/)
* [W3C Media Queries 3 Working Draft Specification](http://drafts.csswg.org/mediaqueries3)
* [LESS CSS Function Reference](http://lesscss.org/functions/)
* [Hugo Giraudel's CSS and SASS Styleguide](http://www.sitepoint.com/css-sass-styleguide/)
* [Architecture for SASS Projects by Hugo Giraudel (SitePoint)](http://www.sitepoint.com/architecture-sass-project/)
* [SASS Structure by Hiro Giraudel](http://hugogiraudel.com/2013/02/11/sass-structure/)
* [CSSDoc Reference](https://github.com/Paratron/CSSdoc)
* [CSS Architectures: New Best Practices](http://www.sitepoint.com/css-architectures-new-best-practices/)
* [Writing Maintainable CSS Style Guides by Ian Feather](http://ianfeather.co.uk/a-maintainable-style-guide/)
* [Typesetting Your CSS Objects by Spiros Martzoukos](http://www.edenspiekermann.com/blog/typesetting-your-css-objects)
* [A List Apart - Creating Style Guides by Susan Robertson](http://alistapart.com/article/creating-style-guides)
