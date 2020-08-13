
# Method

The rationale for PEP 622 states that "Much of the code to do so tends to consist of complex chains of nested if/elif statements, including multiple calls to len(), isinstance() and index/key/attribute access."

I ran two different queries over the standard library, `Tools` and `doc` folders, over 630 000 lines of code in total.

## Queries

The first query finds all if statements with at least two tests of the same variable, where the tests involve `isinstance` or `len`, and where some destrucuring of that variable occurs in the body of the `if` statement.
Destructuring of the variable `var` means any of the following:

* `x, ... = var`
* `y = x[...]`
* `z = var.attr`

https://lgtm.com/query/7291159440077780290/

The second query finds allows for attribute access, but requires at least three tests, to avoid trivial cases.

https://lgtm.com/query/8160264111266768324/



### Query results

The first query finds 24 results, of which a few are false positives, leaving the following that could potentially have the test and destructuring combined:

https://lgtm.com/projects/g/python/cpython/snapshot/d39328cb48ce1e51ecd377c8b727c91cc8735eb0/files/Tools/pynche/Main.py?sort=name&dir=ASC&mode=heatmap#L192
https://lgtm.com/projects/g/python/cpython/snapshot/d39328cb48ce1e51ecd377c8b727c91cc8735eb0/files/Lib/argparse.py?sort=name&dir=ASC&mode=heatmap#L2206
https://lgtm.com/projects/g/python/cpython/snapshot/d39328cb48ce1e51ecd377c8b727c91cc8735eb0/files/Lib/ast.py?sort=name&dir=ASC&mode=heatmap#L284
https://lgtm.com/projects/g/python/cpython/snapshot/d39328cb48ce1e51ecd377c8b727c91cc8735eb0/files/Tools/peg_generator/pegen/build.py?sort=name&dir=ASC&mode=heatmap#L131


https://lgtm.com/projects/g/python/cpython/snapshot/d39328cb48ce1e51ecd377c8b727c91cc8735eb0/files/Lib/xmlrpc/client.py?sort=name&dir=ASC&mode=heatmap#L303

https://lgtm.com/projects/g/python/cpython/snapshot/d39328cb48ce1e51ecd377c8b727c91cc8735eb0/files/Lib/configparser.py?sort=name&dir=ASC&mode=heatmap#L495
https://lgtm.com/projects/g/python/cpython/snapshot/d39328cb48ce1e51ecd377c8b727c91cc8735eb0/files/Lib/dataclasses.py?sort=name&dir=ASC&mode=heatmap#L1207
https://lgtm.com/projects/g/python/cpython/snapshot/d39328cb48ce1e51ecd377c8b727c91cc8735eb0/files/Tools/scripts/db2pickle.py?sort=name&dir=ASC&mode=heatmap#L59
https://lgtm.com/projects/g/python/cpython/snapshot/d39328cb48ce1e51ecd377c8b727c91cc8735eb0/files/Lib/xml/dom/expatbuilder.py?sort=name&dir=ASC&mode=heatmap#L118
https://lgtm.com/projects/g/python/cpython/snapshot/d39328cb48ce1e51ecd377c8b727c91cc8735eb0/files/Lib/distutils/fancy_getopt.py?sort=name&dir=ASC&mode=heatmap#L144

https://lgtm.com/projects/g/python/cpython/snapshot/d39328cb48ce1e51ecd377c8b727c91cc8735eb0/files/Lib/fractions.py?sort=name&dir=ASC&mode=heatmap#L96
https://lgtm.com/projects/g/python/cpython/snapshot/d39328cb48ce1e51ecd377c8b727c91cc8735eb0/files/Lib/imaplib.py?sort=name&dir=ASC&mode=heatmap#L1516

https://lgtm.com/projects/g/python/cpython/snapshot/d39328cb48ce1e51ecd377c8b727c91cc8735eb0/files/Lib/unittest/mock.py?sort=name&dir=ASC&mode=heatmap#L90
https://lgtm.com/projects/g/python/cpython/snapshot/d39328cb48ce1e51ecd377c8b727c91cc8735eb0/files/Doc/tools/rstlint.py?sort=name&dir=ASC&mode=heatmap#L160
https://lgtm.com/projects/g/python/cpython/snapshot/d39328cb48ce1e51ecd377c8b727c91cc8735eb0/files/Lib/smtpd.py?sort=name&dir=ASC&mode=heatmap#L906
https://lgtm.com/projects/g/python/cpython/snapshot/d39328cb48ce1e51ecd377c8b727c91cc8735eb0/files/Lib/test/support/socket_helper.py?sort=name&dir=ASC&mode=heatmap#L255

https://lgtm.com/projects/g/python/cpython/snapshot/d39328cb48ce1e51ecd377c8b727c91cc8735eb0/files/Lib/turtle.py?sort=name&dir=ASC&mode=heatmap#L1852
https://lgtm.com/projects/g/python/cpython/snapshot/d39328cb48ce1e51ecd377c8b727c91cc8735eb0/files/Lib/turtle.py?sort=name&dir=ASC&mode=heatmap#L1884
https://lgtm.com/projects/g/python/cpython/snapshot/d39328cb48ce1e51ecd377c8b727c91cc8735eb0/files/Lib/typing.py?sort=name&dir=ASC&mode=heatmap#L710

The second query finds seventeen results. Some overlap with the first query. Once duplicates and false positives are removed, the following are left:

