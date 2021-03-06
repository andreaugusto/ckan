=======================
Python Coding Standards
=======================

For Python code style follow `PEP 8`_ plus the guidelines below.

.. _PEP 8: http://www.python.org/dev/peps/pep-0008/

Some good links about Python code style:

- `Python Coding Standards <http://lists.osafoundation.org/pipermail/dev/2003-March/000479.html>`_ from Yahoo
- `Google Python Style Guide <http://google-styleguide.googlecode.com/svn/trunk/pyguide.html>`_


Commit Formatting Cleanups on master
------------------------------------

Clean up formatting and PEP 8 issues on master, not on a feature branch.
Unless of course you're changing that piece of code anyway.  This will help
avoid spurious merge conflicts, and aid in reading pull requests.


Use Single Quotes
-----------------

Use the single-quote character, ``'``, rather than the double-quote character,
``"``, for string literals.


Imports
-------

- Don't use ``from module import *`` or ``from module import name``. Instead
  just ``import module`` and then access names with ``module.name``.
  See `Idioms and Anti-Idioms in Python`_.

  You can make long module names more concise by aliasing them::
  
    import foo.bar.baz as baz

  and then access it with ``baz`` in your code. 

- Make all imports at the start of the file, after the module docstring.
  Imports should be grouped in the following order:

  1. Standard library imports
  2. Third-party imports
  3. CKAN imports

.. _Idioms and Anti-Idioms in Python: http://docs.python.org/2/howto/doanddont.html

Logging
-------

- Keep log messages short.

- Don't include object representations in the log message.  It *is* useful
  to include a domain model identifier where appropriate.

- Choose an appropriate log-level (DEBUG, INFO, ERROR, WARNING or CRITICAL,
  see `Python's Logging HOWTO`_).

.. _Python's Logging HOWTO: http://docs.python.org/2/howto/logging.html

String Formatting
------------------

Don't use the old `%s` style string formatting, e.g. ``"i am a %s" % sub``.
This kind of string formatting is not helpful for internationalization and is
going away in Python 3.

Use the `new .format() method`_ instead, and give meaningful names to each
replacement field, for example::

  _(' ... {foo} ... {bar} ...').format(foo='foo-value', bar='bar-value')

.. _new .format() method: http://docs.python.org/2/library/stdtypes.html#str.format

Docstrings
----------

.. _PEP 257: http://www.python.org/dev/peps/pep-0257/

We want CKAN's docstrings to be clear and easy to read for programmers who are
smart and competent but who may not know a lot of CKAN technical jargon and
whose first language may not be English. We also want it to be easy to maintain
the docstrings and keep them up to date with the actual behaviour of the code
as it changes over time. So:

- All modules and all public functions, classes and methods exported by a
  module should normally have docstrings (see `PEP 257`_).
- Keep docstrings short, describe only what's necessary and no more,
- Keep docstrings simple: use plain, concise English.
- Try to avoid repetition.

