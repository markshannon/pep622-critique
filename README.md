# Critique of PEP 622 (Structural Pattern Matching)

## Abstract

This document attempts an objective and fair analysis of PEP 622.

From this analysis, I conclude that PEP 622 has small, possibly negative, net benefit, but high cost in terms of complexity and surprising behaviour.

This analysis includes what I believe to be a rigourous and objective analysis of applying PEP 622 to the entire CPython codebase.
All `if` statements where PEP 622 could be useful are analysed, comparing PEP 622 with with Python 3.9, Python with a simple switch statement, and Python with an operator form of `isinstance`.

## Introduction

For a PEP to succeed it needs to show two things.

 1. Exactly what problem is being solved, or what need is to be fulfilled, and that there is a sufficiently large problem or need to merit the proposed change.
 2. That the proposed change is the best known solution for the problem being addressed.

There is some evidence that there is a problem to solved, but it is very weak given the scale of the proposed changes.
However, PEP 622 fails completely to show that the proposed change is the best known solution.

PEP 622 provides only three examples of where the proposed pattern matching would be useful, yet proposes a very large change to the language; possibly the largest single change since Python 1.

PEP 622 claims to be a general way to express a number of related things, but fails to live up to that promise as it involves many special cases and upsets any intuition that comes from familiarity with the rest of the language.

It also claims to be *structural* pattern matching, but relies on the interface of objects, not their structure when unpacking.

## Analysis of the potential usefulness of PEP 622

See [Analysis of standard library](./stdlib_examples.md) for detailed analysis.

Searching the entire CPython Python code base (~630k LOC) finds only a few `if` statements where PEP 622 shows any readability advantages over much simpler alternatives. The exact number of `if` statements where PEP 622 shows improvement is subjective.
However, it is hard to see how PEP 622 shows a significant readability improvement for more than three or four of those `if` statements.
Regardless of the exact number, it is very few in over 600 thousand lines of code.

This analysis demonstrates that there's no advantage to combining type and length tests with destructuring.
Nor does PEP 622 promise to make the code faster.
Having them separate is much simpler and causes no actual loss in expressibilty or speed.

## Alternative approaches

The response to PEP 622 was positive, suggesting that there is demand for some additional syntax.
Very few examples were given, however, of where it would be useful. So it's hard to guess what the desirable features are. The rationale suggests that chains of `elif`s involving type tests are well suited to be improved by the match statement, though these are comparatively rare in Python where "duck typing" is the norm.

### Suggestions

The following are my opinions and this list is far from being exhaustive.

1. Do nothing. Guaranteed bug-free and zero maintenance cost.
2. Implement `__contains__` on `type`, allows `isinstance(x, str)` to be written as `x in str`. Improves readability in code involving `isinstance` tests, but may allow some errors to pass silently.
3. Add a `isa` or simliar instance check operator, to avoid confusion with overloading `in`.
4. Implement a "switch" statement, which is just syntactic sugar for a chain of `elif`s where all the tests apply to the same variable.
5. Implement more powerful unpacking syntax. For example, zero-or-one matches, named matches, or allowing classes to define their own unpacking.

