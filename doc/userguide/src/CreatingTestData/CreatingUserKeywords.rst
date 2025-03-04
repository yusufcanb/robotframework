Creating user keywords
======================

Keyword sections are used to create new higher-level keywords by
combining existing keywords together. These keywords are called *user
keywords* to differentiate them from lowest level *library keywords*
that are implemented in test libraries. The syntax for creating user
keywords is very close to the syntax for creating test cases, which
makes it easy to learn.

.. contents::
   :depth: 2
   :local:

User keyword syntax
-------------------

Basic syntax
~~~~~~~~~~~~

In many ways, the overall user keyword syntax is identical to the
`test case syntax`_.  User keywords are created in Keyword sections
which differ from Test Case sections only by the name that is used to
identify them. User keyword names are in the first column similarly as
test cases names. Also user keywords are created from keywords, either
from keywords in test libraries or other user keywords. Keyword names
are normally in the second column, but when setting variables from
keyword return values, they are in the subsequent columns.

.. sourcecode:: robotframework

   *** Keywords ***
   Open Login Page
       Open Browser    http://host/login.html
       Title Should Be    Login Page

   Title Should Start With
       [Arguments]    ${expected}
       ${title} =    Get Title
       Should Start With    ${title}    ${expected}

Most user keywords take some arguments. This important feature is used
already in the second example above, and it is explained in detail
`later in this section`__, similarly as `user keyword return
values`_.

__ `User keyword arguments`_

User keywords can be created in `test case files`_, `resource files`_,
and `test suite initialization files`_. Keywords created in resource
files are available for files using them, whereas other keywords are
only available in the files where they are created.

Settings in the Keyword section
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

User keywords can have similar settings as `test cases`__, and they
have the same square bracket syntax separating them from keyword
names. All available settings are listed below and explained later in
this section.

`[Documentation]`:setting:
   Used for setting a `user keyword documentation`_.

`[Tags]`:setting:
   Sets `tags`__ for the keyword.

`[Arguments]`:setting:
   Specifies `user keyword arguments`_.

`[Return]`:setting:
   Specifies `user keyword return values`_. `RETURN` statement (new in RF 5.0)
   should be used instead.

`[Teardown]`:setting:
   Specify `user keyword teardown`_.

`[Timeout]`:setting:
   Sets the possible `user keyword timeout`_. Timeouts_ are discussed
   in a section of their own.

.. note:: The format used above is recommended, but setting names are
          case-insensitive and spaces are allowed between brackets and the name.
          For example, `[ TAGS ]`:setting is valid.

__ `Settings in the test case section`_
__ `User keyword tags`_

.. _User keyword documentation:

User keyword name and documentation
-----------------------------------

The user keyword name is defined in the first column of the
Keyword section. Of course, the name should be descriptive, and it is
acceptable to have quite long keyword names. Actually, when creating
use-case-like test cases, the highest-level keywords are often
formulated as sentences or even paragraphs.

User keywords can have a documentation that is set with the
:setting:`[Documentation]` setting. It supports same formatting,
splitting to multiple lines, and other features as `test case documentation`_.
This setting documents the user keyword in the test data. It is also shown
in a more formal keyword documentation, which the Libdoc_ tool can create
from `resource files`_. Finally, the first logical row of the documentation,
until the first empty row, is shown as a keyword documentation in `test logs`_.

.. sourcecode:: robotframework

   *** Keywords ***
   One line documentation
       [Documentation]    One line documentation.
       No Operation

   Multiline documentation
       [Documentation]    The first line creates the short doc.
       ...
       ...                This is the body of the documentation.
       ...                It is not shown in Libdoc outputs but only
       ...                the short doc is shown in logs.
       No Operation

   Short documentation in multiple lines
       [Documentation]    If the short doc gets longer, it can span
       ...                multiple physical lines.
       ...
       ...                The body is separated from the short doc with
       ...                an empty line.
       No Operation

