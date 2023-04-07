# Make Odoo Easy
[@rizwanch786](https://www.github.com/rizwanch786)


# Domain for Customer/Vendor


## Model

```Python
from odoo import models, fields, api


class AccountMove(models.Model):
    _inherit = 'account.move'

    partner_ids = fields.Many2many('res.partner', compute="_compute_partner_ids")

    @api.depends('partner_id')
    def _compute_partner_ids(self):
        for rec in self:
            if rec.move_type == 'out_invoice':
                record = self.env['res.partner'].search([('customer_rank', '>', 0)])
                if record:
                    rec.partner_ids = record
                else:
                    rec.partner_ids = False
            elif rec.move_type == 'in_invoice':
                record = self.env['res.partner'].search([('supplier_rank', '>', 0)])
                if record:
                    rec.partner_ids = record
                else:
                    rec.partner_ids = False

```

## Views

```XML
<odoo>
    <data>
        <record id="view_move_form_inherited" model="ir.ui.view">
            <field name="name">account.move.form.inherited</field>
            <field name="model">account.move</field>
            <field name="inherit_id" ref="account.view_move_form"/>
            <field name="arch" type="xml">
              <xpath expr="//field[@name='partner_id']" position="after">
                <field name="partner_ids" widget="many2many_tags" invisible="1"/>
              </xpath>
                <xpath expr="//field[@name='partner_id']" position="attributes">
                    <attribute name="domain">[('id', 'in', partner_ids)]</attribute>
                </xpath>
            </field>
        </record>
    </data>
</odoo>

```



# Notification

```Python
return {
        'type': 'ir.actions.client',
        'tag': 'display_notification',
        'params': {
                    'title': ('CR Date Expired'),
                    'message': 'CR date has been expired',
                    'type': 'danger',  # types: success,warning,danger,info
                    'sticky': True,  # True/False will display for few seconds if false
                },
            }

```




# Dynamic URL

```Python
public_url = fields.Char('Public Image', compute='_create_url', help='This is the link for the image or file')

def _create_url(self):
    for record in self:
        base_url = self.env['ir.config_parameter'].get_param('web.base.url')
        record.public_url = base_url + '/web/content/' + str(record.id) + '/example.png'

```



## Invisible One2many column

```XML
<odoo>
    <data>
        <record id="view_order_form_inherited" model="ir.ui.view">
            <field name="name">sale.order.form.inherited</field>
            <field name="model">sale.order</field>
            <field name="inherit_id" ref="sale.view_order_form"/>
            <field name="arch" type="xml">
                <xpath expr="//header" position="inside">
                    <button
                            name="%(action_view_account_voucher_wizard)d"
                            string="Token Money"
                            type="action"
                            attrs="{'invisible': ['|',('state', 'in', ['done','cancel']),('amount_residual', '=', 0)]}"
                    />
                </xpath>
                <field name="tax_totals_json" position="after">
                    <div class="oe_subtotal_footer_separator oe_inline o_td_label">
                        <label for="amount_residual"/>
                    </div>
                    <field
                            name="amount_residual"
                            nolabel="1"
                            class="oe_subtotal_footer_separator"
                            widget="monetary"
                            options="{'currency_field': 'currency_id'}"
                    />
                </field>
                <notebook position="inside">
                    <page string="Token Money">
                        <field
                                name="account_payment_ids"
                                nolabel="1"
                                colspan="4"
                                context="{'form_view_ref': 'account.view_account_payment_form','tree_view_ref': 'account.view_account_payment_tree'}"
                        />
                    </page>
                </notebook>
                <xpath expr="//page[@name='other_information']" position="after">
                    <page string="Payment Plan" name="payment_plan">
                        <field name="plan_ids" attrs="{'readonly':[('state','=','sale')]}">
                            <tree sample="1" editable="bottom">
                                <field name="milestone_id"/>
                                <button string="Add" class="btn btn-primary" name="open_payment_plan_wizard"
                                        type="object" attrs="{'column_invisible':[('parent.state','=','sale')]}"/>
                                <field name="percentage" readonly="1"/>
                                <field name="amount" readonly="1"/>
                                <field name="start_date" readonly="1"/>
                                <field name="end_date" readonly="1"/>
                                <field name="installment_no" readonly="1"/>
                                <field name="installment_period" readonly="1"/>
                            </tree>
                        </field>
                        <notebook name="installment">
                            <page string="Installments">
                                <field name="installment_ids">
                                    <tree sample="1" editable="bottom" edit="0" create="0" delete="0">
                                        <field name="milestone_id" readonly="1"/>
                                        <field name="amount" readonly="1"/>
                                        <field name="move_id" readonly="1" attrs="{'column_invisible':[('parent.state','!=','sale')]}"/>
                                        <field name="invoice_date" readonly="1" attrs="{'column_invisible':[('parent.state','!=','sale')]}"/>
                                        <field name="invoice_payment_date" readonly="1" attrs="{'column_invisible':[('parent.state','!=','sale')]}"/>
                                        <field name="invoice_status" readonly="1" attrs="{'column_invisible':[('parent.state','!=','sale')]}"/>
                                        <field name="payment_status" readonly="1" attrs="{'column_invisible':[('parent.state','!=','sale')]}"/>
                                        <field name="date" invisible="1"/>
                                    </tree>
                                </field>
                            </page>
                        </notebook>
                        <group name="ins_amount" col="6" class="mt-2 mt-md-0">
                            <group colspan="2">
                                <field name="ins_amount" string="Total"/>
                                <div class="oe_clear"/>
                            </group>
                        </group>
                    </page>
                </xpath>
            </field>
        </record>
    </data>
</odoo>

```


## Dump Data from Server

To create a dump file , run the following command

```python
  sudo su - postgres
```

```python
  pg_dump --no-owner --format==custom Database_Name > db_0001.dump
```




## QR Code on Form View

### Python
Libraries
```Python
import qrcode
import base64
from io import BytesIO
```
Fields
```Python
qr_code = fields.Binary("QR Code", attachment=True, compute='_compute_generate_qr_code')
```
Function
```Python
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
OR
```XML
<span t-field="o.name" t-options="{'widget': 'barcode', 'humanreadable': 1,'width': 600, 'height': 100}">
```   


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


## Selection Fields Labels
To display selection field key pair values on reports in Odoo
```XML
<t t-esc="dict(line.fields_get(allfields=['payment_status'])['payment_status']['selection'])[line.payment_status]"/>
```
Python
```Python
dict(self.fields_get(allfields=['inv_status'])['inv_status']['selection'])[rec.inv_status]
```

## Mail Activity

Python
```Python
todos = {
            'res_id': res.id,
            'res_model_id': self.env['ir.model'].sudo().search([('model', '=', 'account.payment')]).id,
            'user_id': self.env.user.search([('login', '=', 'hisham@sanadbags.com')]).id,
            'summary': f'Please Approve the Amount {res.amount}',
            'note': 'Amount Approval',
            'date_deadline': datetime.now(),
            }
if self.env.user.search([('login', '=', 'baashar.mohammed@sanadbags.com')]).id:
    self.env['mail.activity'].sudo().create(todos)
else:
    raise ValidationError('This email does not exist (baashar.mohammed@sanadbags.com)')
```

## Mail Activity: Mark As Done

Function
```Python
@api.model
    def mark_activity_done(self, user_id):
        activity = self.env['mail.activity'].search(
            [('user_id', '=', user_id), ('res_model', '=', 'account.payment'), ('res_id', '=', self.id)])
        activity.action_done()
```
Driver
```Python
self.mark_activity_done(self.env.user.id)
```

