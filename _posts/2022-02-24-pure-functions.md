

Pure functions are functions that don't change the receiver object and don't change their arguments.

In other words, given the same receiver and the same arguments, they alawys return the same result, with no side effects.

consider the following:

```rb
3 + 2
```

what we actually have here is:
```rb
3.+(2)
```

i.e, the receiver object is 3, and the argument is 2. the `+` method does not change the receiver or the argument and always return the same result.
thus `+` can be considered a pure function (method).

A less trivial example could be:

```rb
[1, 2, 3, 4, 5].reduce(:+) 
```

here again, the receiver is not changed and the result is the same

but, had we done:

```rb
def sum_array(arr)
	total = 0
	total += arr.pop
end
```

now what happens when we call the function twice with the same receiver?

```rb
arr = [1, 2, 3, 4, 5]
puts sum_array(arr)  #15
puts sum_array(arr)  #0
```

i.e, called with the same receiver and the same argument, it returns different results, thus not a pure function.
the reason for this is because `sum_array` has a *side effect* of changing its argument - the array to sum.

## side effects
anything other than returning a value is considered a side effect:
* printing to console
* canging the receiver object
* changing the arguments
* raising an exception
* saving a record to the database
* alerting a message


 
## Ruby's bang methods are not pure
Ruby has a lot of methods that come in two versions: bang and non-bang:


all bang versions are not pure because they change their receiver.