Sometimes keywords need to be removed, replaced with new ones, or
deprecated for other reasons.  User keywords can be marked deprecated
by starting the documentation with `*DEPRECATED*`, which will
cause a warning when the keyword is used. For more information, see
the `Deprecating keywords`_ section.

.. note:: Prior to Robot Framework 3.1, the short documentation contained
          only the first physical line of the keyword documentation.

User keyword tags
-----------------

Both user keywords and `library keywords`_ can have tags. Similarly as when
`tagging test cases`_, there are two settings affecting user keyword tags:

`Keyword Tags`:setting: in the Setting section
   All keywords in a file with this setting always get specified tags.

`[Tags]`:setting: with each keyword
   Keywords get these tags in addition to possible tags specified using the
   :setting:`Keyword Tags` setting.

.. sourcecode:: robotframework

   *** Settings ***
   Keyword Tags       gui

   *** Keywords ***
   No own tags
       [Documentation]    This test has tag 'gui'.
       No Operation

   Own tags
       [Documentation]    This test has tags 'gui', 'own' and 'tags'.
       [Tags]    own    tags
       No Operation

Additionally, keyword tags can be specified on the last line of the documentation
with `Tags:` prefix so that tags are separated with a comma. For example,
following two keywords get same three tags:

.. sourcecode:: robotframework

   *** Keywords ***
   Settings tags using separate setting
       [Tags]    my    fine    tags
       No Operation

   Settings tags using documentation
       [Documentation]    I have documentation. And my documentation has tags.
       ...                Tags: my, fine, tags
       No Operation

Keyword tags are shown in logs and in documentation generated by Libdoc_,
where the keywords can also be searched based on tags. The `--removekeywords`__
and `--flattenkeywords`__ commandline options also support selecting keywords by
tag, and new usages for keywords tags are possibly added in later releases.

Similarly as with `test case tags`_, user keyword tags with the `robot:`
prefix are reserved__ for special features by Robot Framework
itself. Users should thus not use any tag with these prefixes unless actually
activating the special functionality.

.. note:: :setting:`Keyword Tags` is new in Robot Framework 5.1. With earlier
          versions all keyword tags need to be specified using the
          :setting:`[Tags]` setting.

.. note:: Robot Framework 5.2 will support `removing globally set tags`__ using
          the `-tag` syntax with the :setting:`[Tags]` setting. Creating tags
          with literal value like `-tag` `is deprecated`__ in Robot Framework 5.1
          and escaped__ syntax `\-tag` must be used if such tags are actually
          needed.

__ `Removing keywords`_
__ `Flattening keywords`_
__ `Reserved tags`_
__ https://github.com/robotframework/robotframework/issues/4374
__ https://github.com/robotframework/robotframework/issues/4380
__ escaping_

User keyword arguments
----------------------

Most user keywords need to take some arguments. The syntax for
specifying them is probably the most complicated feature normally
needed with Robot Framework, but even that is relatively easy,
particularly in most common cases. Arguments are normally specified with
the :setting:`[Arguments]` setting, and argument names use the same
syntax as variables_, for example `${arg}`.

Positional arguments with user keywords
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The simplest way to specify arguments (apart from not having them at all)
is using only positional arguments. In most cases, this is all
that is needed.

The syntax is such that first the :setting:`[Arguments]` setting is
given and then argument names are defined in the subsequent
cells. Each argument is in its own cell, using the same syntax as with
variables. The keyword must be used with as many arguments as there
are argument names in its signature. The actual argument names do not
matter to the framework, but from users' perspective they should
be as descriptive as possible. It is recommended
to use lower-case letters in variable names, either as
`${my_arg}`, `${my arg}` or `${myArg}`.

.. sourcecode:: robotframework

   *** Keywords ***
   One Argument
       [Arguments]    ${arg_name}
       Log    Got argument ${arg_name}

   Three Arguments
       [Arguments]    ${arg1}    ${arg2}    ${arg3}
       Log    1st argument: ${arg1}
       Log    2nd argument: ${arg2}
       Log    3rd argument: ${arg3}