PEP 257 (Docstring Conventions)
```````

Generally, follow `PEP 257`_ for docstrings. We'll only describe the ways that
CKAN differs from or extends PEP 257 below.

CKAN docstrings deviate from PEP 257 in a couple of ways:

- We use ``'''triple single quotes'''`` around docstrings, not ``"""triple
  double quotes"""`` (put triple single quotes around one-line docstrings as
  well as multi-line ones, it makes them easier to expand later)
- We use Sphinx directives for documenting parameters, exceptions and return
  values (see below)

Sphinx Field Lists
``````````````````

Use `Sphinx field lists`_ for documenting the parameters, exceptions and
returns of functions:

- Use ``:param`` and ``:type`` to describe each parameter
- Use ``:returns`` and ``:rtype`` to describe each return
- Use ``:raises`` to describe each exception raised

Example of a short docstring:

::

    @property
    def packages(self):
        '''Return a list of all packages that have this tag, sorted by name.

        :rtype: list of ckan.model.package.Package objects

        '''

Example of a longer docstring:

::

    @classmethod
    def search_by_name(cls, search_term, vocab_id_or_name=None):
        '''Return all tags whose names contain a given string.

        By default only free tags (tags which do not belong to any vocabulary)
        are returned. If the optional argument ``vocab_id_or_name`` is given
        then only tags from that vocabulary are returned.

        :param search_term: the string to search for in the tag names
        :type search_term: string
        :param vocab_id_or_name: the id or name of the vocabulary to look in
            (optional, default: None)
        :type vocab_id_or_name: string

        :returns: a list of tags that match the search term
        :rtype: list of ckan.model.tag.Tag objects

        '''


The phrases that follow ``:param foo:``, ``:type foo:``, or ``:returns:``
should not start with capital letters or end with full stops. These should be
short phrases and not full sentences. If more detail is required put it in the
function description instead.

Indicate optional arguments by ending their descriptions with ``(optional)`` in
brackets. Where relevant also indicate the default value: ``(optional, default:
5)``.

.. _Sphinx field lists: http://sphinx.pocoo.org/markup/desc.html#info-field-lists

You can also use a little inline `reStructuredText markup`_ in docstrings, e.g.
``*stars for emphasis*`` or ````double-backticks for literal text````

.. _reStructuredText markup: http://docutils.sourceforge.net/docs/user/rst/quickref.html#inline-markup

.. _Action API Docstrings:

Action API Docstrings
`````````````````````

Docstrings from CKAN's action API are processed with `autodoc`_ and
included in the API chapter of CKAN's documentation. The intended audience of
these docstrings is users of the CKAN API and not (just) CKAN core developers.

In the Python source each API function has the same two arguments (``context``
and ``data_dict``), but the docstrings should document the keys that the
functions read from ``data_dict`` and not ``context`` and ``data_dict``
themselves, as this is what the user has to POST in the JSON dict when calling
the API.

Where practical, it's helpful to give examples of param and return values in
API docstrings.

CKAN datasets used to be called packages and the old name still appears in the
source, e.g. in function names like ``package_list()``. When documenting
functions like this write dataset not package, but the first time you do this
put package after it in brackets to avoid any confusion, e.g.

::

    def package_show(context, data_dict):
        '''Return the metadata of a dataset (package) and its resources.

Example of a ckan.logic.action API docstring:

::

    def vocabulary_create(context, data_dict):
        '''Create a new tag vocabulary.

        You must be a sysadmin to create vocabularies.

        :param name: the name of the new vocabulary, e.g. ``'Genre'``
        :type name: string
        :param tags: the new tags to add to the new vocabulary, for the format of
            tag dictionaries see ``tag_create()``
        :type tags: list of tag dictionaries

        :returns: the newly-created vocabulary
        :rtype: dictionary

        '''

.. _Autodoc: http://sphinx.pocoo.org/ext/autodoc.html


Some Helpful Tools for Python Code Quality
------------------------------------------

There are various tools that can help you to check your Python code for PEP8
conformance and general code quality. We recommend using them.

* `pep8`_ checks your Python code against some of the style conventions in PEP
  8. As mentioned above, only perform style clean-ups on master to help avoid
  spurious merge conflicts.

* `pylint`_ analyzes Python source code looking for bugs and signs of poor
  quality.

* `pyflakes`_ also analyzes Python programs to detect errors.

* `flake8`_ combines both pep8 and pyflakes into a single tool.

* `Syntastic`_ is a Vim plugin with support for flake8, pyflakes and pylint.

.. _pep8: http://pypi.python.org/pypi/pep8
.. _pylint: http://www.logilab.org/857
.. _pyflakes: http://pypi.python.org/pypi/pyflakes
.. _flake8: http://pypi.python.org/pypi/flake8
.. _Syntastic: https://github.com/scrooloose/syntastic
