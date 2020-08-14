# Hypothetical Switch Statement

This is not a PEP, merely an example of what a `switch` statement in Python could look like.

## Syntax

```
    switch_stmt: "switch" expr ":" NEWLINE INDENT case_block+ DEDENT [ "else" ":" INDENT stmt+ DEDENT ]
    case_block: case+  INDENT stmt+ DEDENT
    case: "case" [ cmpop ] expr [ if expr ] ":" NEWLINE
```

## Semantics

Each case-block is checked in turn. If any case within the block matches the value being tested, then the statements in that block are executed and the swtich statement terminates. If no case-blocks match, but an `else` is present, then the `else` block is executed.
To determine if a case matches, the value being tested is compared to the value of the case, using the given operator or `==` if no operator is present. If an `if` clause is present that is also checked.

## Examples

### Http

```python
switch response.status:
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
    else:
        raise RequestError("we couldn't get the data")
```

### Json decode

Assuming `type.__contains__`:

```python
switch key:
    case in str:
        pass
    case in float:
        key = _floatstr(key)
    case is True:
        key = 'true'
    case is False:
        key = 'false'
    case is None:
        key = 'null'
    case in int:
        key = _intstr(key)
    else:
        if _skipkeys:
            continue
        else:
            raise TypeError(f'keys must be str, int, float, bool or None, '
                            f'not {key.__class__.__name__}')
```