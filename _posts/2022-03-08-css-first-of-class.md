---
layout: post
title:  "CSS: How to select first-of-class element"
date:   2022-03-08 20:40:47 +0200
categories: jekyll css
---
### Motivation

I have implemented a progressbar that is composed from the following HTML:
```html
<body>
	<ul  class="progressbar">
		<li  class="progress completed">part 1</li>
		<li  class="progress completed">part 2</li>
		<li  class="progress active">part 3</li>
		<li  class="progress unreached">part 4</li>
		<li  class="progress unreached">part 5</li>
	</ul>
</body>
```

This progressbar maintains state using CSS classes, according to progress:
* `unreached` - for future steps
* `active` - for current step
* `completed` - for completed steps

Each state has its color to distinguish it from others.

Now, I want to select the first element that has the `.unreached` class, to add to it a `::before` pseudo-element.

Clearly, the `:first-child` `:nth-child` selectors won't work here, since the first `.unreached` element may not be the fist child of its parent, and we don't  want to keep track of child numbers etc.

Another option, is the CSS `:first-of-type` pseudo-class selector. But it works only on element types (tag names, like `p`, `div`, etc.), and not classes.

Surprisingly, there is no `:first-of-class` selector in CSS.

## The solution

The solution I found for this problem is a clever use of [CSS combinators](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Selectors).

Observe that the first `.unreached` element cannot be the first element in the list, because the first step is set to `.active`. thus, it can be anywhere between the second `li` element and the last `li` element (inclusive). 

In particular, the first `.unreached` `li` element must come immediately after another `li` element that does not have the `.unreached` class. (it actually will have the `.active` class).

Thus, we can do the following:

```css
.progress:not(.unreached) + .progress.unreached::before {
	background-color: tomato;
}
```

What we did here, is targeting  the element with the `.progress.unreached` classes, that is an [adjacent sibling](https://developer.mozilla.org/en-US/docs/Web/CSS/Adjacent_sibling_combinator) of another element that has the `.progress` class but not the `.unreached` class.  Which is exactly the first `.unreached` element .


### Stackblitz demo

See the Stackblitz demo for this project [here](https://stackblitz.com/edit/web-platform-yqkgpm?file=styles.css).

See: https://stackoverflow.com/questions/2717480/css-selector-for-first-element-with-class