https://lgtm.com/projects/g/python/cpython/snapshot/65ae89f84ef1db8c130b1a3dafc0c24291ac337d/files/Lib/msilib/__init__.py?sort=name&dir=ASC&mode=heatmap#L107
https://lgtm.com/projects/g/python/cpython/snapshot/65ae89f84ef1db8c130b1a3dafc0c24291ac337d/files/Lib/email/_parseaddr.py?sort=name&dir=ASC&mode=heatmap#L116
https://lgtm.com/projects/g/python/cpython/snapshot/65ae89f84ef1db8c130b1a3dafc0c24291ac337d/files/Lib/ast.py?sort=name&dir=ASC&mode=heatmap#L80
https://lgtm.com/projects/g/python/cpython/snapshot/65ae89f84ef1db8c130b1a3dafc0c24291ac337d/files/Tools/clinic/clinic.py?sort=name&dir=ASC&mode=heatmap#L4479
https://lgtm.com/projects/g/python/cpython/snapshot/65ae89f84ef1db8c130b1a3dafc0c24291ac337d/files/Lib/json/encoder.py?sort=name&dir=ASC&mode=heatmap#L357
https://lgtm.com/projects/g/python/cpython/snapshot/65ae89f84ef1db8c130b1a3dafc0c24291ac337d/files/Tools/peg_generator/scripts/grammar_grapher.py?sort=name&dir=ASC&mode=heatmap#L56
https://lgtm.com/projects/g/python/cpython/snapshot/65ae89f84ef1db8c130b1a3dafc0c24291ac337d/files/Lib/plistlib.py?sort=name&dir=ASC&mode=heatmap#L719

For each example I have shown the (trimmed) original, and the same code rewritten using PEP 622 matching, and (where relevant) using `type.__contains__`, or a simple `switch` statement, along the lines of PEP 275, for comparison.

I have removed comments from the original to avoid obsuring the syntax.

# Examples

## Tools/pynch/Main.py main()

### Original

```python
    if len(args) == 0:
        initialcolor = None
    elif len(args) == 1:
        initialcolor = args[0]
    else:
        usage(1)
```

### PEP 622

```python
    match args:
        case []:
            initialcolor = None
        case [initialcolor]:
            pass
        case _:
            usage(1)
```

### Switch

```python
    switch len(args):
        case 0:
          initialcolor = None
        case 1:
            initialcolor = args[0]
        else:
            usage(1)  
```

## argparse.ArgumentParser._parse_optional

### Original

```python
    if len(option_tuples) > 1:
        options = ', '.join([option_string
            for action, option_string, explicit_arg in option_tuples])
        args = {'option': arg_string, 'matches': options}
        msg = _('ambiguous option: %(option)s could match %(matches)s')
        self.error(msg % args)
    elif len(option_tuples) == 1:
        option_tuple, = option_tuples
        return option_tuple
```

### PEP 622

```python
    match option_tuples:
        case []:
            pass
        case [option_tuple]:
            return option_tuple
        case _:
            options = ', '.join([option_string
                for action, option_string, explicit_arg in option_tuples])
            args = {'option': arg_string, 'matches': options}
            msg = _('ambiguous option: %(option)s could match %(matches)s')
            self.error(msg % args)
```

### Switch

```python
    switch len(option_tuples):
        case 0:
            pass
        case 1:
            option_tuple, = option_tuples
            return option_tuples[0]
        else:
            options = ', '.join([option_string
                for action, option_string, explicit_arg in option_tuples])
            args = {'option': arg_string, 'matches': options}
            msg = _('ambiguous option: %(option)s could match %(matches)s')
            self.error(msg % args)
```


## ast.get_docstring

### Original

```python
    if isinstance(node, Str):
        text = node.s
    elif isinstance(node, Constant) and isinstance(node.value, str):
        text = node.value
    else:
        return None
```

### PEP 622

```python
    match node:
        case Str(s):
            text = s
        case Node(value=str()):
            text = value
        case _:
            return None
```

### type.__contains__

```python
    if node in Str:
        text = node.s
    elif node in Constant and node.value in str:
        text = node.value
    else:
        return None

```

## Tools/peg_generator/pegen/build.py generate_token_definitions()

### Original

```python
    if len(pieces) == 1:
        (token,) = pieces
        non_exact_tokens.add(token)
        all_tokens[index] = token
    elif len(pieces) == 2:
        token, op = pieces
        exact_tokens[op.strip("'")] = index
        all_tokens[index] = token
    else:
        raise ValueError(f"Unexpected line found in Tokens file: {line}")
```

### PEP 622

```python
    match pieces:
        case [token]:
            non_exact_tokens.add(token)
            all_tokens[index] = token
        case [token, op]:
            exact_tokens[op.strip("'")] = index
            all_tokens[index] = token
        case _:
            raise ValueError(f"Unexpected line found in Tokens file: {line}")
```

### Alternative implementation
```python
    if len(pieces) in (1,2):
        token, *op = pieces:
        if op:
            exact_tokens[op[0].strip("'")] = index
        else:
            non_exact_tokens.add(token)
        all_tokens[index] = token
    else:
        raise ValueError(f"Unexpected line found in Tokens file: {line}")
```

### Switch

```python
    switch len(pieces):
        case 1:
            (token,) = pieces
            non_exact_tokens.add(token)
            all_tokens[index] = token
        case 2:
            token, op = pieces
            exact_tokens[op.strip("'")] = index
            all_tokens[index] = token
        else:
            raise ValueError(f"Unexpected line found in Tokens file: {line}")

```


## xmlrpc.client.DateTime.make_comparable

### Original

