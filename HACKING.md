# Developing angr

These are some guidelines so that we can keep the codebase in good shape!

## Coding style
We try to get as close as the [PEP8 code convention](http://legacy.python.org/dev/peps/pep-0008/) as is reasonable without being dumb. If you use Vim, the [python-mode](https://github.com/klen/python-mode) plugin does all you need. You can also [manually configure](https://wiki.python.org/moin/Vim) vim to adopt this behavior.

Most importantly, please consider the following when writing code as part of angr:

- Try to use attribute access (see the `@property` decorator) instead of getters and setters wherever you can. This isn't Java, and attributes enable tab completion in iPython. That being said, be reasonable: attributes should be fast. A rule of thumb is that if something could require a constraint solve, it should not be an attribute.

- Use our `.pylintrc`. It's fairly permissive, but our CI server will fail your builds if pylint complains under those settings.

- DO NOT, under ANY circumstances, `raise Exception` or `assert False`. **Use the right exception type**. If there isn't a correct exception type, subclass the core exception of the module that you're working in (i.e. `AngrError` in angr, `SimError` in SimuVEX, etc) and raise that. We catch, and properly handle, the right types of errors in the right places, but `AssertionError` and `Exception` are not handled anywhere and force-terminate analyses.

- Avoid tabs; use space indentation instead. Even though it's wrong, the de facto standard is 4 spaces. It is a good idea to adopt this from the beginning, as merging code that mixes both tab and space indentation is awful.

- Avoid super long lines. It's okay to have longer lines, but keep in mind that long lines are harder to read and should be avoided. Let's try to stick to **120 characters**.

- Avoid extremely long functions, it is often better to break them up into smaller functions.

- Prefer `_` to `__` for private members (so that we can access them when debugging). *You* might not think that anyone has a need to call a given function, but trust us, you're wrong.

## Documentation 
Document your code. Every *class definition* and *public function definition* should have some description of:
 - What it does.
 - What are the type and the meaning of the parameters.
 - What it returns.

We use [Sphinx](http://www.sphinx-doc.org/en/stable/) to generate the API documentation. Sphinx supports special [keywords](http://www.sphinx-doc.org/en/stable/domains.html#info-field-lists) to document function parameters, return values, return types etc. 

Here is an example of function documentation. Ideally the parameter descriptions should be aligned vertically to make the docstrings as readable as possible. 

```python
def prune(self, filter_func=None, from_stash=None, to_stash=None):
    """
    Prune unsatisfiable paths from a stash.

    :param filter_func: Only prune paths that match this filter.
    :param from_stash:  Prune paths from this stash. (default: 'active')
    :param to_stash:    Put pruned paths in this stash. (default: 'pruned')

    :returns:           The resulting PathGroup.
    :rtype:             PathGroup
    """
 ```

This format has the advantage that the function parameters are clearly identified in the generated documentation. However it can make the documentation repetitive, in some cases a textual description can be more readable. Pick the format you feel is more appropriate for the functions or classes you are documenting. 

 ```python
 def read_bytes(self, addr, n):
    """
    Read `n` bytes at address `addr` in memory and return an array of bytes.
    """
 ```
 
## Unit tests
If you're pushing a new feature and it is not accompanied by a test case it **will be broken** in very short order. Please write test cases for your stuff.
