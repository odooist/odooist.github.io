.. title: Form's initial mode
.. slug: forms-initial-mode
.. date: 2019-11-16 04:21:21 UTC+01:00
.. tags: views
.. category: 
.. link: 
.. description: 
.. type: text

When you click on a tree view row a form view is opened and this form is opened
in read mode.

When you click the ``Create`` button from a tree view, a new record form view is opened
in edit mode (how otherwise? :-)

But sometimes it's more usable to open an existing immediately record in edit mode.

This can be done using ``form`` option ``initial_mode`` like is shown in the following example: 

.. code:: xml

    <form options="{'initial_mode': 'view'}">

This option is available in all Odoo versions.
