- [Set Functions - w3 schools](https://www.w3schools.com/python/python_ref_set.asp)
- ``{1,2,3}`` is a set. However, to create an empty set, if we use the ``{}`` syntax, an empty dictionary will be created instead.
	- To create an empty set, we can use the ``set()`` function
```Python
import functools
def my_print(st):
	functools.reduce(lambda acc, x: print(x), st)
my_print({1,2,3,4,5})
```
- This will not work in python2, only in python3
	- This is because in python2 print is not a function, but a statement, that s why ``print(x) == print x`` This means the lambda function would have two statements, since it would have to also ``return;``
```Python
lambda acc, x: print(x)
# That is equivalent to:
def f(acc,x):
	print(x)
	return;
```
- Workaround in python2:
```Python
import functools
def f(acc,x):
	print(x)
def my_print(st):
	functools.reduce(f,st)
my_print({1,2,3,4,5})
```
- This will print ``2 3 4 5``, because the first 1 is taken as an accumulator. Then the accumulator is ``None``, since that's what the print function returns.
- To solve this, we specify a start accumulator:
```Python
import functools
def f(acc,x):
	print(x)
def my_print(st):
	functools.reduce(f,st,0) # instead of zero, any other value can be specified
my_print({1,2,3,4,5})
```
- Still, this time the acumulator will be ``None`` too, since, again, that's what the print function returns. We can check the accumulator this way:
```Python
import functools
def f(acc,x):
	print(x)
def my_print(st):
	return functools.reduce(f,st)
print(my_print({1,2,3,4,5}))
```
- This will return the accumulator, which is ``None``

- Sum of elements inside set with reduce function:
```Python
import functools

def set_sum(st):
	return functools.reduce(lambda acc,x : acc + x ,st)
print(set_sum({1,2,3,4,5}))
```

- Function that displays only elements that meet a condition inside a set:
```Python
import functools
def my_print(st,cond):
	def f(acc,x):
		if(cond(x)):
			print(x)
	return functools.reduce(f,st)
print(my_print({1,2,3,4,5}, lambda x: x%2 == 0))
```
- If the ``f`` function wasn't inside the ``my_print`` function, we wouldn't be able to call the ``cond`` function. It also cannot have it as an argument, since it has to have 2 parameters for the ``reduce`` function to be able to call it. Another approach would be to still display all the elements inside the set, but filter the input set with the condition, using the ``filter`` function.

- If we use ``set()`` as a default argument, it will be persisted.
```Python
def divizori(nr, divs = set(), d = 1):
	if d == nr:
		divs.add(d)
		return divs
	if nr % d == 0:
		divs.add(d)
	return divizori(nr, divs, d+1)
print(divizori(7)) # returns {1,7}
print(divizori(2)) # returns {1,2,7}
```
- This is because the default argument is created and allocated only once. A workaround is to allocate the set each time inside the function:
```Python
def divizori(nr, divs = None, d = 1):
	if divs == None:
		divs = set()
	if d == nr:
		divs.add(d)
		return divs
	if nr % d == 0:
		divs.add(d)
	return divizori(nr, divs, d+1)
print(divizori(7)) # returns {1,7}
print(divizori(2)) # returns {1,2,7}
```

- This approach with the default argument for the set can of course be replaced with creating an empty set and recursively adding to it inside the function.