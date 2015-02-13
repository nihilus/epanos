============================
Using the flufl.enum library
============================

The ``flufl.enum`` package provides an enumeration data type for Python.
The specification for this package resides in `PEP 435`_ and is available in
`Python 3.4`_. ``flufl.enum`` is made available for compatibility with older
versions of Python, including Python 2.7, 3.2, and 3.3.

An enumeration is a set of symbolic names bound to unique, constant values.
Within an enumeration, the values can be compared by identity, and the
enumeration itself can be iterated over.  The underlying values can be
retrieved from the enumeration items.  An integer based variant is provided
which allows items to be used as slices, to interoperate with C-based APIs,
and for logical operations.


Motivation
==========

[Lifted from `PEP 354`_ - the original rejected enumeration PEP]

The properties of an enumeration are useful for defining an immutable, related
set of constant values that have a defined sequence but no inherent semantic
meaning.  Classic examples are days of the week (Sunday through Saturday) and
school assessment grades ('A' through 'D', and 'F').  Other examples include
error status values and states within a defined process.

It is possible to simply define a sequence of values of some other basic type,
such as ``int`` or ``str``, to represent discrete arbitrary values.  However,
an enumeration ensures that such values are distinct from any others, and that
operations without meaning ("Wednesday times two") are not defined for these
values.


Creating an Enumeration
=======================

Class syntax
------------

Enumerations can be created using the class syntax, which makes them easy to
read and write.  Every enumeration value must have a unique value and the only
restriction on their names is that they must be valid Python identifiers.  To
define an enumeration, derive from the ``Enum`` class and add attributes with
assignment to their values.  Values may not be duplicated.
::

    >>> from flufl.enum import Enum
    >>> class Colors(Enum):
    ...     red = 1
    ...     green = 2
    ...     blue = 3

The enumeration values have nice, human readable string representations.

    >>> print(Colors.red)
    Colors.red

The ``reprs`` have additional detail.

    >>> print(repr(Colors.red))
    <EnumValue: Colors.red [value=1]>


Integer Enumerations
--------------------

A special subclass of ``Enum`` can be used when the enumeration values need to
act like integers.  In fact, the values in this ``IntEnum`` class *are*
integers and can be used any place an integer needs to be used, including when
interfacing with C APIs.
::

    >>> from flufl.enum import IntEnum
    >>> class Animals(IntEnum):
    ...     ant = 1
    ...     bee = 2
    ...     cat = 3

These enumeration values can be converted to integers.

    >>> int(Animals.bee)
    2

These enumeration values can also be used as slice indexes.

    >>> list(range(10)[Animals.ant:Animals.cat])
    [1, 2]


Convenience API
---------------

For convenience, you can create an enumeration by calling the ``Enum`` class.
The first argument is the name of the new enumeration, and the second is
provides the enumeration values.  There are several ways to specify the values
-- see the section `Functional API`_ for details -- but the easiest way is to
provide a string of space separated attribute names.  The values are
auto-assigned integers starting from 1.

    >>> Rush = Enum('Rush', 'geddy alex neil')

The ``str`` and ``repr`` provide details.

    >>> print(Rush.geddy)
    Rush.geddy
    >>> print(repr(Rush.geddy))
    <EnumValue: Rush.geddy [value=1]>

See the section on the `Functional API`_ for more options and information.


Values
------

Enumeration items can have any value you choose, but typically they will be
integer or string values.
::

    >>> class Rush(Enum):
    ...     geddy = 'bass'
    ...     alex = 'guitar'
    ...     neil = 'drums'

    >>> print(repr(Rush.alex))
    <EnumValue: Rush.alex [value=guitar]>


Inspecting Enumerations
=======================

The enumeration value names are available through the class members.

    >>> for member in Colors.__members__:
    ...     print(member)
    red
    green
    blue

The str and repr of the enumeration class also provides useful information.

    >>> print(Colors)
    <Colors {red: 1, green: 2, blue: 3}>
    >>> print(repr(Colors))
    <Colors {red: 1, green: 2, blue: 3}>

Let's say you wanted to encode an enumeration value in a database.  You might
want to get the enumeration class object from an enumeration value.

    >>> cls = Colors.red.enum
    >>> print(cls.__name__)
    Colors

Enumerations also have a property that contains just their item name.

    >>> print(Colors.red.name)
    red
    >>> print(Colors.green.name)
    green
    >>> print(Colors.blue.name)
    blue

The underlying item value can also be retrieved via the ``.value`` attribute.

    >>> print(Rush.geddy.value)
    bass

Integer enumerations can also be explicitly convert to their integer value
using the ``int()`` built-in.

    >>> int(Animals.ant)
    1
    >>> int(Animals.bee)
    2
    >>> int(Animals.cat)
    3


