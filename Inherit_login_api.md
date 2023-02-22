
## Inherit Login Controller
API
```Python
from odoo import http
from odoo.addons.web.controllers.home import Home


class Extension_Home(Home):
    # @http.route()
    @http.route('/web/login', type='http', auth="none")
    def web_login(self, redirect=None, **kw):
        print(kw)
        if 'login' in kw:
            print('Login successful')
        return super(Extension_Home, self).web_login()
```

