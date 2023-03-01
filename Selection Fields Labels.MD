
## Selection Fields Labels
To display selection field key pair values on reports in Odoo
```XML
<t t-esc="dict(line.fields_get(allfields=['payment_status'])['payment_status']['selection'])[line.payment_status]"/>
```
Python
```Python
dict(self.fields_get(allfields=['inv_status'])['inv_status']['selection'])[rec.inv_status]
```
