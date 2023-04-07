## Odoo Custom Font in Qweb Reports
To use a custom font file like Times New Roman in an Odoo report, you can follow these steps:

Place the Times New Roman .ttf file in a folder within your Odoo module, for example in the static folder.

In the report QWeb template, add a reference to the font file using the @font-face rule in a <style> tag. The font file paths should be relative to the module's static folder. For example:

CSS
```CSS
<style>
  @font-face {
    font-family: 'Times New Roman';
    src: url('/module_name/static/fonts/times-new-roman.ttf') format('truetype');
  }
  body {
    font-family: 'Times New Roman', serif;
  }
</style>

```

Use the font in the report by setting the font-family property of the relevant HTML elements to 'Times New Roman'.

For example, to use the font for all text in the report, you could add the following to your QWeb template:

HTML
```HTML
<div style="font-family: 'Times New Roman';">
  ...
</div>

```
Or you could use a more specific selector to apply the font only to certain parts of the report.

Restart the Odoo server and check the report to make sure the font is being used correctly.

Note that you may need to adjust the src URL in the @font-face rule if your font file has a different file extension or if you're using a different folder structure.



