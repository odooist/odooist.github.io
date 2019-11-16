.. title: Начальный режим формы
.. slug: forms-initial-mode
.. date: 2019-11-16 04:21:21 UTC+01:00
.. tags: views
.. category: 
.. link: 
.. description: 
.. type: text

Когда Вы кликаете строчку в списочном виде (tree view), открывается вид формы в режиме 
чтения.

Когда в виде списка Вы кликаете в кнопку ``Создать``,  открывается форма создания
новой записи в режиме редактирования (а как иначе? :-)

Но иногда с точки зрения юзабилити или по другим причинам необходимо сделать так,
чтобы при переходе из вида списка в вид формы последняя открылась сразу в режиме редактирования.

Делается это при помощи опции формы ``initial_mode``. Возможные значения: ``view`` или ``edit``.

Данная опция доступна во всех вериях Odoo, но в разных версиях работает по-разному.

####
10.0
####

В данной версии это работает при декларации формы 
(и по `словам разработчиков <https://github.com/odoo/odoo/issues/22353>`_, это случайно), пример:

.. code:: xml

    <form options="{'initial_mode': 'edit'}">

Если переход в другую форму делается из кода, то используется такая форма:

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

В следующих после 10.0 версиях Odoo необходимо использовать ``context``. Например:

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
