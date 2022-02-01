
## Understanding the spaceship operator <=> in Ruby


Let's say we have a Vector class:

```ruby
class Vector
	def initialize(x, y)
		@x = x
		@y = y
	end

	def +(other)
		Vector.new(@x + other.x, @y + other.y)
	end
end


v = Vector.new(2,10)
w = Vector.new(-5, 5)
```


now we can add vectors. but what if we want to compare two vectors?

we can do that based on their magniture.


to do so, we have to 
