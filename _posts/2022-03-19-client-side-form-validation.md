---
layout: post
title:  "Client side form validation"
date:   2022-03-19 09:40:47 +0200
categories: html form validators
---
## Motivation:
       
I have built the following form:

<img src="{{ site.baseurl }}/assets/images/form-validator.png" alt="form-image">


The `key` input if of type `text`, and I want it to allow only certain vlaues.
Specifically I want it to accept a single letter of either `a` or `b` or `c` or `d`. Any other value should not be permitted.


## Client side solution

Apparently, the `input` tag has a `pattern` attribute which allow one to specify a Regex pattern for the received input.

It can be used like so: 

```html
<input 
  id="key"
  type="text"
  formControlName="key"
  pattern="[abcd]"
  required /> 
```

Here the Regex is `[abcd]` which matches any single character inside the square brackets. Note that we do not need to enclose
it with forward slashes.

This will accept any of the following:
*	`a`
*	`b`
*	`c`
*	`d` 

but will reject any of the following:
* `e`
* `ae`
* `bb`
* `abc`


Now on the `submmit` button we have:


```html
<button 
  class="submit-button"
  type="submit"
  [disabled]="questionForm.invalid">
``` 

So the button is disabled as long as an invalid value is entered.


This way, illegal values are disabled even before the user sends any information, saving us some work in the backend.