```python
    if isinstance(other, DateTime):
        s = self.value
        o = other.value
    elif isinstance(other, datetime):
        s = self.value
        o = _iso8601_format(other)
    elif isinstance(other, str):
        s = self.value
        o = other
    elif hasattr(other, "timetuple"):
        s = self.timetuple()
        o = other.timetuple()
    else:
        s = self
        o = NotImplemented
```

### PEP 622

```python
    match other:
        case DateTime(value):
            s = self.value
            o = value
        case datetime():
            s = self.value
            o = _iso8601_format(other)
        case str():
            s = self.value
            o = other
        case _:
            if hasattr(other, "timetuple"):
                s = self.timetuple()
                o = other.timetuple()
            else:
                s = self
                o = NotImplemented
```

### type.__contains__

```python
    if other in DateTime:
        s = self.value
        o = other.value
    elif other in datetime:
        s = self.value
        o = _iso8601_format(other)
    elif other in str:
        s = self.value
        o = other
    elif hasattr(other, "timetuple"):
        s = self.timetuple()
        o = other.timetuple()
    else:
        s = self
        o = NotImplemented
```

## configparder.ExtendedInterpolation._interpolate_some

### Original

```python
    if len(path) == 1:
        opt = parser.optionxform(path[0])
        v = map[opt]
    elif len(path) == 2:
        sect = path[0]
        opt = parser.optionxform(path[1])
        v = parser.get(sect, opt, raw=True)
    else:
        raise InterpolationSyntaxError(
            option, section,
            "More than one ':' found: %r" % (rest,))
```

### PEP 622

```python
    match path:
        case [optionstr]:
            opt = parser.optionxform(optionstr)
            v = map[opt]
        case [sect, optionstr]:
            opt = parser.optionxform(optionstr)
            v = parser.get(sect, opt, raw=True)
        case _:
            raise InterpolationSyntaxError(
                option, section,
                "More than one ':' found: %r" % (rest,))

```

### Switch

```python
    switch len(path):
        case 1:
            opt = parser.optionxform(path[0])
            v = map[opt]
        case 2:
            sect = path[0]
            opt = parser.optionxform(path[1])
            v = parser.get(sect, opt, raw=True)
        else:
            raise InterpolationSyntaxError(
                option, section,
                "More than one ':' found: %r" % (rest,))
```

## dataclasses.make_dataclass

### Original

```python
    if isinstance(item, str):
        name = item
        tp = 'typing.Any'
    elif len(item) == 2:
        name, tp, = item
    elif len(item) == 3:
        name, tp, spec = item
        namespace[name] = spec
    else:
        raise TypeError(f'Invalid field: {item!r}')
```

### PEP 622

```python
    match item:
        case str():
            name = item
            tp = 'typing.Any'
        case [name, tp]:
            pass
        case [name, tp, spec]:
            namespace[name] = spec
        case _:
            raise TypeError(f'Invalid field: {item!r}')
  
```

### type.__contains__

```python
    if item in str:
        name = item
        tp = 'typing.Any'
    elif len(item) == 2:
        name, tp, = item
    elif len(item) == 3:
        name, tp, spec = item
        namespace[name] = spec
    else:
        raise TypeError(f'Invalid field: {item!r}')
```


## Tools/scripts/db2pickle.py main()

### Original

```python
    if len(args) == 0 or len(args) > 2:
        usage()
        return 1
    elif len(args) == 1:
        dbfile = args[0]
        pfile = sys.stdout
    else:
        dbfile = args[0]
        try:
            pfile = open(args[1], 'wb')
        except IOError:
            sys.stderr.write("Unable to open %s\n" % args[1])
            return 1
```

### PEP 622

```python
    match args:
        case [dbfile]:
            pfile = sys.stdout
        case [dbfile, filename]:
            try:
                pfile = open(filename), 'wb')
            except IOError:
                sys.stderr.write("Unable to open %s\n" % filename)
                return 1
        case _:
            usage()
            return 1
```

### Switch

```python
    switch len(args):
        case 1:
            dbfile = args[0]
            pfile = sys.stdout
        case 2:
            dbfile, filename = args
            try:
                pfile = open(filename), 'wb')
            except IOError:
                sys.stderr.write("Unable to open %s\n" % filename)
                return 1
        else:
            usage()
            return 1

```

## xml.dom.expatbuilder._parse_ns_name

### Original

```python
    if len(parts) == 3:
        uri, localname, prefix = parts
        prefix = intern(prefix, prefix)
        qname = "%s:%s" % (prefix, localname)
        qname = intern(qname, qname)
        localname = intern(localname, localname)
    elif len(parts) == 2:
        uri, localname = parts
        prefix = EMPTY_PREFIX
        qname = localname = intern(localname, localname)
    else:
        raise ValueError("Unsupported syntax: spaces in URIs not supported: %r" % name)
```

### PEP 622

```python
    match parts:
        case [uri, localname, prefix]:
            prefix = intern(prefix, prefix)
            qname = "%s:%s" % (prefix, localname)
            qname = intern(qname, qname)
            localname = intern(localname, localname)
        case [uri, localname]:
            prefix = EMPTY_PREFIX
            qname = localname = intern(localname, localname)
        case _:
            raise ValueError("Unsupported syntax: spaces in URIs not supported: %r" % name)
```

## distutils.fancy_getopt.FancyGetopt._grok_option_table

### Original

```python
    if len(option) == 3:
        long, short, help = option
        repeat = 0
    elif len(option) == 4:
        long, short, help, repeat = option
    else:
        # the option table is part of the code, so simply
        # assert that it is correct
        raise ValueError("invalid option tuple: %r" % (option,))
```

### PEP 622