Comparison
==========

Enumeration values are compared by identity.

    >>> Colors.red is Colors.red
    True
    >>> Colors.blue is Colors.blue
    True
    >>> Colors.red is not Colors.blue
    True
    >>> Colors.blue is Colors.red
    False


Standard Enumerations
---------------------

The standard ``Enum`` class does not do comparisons against the integer
equivalent values, because if you define an enumeration with similar item
names and integer values, they will not be identical.

    >>> class OtherColors(Enum):
    ...     red = 1
    ...     blue = 2
    ...     yellow = 3
    >>> Colors.red is OtherColors.red
    False
    >>> Colors.blue is not OtherColors.blue
    True

These enumeration values are not equal, nor do they hash equally.

    >>> Colors.red == OtherColors.red
    False
    >>> len(set((Colors.red, OtherColors.red)))
    2

Ordered comparisons between enumeration values are *not* supported.  The base
enumeration values are not integers!

    >>> Colors.red < Colors.blue
    Traceback (most recent call last):
    ...
    NotImplementedError
    >>> Colors.red <= Colors.blue
    Traceback (most recent call last):
    ...
    NotImplementedError
    >>> Colors.blue > Colors.green
    Traceback (most recent call last):
    ...
    NotImplementedError
    >>> Colors.blue >= Colors.green
    Traceback (most recent call last):
    ...
    NotImplementedError

Equality comparisons are defined though.

    >>> Colors.blue == Colors.blue
    True
    >>> Colors.green != Colors.blue
    True

Enumeration values do not support ordered comparisons.

    >>> Colors.red < Colors.blue
    Traceback (most recent call last):
    ...
    NotImplementedError
    >>> Colors.red < 3
    Traceback (most recent call last):
    ...
    NotImplementedError
    >>> Colors.red <= 3
    Traceback (most recent call last):
    ...
    NotImplementedError
    >>> Colors.blue > 2
    Traceback (most recent call last):
    ...
    NotImplementedError
    >>> Colors.blue >= 2
    Traceback (most recent call last):
    ...
    NotImplementedError

While equality comparisons are allowed, comparisons against non-enumeration
values will always compare not equal.

    >>> Colors.green == 2
    False
    >>> Colors.blue == 3
    False
    >>> Colors.green != 3
    True
    >>> Colors.green == 'green'
    False


Integer enumerations
--------------------

With the special ``IntEnum`` class though, enumeration values *are* integers,
so all the ordered comparisons work as expected.

    >>> Animals.ant < Animals.bee
    True
    >>> Animals.cat > Animals.ant
    True

Comparisons against other numbers also work as expected.

    >>> Animals.ant <= 1.0
    True
    >>> Animals.bee == 2
    True

You can even compare integer enumeration values against other unrelated
integer values, since the comparisons use the standard integer operators.
::

    >>> class Toppings(IntEnum):
    ...     anchovies = 1
    ...     black_olives = 2
    ...     cheese = 4
    ...     dried_tomatoes = 8
    ...     eggplant = 16

    >>> Toppings.black_olives == Animals.bee
    True


Conversions
===========

You can convert back to the enumeration item by using the ``Enum`` class's
``getitem`` syntax, passing in the value for the item you want.

    >>> Colors[2]
    <EnumValue: Colors.green [value=2]>
    >>> Rush['bass']
    <EnumValue: Rush.geddy [value=bass]>
    >>> Colors[1] is Colors.red
    True

The ``Enum`` class also accepts the string name of the enumeration value.

    >>> Colors['red']
    <EnumValue: Colors.red [value=1]>
    >>> Rush['alex']
    <EnumValue: Rush.alex [value=guitar]>
    >>> Colors['blue'] is Colors.blue
    True

For consistency, ``getitem`` syntax accept an enumeration value.

    >>> Colors[Colors.green]
    <EnumValue: Colors.green [value=2]>


Iteration
=========

The ``Enum`` class support iteration.  Enumeration values are returned in the
sorted order of their equivalent values.

    >>> [v.name for v in Colors]
    ['red', 'green', 'blue']
    >>> [v.value for v in Colors]
    [1, 2, 3]
    >>> [v.name for v in Rush]
    ['geddy', 'neil', 'alex']
    >>> for v in Rush:
    ...     print(v.value)
    bass
    drums
    guitar

Enumeration values are hashable, so they can be used in dictionaries and sets.

    >>> from operator import attrgetter
    >>> getvalue = attrgetter('value')
    >>> apples = {}
    >>> apples[Colors.red] = 'red delicious'
    >>> apples[Colors.green] = 'granny smith'
    >>> for color in sorted(apples, key=getvalue):
    ...     print(color.name, '->', apples[color])
    red -> red delicious
    green -> granny smith


