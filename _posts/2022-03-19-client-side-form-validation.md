## Motivation:
       
I have built the following form:

<img src="{{ site.baseurl }}/assets/images/form-validator.png" alt="sniffer example">


The `key` input if of type `text`, and I want it to allow only
certain vlaues. specifically only `a` or `b` or `c`  or `d`. Any
other value should not be permitted.


## The solution

Apparently, the `input` tag has a `pattern` attribute which allow
one to specify a Regex pattern for the received input.

I used it like so: 

```html
<input  	id="key" 	type="text" 	formControlName="key"
pattern="[abcd]" 	required /> 
```

Here the Regex is `[abcd]`. note that we do not need to enclose
it with forward slashes.

This will accept:
*	`a`
*	`b`
*	`c`
*	`d` but will reject any of the following:
* `e`
* `ae`
* `bb`
* `abc`

Now on the `submmit` button we have:

```html
<button 	class="submit-button" 	type="submit"
[disabled]="questionForm.invalid">
``` 

So the button is disabled as long as an invalid value is entered.

This way, illegal values are disabled even before the user sends
any information, saving us some work in the backend.