Default values with user keywords
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

When creating user keywords, positional arguments are sufficient in
most situations. It is, however, sometimes useful that keywords have
`default values`_ for some or all of their arguments. Also user keywords
support default values, and the needed new syntax does not add very much
to the already discussed basic syntax.

In short, default values are added to arguments, so that first there is
the equals sign (`=`) and then the value, for example `${arg}=default`.
There can be many arguments with defaults, but they all must be given after
the normal positional arguments. The default value can contain a variable_
created on `test, suite or global scope`__, but local variables of the keyword
executor cannot be used. Default value can
also be defined based on earlier arguments accepted by the keyword.

.. note:: The syntax for default values is space sensitive. Spaces
          before the `=` sign are not allowed, and possible spaces
          after it are considered part of the default value itself.

.. sourcecode:: robotframework

   *** Keywords ***
   One Argument With Default Value
       [Arguments]    ${arg}=default value
       [Documentation]    This keyword takes 0-1 arguments
       Log    Got argument ${arg}

   Two Arguments With Defaults
       [Arguments]    ${arg1}=default 1    ${arg2}=${VARIABLE}
       [Documentation]    This keyword takes 0-2 arguments
       Log    1st argument ${arg1}
       Log    2nd argument ${arg2}

   One Required And One With Default
       [Arguments]    ${required}    ${optional}=default
       [Documentation]    This keyword takes 1-2 arguments
       Log    Required: ${required}
       Log    Optional: ${optional}

    Default Based On Earlier Argument
       [Arguments]    ${a}    ${b}=${a}    ${c}=${a} and ${b}
       Should Be Equal    ${a}    ${b}
       Should Be Equal    ${c}    ${a} and ${b}

When a keyword accepts several arguments with default values and only
some of them needs to be overridden, it is often handy to use the
`named arguments`_ syntax. When this syntax is used with user
keywords, the arguments are specified without the `${}`
decoration. For example, the second keyword above could be used like
below and `${arg1}` would still get its default value.

.. sourcecode:: robotframework

   *** Test Cases ***
   Example
       Two Arguments With Defaults    arg2=new value

As all Pythonistas must have already noticed, the syntax for
specifying default arguments is heavily inspired by Python syntax for
function default values.

__ `Variable priorities and scopes`_

Variable number of arguments with user keywords
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Sometimes even default values are not enough and there is a need
for a keyword accepting `variable number of arguments`_. User keywords
support also this feature. All that is needed is having `list variable`_ such
as `@{varargs}` after possible positional arguments in the keyword signature.
This syntax can be combined with the previously described default values, and
at the end the list variable gets all the leftover arguments that do not match
other arguments. The list variable can thus have any number of items, even zero.

.. sourcecode:: robotframework

   *** Keywords ***
   Any Number Of Arguments
       [Arguments]    @{varargs}
       Log Many    @{varargs}

   One Or More Arguments
       [Arguments]    ${required}    @{rest}
       Log Many    ${required}    @{rest}

   Required, Default, Varargs
       [Arguments]    ${req}    ${opt}=42    @{others}
       Log    Required: ${req}
       Log    Optional: ${opt}
       Log    Others:
       FOR    ${item}    IN    @{others}
           Log    ${item}
       END

Notice that if the last keyword above is used with more than one
argument, the second argument `${opt}` always gets the given
value instead of the default value. This happens even if the given
value is empty. The last example also illustrates how a variable
number of arguments accepted by a user keyword can be used in a `for
loop`__. This combination of two rather advanced functions can
sometimes be very useful.

The keywords in the examples above could be used, for example, like this:

