# Objections to PEP 622 (Structural Pattern Matching)


The authors believe that for a PEP to succeed it needs to show two things.
 
 1. Exactly what problem is being solved, or need is to be fulfilled, and that is a sufficiently large problem, or need, to merit the proposed change.
 2. That the proposed change is the best known solution for the problem being addressed.

We believe that PEP 622 fails to show either of these things.

PEP 622 provides only three examples of where the proposed pattern matching, yet proposes a very large change to the language; possibly the largest single change since Python 1.

PEP 622 claims to be a general way to express a number of related things, but fails to live up to that promise as it involves many special cases and upsets any intuition that comes from familiarity with the rest of the language.


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

This is quite a complex example. The case we want to distinguish is where `value` is either a `list` or `tuple`, there are at least elements in `value` and the last element is either a `Promise` or a `str`.
The code is not very concise, but is reasonable clear.

The version of this example given in PEP 626 is as follows:

```
match value:
    case [*v, label := (Promise() | str())] if v:
        value = tuple(v)
    case _:
        label = key.replace('_', ' ').title()
```

This is highly confusing. The use of walrus operator with a sequence match makes the order of evaluation hard to follow. The test `if v` after `v` is assigned is confusing in a `match` statement which, one would expect, does assignment only when a match occurs.
This means that if `value` has a length of one, then `[]` is assigned to `v`, even though the the first case does not match.
It shouldn't be this complicated.


Also note:
The match version has different semantics. This has been pointed out to the PEP's authors but they seem unwilling to either update the example or state in the PEP that the semantics are different.
If the exact semantics are to be retained, then the PEP 626 example should read

```
match value:
    case [*v, label := (Promise() | str())] if v and isinstance(value, (list, tuple)):
        value = tuple(v)
    case _:
        label = key.replace('_', ' ').title()

```


### is_tuple example

```
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

PEP 626 shows this example rewritten as:

```
def is_tuple(node: Node) -> bool:
    match node:
        case Node(children=[LParen(), RParen()]):
            return True
        case Node(children=[Leaf(value="("), Node(), Leaf(value=")")]):
            return True
        case _:
            return False
```

This however is merely taking code that is a bit verbose and rewriting it more concisely.
We can already do this in Python 3.9:

```
def is_tuple(node):
    if isinstance(node, Node) and len(node.children) in (2, 3):
        l, *n, r = node.children
        if l == LParen() and r == RParen():
            return not n or isinstance(n[0], Node)
    return False
```

which is shorter, arguably clearer, and needs no new syntax.

### Http response example

```
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

The final example is a simple switch statement, and shows that there may be some merit in such a proposal.
However PEP 626 prevents the use of simple symbolic constants so the statement *cannot* be written as

```
match response.status:
    case HTTP_OK:
        do_something(response.data)  # OK
    case HTTP_MOVED_PERMANENTLY:
    case HTTP_FOUND:
        retry(response.location)  # Redirect
    case HTTP_UNAUTHORIZED:
        retry(auth=get_credentials())  # Login first
    case HTTP_UPGRADE:
        sleep(DELAY)  # Server is swamped, try after a bit
        retry()
    case _:
        raise RequestError("we couldn't get the data")
```

However, PEP 626 does not prohibit the above syntax, it merely assigns `response.status` to `HTTP_OK` regardless of the status and changing what was assumed to be a constant.

## Alternative approaches

The response to PEP 626 was positive, suggesting that there is demand for some additional syntax.
Very few examples were given, however, of where it would be useful. So it is hard to guess what are the desirable features. The rationale suggests that chains of `elif`s involving type tests are well suited to be improved by the match statement. 


## Examples of chained `elif` tests in CPython

There are five examples of in the standard library of an `if` statement containing two or more `elif` clauses which include an `isinstance` check. Query: https://lgtm.com/query/8460807749847811825/

