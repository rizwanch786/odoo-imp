
## Function

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
