.. title: Working with Savepoint
.. slug: working-with-savepoint
.. date: 2019-11-21 08:35:18 UTC+01:00
.. tags: 
.. category: 
.. link: 
.. description: 
.. type: text
.. state: draft

Here I'll write about Odoo transactions.

It's a general recommendation to let Odoo manage transactions and Odoo does it 
quite well. 

By default a new transaction is opened on every HTTP request thus covering the whole bunch of 
operations the method executed.

If an exception happends during any method execution the whole transation is 
rolled back and error is returned.

But sometimes we need to keep some amount of work done in the middle of request and if the rest of the request fails we are saved.

This is done by using ``self.env.cr.commit()`` call.

Savepoint
#########

Savepoint defines a new saving point in current transation. So when an error occurs the transation
is rolled back to that point. 

If no error happens ``RELEASE SAVEPOINT`` is called to  keep the effects of commands 
executed after the savepoint was established.

Here is the code snippet from ``odoo/sql_db.py``:

.. code:: python

    @contextmanager
    @check
    def savepoint(self, flush=True):
        """context manager entering in a new savepoint"""
        name = uuid.uuid1().hex
        if flush:
            flush_env(self)
        self.execute('SAVEPOINT "%s"' % name)
        try:
            yield
            if flush:
                flush_env(self)
        except Exception:
            if flush:
                clear_env(self)
            self.execute('ROLLBACK TO SAVEPOINT "%s"' % name)
            raise
        else:
            self.execute('RELEASE SAVEPOINT "%s"' % name)

