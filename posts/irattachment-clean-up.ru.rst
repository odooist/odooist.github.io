.. title: ir.attachment clean up
.. slug: ir-attachment-clean-up
.. date: 2019-11-15 05:57:50 UTC+01:00
.. tags: ir.attachment, maintainance
.. category: 
.. link: 
.. description: 
.. type: text
.. status: draft

Как вы знаете.
attachments. 

And sometimes when you delete 

.. code:: python

    from datetime import datetime
    import filetype
    import os


    names = ['picture1', 'picture1_small', 'picture2', 'picture2_small',
             'picture3', 'picture3_small']
    root = '/var/lib/odoo/filestore/barrier'

    records = env['ir.attachment'].search([('res_field', '!=', False)])


    def get_data():
        data = []
        count = len(records)
        for i in range(0, count, 1000):
            limit = i + 1000
            if limit > count:
                limit = count
            data.extend(
                records[i:limit].read(['store_fname', 'name', 'create_date']))
        res = {}
        for item in data:
            res[item['store_fname']] = {'name': item['name'],
                                        'create_date': item['create_date']}
        return data



    def c():
        i = 0
        for folder in os.listdir(root):
            subfolder = os.path.join(root, folder)
            for f in os.listdir(subfolder):
                f_path = os.path.join(root, subfolder, f)
                f_name = os.path.join(folder, f)
                f_stat = os.stat(f_path)
                m_dt = datetime.utcfromtimestamp(f_stat.st_mtime)
                now = datetime.utcnow()
                if (now - m_dt).days > 30:
                    #print('Checking name: ', f_name)
                    if f_name not in data2.keys():
                        print('File not found ', f_name)
                    else:
                        pass
                        #print('Found file name: ', f_name)
                else:
                    pass
                    #print('Ommit fresh file ', f_name)

