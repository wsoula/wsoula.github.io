Python Tuples
---
I made a simple discovery today by accident.  I was copying objects from a list to refactor
my code and left a dangling comma that was at the end of a long line so I didn't see it.
Now the long line is its own separate issue but when I went to access the object I got an
error about no attribute in tuple.  I eventually figured out the issue but it was quite the
unexpected error.

Here is an example
```
foo="bar",
print(foo+'*')
```

Will result in error
```
Traceback (most recent call last):
  File "main.py", line 2, in <module>
    print(foo+'*')
TypeError: can only concatenate tuple (not "str") to tuple
```