See [Possible switch statement](./switch.md) for a hypothetical switch statement.
Note that the suggested syntax is just for comparison; there are many alternatives. See [PEP 3103](https://www.python.org/dev/peps/pep-3103/).

## Analysis of the examples in PEP 622

### make_point_3d example

```python
def make_point_3d(pt):
    match pt:
        case (x, y):
            return Point3d(x, y, 0)
        case (x, y, z):
            return Point3d(x, y, z)
        case Point2d(x, y):
            return Point3d(x, y, 0)
        case Point3d(_, _, _):
            return pt
        case _:
            raise TypeError("not a point we support")
```

What's interesting about this example isn't what it *is* so much as what it *isn't.*
The code creates an object but it's written as a module-level function.

Why doesn't this example show us `Point3d.__init__` written using pattern matching?
The authors' intent may well be to show handling of classes outside of the author's control,
but many classes are within the author's control, in which case the logic should be included
in `Point3d.__init__`.

Unfortunately, PEP 622's pattern matching doesn't work well in `__init__` methods; the "capture pattern" can't
write to attributes of `self`. PEP 622 claims this example is clearer when written
using pattern matching, but PEP 622's limitations make it less useful.
Since assignments to `self.x` is impossible in a case, temporaries need to be used:

```python
class Point3d:
    def __init__(self, pt):
        match pt:
            case (x, y):
                z = 0
            case (x, y, z):
                pass
            case Point2d(x, y):
                z = 0
            case Point3d(x, y, z):
                pass
            case _:
                raise TypeError("not a point we support")
        self.x = x
        self.y = y
        self.z = z
```

### Django example

```python
if (
    isinstance(value, (list, tuple)) and
    len(value) > 1 and
    isinstance(value[-1], (Promise, str))
):
    *value, label = value
    value = tuple(value)
else:
    label = key.replace('_', ' ').title()
```

This is quite a complex example. The case we want to distinguish is where `value` is either a `list` or `tuple`,
there are at least 2 elements in `value`, and the last element is either a `Promise` or a `str`.
The code is not very concise, but is reasonably clear.

The version of this example given in PEP 622 is as follows:

```python
match value:
    case [*v, label := (Promise() | str())] if v:
        value = tuple(v)
    case _:
        label = key.replace('_', ' ').title()
```

This is confusing. The use of the walrus operator with a sequence match makes the order of evaluation hard to follow. The test `if v` after `v` is assigned is confusing in a `match` statement which, one would expect, does assignment only when a match occurs.
This means that if `value` has a length of one, then `[]` is assigned to `v`, even though the the first case does not match.

It shouldn't be this complicated.


Also note:
The match version has different semantics. This has been pointed out to the PEP's authors but they have so far declined to either update the example or state in the PEP that the semantics are different.
If the exact semantics are to be retained, then the PEP 622 example should read

```python
match value:
    case [*v, label := (Promise() | str())] if v and isinstance(value, (list, tuple)):
        value = tuple(v)
    case _:
        label = key.replace('_', ' ').title()
```


### is_tuple example

```python
def is_tuple(node):
    if isinstance(node, Node) and node.children == [LParen(), RParen()]:
        return True
    return (isinstance(node, Node)
            and len(node.children) == 3
            and isinstance(node.children[0], Leaf)
            and isinstance(node.children[1], Node)
            and isinstance(node.children[2], Leaf)
            and node.children[0].value == "("
            and node.children[2].value == ")")
```

PEP 622 shows this example rewritten as:

```python
def is_tuple(node: Node) -> bool:
    match node:
        case Node(children=[LParen(), RParen()]):
            return True
        case Node(children=[Leaf(value="("), Node(), Leaf(value=")")]):
            return True
        case _:
            return False
```

This is merely taking code that is a bit verbose and rewriting it more concisely.
We can already do this in Python 3.9:

```python
def is_tuple(node):
    if isinstance(node, Node):
        l, *n, r = node.children
        if l == LParen() and r == RParen():
            return not n or len(n) == 1 and isinstance(n[0], Node)
    return False
```

which is shorter, arguably clearer, and needs no new syntax.

### HTTP response example

```python
match response.status:
    case 200:
        do_something(response.data)  # OK
    case 301 | 302:
        retry(response.location)  # Redirect
    case 401:
        retry(auth=get_credentials())  # Login first
    case 426:
        sleep(DELAY)  # Server is swamped, try after a bit
        retry()
    case _:
        raise RequestError("we couldn't get the data")
```

The final example is a simple switch statement, and shows that there may be some merit in adding
switch-statement-like functionality to Python.
However, PEP 622 prevents the use of simple symbolic constants, so the statement *cannot* be written as

```python
match response.status:
    case HTTP_OK:
        do_something(response.data)  # OK
    case HTTP_MOVED_PERMANENTLY | HTTP_FOUND:
        retry(response.location)  # Redirect
    case HTTP_UNAUTHORIZED:
        retry(auth=get_credentials())  # Login first
    case HTTP_UPGRADE:
        sleep(DELAY)  # Server is swamped, try after a bit
        retry()
    case _:
        raise RequestError("we couldn't get the data")
```

Note that PEP 622 does not prohibit the above syntax.  It merely assigns `response.status` to `HTTP_OK` regardless of the status and changing what was assumed to be a constant.

## Irregularities and surprising behavior

PEP 622 is large and complicated, which makes it far too easy to make mistakes.
Many features that might be assumed to do one thing in fact do another, silently changing the meaning of the program rather than failing.

### Handling of booleans

Any match involving booleans and ints must match on `case bool()`, not `case True` or case `False`,
and must do so before any `case int()`, `case 0`, or `case 1`.
This is surprising and dangerous.

Starting with the JSON encoder code as an example:

```python
    ...
    elif key is True:
        key = 'true'
    elif key is False:
        key = 'false'
    elif isinstance(key, int):
        key = _intstr(key)
    ...
```

The direct transliteration is:

```python
    match key:
        ...
        case True:
            key = 'true'
        case False:
            key = 'false'
        case int():
            key = _intstr(key)
        ...
```

but this is a mis-translation. A key of `1` now becomes `"true"` instead of `"1"`.

A correct, but non-obvious, translation is:

```python
    match key:
        ...
        case bool():
            key = 'true' if key else 'false'
        case int():
            key = _intstr(key)
        ...
```

### Cannot use (undotted) symbolic constants

Using the match statement as a switch statement, as several people on the python-dev mailing list have done,
one might write the following:

```python
match response:
    case HTTP_OK:
        ...
    case HTTP_UNAUTHORIZED:
        ...
```

but if response is `HTTP_UNAUTHORIZED` this code not only does the wrong thing, it assigns to `HTTP_UNAUTHORIZED` to `HTTP_OK` potentially causing other bugs.

### Cannot use easily names from the builtin namespace in cases

This has the same root cause as the problem with symbolic constants.


```python
    match type(x): # We want to match on exact type
        case bool:
            print("bool")
        case int:
            print("int")
        case float:
            print("float")
```

This will print "bool" regardless of the input. PEP 622 says that dotted names should be used for constant values, which means the following workaround must be used:

```python
    import builtins
    match type(x): # We want to match on exact type
        case builtins.bool:
            print("bool")
        case builtins.int:
            print("int")
        case builtins.float:
            print("float")
```

### Self attributes

PEP 622 treats any name with a dot in it as a constant, so the way to fix the previous example is to use a dotted name; `HTTP_CONSTANTS.HTTP_OK` instead of just `HTTP_OK`.
The problem with this is that a minor syntax element within the pattern determines whether it is read or written to. This is likely to be a source of confusion when using attributes of local variables, particularly `self`.

```python
    match var:
        case self.x: # test var == self.attr
            ...
        case x: # assignment of x
            ...
```

A programmer unfamiliar with PEP 622 might interpret these two case statements as doing similar things.
But PEP 622 interprets them very differently.  In particular, it interprets `self.x` as a *constant*
which it clearly is not.

### Attribute lookups with side-effects

PEP 622 does not specify order of execution, or the number of times that an attribute may be executed. In fact, it explicitly states that "User code relying on that behavior should be considered buggy."

However, the `@property` decorator allows complex code to be hidden behind an attribute.
ORMs and many other libraries have side-effecting properties. Even the standard library has side-effecting properties in `importlib.metadata`.

### Bitwise "or" on integers

`case 1 | 2:` is not the same as `case 1 + 2:` even though `(1 | 2) == (1 + 2)`

# Summary

PEP 622 promises easy-to-use structural pattern matching, but delivers a complicated-to-use sub-language of little value with many complexities and corner cases.
It should be rejected.