```python
    match option:
        case [long, short, help]:
            repeat = 0
        case [long, short, help, repeat]:
            pass
        case _:
            # the option table is part of the code, so simply
            # assert that it is correct
            raise ValueError("invalid option tuple: %r" % (option,))
```

### Alternative Python implementation

```python
    if len(option) in (3, 4):
        long, short, help, *opt_repeat = option
        repeat = opt_repeat[0] if opt_repeat else 0
    else:
        raise ValueError("invalid option tuple: %r" % (option,))
```


## fractions.Fraction.__new__

### Original

```python
    if type(numerator) is int:
        self._numerator = numerator
        self._denominator = 1
        return self
    elif isinstance(numerator, numbers.Rational):
        self._numerator = numerator.numerator
        self._denominator = numerator.denominator
        return self
    elif isinstance(numerator, (float, Decimal)):
        # Exact conversion
        self._numerator, self._denominator = numerator.as_integer_ratio()
        return self
    elif isinstance(numerator, str):
        # Handle construction from strings.
        ... # Trimmed, as no destructuring occurs
    else:
        raise TypeError("argument should be a string "
                        "or a Rational instance")
```

### PEP 622

```python
    match numerator:
        case _ if type(numerator) == int:
            self._numerator = numerator
            self._denominator = 1
            return self
        case numbers.Rational(numerator, demoninator)):
            self._numerator = numerator
            self._denominator = denominator
            return self
        case float() | Decimal():
            # Exact conversion
            self._numerator, self._denominator = numerator.as_integer_ratio()
            return self
        case str():
            # Handle construction from strings.
            ... # Trimmed, as no destructuring occurs
        case _:
            raise TypeError("argument should be a string "
                            "or a Rational instance")
```

Note that the numerator must be an `int`, not a subclass, so a class pattern doesn't work.
Also, not the potential confusion as the variable and `Rational` attribute name are the same.

### type.__contains__

```python
    if type(numerator) is int:
        self._numerator = numerator
        self._denominator = 1
        return self
    elif numerator in numbers.Rational:
        self._numerator = numerator.numerator
        self._denominator = numerator.denominator
        return self
    elif in float or numerator in Decimal:
        # Exact conversion
        self._numerator, self._denominator = numerator.as_integer_ratio()
        return self
    elif numerator in str:
        # Handle construction from strings.
        ... # Trimmed, as no destructuring occurs
    else:
        raise TypeError("argument should be a string "
                        "or a Rational instance")
```


## imaplib.Time2Internaldate

### Original

```python
    if isinstance(date_time, (int, float)):
        dt = datetime.fromtimestamp(date_time,
                                    timezone.utc).astimezone()
    elif isinstance(date_time, tuple):
        try:
            gmtoff = date_time.tm_gmtoff
        except AttributeError:
            if time.daylight:
                dst = date_time[8]
                if dst == -1:
                    dst = time.localtime(time.mktime(date_time))[8]
                gmtoff = -(time.timezone, time.altzone)[dst]
            else:
                gmtoff = -time.timezone
        delta = timedelta(seconds=gmtoff)
        dt = datetime(*date_time[:6], tzinfo=timezone(delta))
    elif isinstance(date_time, datetime):
        if date_time.tzinfo is None:
            raise ValueError("date_time must be aware")
        dt = date_time
    elif isinstance(date_time, str) and (date_time[0],date_time[-1]) == ('"','"'):
        return date_time        # Assume in correct format
    else:
        raise ValueError("date_time not of a known type")
```

### PEP 622

```python
    match date_time:
        case int() | float():
            dt = datetime.fromtimestamp(date_time,
                                        timezone.utc).astimezone()
        case tuple(tm_gmtoff):
            gmtoff = tm_gmtoff
            delta = timedelta(seconds=gmtoff)
            dt = datetime(*date_time[:6], tzinfo=timezone(delta))
        case tuple():
            if time.daylight:
                dst = date_time[8]
                if dst == -1:
                    dst = time.localtime(time.mktime(date_time))[8]
                gmtoff = -(time.timezone, time.altzone)[dst]
            else:
                gmtoff = -time.timezone
            delta = timedelta(seconds=gmtoff)
            dt = datetime(*date_time[:6], tzinfo=timezone(delta))
        case datetime():
            if date_time.tzinfo is None:
                raise ValueError("date_time must be aware")
            dt = date_time
        case str() if (date_time[0],date_time[-1]) == ('"','"'):
            return date_time        # Assume in correct format
        case _:
            raise ValueError("date_time not of a known type")
```

### type.__contains__

```python
    if date_time in int or date_time in float:
        dt = datetime.fromtimestamp(date_time,
                                    timezone.utc).astimezone()
    elif date_time in tuple:
        try:
            gmtoff = date_time.tm_gmtoff
        except AttributeError:
            if time.daylight:
                dst = date_time[8]
                if dst == -1:
                    dst = time.localtime(time.mktime(date_time))[8]
                gmtoff = -(time.timezone, time.altzone)[dst]
            else:
                gmtoff = -time.timezone
        delta = timedelta(seconds=gmtoff)
        dt = datetime(*date_time[:6], tzinfo=timezone(delta))
    elif date_time in  datetime:
        if date_time.tzinfo is None:
            raise ValueError("date_time must be aware")
        dt = date_time
    elif date_time in str and (date_time[0],date_time[-1]) == ('"','"'):
        return date_time        # Assume in correct format
    else:
        raise ValueError("date_time not of a known type")
```


## unittest.mock._get_signature_object

### Original

