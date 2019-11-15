.. title: New xpath element named "move"
.. slug: new-xpath-element-named-move
.. date: 2019-11-15 14:37:56 UTC+01:00
.. tags: 12.0, xml, views, inherit
.. category: 
.. link: https://github.com/odoo/odoo/pull/23877
.. description: 
.. type: text


The position='move' has been introduced to move an element in an inherited
view.

It's used as

.. code:: xml

    <xpath expr="//@target" position="after">
        <xpath expr="//@node" position="move"/>
    </xpath>

Or also:

.. code:: xml

    <field name="target_field" position="after">
        <field name="my_field" position="move"/>
    </field>

The xpath with a position after, before, inside or replace can thus now
have another xpath as direct child with position move.
