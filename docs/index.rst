.. Copyright 2017 pymox authors. All rights reserved.
   Use of this source code is governed by a APACHE-style
   license that can be found in the LICENSE file.

Pymox
=================================

Pymox is an open source mock object framework for Python.

.. toctree::
   :maxdepth: 2
   :caption: Contents:


Basic usage
-----------

.. highlight:: python

::

    import mox
    import unittest


    class PersonTest(mox.MoxTestBase):

        def testUsingMox(self):
            # Create a mock Person
            mock_person = self.mox.CreateMock(Person)

            test_person = ...
            test_primary_key = ...
            unknown_person = ...

            # Expect InsertPerson to be called with test_person; return
            # test_primary_key at that point
            mock_person.InsertPerson(test_person).AndReturn(test_primary_key)

            # Raise an exception when this is called
            mock_person.DeletePerson(unknown_person).AndRaise(UnknownPersonError())

            # Switch from record mode to replay mode
            self.mox.ReplayAll()

            # Run the test
            ret_pk = mock_person.InsertPerson(test_person)
            self.assertEquals(test_primary_key, ret_pk)
            self.assertRaises(UnknownPersonError, mock_person, unknown_person)


Getting started
---------------
* :doc:`Why use Pymox </why>`
* :doc:`Installation </install>`
* :doc:`Quick tutorial </tutorial>`
* :doc:`Reference </reference>`


Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