```python
    if isinstance(func, type) and not as_instance:
        # If it's a type and should be modelled as a type, use __init__.
        func = func.__init__
        # Skip the `self` argument in __init__
        eat_self = True
    elif not isinstance(func, FunctionTypes):
        # If we really want to model an instance of the passed type,
        # __call__ should be looked up, not __init__.
        try:
            func = func.__call__
        except AttributeError:
            return None
```

### PEP 622

```python
    match func:
        case type() if not as_instance:
            # If it's a type and should be modelled as a type, use __init__.
            func = func.__init__
            # Skip the `self` argument in __init__
            eat_self = True
        case _ if not isinstance(func, FunctionTypes):
            # If we really want to model an instance of the passed type,
            # __call__ should be looked up, not __init__.
            try:
                func = func.__call__
            except AttributeError:
                return None
```

### type.__contains__

```python
   if func in type and not as_instance:
        # If it's a type and should be modelled as a type, use __init__.
        func = func.__init__
        # Skip the `self` argument in __init__
        eat_self = True
    elif func not in FunctionTypes:
        # If we really want to model an instance of the passed type,
        # __call__ should be looked up, not __init__.
        try:
            func = func.__call__
        except AttributeError:
            return None
```


## Doc/tools/rstlint.py main()

### Original

```python
    if len(args) == 0:
        path = '.'
    elif len(args) == 1:
        path = args[0]
    else:
        print(usage)
        return 2
```

### PEP 622

```python
    match args:
        case []:
            path = '.'
        case [path]:
            pass
        case _:
            print(usage)
            return 2
```

### Switch

```python
    switch len(args):
        case 0:
            path = '.'
        case 1:
            path = args[0]
        else:
            print(usage)
            return 2
```

## smtpd.parseargs

### Original

```python
    if len(args) < 1:
        localspec = 'localhost:8025'
        remotespec = 'localhost:25'
    elif len(args) < 2:
        localspec = args[0]
        remotespec = 'localhost:25'
    elif len(args) < 3:
        localspec = args[0]
        remotespec = args[1]
    else:
        usage(1, 'Invalid arguments: %s' % COMMASPACE.join(args))
 
```

### PEP 622

```python
    match args:
        case []:
            localspec = 'localhost:8025'
            remotespec = 'localhost:25'
        case [localspec]:
            remotespec = 'localhost:25'
        case [localspec, remotespec]:
            pass
        else:
            usage(1, 'Invalid arguments: %s' % COMMASPACE.join(args))
```

### Alternative Python implementation

```python
    remotespec = 'localhost:25'
    if len(args) < 1:
        localspec = 'localhost:8025'
    elif len(args) < 2:
        localspec = args[0]
    elif len(args) < 3:
        localspec = args[0]
        remotespec = args[1]
    else:
        usage(1, 'Invalid arguments: %s' % COMMASPACE.join(args))
```

## test.support.socket_helper.transient_internet

### Original

```python
    if len(a) >= 1 and isinstance(a[0], OSError):
        err = a[0]
    elif len(a) >= 2 and isinstance(a[1], OSError):
        err = a[1]
    else:
        break
```

### PEP 622

```python
    match a:
        case [err:=OSError(), *_]:
            pass
        case [_, err:=OSError(), _, *_]:
            err = a[1]
        case _:
            break

```

### type.__contains__

```python
    if len(a) >= 1 and a[0] in OSError:
        err = a[0]
    elif len(a) >= 2 and a[1] in OSError:
        err = a[1]
    else:
        break

```

## turtle.TNavigator.distance and turtle.TNavigator.towards

Both `turtle.TNavigator.distance` and `turtle.TNavigator.towards` contain the same if statement.

### Original

```python
    if isinstance(x, Vec2D):
        pos = x
    elif isinstance(x, tuple):
        pos = Vec2D(*x)
    elif isinstance(x, TNavigator):
        pos = x._position
```

### PEP 622

```python
    match x:
        case Vec2D():
            pos = x
        case tuple():
            pos = Vec2D(*x)
        case TNavigator(_position):
            pos = _position
```

### type.__contains__

```python
    if x in Vec2D:
        pos = x
    elif x in tuple:
        pos = Vec2D(*x)
    elif x in TNavigator:
        pos = x._position
```

## typing.GenericAlias.__getitem__

### Original

```python
    if isinstance(arg, TypeVar):
        arg = subst[arg]
    elif isinstance(arg, (_GenericAlias, GenericAlias)):
        subparams = arg.__parameters__
        if subparams:
            subargs = tuple(subst[x] for x in subparams)
            arg = arg[subargs]
```

### PEP 622

```python
    match arg:
        case TypeVar():
            arg = subst[arg]
        case _GenericAlias(__parameters__) | GenericAlias(__parameters__):
            subparams = __parameters__
            if subparams:
                subargs = tuple(subst[x] for x in subparams)
                arg = arg[subargs]
```

### type.__contains__

```python
    if arg in TypeVar:
        arg = subst[arg]
    elif arg in _GenericAlias or arg in GenericAlias:
        subparams = arg.__parameters__
        if subparams:
            subargs = tuple(subst[x] for x in subparams)
            arg = arg[subargs]
```


## msilib.add_data

### Original

```python
    if isinstance(field, int):
        r.SetInteger(i+1,field)
    elif isinstance(field, str):
        r.SetString(i+1,field)
    elif field is None:
        pass
    elif isinstance(field, Binary):
        r.SetStream(i+1, field.name)
    else:
        raise TypeError("Unsupported type %s" % field.__class__.__name__)
```

### PEP 622

```python
    match field:
        case int():
            r.SetInteger(i+1,field)
        case str():
            r.SetString(i+1,field)
        case Binary(name)):
            r.SetStream(i+1, name)
        case _ if field is not None:
            raise TypeError("Unsupported type %s" % field.__class__.__name__)
```