These are:
https://lgtm.com/projects/g/python/cpython/snapshot/cb3202860b5cd12bcfda1489774333cb6747ff5e/files/Lib/ast.py?sort=name&dir=ASC&mode=heatmap#L80
https://lgtm.com/projects/g/python/cpython/snapshot/cb3202860b5cd12bcfda1489774333cb6747ff5e/files/Lib/logging/config.py?sort=name&dir=ASC&mode=heatmap#L444
https://lgtm.com/projects/g/python/cpython/snapshot/cb3202860b5cd12bcfda1489774333cb6747ff5e/files/Lib/http/cookies.py?sort=name&dir=ASC&mode=heatmap#L408
https://lgtm.com/projects/g/python/cpython/snapshot/cb3202860b5cd12bcfda1489774333cb6747ff5e/files/Lib/unittest/loader.py?sort=name&dir=ASC&mode=heatmap#L190
https://lgtm.com/projects/g/python/cpython/snapshot/cb3202860b5cd12bcfda1489774333cb6747ff5e/files/Lib/random.py?sort=name&dir=ASC&mode=heatmap#L143

### ast.literal_eval

```
    if isinstance(node, Constant):
        return node.value
    elif isinstance(node, Tuple):
        return tuple(map(_convert, node.elts))
    elif isinstance(node, List):
        return list(map(_convert, node.elts))
    elif isinstance(node, Set):
        return set(map(_convert, node.elts))
    elif (isinstance(node, Call) and isinstance(node.func, Name) and
            node.func.id == 'set' and node.args == node.keywords == []):
        return set()
    elif isinstance(node, Dict):
        if len(node.keys) != len(node.values):
            _raise_malformed_node(node)
        return dict(zip(map(_convert, node.keys),
                        map(_convert, node.values)))
    elif isinstance(node, BinOp) and isinstance(node.op, (Add, Sub)):
        left = _convert_signed_num(node.left)
        right = _convert_num(node.right)
        if isinstance(left, (int, float)) and isinstance(right, complex):
            if isinstance(node.op, Add):
                return left + right
            else:
                return left - right
```

This example contains a list of `isinstance` checks. 
Using the PEP 626 match statement would make it clearer that the test was repeated being performed on the same value, but a simple switch statement would do the same.

#### PEP 622 version

```
    match node:
        case Constant():
            return node.value
        case Tuple():
            return tuple(map(_convert, node.elts))
        case List():
            return list(map(_convert, node.elts))
        case Set():
            return set(map(_convert, node.elts))
        case Call() if (isinstance(node.func, Name) and
                        node.func.id == 'set' and node.args == node.keywords == []):
            return set()
        case Dict():
            if len(node.keys) != len(node.values):
                _raise_malformed_node(node)
            return dict(zip(map(_convert, node.keys),
                            map(_convert, node.values)))
        case BinOp() if isinstance(node.op, (Add, Sub):
            left = _convert_signed_num(node.left)
            right = _convert_num(node.right)
            if isinstance(left, (int, float)) and isinstance(right, complex):
                if isinstance(node.op, Add):
                    return left + right
                else:
                    return left - right
```

#### Using type.__contains__

```
    if node in Constant:
        return node.value
    elif node in Tuple:
        return tuple(map(_convert, node.elts))
    elif node in List:
        return list(map(_convert, node.elts))
    elif node in Set:
        return set(map(_convert, node.elts))
    elif (node in Call and isinstance(node.func, Name) and
            node.func.id == 'set' and node.args == node.keywords == []):
        return set()
    elif node in Dict:
        if len(node.keys) != len(node.values):
            _raise_malformed_node(node)
        return dict(zip(map(_convert, node.keys),
                        map(_convert, node.values)))
    elif node in BinOp and (node.op in Add or node.op in Sub):
        left = _convert_signed_num(node.left)
        right = _convert_num(node.right)
        if (left in int or left in float) and right in complex:
            if node.op in Add:
                return left + right
            else:
                return left - right
```

#### Using both `switch` statement and `type.__contains__`