.. sourcecode:: robotframework

    *** Test Cases ***
    Varargs with user keywords
        Any Number Of Arguments
        Any Number Of Arguments    arg
        Any Number Of Arguments    arg1    arg2    arg3   arg4
        One Or More Arguments    required
        One Or More Arguments    arg1    arg2    arg3   arg4
        Required, Default, Varargs    required
        Required, Default, Varargs    required    optional
        Required, Default, Varargs    arg1    arg2    arg3    arg4    arg5

Again, Pythonistas probably notice that the variable number of
arguments syntax is very close to the one in Python.

__ `for loops`_

Free named arguments with user keywords
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

User keywords can also accept `free named arguments`_ by having a `dictionary
variable`_ like `&{named}` as the absolutely last argument. When the keyword
is called, this variable will get all `named arguments`_ that do not match
any `positional argument`__ or `named-only argument`__ in the keyword
signature.

.. sourcecode:: robotframework

   *** Keywords ***
   Free Named Only
       [Arguments]    &{named}
       Log Many    &{named}

   Positional And Free Named
       [Arguments]    ${required}    &{extra}
       Log Many    ${required}    &{extra}

   Run Program
       [Arguments]    @{args}    &{config}
       Run Process    program.py    @{args}    &{config}

The last example above shows how to create a wrapper keyword that
accepts any positional or named argument and passes them forward.
See `free named argument examples`_ for a full example with same keyword.

Free named arguments support with user keywords works similarly as kwargs
work in Python. In the signature and also when passing arguments forward,
`&{kwargs}` is pretty much the same as Python's `**kwargs`.

__ `Positional arguments with user keywords`_
__ `Named-only arguments with user keywords`_

Named-only arguments with user keywords
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Starting from Robot Framework 3.1, user keywords support `named-only
arguments`_ that are inspired by `Python 3 keyword-only arguments`__.
This syntax is typically used by having normal arguments *after*
`variable number of arguments`__ (`@{varargs}`). If the keywords does not
use varargs, it is possible to use just `@{}` to denote that the subsequent
arguments are named-only:

.. sourcecode:: robotframework

   *** Keywords ***
   With Varargs
       [Arguments]    @{varargs}    ${named}
       Log Many    @{varargs}    ${named}

   Without Varargs
       [Arguments]    @{}    ${first}    ${second}
       Log Many    ${first}    ${second}

Named-only arguments can be used together with `positional arguments`__ as
well as with `free named arguments`__. When using free named arguments, they
must be last:

.. sourcecode:: robotframework

   *** Keywords ***
   With Positional
       [Arguments]    ${positional}    @{}    ${named}
       Log Many    ${positional}    ${named}

   With Free Named
       [Arguments]    @{varargs}    ${named only}    &{free named}
       Log Many    @{varargs}    ${named only}    &{free named}

When passing named-only arguments to keywords, their order does not matter
other than they must follow possible positional arguments. The keywords above
could be used, for example, like this:

.. sourcecode:: robotframework

   *** Test Cases ***
   Example
       With Varargs    named=value
       With Varargs    positional    second positional    named=foobar
       Without Varargs    first=1    second=2
       Without Varargs    second=toka    first=eka
       With Positional    foo    named=bar
       With Positional    named=2    positional=1
       With Free Named    positional    named only=value    x=1    y=2
       With Free Named    foo=a    bar=b    named only=c    quux=d

Named-only arguments can have default values similarly as `normal user
keyword arguments`__. A minor difference is that the order of arguments
with and without default values is not important.

.. sourcecode:: robotframework

   *** Keywords ***
   With Default
       [Arguments]    @{}    ${named}=default
       Log Many    ${named}

   With And Without Defaults
       [Arguments]    @{}    ${optional}=default    ${mandatory}    ${mandatory 2}    ${optional 2}=default 2    ${mandatory 3}
       Log Many    ${optional}    ${mandatory}    ${mandatory 2}    ${optional 2}    ${mandatory 3}

__ https://www.python.org/dev/peps/pep-3102
__ `Variable number of arguments with user keywords`_
__ `Positional arguments with user keywords`_
__ `Free named arguments with user keywords`_
__ `Default values with user keywords`_