### type.__contains__

```python
    if field in int:
        r.SetInteger(i+1,field)
    elif field in str:
        r.SetString(i+1,field)
    elif field is None:
        pass
    elif field in Binary:
        r.SetStream(i+1, field.name)
    else:
        raise TypeError("Unsupported type %s" % field.__class__.__name__)
```


## email._parseaddr._parsedate_tz

### Original

```python
    if len(tm) == 2:
        [thh, tmm] = tm
        tss = '0'
    elif len(tm) == 3:
        [thh, tmm, tss] = tm
    elif len(tm) == 1 and '.' in tm[0]:
        # Some non-compliant MUAs use '.' to separate time elements.
        tm = tm[0].split('.')
        if len(tm) == 2:
            [thh, tmm] = tm
            tss = 0
        elif len(tm) == 3:
            [thh, tmm, tss] = tm
    else:
        return None
```

### PEP 622

```python
    match tm:
        case [thh, tmm]:
            tss = '0'
        case [thh, tmm, tss]:
            pass
        case [tm0] if '.' in tm[0]:
            # Some non-compliant MUAs use '.' to separate time elements.
            tm = tm[0].split('.')
            match tm:
                case [thh, tmm]:
                    tss = 0
                case [thh, tmm, tss]:
                    pass
        case _:
            return None
```

### Rewritten more concisely in Python 3.9

```python
    if len(tm) == 1 and '.' in tm[0]:
        # Some non-compliant MUAs use '.' to separate time elements.
        tm = tm[0].split('.')
    if len(tm) in (2,3):
        thh, tmm, *tss = tm
        tss = tss[0] if tss else '0'
    else:
        return None
```

## ast.liter_eval

### Original

```python
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

### PEP 622 version

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
        case Call(func=Name(id='set'), args=[], keywords=[])):
            return set()
        case Dict(keys, values):
            if len(keys) != len(values):
                _raise_malformed_node(node)
            return dict(zip(map(_convert, keys),
                            map(_convert, values)))
        case BinOp(op=Add() | Sub()):
            left = _convert_signed_num(node.left)
            right = _convert_num(node.right)
            if isinstance(left, (int, float)) and isinstance(right, complex):
                if isinstance(node.op, Add):
                    return left + right
                else:
                    return left - right
```

### Using type.__contains__

```
    if node in Constant:
        return node.value
    elif node in Tuple:
        return tuple(map(_convert, node.elts))
    elif node in List:
        return list(map(_convert, node.elts))
    elif node in Set:
        return set(map(_convert, node.elts))
    elif (node in Call and node.func in Name and
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


## plistlib._BinaryPlistWriter._write_object

I've elided a lot of complex logic int cases, as it is not relevant.

### Original

```python
    if value is None:
        self._fp.write(b'\x00')
    elif value is False:
        self._fp.write(b'\x08')
    elif value is True:
        self._fp.write(b'\x09')
    elif isinstance(value, int):
        ... # process int
    elif isinstance(value, float):
        self._fp.write(struct.pack('>Bd', 0x23, value))
    elif isinstance(value, datetime.datetime):
        f = (value - datetime.datetime(2001, 1, 1)).total_seconds()
        self._fp.write(struct.pack('>Bd', 0x33, f))
    elif isinstance(value, (bytes, bytearray)):
        self._write_size(0x40, len(value))
        self._fp.write(value)
    elif isinstance(value, str):
        ... # process str
    elif isinstance(value, UID):
        ... # process UID -- Uses value.data
    elif isinstance(value, (list, tuple)):
        refs = [self._getrefnum(o) for o in value]
        s = len(refs)
        self._write_size(0xA0, s)
        self._fp.write(struct.pack('>' + self._ref_format * s, *refs))
    elif isinstance(value, dict):
        ... # process dict
    else:
        raise TypeError(value)
```

### PEP 622

```python
    match value:
        case bool():
            if value:
                self._fp.write(b'\x09')
            else:
                self._fp.write(b'\x08')
        case int():
            ... # process int
        case float():
            self._fp.write(struct.pack('>Bd', 0x23, value))
        case datetime.datetime():
            f = (value - datetime.datetime(2001, 1, 1)).total_seconds()
            self._fp.write(struct.pack('>Bd', 0x33, f))
        case bytes() | bytearray():
            self._write_size(0x40, len(value))
            self._fp.write(value)
        case str():
            ... # process str
        case UID(data):
            ... # process UID -- Uses data
        case [*_]:
            refs = [self._getrefnum(o) for o in value]
            s = len(refs)
            self._write_size(0xA0, s)
            self._fp.write(struct.pack('>' + self._ref_format * s, *refs))
        case dict():
            ... # process dict
        case _:
            if value is None:
                self._fp.write(b'\x00')
            else:
                raise TypeError(value)

```

### type.__contains__

```python
   if value is None:
        self._fp.write(b'\x00')
    elif value is False:
        self._fp.write(b'\x08')
    elif value is True:
        self._fp.write(b'\x09')
    elif value in int:
        ... # process int
    elif value in float:
        self._fp.write(struct.pack('>Bd', 0x23, value))
    elif value in datetime.datetime:
        f = (value - datetime.datetime(2001, 1, 1)).total_seconds()
        self._fp.write(struct.pack('>Bd', 0x33, f))
    elif value in bytes or value in bytearray:
        self._write_size(0x40, len(value))
        self._fp.write(value)
    elif value in str:
        ... # process str
    elif value in UID:
        ... # process UID -- Uses value.data
    elif value in list or value in tuple:
        refs = [self._getrefnum(o) for o in value]
        s = len(refs)
        self._write_size(0xA0, s)
        self._fp.write(struct.pack('>' + self._ref_format * s, *refs))
    elif value in dict:
        ... # process dict
    else:
        raise TypeError(value)

