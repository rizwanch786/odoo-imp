

## Dynamic URL

```Python
public_url = fields.Char('Public Image', compute='_create_url', help='This is the link for the image or file')

def _create_url(self):
    for record in self:
        base_url = self.env['ir.config_parameter'].get_param('web.base.url')
        record.public_url = base_url + '/web/content/' + str(record.id) + '/example.png'

```
