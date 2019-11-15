.. title: Hiding entire tree column with column_invisible
.. slug: hiding-entire-tree-column-with-column_invisible
.. date: 2019-11-15 12:15:55 UTC+01:00
.. tags: 12.0, 11.0, views
.. category: 
.. link: 
.. description: 
.. type: text

We all know a way to hide a field in tree & form views using ``attrs={'invisible': [(...)]}``.

We also know that we can hide **entire column** using ``invisible="1"`` attribute like this:

.. code:: xml

    <tree decoration-warning="event_input_mode == True">
        <field name="event_input_mode" invisible="1"/>
        <field name="description"/>
        <field name="facility" invisible="context.get('hide_facility')"/>
        <field name="uid"/>
    </tree>


In the above example we need ``event_input_mode`` column just to highlight a row if
it have ``event_input_mode`` set so we hide it with ``invisible="1"``.

We can also notice that we can use ``context`` values in condition expression. 
In the above example if ``context`` has ``hide_facility`` key then entire column is hidden.

The treasure
############
But what if you need to hide entire column by a condition coming from complex business
logic implemented in Python?

Here **column_invisible** attribute comes!

See this examples:

.. code:: xml

    <tree>
        ...
        <field name="foo" attrs="{'column_invisible': [('parent.product_id', '!=', False)]}"/>
        ...
    </tree>

By digging the source code I can see ``column_invisible`` was introduced in Odoo 11.0.

Nice, but works **only** for ``many2one`` and ``one2many`` fields :-)

