```Python
def my_filter(f, s):  
    return {  
        functools.reduce(lambda acc, x: acc | frozenset({x}) if f(x) else acc, s, frozenset()),  
        functools.reduce(lambda acc, x: acc | frozenset({x}) if not f(x) else acc, s, frozenset())  
            }
```
- This code won't work with normal sets since we're returning a ``SET`` that contains as its two elements some ``SETS``. Normal sets are mutable, therefore impossible to be used as elements in another set.
- To solve this, we simply do not return a set, instead either a **dictionary** or a **tuple**.
```Python
return {  
    "t_v": functools.reduce(lambda acc, x: acc | {x} if f(x) else acc, s, set()),  
    "f_v": functools.reduce(lambda acc, x: acc | {x} if not f(x) else acc, s, set())  
}
```
or
```Python
def my_partition(f, s):  
    return (  
        functools.reduce(lambda acc, x: acc | {x} if f(x) else acc, s, set()),  
        functools.reduce(lambda acc, x: acc | {x} if not f(x) else acc, s, set())  
    )
```

- **Frozen Sets** can be used as elements in another set.