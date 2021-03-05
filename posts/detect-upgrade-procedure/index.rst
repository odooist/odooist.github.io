.. title: Detect upgrade procedure
.. slug: detect-upgrade-procedure
.. date: 2021-01-10 13:00:27 UTC+01:00
.. tags: xml,upgrade
.. category: 
.. link: 
.. description: Conditional upgrades.
.. type: text

In Odoo it is possible to define model records in XML data files.

During module upgrade procedure Odoo reads XML datafiles and updates database records defined there.

But what if we need to have a conditional update? So that we do not update the records if 
some condition is not met.

To have this done we need to redefine *write* method for the model and put there the following:

.. code:: python

    def write(self, vals):
        for rec in self:
            if self.env.context.get('install_mode') or self.env.context.get(
                    'module') == 'your_module_name':
                logger.info('We are in upgrade procedure!')
                # Check a condition
                if False:
                    logger.info('Ignoring updated record.')
                else:
                    super(MyCustomModel, rec).write(vals)
        return True