.. _Embedded argument syntax:

Embedding arguments into keyword name
-------------------------------------

Robot Framework has also another approach to pass arguments to user
keywords than specifying them in cells after the keyword name as
explained in the previous section. This method is based on embedding
the arguments directly into the keyword name, and its main benefit is
making it easier to use real and clear sentences as keywords.

Basic syntax
~~~~~~~~~~~~

It has always been possible to use keywords like :name:`Select dog
from list` and :name:`Selects cat from list`, but all such keywords
must have been implemented separately. The idea of embedding arguments
into the keyword name is that all you need is a keyword with name like
:name:`Select ${animal} from list`.

.. sourcecode:: robotframework

   *** Keywords ***
   Select ${animal} from list
       Open Page    Pet Selection
       Select Item From List    animal_list    ${animal}

Keywords using embedded arguments cannot take any "normal" arguments
(specified with :setting:`[Arguments]` setting) but otherwise they are
created just like other user keywords. The arguments used in the name
will naturally be available inside the keyword and they have different
value depending on how the keyword is called. For example,
`${animal}` in the previous has value `dog` if the keyword
is used like :name:`Select dog from list`. Obviously it is not
mandatory to use all these arguments inside the keyword, and they can
thus be used as wildcards.

These kind of keywords are also used the same way as other keywords
except that spaces and underscores are not ignored in their
names. They are, however, case-insensitive like other keywords. For
example, the keyword in the example above could be used like
:name:`select x from list`, but not like :name:`Select x fromlist`.

Embedded arguments do not support default values or variable number of
arguments like normal arguments do. Using variables when
calling these keywords is possible but that can reduce readability.
Notice also that embedded arguments only work with user keywords.

Embedded arguments matching too much
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

One tricky part in using embedded arguments is making sure that the
values used when calling the keyword match the correct arguments. This
is a problem especially if there are multiple arguments and characters
separating them may also appear in the given values. For example,
keyword :name:`Select ${city} ${team}` does not work correctly if used
with city containing two parts like :name:`Select Los Angeles Lakers`.

An easy solution to this problem is quoting the arguments (e.g.
:name:`Select "${city}" "${team}"`) and using the keyword in quoted
format (e.g. :name:`Select "Los Angeles" "Lakers"`). This approach is
not enough to resolve all this kind of conflicts, though, but it is
still highly recommended because it makes arguments stand out from
rest of the keyword. A more powerful but also more complicated
solution, `using custom regular expressions`_ when defining variables,
is explained in the next section. Finally, if things get complicated,
it might be a better idea to use normal positional arguments instead.

The problem of arguments matching too much occurs often when creating
keywords that `ignore given/when/then/and/but prefixes`__ . For example,
:name:`${name} goes home` matches :name:`Given Janne goes home` so
that `${name}` gets value `Given Janne`. Quotes around the
argument, like in :name:`"${name}" goes home`, resolve this problem
easily.

__ `Ignoring Given/When/Then/And/But prefixes`_

Using custom regular expressions
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

When keywords with embedded arguments are called, the values are
matched internally using `regular expressions`__
(regexps for short). The default logic goes so that every argument in
the name is replaced with a pattern `.*?` that basically matches
any string. This logic works fairly well normally, but as just
discussed above, sometimes keywords `match more than
intended`__. Quoting or otherwise separating arguments from the other
text can help but, for example, the test below fails because keyword
:name:`I execute "ls" with "-lh"` matches both of the defined
keywords.

.. sourcecode:: robotframework

   *** Test Cases ***
   Example
       I execute "ls"
       I execute "ls" with "-lh"

   *** Keywords ***
   I execute "${cmd}"
       Run Process    ${cmd}    shell=True

   I execute "${cmd}" with "${opts}"
       Run Process    ${cmd} ${opts}    shell=True

