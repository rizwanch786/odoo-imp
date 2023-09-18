# How Can we set custom Font in odoo QWEB Reports

Download any font that you want to apply on reports

## Add Font in css

add font by using following path

/home/rizwan/odoo-16.0/Aims-Hcc/aims_custom_reports/static/src/fonts/times.ttf
after that create an .css file

/home/rizwan/odoo-16.0/Aims-Hcc/aims_custom_reports/static/src/css/report_styles.css

Now, Add or call font file on css by using this Script
```bash
   @font-face {
    font-family: 'Times New Romans';
    src: url('/aims_custom_reports/static/src/fonts/times.ttf') format('truetype');
}

.times {
    font-family: 'Times New Romans', sans-serif;
}

```

## Usage
On Qweb report on which you want to apply font call css

```css
<link rel="stylesheet" type="text/css"
                          href="/aims_custom_reports/static/src/css/report_styles.css"/>
```
Also use class name on any element on which you can want to apply

```html
<div class="times">
</div>
```
