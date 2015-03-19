# Building a new release tarball #

There are only a couple of simple steps necessary in building a new release of Mox.

0.  Make sure all of the tests pass
```
$ python mox_test.py
$ python stubout_test.py
```

1.  Increment the version number in the `setup.py` file
```
#!/usr/bin/python2.4

from distutils.core import setup

setup(name='mox',
      version='0.5.1',                           # <<<<<<<< This line.
      py_modules=['mox', 'stubout'],
      url='http://code.google.com/p/pymox/',
      maintainer='pymox maintainers',
      maintainer_email='mox-discuss@googlegroups.com',
      license='Apache License, Version 2.0',
      description='Mock object framework',
      long_description='''Mox is a mock object framework for Python based on the
Java mock object framework EasyMock.''',
      )
```

2.  Ensure the `MANIFEST.in` template contains all the necessary non-module files, like new test files, etc.

3.  Submit the changes

4.  Build a tarball with the command:
```
python setup.py sdist
```

5.  Upload the resulting tarball (in `pymox/dist`) to the Downloads page.

6.  Relax.