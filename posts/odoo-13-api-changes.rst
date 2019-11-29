.. title: Odoo 13 API changes
.. slug: odoo-13-api-changes
.. date: 2019-11-16 12:30:17 UTC+01:00
.. tags: 
.. category: 
.. link: 
.. description: 
.. type: text
.. status: draft

So, Odoo 13.0 is here. And we have to upgrade all our 12.0 modules... Let's go!

In this post I will share my experience in upgrading my addons from 12.0 to 13.0.

@api decorators - EOL
###########################

* @api.multi
* @api.one
* @api.returns
* @api.cr
* @api.model_cr 
* @api.guess
* @api.noguess

all the above decorators are removed from the code. Now every method is multi-record by default.
We have to remove them from the code.

Compute methods
###############
Check that all "compute" methods of non-stored computed fields assign a value 
in any case to the field, even if it is a falsy one. 
(https://github.com/odoo/odoo/pull/36743/commits/2e43bfc1c4b2f61e0459614f61f90a77dc3b7233).

sudo() changes
##############
Replace sudo(user): "deprecated use of sudo(user), use with_user(user) instead"

New field type Image.
#####################
Takes two additional attributes:
* max_width
* max_height
If at least one of max_width / max_height is provided, then the image is resized on create()/write()

Many2oneReference
#################
New field type Many2oneReference is added for cases where the model/ID
(res_model, res_id) pair is stored. Takes one additional attribute:
model_field - name of the field on the current model which stores the model name (res_model).

Uknown fields throw exception
#############################
In previous versions if you have an uknown field in create or write methods Odoo issued
a warning and ignored this field. In 13.0 it now gives an error and fails.

track_visibility changes
########################
This is only applicable if `mail` module is installed.
track_visibility field attribute is replaced with tracking=True|False.
If tracking is an integer, then its value is also used as the sequence 
(replacement of track_sequence).

Font Awesome changes
####################
Some of the Font Awesome (FA) icons have changed their name as now Odoo uses 
FA v5, so you might need to change them in your module views. 
Check the changed names in 
https://fontawesome.com/how-to-use/on-the-web/setup/upgrading-from-version-4#name-changes.

oldname attribute
#################
Remove all the oldname field attributes in the code. If they were added in previous 
version, they have served their function any way, and now in this version it's 
not supported, so if you have the need, create a migration script and use 
openupgradelib's rename_fields method.

Remove view_type tag
####################
Remove view_type tag on action window XML definition. It's now always form
(tree is not supported since 11.0 any way).

multi field in ir.actions.act_window
####################################
Remove multi field from ir.actions.act_window models. 
Now you have binding_view_types field for indicating in which
view the action will be available: list, form or empty for both. 
If declaring the action through the accelerator tag <act_window>, 
then use the attribute binding_views. 
More reference in 
https://github.com/odoo/odoo/pull/24738/commits/33d51480688065e367eb646f12b89d721749cac9.

Archive / Unarchive buttons
###########################
If having an smart-button for active field, with widget toggle_button, 
the archive/unarchive actions are available without doing anything more, 
so you can remove it. And the new paradigm is to put instead a ribbon 
when archived with the code 

.. code:: xml

    <widget
        name="web_ribbon"
        text="Archived"
        bg_color="bg-danger"
        attrs="{'invisible': [('active', '=', True)]}"/>

Testing

``@at_install`` and ``@post_install`` test decorators, which where deprecated in v12,
are removed, @tagged should be used instead, e.g.:

.. code:: python

    # Equivalent to `@at_install(True)`
    @tagged('at_install')

    # Equivalent to `@at_install(False)`
    @tagged('-at_install')

    # Equivalent to `@post_install(True)`
    @tagged('post_install')

    # Equivalent to `@post_install(False)`
    @tagged('-post_install')



Links
#####
* `Odoo 13 features <https://www.odoo.com/forum/help-1/question/odoo-13-features-148369#answer-148370>`_
* https://naglis.me/post/odoo-13-changelog/
* https://github.com/OCA/maintainer-tools/wiki/Migration-to-version-13.0