```


## json.encoder._iterencode_dict

### Original

```python
    if isinstance(key, str):
        pass
    elif isinstance(key, float):
        key = _floatstr(key)
    elif key is True:
        key = 'true'
    elif key is False:
        key = 'false'
    elif key is None:
        key = 'null'
    elif isinstance(key, int):
        key = _intstr(key)
    elif _skipkeys:
        continue
    else:
        raise TypeError(f'keys must be str, int, float, bool or None, '
                        f'not {key.__class__.__name__}')
```

### PEP 622

```python
    match key:
        case str():
            pass
        case float():
            key = _floatstr(key)
        case bool():
            key = 'true' if key else 'false'
        case None:
            key = 'null'
        case int():
            key = _intstr(key)
        case _:
            if _skipkeys:
                continue
            else:
                raise TypeError(f'keys must be str, int, float, bool or None, '
                                f'not {key.__class__.__name__}')
```

### type.__contains__

```python
    if key in str:
        pass
    elif key in float:
        key = _floatstr(key)
    elif key is None:
        key = 'null'
    elif key is True:
        key = 'true'
    elif key is False:
        key = 'false'
    elif key in int:
        key = _intstr(key)
    elif _skipkeys:
        continue
    else:
        raise TypeError(f'keys must be str, int, float, bool or None, '
                        f'not {key.__class__.__name__}')
```

## /Tools/clinic/clinic.py DSLParser.state_parameter()

### Original

```python
    if isinstance(expr, ast.Name) and expr.id == 'NULL':
        value = NULL
        py_default = '<unrepresentable>'
        c_default = "NULL"
    elif (isinstance(expr, ast.BinOp) or
        (isinstance(expr, ast.UnaryOp) and
            not (isinstance(expr.operand, ast.Num) or
                (hasattr(ast, 'Constant') and
                isinstance(expr.operand, ast.Constant) and
                type(expr.operand.value) in (int, float, complex)))
        )):
        c_default = kwargs.get("c_default")
        if not (isinstance(c_default, str) and c_default):
            fail("When you specify an expression (" + repr(default) + ") as your default value,\nyou MUST specify a valid c_default." + ast.dump(expr))
        py_default = default
        value = unknown
    elif isinstance(expr, ast.Attribute):
        a = []
        n = expr
        while isinstance(n, ast.Attribute):
            a.append(n.attr)
            n = n.value
        if not isinstance(n, ast.Name):
            fail("Unsupported default value " + repr(default) + " (looked like a Python constant)")
        a.append(n.id)
        py_default = ".".join(reversed(a))
        c_default = kwargs.get("c_default")
        if not (isinstance(c_default, str) and c_default):
            fail("When you specify a named constant (" + repr(py_default) + ") as your default value,\nyou MUST specify a valid c_default.")
        try:
            value = eval(py_default)
        except NameError:
            value = unknown
    else:
        value = ast.literal_eval(expr)
        py_default = repr(value)
        if isinstance(value, (bool, None.__class__)):
            c_default = "Py_" + py_default
        elif isinstance(value, str):
            c_default = c_repr(value)
        else:
            c_default = py_default
```

### PEP 622

```python
    match expr:
        case ast.Name(id='NULL'):
            value = NULL
            py_default = '<unrepresentable>'
            c_default = "NULL"
        case ast.BinOp() | ast.UnaryOp() if not (
                isinstance(expr.operand, ast.Num) or
                (hasattr(ast, 'Constant') and
                isinstance(expr.operand, ast.Constant) and
                type(expr.operand.value) in (int, float, complex))):
            c_default = kwargs.get("c_default")
            if not (isinstance(c_default, str) and c_default):
                fail("When you specify an expression (" + repr(default) + ") as your default value,\nyou MUST specify a valid c_default." + ast.dump(expr))
            py_default = default
            value = unknown
        case ast.Attribute():
            a = []
            n = expr
            while isinstance(n, ast.Attribute):
                a.append(n.attr)
                n = n.value
            if not isinstance(n, ast.Name):
                fail("Unsupported default value " + repr(default) + " (looked like a Python constant)")
            a.append(n.id)
            py_default = ".".join(reversed(a))
            c_default = kwargs.get("c_default")
            if not (isinstance(c_default, str) and c_default):
                fail("When you specify a named constant (" + repr(py_default) + ") as your default value,\nyou MUST specify a valid c_default.")
            try:
                value = eval(py_default)
            except NameError:
                value = unknown
        case _:
            value = ast.literal_eval(expr)
            py_default = repr(value)
            if isinstance(value, (bool, None.__class__)):
                c_default = "Py_" + py_default
            elif isinstance(value, str):
                c_default = c_repr(value)
            else:
                c_default = py_default

