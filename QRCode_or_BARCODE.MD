
## QR Code on Form View

Python
```Python
import qrcode
import base64
from io import BytesIO

qr_code = fields.Binary("QR Code", attachment=True, compute='_compute_generate_qr_code')

@api.depends('name')
    def _compute_generate_qr_code(self):
        qr = qrcode.QRCode(
            version=1,
            error_correction=qrcode.constants.ERROR_CORRECT_L,
            box_size=13,
            border=4,
        )
        qr.add_data(self.name)
        qr.make(fit=True)
        img = qr.make_image()
        temp = BytesIO()
        img.save(temp, format="PNG")
        qr_image = base64.b64encode(temp.getvalue())
        self.qr_code = qr_image
```

XML
```XML
<record id="purchase_order_form_inherited" model="ir.ui.view">
            <field name="name">purchase.order.form.inherited</field>
            <field name="model">purchase.order</field>
            <field name="inherit_id" ref="purchase.purchase_order_form"/>
            <field name="arch" type="xml">
                <xpath expr="//field[@name='currency_id']" position="after">
                    <field name="qr_code" widget="image" options="{'zoom': true, 'max_width': 100, 'max_height': 100}" invisible="1"/>
                </xpath>
            </field>
        </record>
```

## QR Code on QWEB Report
```XML
<img t-if="o.qr_code" style="display:block; height:150px; width:150px;" t-att-src="image_data_uri(o.qr_code)" alt="o.name"/>
```

### ------------------------------------ Shortcut Method ------------------------------------

QWEB Report QR Code
```XML
<img t-att-src="'/report/barcode/QR/'+o.name" style="height:150px; width:150px;" alt="QR Code"/>
```

QWEB Report Bar Code
```XML
<img t-att-src="'/report/barcode/Code128/'+o.name" style="height:150px; width:150px;" alt="QR Code"/>
```
    
