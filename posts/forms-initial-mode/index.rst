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

But sometimes it's more usable to immediately open an existing record in edit mode.

This can be done using ``form`` option ``initial_mode``.

In different Odoo versions this can be done in different ways:

10.0
####
In this version it can work in form declaration 
(by chance as `developers say <https://github.com/odoo/odoo/issues/22353>`_)
like in the following example:

.. code:: xml

    <form options="{'initial_mode': 'edit'}">

If transition from tree to form is done within python code:

.. code:: python

    return {
        'name': action.name,
        'help': action.help,
        'type': action.type,
        'views': [(form_id, 'form')],
        'view_mode': 'action.view_mode',
        'target': action.target,
        'context': action.context,
        'res_model': action.res_model,
        'flags': {'initial_mode': 'edit'},
        'res_id': record.id
    }

###############
11.0, 12.0, ...
###############
Next Odoo versions "fix" the above and now you should use ``context``:

.. code:: python

    context = dict(self.env.context)
    context['form_view_initial_mode'] = 'edit'
    return {
        'type': 'ir.actions.act_window',
        'view_type': 'form',
        'view_mode': 'form',
        'res_model': 'your.model',
        'res_id': your_object_id,
        'context': context,
    }

{{% emoji see_no_evil_monkey %}}