```
    switch node:
        case in Constant:
            return node.value
        case in Tuple:
            return tuple(map(_convert, node.elts))
        case in List:
            return list(map(_convert, node.elts))
        case in Set:
            return set(map(_convert, node.elts))
        case in Call if (node.func in Name and
                        node.func.id == 'set' and node.args == node.keywords == []):
            return set()
        case in Dict:
            if len(node.keys) != len(node.values):
                _raise_malformed_node(node)
            return dict(zip(map(_convert, node.keys),
                            map(_convert, node.values)))
        case in BinOp if node.op in Add or node.op in Sub:
            left = _convert_signed_num(node.left)
            right = _convert_num(node.right)
            if (left in int or left in float) and right in complex:
                if node.op in Add:
                    return left + right
                else:
                    return left - right
```

### logging.config.BaseConfigurator.convert

#### Python 3.9

```python
    if not isinstance(value, ConvertingDict) and isinstance(value, dict):
        value = ConvertingDict(value)
        value.configurator = self
    elif not isinstance(value, ConvertingList) and isinstance(value, list):
        value = ConvertingList(value)
        value.configurator = self
    elif not isinstance(value, ConvertingTuple) and\
                isinstance(value, tuple) and not hasattr(value, '_fields'):
        value = ConvertingTuple(value)
        value.configurator = self
    elif isinstance(value, str): # str for py3k
        ... # This case is long but does involve any matching.
```

#### PEP 622

```python
    match value:
        case dict() if not isinstance(value, ConvertingDict):
            value = ConvertingDict(value)
            value.configurator = self
        case list() if not isinstance(value, ConvertingList):
            value = ConvertingList(value)
            value.configurator = self
        case tuple() if not isinstance(value, ConvertingTuple) and\
                    not hasattr(value, '_fields'):
            value = ConvertingTuple(value)
            value.configurator = self
        case str(): # str for py3k
            ... # This case is long but does involve any matching.
``` 

#### Using type.__contains__

```python
    if value in dict and value not in ConvertingDict:
        value = ConvertingDict(value)
        value.configurator = self
    elif value in list and value not in ConvertingList:
        value = ConvertingList(value)
        value.configurator = self
    elif value in tuple and value not in ConvertingTuple and\
                not hasattr(value, '_fields'):
        value = ConvertingTuple(value)
        value.configurator = self
    elif value in str: # str for py3k
        ... # This case is long but does involve any matching.
```

#### Using both `switch` statement and `type.__contains__`

```python
    switch value:
        case in dict if value not in ConvertingDict:
            value = ConvertingDict(value)
            value.configurator = self
        case in list if value not in ConvertingList:
            value = ConvertingList(value)
            value.configurator = self
        case in tuple if value not in ConvertingTuple and\
                not hasattr(value, '_fields'):
            value = ConvertingTuple(value)
            value.configurator = self
        case in str: # str for py3k
            ... # This case is long but does involve any matching.
```

### http.cookies.Morsel.OutputString

#### Python 3.9

```python
    if key == "expires" and isinstance(value, int):
        append("%s=%s" % (self._reserved[key], _getdate(value)))
    elif key == "max-age" and isinstance(value, int):
        append("%s=%d" % (self._reserved[key], value))
    elif key == "comment" and isinstance(value, str):
        append("%s=%s" % (self._reserved[key], _quote(value)))
    elif key in self._flags:
        if value:
            append(str(self._reserved[key]))
    else:
        append("%s=%s" % (self._reserved[key], value))
```

#### PEP 622

```python
    match key:
        case "expires" if isinstance(value, int):
            append("%s=%s" % (self._reserved[key], _getdate(value)))
        case "max-age" and isinstance(value, int):
            append("%s=%d" % (self._reserved[key], value))
        case "comment" and isinstance(value, str):
            append("%s=%s" % (self._reserved[key], _quote(value)))
        case _:
            if key in self._flags:
                if value:
                    append(str(self._reserved[key]))
            else:
                append("%s=%s" % (self._reserved[key], value))
```

