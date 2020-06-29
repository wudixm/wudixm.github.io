## code object

[原文](https://stackoverflow.com/questions/5768684/what-is-a-python-code-object)
```
While trying to use Python's "exec" statement, I got the following error:

TypeError: exec: arg 1 must be a string, file, or code object
I don't want to pass in a string or a file, but what is a code object, and how do I create one?
```


1. One way to create a code object is to use compile built-in function:

```python
>>> compile('sum([1, 2, 3])', '', 'single')
<code object <module> at 0x19ad730, file "", line 1>
>>> exec compile('sum([1, 2, 3])', '', 'single')
6
>>> compile('print "Hello world"', '', 'exec')
<code object <module> at 0x19add30, file "", line 1>
>>> exec compile('print "Hello world"', '', 'exec')
Hello world
```

2. also, functions have the function attribute __code__ (also known as func_code in older versions) from which you can obtain the function's code object:

```python
>>> def f(s): print s
... 
>>> f.__code__
<code object f at 0x19aa1b0, file "<stdin>", line 1>
```
