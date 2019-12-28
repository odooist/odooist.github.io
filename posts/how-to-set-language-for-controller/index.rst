.. title: How to set language for controller
.. slug: how-to-set-language-for-controller
.. date: 2019-12-28 12:25:16 UTC+01:00
.. tags: controller,i18n
.. category: 
.. link: 
.. description: 
.. type: text

Imagine that you have a signup procedure, like this:

.. code:: python

  class MobileAppBaseController(http.Controller):
    @http.route('/barrier/verify_user/1', type='json', auth='public',
                methods=['POST'], csrf=False)
    def verify_user_step_1(self):
        user_name = http.request.jsonrequest.get('username')
        if not user_name:
            return {'status': False, 'msg': _('User name not sent.')}
        ...

When user name is not specified in JSON request it returns 'User name not sent' message.

Now we want this message to be returned in different languages.

After learning fron *odoo/tools/translation.py* and *odoo/http.py* two solutions where found.

1. Language selection by client
-------------------------------
To request messages translated into specified language Accept-Language header is used:

.. code:: sh

  http -vv --json 'http://127.0.0.1:38069/barrier/verify_user/1' "_username=user" 'Accept-Language:ru'
  
Result:

.. code:: sh

  odooist@MacBook-Pro app % http -v --json 'http://127.0.0.1:38069/barrier/verify_user/1' "_username=user" 'Accept-Language:ru'
  POST /barrier/verify_user/1 HTTP/1.1
  Accept: application/json, */*
  Accept-Encoding: gzip, deflate
  Accept-Language: ru
  Connection: keep-alive
  Content-Length: 21
  Content-Type: application/json
  Host: 127.0.0.1:38069
  User-Agent: HTTPie/1.0.3
   
   {
      "_username": "user"
   }

  HTTP/1.0 200 OK
  Content-Length: 224
  Content-Type: application/json
  Date: Sat, 28 Dec 2019 11:33:49 GMT
  Server: Werkzeug/0.16.0 Python/3.5.3
  Set-Cookie: session_id=7b62bc401c248052e8a0ffb7626b7122340f774a; Expires=Fri, 27-Mar-2020 11:33:49 GMT; Max-Age=7776000; HttpOnly; Path=/

  {
    "id": null,
    "jsonrpc": "2.0",
    "result": {
        "msg": "Имя пользователя не отослано.",
        "status": false
    }
  }

As you can see in request headers Accept-Language is set to *ru*, and in result we got msg translated.

2. Define response language at server
-------------------------------------
In *odoo/tools/translation.py* interesting code was found:

.. code:: python   

    def _get_lang(self, frame):
        # try, in order: context.get('lang'), kwargs['context'].get('lang'),
        # self.env.lang, self.localcontext.get('lang'), request.env.lang
        lang = None
        if frame.f_locals.get('context'):
            lang = frame.f_locals['context'].get('lang')
        if not lang:
            kwargs = frame.f_locals.get('kwargs', {})
            if kwargs.get('context'):
                lang = kwargs['context'].get('lang')
        if not lang:
            s = frame.f_locals.get('self')
            if hasattr(s, 'env'):
                lang = s.env.lang
            if not lang:
                if hasattr(s, 'localcontext'):
                    lang = s.localcontext.get('lang')
            if not lang:
                try:
                    from odoo.http import request
                    lang = request.env.lang
                except RuntimeError:
                    pass
            if not lang:
                # Last resort: attempt to guess the language of the user
                # Pitfall: some operations are performed in sudo mode, and we
                #          don't know the original uid, so the language may
                #          be wrong when the admin language differs.
                (cr, dummy) = self._get_cr(frame, allow_create=False)
                uid = self._get_uid(frame)
                if cr and uid:
                    env = odoo.api.Environment(cr, uid, {})
                    lang = env['res.users'].context_get()['lang']
        return lang


So to define server response language we had to put a context dictionary on a 
function level like this (line 5):

.. code:: python
   :linenos:

      class MobileAppBaseController(http.Controller):
        @http.route('/barrier/verify_user/1', type='json', auth='public',
                    methods=['POST'], csrf=False)
        def verify_user_step_1(self):
            context = {'language': 'ru_RU'}
            user_name = http.request.jsonrequest.get('username')
            if not user_name:
                return {'status': False, 'msg': _('User name not sent.')}


**Notice** that in the first case we added *Accept-Language: ru*, but in the second one we 
set language to *ru_RU*.