#### Using type.__contains__

```python
    if key == "expires" and value in int:
        append("%s=%s" % (self._reserved[key], _getdate(value)))
    elif key == "max-age" and value in int:
        append("%s=%d" % (self._reserved[key], value))
    elif key == "comment" and value in str:
        append("%s=%s" % (self._reserved[key], _quote(value)))
    elif key in self._flags:
        if value:
            append(str(self._reserved[key]))
    else:
        append("%s=%s" % (self._reserved[key], value))
```


#### Using both `switch` statement and `type.__contains__`

```python
    switch key:
        case "expires" if value in int:
            append("%s=%s" % (self._reserved[key], _getdate(value)))
        case "max-age" if value in int:
            append("%s=%d" % (self._reserved[key], value))
        case "comment" if value in str:
            append("%s=%s" % (self._reserved[key], _quote(value)))
        case in self._flags:
            if value:
                append(str(self._reserved[key]))
        else:
            append("%s=%s" % (self._reserved[key], value))
```

### unitest.loader.loadTestsFromName


#### Python 3.9

```python
    if isinstance(obj, types.ModuleType):
        return self.loadTestsFromModule(obj)
    elif isinstance(obj, type) and issubclass(obj, case.TestCase):
        return self.loadTestsFromTestCase(obj)
    elif (isinstance(obj, types.FunctionType) and
            isinstance(parent, type) and
            issubclass(parent, case.TestCase)):
        name = parts[-1]
        inst = parent(name)
        # static methods follow a different path
        if not isinstance(getattr(inst, name), types.FunctionType):
            return self.suiteClass([inst])
    elif isinstance(obj, suite.TestSuite):
        return obj
```

#### PEP 622

```python
    match obj:
        case types.ModuleType():
            return self.loadTestsFromModule(obj)
        case type() if issubclass(obj, case.TestCase):
            return self.loadTestsFromTestCase(obj)
        case type.FunctionType() if (
                isinstance(parent, type) and
                issubclass(parent, case.TestCase)):
            name = parts[-1]
            inst = parent(name)
            # static methods follow a different path
            if not isinstance(getattr(inst, name), types.FunctionType):
                return self.suiteClass([inst])
        case suite.TestSuite():
            return obj
```

#### Using type.__contains__

```python
    if obj in types.ModuleType:
        return self.loadTestsFromModule(obj)
    elif obj in type and issubclass(obj, case.TestCase):
        return self.loadTestsFromTestCase(obj)
    elif (obj in types.FunctionType and parent in type and
            issubclass(parent, case.TestCase)):
        name = parts[-1]
        inst = parent(name)
        # static methods follow a different path
        if getattr(inst, name) not in types.FunctionType:
            return self.suiteClass([inst])
    elif obj in suite.TestSuite:
        return obj
```

#### Using both `switch` statement and `type.__contains__`

```python
    switch obj:
        case in types.ModuleType:
            return self.loadTestsFromModule(obj)
        case in type and issubclass(obj, case.TestCase):
            return self.loadTestsFromTestCase(obj)
        case in types.FunctionType if (
                parent in type and
                issubclass(parent, case.TestCase)):
            name = parts[-1]
            inst = parent(name)
            # static methods follow a different path
            if not isinstance(getattr(inst, name), types.FunctionType):
                return self.suiteClass([inst])
        case in suite.TestSuite:
            return obj
```

### random.Random.seed


#### Python 3.9

The code is the three clauses is uninteresting from our point of view.

```python
    if version == 1 and isinstance(a, (str, bytes)):
        ...
    elif version == 2 and isinstance(a, (str, bytes, bytearray)):
        ...
    elif not isinstance(a, (type(None), int, float, str, bytes, bytearray)):
        ...
```

#### PEP 622


