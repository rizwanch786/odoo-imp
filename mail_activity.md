# Mail Activity

Python
```
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

# Mail Activity: Mark As Done

Function
```
@api.model
    def mark_activity_done(self, user_id):
        activity = self.env['mail.activity'].search(
            [('user_id', '=', user_id), ('res_model', '=', 'account.payment'), ('res_id', '=', self.id)])
        activity.action_done()
```
Driver
```
self.mark_activity_done(self.env.user.id)
```

