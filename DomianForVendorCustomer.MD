
# Domain for Customer/Vendor

M. Rizwan


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