A solution to this problem is using a custom regular expression that
makes sure that the keyword matches only what it should in that
particular context. To be able to use this feature, and to fully
understand the examples in this section, you need to understand at
least the basics of the regular expression syntax.

A custom embedded argument regular expression is defined after the
base name of the argument so that the argument and the regexp are
separated with a colon. For example, an argument that should match
only numbers can be defined like `${arg:\d+}`. Using custom
regular expressions is illustrated by the examples below.

.. sourcecode:: robotframework

   *** Test Cases ***
   Example
       I execute "ls"
       I execute "ls" with "-lh"
       I type 1 + 2
       I type 53 - 11
       Today is 2011-06-27

   *** Keywords ***
   I execute "${cmd:[^"]+}"
       Run Process    ${cmd}    shell=True

   I execute "${cmd}" with "${opts}"
       Run Process    ${cmd} ${opts}    shell=True

   I type ${num1:\d+} ${operator:[+-]} ${num2:\d+}
       Calculate    ${num1}    ${operator}    ${num2}

   Today is ${date:\d{4}-\d{2}-\d{2}}
       Log    ${date}

In the above example keyword :name:`I execute "ls" with "-lh"` matches
only :name:`I execute "${cmd}" with "${opts}"`. That is guaranteed
because the custom regular expression `[^"]+` in :name:`I execute
"${cmd:[^"]}"` means that a matching argument cannot contain any
quotes. In this case there is no need to add custom regexps to the
other :name:`I execute` variant.

.. tip:: If you quote arguments, using regular expression `[^"]+`
         guarantees that the argument matches only until the first
         closing quote.

Supported regular expression syntax
'''''''''''''''''''''''''''''''''''

Being implemented with Python, Robot Framework naturally uses Python's
:name:`re` module that has pretty standard `regular expressions
syntax`__. This syntax is otherwise fully supported with embedded
arguments, but regexp extensions in format `(?...)` cannot be
used. If the regular expression syntax is invalid,
creating the keyword fails with an error visible in `test execution
errors`__.

Escaping special characters
'''''''''''''''''''''''''''

Regular expressions use the backslash character (:codesc:`\\`) heavily both
to escape characters that have a special meaning in regexps (e.g. `\$`) and
to form special sequences (e.g. `\d`). Typically in Robot Framework data
backslash characters `need to be escaped`__ with another backslash, but
that is not required in this context. If there is a need to have a literal
backslash in the pattern, then the backslash must be escaped__ like
`${path:c:\\temp\\.*}`.

__ escaping_

Possible lone opening and closing curly braces in the pattern must be escaped
like `${open:\{}` and `${close:\}}`. If there are matching braces like
`${digits:\d{2}}`, escaping is not needed. Escaping only opening or
closing brace is not allowed.

.. note:: Prior to Robot Framework 3.2, it was mandatory to escape all
          closing curly braces in the pattern like `${digits:\d{2\}}`.
          This syntax is unfortunately not supported by Robot Framework 3.2
          or newer and keywords using it must be updated when upgrading.

.. note:: Prior to Robot Framework 5.1, using literal backslashes in the pattern
          required double escaping them like `${path:c:\\\\temp\\\\.*}`.
          Patterns using literal backslashes need to be updated when upgrading.