```python
    match version:
        case 1 if isinstance(a, (str, bytes)):
            ...
        case 2 if isinstance(a, (str, bytes, bytearray)):
            ...
        case _ if not isinstance(a, (type(None), int, float, str, bytes, bytearray)):
            ...
```

#### Using type.__contains__

```python    
    if version == 1 and (a in str or a in bytes):
        ...
    elif version == 2 and (a in str or a in bytes or a in bytearray):
        ...
    elif not isinstance(a, (type(None), int, float, str, bytes, bytearray)):
        ...
```

Note that if PEP 604 is accepted, this could potentially be rewritten as:


```python    
    if version == 1 and a in str | bytes:
        ...
    elif version == 2 and a in str | bytes | bytearray:
        ...
    elif a not in type(None) | int | float | str | bytes | bytearray:
        ...
```

#### Using both `switch` statement and `type.__contains__`

```python
    switch version:
        case 1 if a in str | bytes:
            ...
        case 2 and a in str | bytes | bytearray:
            ...
        else:
            if a not in type(None) | int | float | str | bytes | bytearray:
                ...


There a further XXX cases in the `Tools` folder (which I had to search the hard way using `git grep`) 


## Notes


Rationale and Goals
-------------------

> Python programs frequently need to handle data which varies in type, presence of attributes/keys, or number of elements. Typical examples are operating on nodes of a mixed structure like an AST, handling UI events of different types, processing structured input (like structured files or network messages), or “parsing” arguments for a function that can accept different combinations of types and numbers of parameters. 

AST, and UI objects usually (pretty much always) form a class hierarchy, making is easy to add utility matching methods to the base class.
Where matching *might* have some value is when unrelated types are involved, but you need to show that enhanced destructuring would be insufficient.

> In fact, the classic 'visitor' pattern is an example of this, done in an OOP style -- but matching makes it much less tedious to write.

This might be true of the "classic" visitor pattern, but that's a straw man.
The Python visitor pattern is a joy to use.
Each case gets its own self contained method, clearly named, which is much better than a giant `match` statement.

> Much of the code to do so tends to consist of complex chains of nested if/elif statements, including multiple calls to len(), isinstance() and index/key/attribute access. Inside those branches users sometimes need to destructure the data further to extract the required component values, which may be nested several objects deep.

There seem to be three things you want to enhance here:
Unpacking; to avoid calls to `len`
Type checking; to avoid calls to `isinstance`.
To avoiding nesting by using complex lvalues.

You fail to justify why the first two cannot be handled separately with much simpler extensions to the language and why complex lvalues are better than nesting.

The examples
------------

There are only three examples, none of which are compelling.
In fact, two of them serve as a warning against the additional complexity this PEP entails.

Django example:
'''''''''''''''

Saves two lines of code, but introduces two bugs!
(Assuming that the original behavior should be preserved)

If the authors of the PEP cannot use this feature correctly in the first example they give, what chance do the rest of us have?


is_tuple example:
'''''''''''''''''

(Repeating my earlier email)
Python's support for OOP provides an alternative to ADTs.
For example, by adding a simple "matches" method to Node and Leaf, `is_tuple` can be rewritten as something like:

def is_tuple(node):
    if not isinstance(node, Node):
        return False
    return node.matches("(", ")") or node.matches("(", ..., ")")


The "switch on http response codes" example.
''''''''''''''''''''''''''''''''''''''''''''

This clearly demonstrates the value of symbolic constants for readability and the serious flaw in the PEP that I cannot use them.

I really don't see you how you can claim that
`case 406:`
is more readable than
`elif response == HTTP_UPGRADE_REQUIRED:`
?

Preventing the use of symbolic constants or other complex rvalues is a impairment to usability.
Silently failing when symbolic constants are used is terrible.

I do see the value of a `switch` statement for repeated tests against the same value; its intent is clearer than repeated `elif`s. But there is no need for it to be so hard to use.



--------------------------

Side effects in attribute lookup.
importlib.metadata.Distribution.files

Over 500 properties in the standard library.