```

### type.__contains__

```python
    if expr in ast.Name and expr.id == 'NULL':
        value = NULL
        py_default = '<unrepresentable>'
        c_default = "NULL"
    elif expr in ast.BinOp or
        (expr in ast.UnaryOp and
            not (expr.operand in ast.Num or
                (hasattr(ast, 'Constant') and
                expr.operand in ast.Constant and
                type(expr.operand.value) in (int, float, complex)))
        ):
        c_default = kwargs.get("c_default")
        if not (c_default in str and c_default):
            fail("When you specify an expression (" + repr(default) + ") as your default value,\nyou MUST specify a valid c_default." + ast.dump(expr))
        py_default = default
        value = unknown
    elif expr in ast.Attribute:
        a = []
        n = expr
        while n in ast.Attribute:
            a.append(n.attr)
            n = n.value
        if n not in ast.Name:
            fail("Unsupported default value " + repr(default) + " (looked like a Python constant)")
        a.append(n.id)
        py_default = ".".join(reversed(a))
        c_default = kwargs.get("c_default")
        if not (isinstance(c_default, str) and c_default):
            fail("When you specify a named constant (" + repr(py_default) + ") as your default value,\nyou MUST specify a valid c_default.")
        try:
            value = eval(py_default)
        except NameError:
            value = unknown
    else:
        value = ast.literal_eval(expr)
        py_default = repr(value)
        if value in bool or value is None:
            c_default = "Py_" + py_default
        elif value in str:
            c_default = c_repr(value)
        else:
            c_default = py_default
```


## Tools/peg_generator/scripts/grammar_grapher.py references_for_item()

### Original

```python
    if isinstance(item, Alt):
        return [_ref for _item in item.items for _ref in references_for_item(_item)]
    elif isinstance(item, Cut):
        return []
    elif isinstance(item, Group):
        return references_for_item(item.rhs)
    elif isinstance(item, Lookahead):
        return references_for_item(item.node)
    elif isinstance(item, NamedItem):
        return references_for_item(item.item)
    elif isinstance(item, NameLeaf):
        if item.value == "ENDMARKER":
            return []
        return [item.value]
    elif isinstance(item, Leaf):
        return []
    elif isinstance(item, Opt):
        return references_for_item(item.node)
    elif isinstance(item, Repeat):
        return references_for_item(item.node)
    elif isinstance(item, Rhs):
        return [_ref for alt in item.alts for _ref in references_for_item(alt)]
    elif isinstance(item, Rule):
        return references_for_item(item.rhs)
    else:
        raise RuntimeError(f"Unknown item: {type(item)}")

```
### PEP 622

```python
    match item:
        case Alt(items):
            return [_ref for _item in items for _ref in references_for_item(_item)]
        case Cut():
            return []
        case Group(rhs):
            return references_for_item()
        case Lookahead(node):
            return references_for_item(node)
        case NamedItem(item):
            return references_for_item(item)  # item now refers to item.item
        case NameLeaf(value):
            if value == "ENDMARKER":
                return []
            return [value]
        case Leaf():
            return []
        case Opt(node):
            return references_for_item(node)
        case Repeat(node):
            return references_for_item(node)
        case Rhs(alts):
            return [_ref for alt in alts for _ref in references_for_item(alt)]
        case Rule(rhs):
            return references_for_item(rhs)
        else:
            raise RuntimeError(f"Unknown item: {type(item)}")
```

### type.__contains__

```python
    if item in Alt:
        return [_ref for _item in item.items for _ref in references_for_item(_item)]
    elif item in Cut:
        return []
    elif item in Group:
        return references_for_item(item.rhs)
    elif item in Lookahead:
        return references_for_item(item.node)
    elif item in NamedItem:
        return references_for_item(item.item)
    elif item in NameLeaf:
        if item.value == "ENDMARKER":
            return []
        return [item.value]
    elif item in Leaf:
        return []
    elif item in Opt:
        return references_for_item(item.node)
    elif item in Repeat:
        return references_for_item(item.node)
    elif item in Rhs:
        return [_ref for alt in item.alts for _ref in references_for_item(alt)]
    elif item in Rule:
        return references_for_item(item.rhs)
    else:
        raise RuntimeError(f"Unknown item: {type(item)}")
```


## mailbox.MaildirMessage._explain_to

### Original

```python
```

### PEP 622

```python

```

### type.__contains__

```python

```


# Conclusions

## Overview

The 18 examples can be broken down into the following categories:

* Tests on length only: 10 cases
* Test on type only: 5 cases
* Tests on both type and length: 1 case
* Test on either type or length and some other condition: 2 cases

The case where testing is done on both type and length (`dataclasses.make_dataclass`) is
the only case where PEP 622 shows an improvement in readability.
It would appear that the benefits of PEP 622 are slight.

## Sequence unpacking

That over half the cases test for a narrow range of sequence lengths suggests that a zero-or-one
unpacking syntax might be useful; `?name` as an alternative to `*name`.
`?name` would match zero or one items, as opposed to `*name` which matches zero or more.

Using this extension, the `smtpd.parseargs` example could be rewritten as:

```python
    localspec = 'localhost:8025'
    remotespec = 'localhost:25'
    try:
        ?localspec, ?remotespec = args
    except ValueError:
        usage(1, 'Invalid arguments: %s' % COMMASPACE.join(args))
```

## Unpacking is often all the code does

A common pattern seems to be the following:

```
    elif len(args) == 1:
        val = args[0]
```

Whilst this converts neatly to PEP 622 syntax, it doesn't improve the code.

```
    match args:
        case [val]:
            pass
```

because the only purpose of the block was to assign `args[0]` to `val`, we need to insert a `pass`.
This degrades readability as the pattern match is used for side-effect not pure matching.

## Dangerous handling of booleans

Any match involving booleans and ints must match on `case bool()`, not `case True` or case `False` and must do
so before any `case int()`, `case 0`, or `case 1`.
This is surprising and dangerous.

Starting with the json decoder code as an example:

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

it is too easy to make the transliteration:

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

The correct, and non-obvious, translation is:

```python
match key:
    ...
    case bool():
        key = 'true' if key else 'false'
    case int():
        key = _intstr(key)
    ...
```

