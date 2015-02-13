===================
NEWS for flufl.enum
===================

4.0.1 (2014-06-11)
==================
 * Include MANIFEST.in and tox.ini in the sdist tarball, otherwise the Debian
   package won't built correctly.
 * Drop use of distribute.
 * Narrow tox supported environments.
 * Bump copyright years.


4.0 (2013-04-05)
================
 * Fix documentation bugs.  (LP: #1026403, LP: #1132830)
 * Deprecate ``EnumValue.__int__()``; use ``IntEnumValue`` (via ``IntEnum``)
   instead.
 * Add ``IntEnum`` class which returns int-subclass enum values. (LP: #1132976)
   - Add ``__index__()`` method to support slicing.  (LP: #1132972)
   - Add non-deprecated ``__int__()`` method.
 * Deprecate ``make()``; use ``Enum()`` instead.
   - Call ``IntEnum()`` to create integer valued enums.  (LP: #1162375)
   - Accept a space-separate string of enum values which are auto-split.
   - Accept a dictionary of enumeration name/value pairs.
 * Add ``.value`` attribute to enum values.  (LP: #1132859)
 * For ``__getitem__()`` and ``__call__()``, fall back to using the ``.value``
   attribute if the argument has one. (LP: #1124596)
 * Previously deprecated APIs ``EnumValue.enumclass``, ``EnumValue.enumname``,
   and ``enum.make_enum()`` are removed.  (LP: #1132951)
 * Small change to the ``repr`` of enum values; they now say "value=" instead
   of "int=".
 * Multiple enum values now raise a `ValueError` instead of a `TypeError`.


3.3.2 (2012-04-19)
==================
 * Add classifiers to setup.py and make the long description more compatible
   with the Cheeseshop.
 * Other changes to make the Cheeseshop page look nicer.  (LP: #680136)
 * setup_helper.py version 2.1.


3.3.1 (2012-01-19)
==================
 * Fix Python 3 compatibility with Sphinx's conf.py ($python setup.py install).


3.3 (2012-01-19)
================
 * Remove the dependency on 2to3 for Python 3 support; support Python 3
   directly with a single code base.
 * flufl.enum.make_enum() is deprecated in favor of flufl.enum.make() which
   provides a better API.  (LP: #839529)
 * Updated to distribute 0.6.19.
 * Moved all documentation to .rst suffix.
 * Make test_deprecations() compatible with Python 3 and Python 2.
 * Removed markup for pylint.
 * Improve documentation to illustrate that enum values with similar names and
   integer representations still do not hash equally.  (Found by Jeroen
   Vermeulen).


3.2 (2011-08-19)
================
 * make_enum() accepts an optional `iterable` argument to provide the values
   for the enums.
 * The .enumclass and .enumname attributes are deprecated.  Use .enum and
   .name instead, respectively.
 * Improve the documentation regarding ordered comparisons and equality
   tests.  (LP: #794853)
 * make_enum() now enforces the use of valid Python identifiers. (LP: #803570)


3.1 (2011-03-01)
================
 * New convenience function `make_enum()`. (Contributed by Michael Foord)
 * Fix `from flufl.enum import *`.
 * Enums created with the class syntax can be pickled and unpickled.
   (Suggestion and basic implementation idea by Phillip Eby).


3.0.1 (2010-06-07)
==================
 * Fixed typo which caused the package to break.


3.0 (2010-04-24)
================
 * Package renamed to flufl.enum.


2.0.2 (2010-01-29)
==================
 * Fixed some test failures when running under 2to3.


2.0.1 (2010-01-08)
==================
 * Fix the manifest and clarify license.


2.0 (2010-01-07)
================
 * Use Sphinx to build the documentation.
 * Updates to better package Debian/Ubuntu.
 * Use distribute_setup instead of ez_setup.
 * Rename pep-xxxx.txt; this won't be submitted as a PEP.
 * Remove dependencies on nose and setuptools_bzr
 * Support Python 3 via 2to3.


Earlier
=======

Try `bzr log lp:flufl.enum` for details.