Using variables with custom embedded argument regular expressions
'''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

When custom embedded argument regular expressions are used, Robot
Framework automatically enhances the specified regexps so that they
match variables in addition to the text matching the pattern.
For example, the following test case would pass
using the keywords from the earlier example.

.. sourcecode:: robotframework

   *** Variables ***
   ${DATE}    2011-06-27

   *** Test Cases ***
   Example
       Today is ${DATE}
       I type ${1} + ${2}

Notice that the actual value of the variable does not need to match the custom
regular expression. This is likely to change in the future, though,
as discussed in `issue #4069`__.

__ http://en.wikipedia.org/wiki/Regular_expression
__ `Embedded arguments matching too much`_
__ http://docs.python.org/library/re.html
__ `Errors and warnings during execution`_
__ Escaping_
__ https://github.com/robotframework/robotframework/issues/4069

Behavior-driven development example
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The biggest benefit of having arguments as part of the keyword name is that it
makes it easier to use higher-level sentence-like keywords when writing test
cases in `behavior-driven style`_. The example below illustrates this. Notice
also that prefixes :name:`Given`, :name:`When` and :name:`Then` are `left out
of the keyword definitions`__.

.. sourcecode:: robotframework

   *** Test Cases ***
   Add two numbers
       Given I have Calculator open
       When I add 2 and 40
       Then result should be 42

   Add negative numbers
       Given I have Calculator open
       When I add 1 and -2
       Then result should be -1

   *** Keywords ***
   I have ${program} open
       Start Program    ${program}

   I add ${number 1} and ${number 2}
       Input Number    ${number 1}
       Push Button     +
       Input Number    ${number 2}
       Push Button     =

   Result should be ${expected}
       ${result} =    Get Result
       Should Be Equal    ${result}    ${expected}

.. note:: Embedded arguments feature in Robot Framework is inspired by
          how *step definitions* are created in a popular BDD tool Cucumber__.

__ `Ignoring Given/When/Then/And/But prefixes`_
__ http://cukes.info

User keyword return values
--------------------------

Similarly as library keywords, also user keywords can return values.
When using Robot Framework 5.0 or newer, the recommended approach is
using the native `RETURN` statement. Old :setting:`[Return]`
setting and BuiltIn_ keywords :name:`Return From Keyword` and
:name:`Return From Keyword If` still work but they will be deprecated
and removed in the future.

Regardless how values are returned, they can be `assigned to variables`__
in test cases and in other user keywords.

__ `Return values from keywords`_

.. _RETURN:

Using `RETURN` statement
~~~~~~~~~~~~~~~~~~~~~~~~

The recommended approach to return values is using the `RETURN` statement.
It accepts optional return values and can be used with IF_ and `inline IF`_
structures. Its usage is easiest explained with examples:

.. sourcecode:: robotframework

   *** Keywords ***
   Return One Value
       [Arguments]    ${arg}
       [Documentation]    Return a value unconditionally.
       ...                Notice that keywords after RETURN are not executed.
       ${value} =    Convert To Upper Case    ${arg}
       RETURN    ${value}
       Fail    Not executed

   Return Three Values
       [Documentation]    Return multiple values.
       RETURN    a    b    c

   Conditional Return
       [Arguments]    ${arg}
       [Documentation]    Return conditionally.
       Log    Before
       IF    ${arg} == 1
           Log    Returning!
           RETURN
       END
       Log    After

   Find Index
       [Arguments]    ${test}    ${items}
       [Documentation]    Advanced example involving FOR loop, inline IF and @{list} variable syntax.
       FOR    ${index}    ${item}    IN ENUMERATE    @{items}
           IF    $item == $test    RETURN    ${index}
       END
       RETURN    ${-1}

If you want to test the above examples yourself, you can use them with these test cases:

.. sourcecode:: robotframework

   *** Settings ***
   Library           String

   *** Test Cases ***
   One return value
       ${ret} =    Return One Value    argument
       Should Be Equal    ${ret}    ARGUMENT

   Multiple return values
       ${a}    ${b}    ${c} =    Return Three Values
       Should Be Equal    ${a}, ${b}, ${c}    a, b, c

   Conditional return
       Conditional Return    1
       Conditional Return    2

   Advanced
       @{list} =    Create List    foo    bar    baz
       ${index} =    Find Index    bar    ${list}
       Should Be Equal    ${index}    ${1}
       ${index} =    Find Index    non existing    ${list}
       Should Be Equal    ${index}    ${-1}


.. note:: `RETURN` syntax is case-sensitive similarly as IF_ and FOR_.

.. note:: `RETURN` is new in Robot Framework 5.0. Use approaches explained
          below if you need to support older versions.

Using :setting:`[Return]` setting
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The :setting:`[Return]` setting defines what the keyword should return after
it has been executed. Although it is recommended to have it at the end of keyword
where it logically belongs, its position does not affect how it is used.

An inherent limitation of the :setting:`[Return]` setting is that cannot be used
conditionally. Thus only the first two earlier `RETURN` statement examples
can be created using it.

.. sourcecode:: robotframework

   *** Keywords ***
   Return One Value
       [Arguments]    ${arg}
       ${value} =    Convert To Upper Case    ${arg}
       [Return]    ${value}

   Return Three Values
       [Return]    a    b    c

.. note:: The :setting:`[Return]` setting is effectively deprecated and the `RETURN`
          statement should be used unless there is a need to support also older
          versions than Robot Framework 5.0. There is no visible deprecation warning
          when using the setting yet, but it will be loudly deprecated and eventually
          removed in the future.

Using special keywords to return
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

BuiltIn_ keywords :name:`Return From Keyword` and :name:`Return From Keyword If`
allow returning from a user keyword conditionally in the middle of the keyword.
Both of them also accept optional return values that are handled exactly like
with the `RETURN` statement and the :setting:`[Return]` setting discussed above.

The introduction of the `RETURN` statement makes these keywords redundant.
Examples below contain same keywords as earlier `RETURN` examples but these
ones are more verbose:

.. sourcecode:: robotframework

   *** Keywords ***
   Return One Value
       [Arguments]    ${arg}
       ${value} =    Convert To Upper Case    ${arg}
       Return From Keyword    ${value}
       Fail    Not executed

   Return Three Values
       Return From Keyword        a    b    c

   Conditional Return
       [Arguments]    ${arg}
       Log    Before
       IF    ${arg} == 1
           Log    Returning!
           Return From Keyword
       END
       Log    After

   Find Index
       [Arguments]    ${test}    ${items}
       FOR    ${index}    ${item}    IN ENUMERATE    @{items}
           Return From Keyword If    $item == $test    ${index}
       END
       Return From Keyword    ${-1}

.. note:: These keywords are effectively deprecated and the `RETURN` statement should be
          used unless there is a need to support also older versions than Robot Framework
          5.0. There is no visible deprecation warning when using these keywords yet, but
          they will be loudly deprecated and eventually removed in the future.

User keyword teardown
---------------------

User keywords may have a teardown defined using :setting:`[Teardown]` setting.

Keyword teardown works much in the same way as a `test case
teardown`__.  Most importantly, the teardown is always a single
keyword, although it can be another user keyword, and it gets executed
also when the user keyword fails. In addition, all steps of the
teardown are executed even if one of them fails. However, a failure in
keyword teardown will fail the test case and subsequent steps in the
test are not run. The name of the keyword to be executed as a teardown
can also be a variable.

.. sourcecode:: robotframework

   *** Keywords ***
   With Teardown
       Do Something
       [Teardown]    Log    keyword teardown

   Using variables
       [Documentation]    Teardown given as variable
       Do Something
       [Teardown]    ${TEARDOWN}

__ `test setup and teardown`_

Private user keywords
---------------------

User keywords can be tagged__ with a special `robot:private` tag to indicate
that they should only be used in the file where they are created:

.. sourcecode:: robotframework

   *** Keywords ***
   Public Keyword
       Private Keyword

   Private Keyword
       [Tags]    robot:private
       No Operation

Using the `robot:private` tag does not outright prevent using the keyword
outside the file where it is created, but such usages will cause a warning.
If there is both a public and a private keyword with the same name,
the public one will be used but also this situation causes a warning.

Private keywords are included in spec files created by Libdoc_ but not in its
HTML output files.

.. note:: Private user keywords are new in Robot Framework 5.1.

__ `User keyword tags`_
