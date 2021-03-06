# Before writing any Python code

# Docstrings

All non-trivial methods should have docstrings. Docstrings should follow guidelines here: [PEP257](http://www.python.org/dev/peps/pep-0257/). For more examples, see the [Google style guide](https://google.github.io/styleguide/pyguide.html#Comments) around docstrings.

To summarize: There are two types of docstrings, long-form and short-form.

A short-form docstring fits entirely on one line, including the triple quotes. It is used for simple functions, especially (though by no means exclusively) ones that are not part of a public API:


```python 

"""Return a user-readable form of a Frobnozz, html-escaped.""" 

```


Note that the text is specified as an action (“return”) rather than a description (“returns”). This has the added advantage of taking less space, so the comment is more likely to fit on a single line. :-)

If the description spills past one line, you should move to the long-form docstring: a summary line (one physical line) starting with a triple-quote ("""), terminated by a period, question mark, or exclamation point, followed by a blank line, followed by the rest of the doc string starting at the same cursor position as the first quote of the first line, ending with triple-quotes on a line by themselves. (Unlike what the BDFL suggests in PEP 257, we do not put a blank line before the ending quotes.)


```python

"""This comment serves to demonstrate the format of a docstring.

Note that the summary line is always at most one line long, and
on the same line as the opening triple-quote, and with no spaces
between the triple-quote and the text.  Always use double-quotes
(") rather than single-quotes (') for your docstring.   (This
lets us reserve ''' (3 single-quotes) as a way of commenting out
big blocks of code.
"""

```


#### A function (including methods and generators) must have a docstring, unless it meets all of the following criteria:

* not externally visible
* very short
* obvious


The docstring should describe the function's calling syntax and its semantics, not its implementation.

The docstring should end with the following special sections (see the [Google style guide](https://google.github.io/styleguide/pyguide.html?showone=Comments#Comments) for more details).


* Arguments: List each parameter by name, and a description of it. The description can span several lines (use a hanging indent if so). Use instead of "Args".

* Returns: (or Yields: for generators): Describe the type and semantics of the return value. If the function only returns None, this section is not required.

* Raises: List all exceptions that are relevant to the interface.

Classes should follow a similar format: a single line describing the class, plus more details, but instead of Arguments/Returns/Raises, it should have an Attributes: section that lists and describes the public attributes of the class (if any).

Modules (files) should have a docstring too, at the top of the file, starting with the usual one-line summary:

```python

"""One line summary.

Longer description.
"""

```

> Rationale: People will read a piece of code many more times than they will write it. Time spent documenting at write-time more than pays off at read time. What is obvious to you as the code-author, well versed in the module where this function lives, may not be at all obvious to a code reader, who is possibly jumping into this function from some unrelated part of the codebase.

The rules here may seem like overkill, especially the need to document every argument and return value. I can say from experience two things: it often does seem like overkill when writing it (especially when the docstring is longer than the function!) but I've almost never thought it was overkill when reading unfamiliar code. You may find, as you write the docstring, you're putting down something that wasn't as obvious as you thought it was:

```py
def WriteTimestamp(now):
    """Write the current time to stdout in an arbitrary user-readable format.

    Arguments:
        now: the current time as a time_t. Should be time.time() except for tests.
    """
    ...
```

Even though the meaning of now may seem obvious, it's not obvious that it's only being passed in so it can be mocked out for tests. Its type also isn't obvious (a time_t? a tuple? a datetime object?), so it's nice to have that documented as well.


## Top of the file

Start your file with a module docstring. Do not put a shebang line (`#!/usr/bin/python`) or copyright notice, or anything else.  Follow the docstring with your imports; don't put an `__author__` line.

> *Exception:* if the python file is meant to be executable, it should start with the following shebang line:

>```py
>#!/usr/bin/env python
>```

> *Rationale:* a shebang line is useless for non-executable files. An `__author__` line just gets out of date, and is better determined by looking at source control history in any case. Code is automatically copyrighted; a copyright line doesn't help. No need to put this useless boilerplate at the top of the file!

## Unused variables

If you want to assign a value to a variable that will not be used again, name the variable either `_` (python convention) or `unused_<something>` (less-well-known python convention).  This will keep our lint checkers from complaining.

## Splitting lines

Using PEP8 as a guideline for Python formatting runs us head-long into a great debate: the 79-character line limit. For better or worse, the PEP8 limit is part of the lint check for Khan Academy's Python code.

> Rationale: short lines have benefits, including:

> - Broad tool support. Code is read more frequently than it is written, often by those whose tools don't match the original author. Short lines are always well-supported. Many tools only naively support long lines (`<pre>` tags and most terminal tools), and no editor wraps long lines intelligently enough in all cases.
> - Side-by-side code windows. Try it out, it's great.
> - A free gut check. It's easy to reach 100 or 200 columns when writing complex expressions or nesting deeply. If this indicates unclear code, future readers would appreciate a quick refactoring.

> Of course having a hard limit for line length is silly. Any reasonable limit runs into a case where breaking the rule produces better code. However, having unnecessarily long lines scattered about due to assumptions about a reader's tools is also silly.

Python expressions end with a newline, not a semicolon, unlike many C-based languages. The trick is that lines can be continued within parentheses, brackets, and braces, or following a backslash. Parentheses are recommended. Backslashes should be avoided.

```py
>>> True and (True or
...           False)


>>> [x * x
...  for x
...  in xrange(0, 10)
...  if x % 2]


>>> {'Earth': 1,
...  'Jupiter': 5.3,
...  'Saturn': 9}
```

Notably, splitting string literals doesn't require use of the + operator. Adjacent literals are automatically combined.

```py
>>> twist = 'Peter Piper ' 'split a set ' 'of simple strings'
>>> twist
'Peter Piper split a set of simple strings'
```

This makes splitting long messages easy.

```py
>>> twist = ('Peter Piper '
...          'split a set '
...          'of simple strings')
```

Because Python's indentation style is unlike many C-based languages, your editor might need some cajoling to support it.

- Emacs has great support out of the box via `python-mode`.
- Vim needs some help. Installing [this indent script by Eric Mc Sween](http://www.vim.org/scripts/script.php?script_id=974) will get you there.

Examples of line splitting from our code:

```py
BAD:  zero_duration_videos = video_models.Video.all().filter("duration =", 0).fetch(10000)

GOOD: zero_duration_videos = (video_models.Video.all()
                        .filter("duration =", 0)
                        .fetch(10000))
```

```py
# BAD:
return current_app.response_class("OAuth error. %s" % e.message, status=401, headers=build_authenticate_header(realm="http://www.khanacademy.org"))

# GOOD:
return current_app.response_class(
    "OAuth error. %s" % e.message, status=401,
    headers=build_authenticate_header(realm="http://www.khanacademy.org"))
```

```py
# Bad:
kwargs = dict((str(key), value) for key, value in topic_json.iteritems() if key in ['id', 'title', 'standalone_title', 'description', 'tags', 'hide'])
# Good:
kwargs = dict((str(key), value)
              for key, value in topic_json.iteritems()
              if key in ['id', 'title', 'standalone_title',
                         'description', 'tags', 'hide'])
```


## Splitting tricky lines

There are cases where line splitting doesn't feel nice. Let's look at a few of them, sigh, and move on.

This long method reference needs surrounding parens and splits the line before the dot operator:

```py
# Bad:
badge_name = badges.topic_exercise_badges.TopicExerciseBadge.name_for_topic_key_name(self.key().name())
# Good:
badge_name = (badges.topic_exercise_badges.TopicExerciseBadge
              .name_for_topic_key_name(self.key().name()))
```

This long string path needs to be split.

```py
# Bad:
self.redirect("/class_profile?selected_graph_type=%s&coach_email=%s&graph_query_params=%s" %
        (self.GRAPH_TYPE, urllib.quote(coach.email), urllib.quote(urllib.quote(self.request.query_string))))
# Good:
self.redirect(
    "/class_profile?selected_graph_type=%s&coach_email=%s"
    "&graph_query_params=%s" % (
        self.GRAPH_TYPE,
        urllib.quote(coach.email),
        urllib.quote(urllib.quote(self.request.query_string))))
```

Sometimes, the best way to avoid long lines is to use temporary variables.  This can improve readability in any case.

```py
# Bad:
topics_list = [t for t in topics if not (
    (t.standalone_title == "California Standards Test: Algebra I" and t.id != "algebra-i") or
    (t.standalone_title == "California Standards Test: Geometry" and t.id != "geometry-2"))
    ]
# Good:
bad_title_and_ids = [("California Standards Test: Algebra I", "algebra-i"),
                     ("California Standards Test: Geometry", "geometry-2"),
                    ]
topics_list = [t for t in topics
               if not (t.standalone_title, t.id) in bad_title_and_ids]
```

## Indenting continued lines properly

When a logical statement is split into multiple lines and is followed by an indented line, the continued lines should be indented further to set them apart from the next logical statement.

In the following example taken from http://www.python.org/dev/peps/pep-0008/#maximum-line-length, notice how the second and third lines of the if condition are indented further to differentiate them from the actual raise statement within the if block:

```py
class Rectangle(Blob):
    def __init__(self, width, height,
                     color='black', emphasis=None, highlight=0):
        if (width == 0 and height == 0 and
                color == 'red' and emphasis == 'strong' or
                highlight > 100):
            raise ValueError("sorry, you lose")
```

## Naming

There is a good article on how to name variable names. [Good variable names](http://wiki.c2.com/?GoodVariableNames)


* Variables, functions, methods, packages, modules
    * lower_case_with_underscores 
    
    ```python
        
    def example_method():
        pass    
       
    ```

* Classes and Exceptions
    * CapWords
    
    ```python
        
    class ExampleClass:
        pass    
       
    ```
    
* Protected methods and internal functions
    * _single_leading_underscore(self, ...)
    
    ```python
    
    def _example_protected_method(self, ...):
        pass
  
    ```   

* Constants
    * ALL_CAPS_WITH_UNDERSCORES
    
    ```python
    
    EXAMPLE_CONSTANT_VARIABLE = 100
  
    ```
    
General Naming Guidelines
Avoid one-letter variables (esp. l, O, I).

Exception: In very short blocks, when the meaning is clearly visible from the immediate context

#### Fine

```python

for e in elements:
    e.mutate()

```

Avoid redundant labeling.

#### Yes

```python

import audio

core = audio.Core()
controller = audio.Controller()

```


#### No

```python

import audio

core = audio.AudioCore()
controller = audio.AudioController()

```

##### Prefer "reverse notation".

#### Yes

```python

elements = ...
elements_active = ...
elements_defunct = ...

```


#### No

```python

elements = ...
active_elements = ...
defunct_elements ...

```





## Identation

* Use 4 spaces--never tabs. Enough said.


# General concepts

## Explicit code

* While any kind of black magic is possible with Python, the most explicit and straightforward manner is preferred.

Bad

```python

def make_complex(*args):
    x, y = args
    return dict(**locals())

```

Good


```python

def make_complex(x, y):
    return {'x': x, 'y': y}

```

In the good code above, x and y are explicitly received from the caller, and an explicit dictionary is returned. The developer using this function knows exactly what to do by reading the first and last lines, which is not the case with the bad example.


## One statement per line

* While some compound statements such as list comprehensions are allowed and appreciated for their brevity and their expressiveness, it is bad practice to have two disjointed statements on the same line of code.

Bad

```python

print('one'); print('two')

if x == 1: print('one')

if <complex comparison> and <other complex comparison>:
    # do something

```

Good

```python

print('one')
print('two')

if x == 1:
    print('one')

cond1 = <complex comparison>
cond2 = <other complex comparison>
if cond1 and cond2:
    # do something

```

## Unpacking

* If you know the length of a list or tuple, you can assign names to its elements with unpacking. For example, since enumerate() will provide a tuple of two elements for each item in list:

```python
for index, item in enumerate(some_list):
    # do something with index and item
```

* You can use this to swap variables as well:

```python
a, b = b, a
```

* Nested unpacking works too:

``` python
a, (b, c) = 1, (2, 3)
```

* In Python 3, a new method of extended unpacking was introduced by [PEP 3132](https://www.python.org/dev/peps/pep-3132):

```python

a, *rest = [1, 2, 3]
# a = 1, rest = [2, 3]
a, *middle, c = [1, 2, 3, 4]
# a = 1, middle = [2, 3], c = 4

```


# Imports

## from...import

Only import entire modules, never individual symbols from a module. For top-level modules, that means import foo. For sub-modules, you can do either import foo.bar or from foo import bar.

```python

import auth_util                      # module import: importing the file auth_util.py
import auth.oauth_credentials         # module import: importing the file auth/oauth_credentials.py
from auth import oauth_credentials    # module import: importing the file auth/oauth_credentials.py
from auth_util import get_response    # BAD: symbol import: importing the function get_response from auth_util.py
from auth_util import Authorize       # BAD: symbol import: importing the class Authorize from auth_util.py
from auth_util import AUTH_HOST       # BAD: symbol import: importing the variable AUTH_HOST from auth_util.py

```


## Commit checklist

* Run Pylint - [Pylint website](https://www.pylint.org/)
* Run tests



