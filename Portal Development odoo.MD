# Download payslip documant from Portal

Python-Controller
```Bash
@http.route('/my/salary_slip_print/<int:pay_id>', type='http', auth="user", website=True)
    def portal_my_salary_slip_print(self, pay_id, **kw):

        if pay_id:
            payslip = request.env['hr.payslip'].sudo().browse(pay_id)
            pdf = request.env['ir.actions.report'].sudo()._render_qweb_pdf('custom_reports.custom_payslip_report_id', [pay_id])[0]
            pdfhttpheaders = [
                ('Content-Type', 'application/pdf'),
                ('Content-Length', len(pdf)),
                ('Content-Disposition', f'attachment; filename={payslip.display_name}.pdf')
            ]
            return request.make_response(pdf, headers=pdfhttpheaders)
```

XML
```XML
<a t-att-href="'/my/salary_slip_print/' + str(payslip.id)">
    <button class="btn btn-primary">Preview</button>
</a>
```