Extending an enumeration through subclassing
============================================

You can extend previously defined enumerations by subclassing.  Just as
before, values cannot be duplicated in either the base class or subclass.

    >>> class MoreColors(Colors):
    ...     pink = 4
    ...     cyan = 5

When extended in this way, the base enumeration's values are identical to the
same named values in the derived class.

    >>> Colors.red is MoreColors.red
    True
    >>> Colors.blue is MoreColors.blue
    True


Pickling
========

Enumerations created with the class syntax can also be pickled and unpickled:

    >>> from flufl.enum.tests.fruit import Fruit
    >>> from pickle import dumps, loads
    >>> Fruit.tomato is loads(dumps(Fruit.tomato))
    True


Functional API
==============

As described above, you can create enumerations functionally by calling
``Enum`` or ``IntEnum``.

The first argument is always the name of the new enumeration.  The second
argument describes the enumeration value names and values.  As mentioned
previously, the easiest way to create new enumerations is to provide a single
string with space-separated attribute names.  In this case, the values are
auto-assigned integers starting from 1.

    >>> Enum('Animals', 'ant bee cat dog')
    <Animals {ant: 1, bee: 2, cat: 3, dog: 4}>

The second argument can also be a sequence of strings.  In this case too, the
values are auto-assigned integers starting from 1.

    >>> Enum('People', ('anne', 'bart', 'cate', 'dave'))
    <People {anne: 1, bart: 2, cate: 3, dave: 4}>

The items in source can also be 2-tuples, where the first item is the
enumeration value name and the second is the value to use.  If 2-tuples are
given, all items must be 2-tuples.

    >>> def enumiter():
    ...     start = 1
    ...     while True:
    ...         yield start
    ...         start <<= 1
    >>> Enum('Flags', zip(list('abcdefg'), enumiter()))
    <Flags {a: 1, b: 2, c: 4, d: 8, e: 16, f: 32, g: 64}>

Since values for the ``Enum`` type need not be integers, you can also provide
as the second argument a dictionary mapping names to values.  Remember that
the ``repr`` is sorted by value.

    >>> bassists = dict(geddy='rush', chris='yes', flea='rhcp', jack='cream')
    >>> Enum('Bassists', bassists)
    <Bassists {jack: cream, flea: rhcp, geddy: rush, chris: yes}>

If you want to create an ``IntEnum`` where the values are integer subclasses,
call that class instead.  This has the same signature as calling ``Enum`` but
the items of the returned enumeration are int subclasses.

    >>> Numbers = IntEnum('Numbers', 'one two three four'.split())
    >>> Numbers.three == 3
    True


Differences from PEP 354
========================

Unlike PEP 354, enumeration values are not defined as a sequence of strings,
but as attributes of a class.  This design was chosen because it was felt that
class syntax is more readable.

Unlike PEP 354, enumeration values require an explicit integer value.  This
difference recognizes that enumerations often represent real-world values, or
must interoperate with external real-world systems.  For example, to store an
enumeration in a database, it is better to convert it to an integer on the way
in and back to an enumeration on the way out.  Providing an integer value also
provides an explicit ordering.  However, there is no automatic conversion to
and from the integer values, because explicit is better than implicit.

Unlike PEP 354, this implementation does use a metaclass to define the
enumeration's syntax, and allows for extended base-enumerations so that the
common values in derived classes are identical (a singleton model).  While PEP
354 dismisses this approach for its complexity, in practice any perceived
complexity, though minimal, is hidden from users of the enumeration.

Unlike PEP 354, enumeration values can only be tested by identity comparison.
This is to emphasis the fact that enumeration values are singletons, much like
``None``.


Acknowledgments
===============

The ``flufl.enum`` implementation is based on an example by Jeremy Hylton.  It
has been modified and extended by Barry Warsaw for use in the `GNU Mailman`_
project.  Ben Finney is the author of the earlier enumeration PEP 354.  Eli
Bendersky is the co-author of PEP 435.  Numerous people on the `python-ideas`_
and `python-dev`_ mailing lists have provided valuable feedback.


.. _`PEP 435`: http://www.python.org/dev/peps/pep-0435/
.. _`Python 3.4`: http://www.python.org/dev/peps/pep-0429/
.. _`PEP 354`: http://www.python.org/dev/peps/pep-0354/
.. _enum: http://cheeseshop.python.org/pypi/enum/
.. _`GNU Mailman`: http://www.list.org
.. _`python-ideas`: http://mail.python.org/mailman/listinfo/python-ideas
.. _`python-dev`: http://mail.python.org/mailman/listinfo/python-dev
