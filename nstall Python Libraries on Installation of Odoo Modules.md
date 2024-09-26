Create a python file in your Odoo module and name it "setup.py".
Copy below code snippet in the above file.
Register your newly created python file "setup.py" in __init.py__ file.

```python
class Install_Packages:
    """
    This Class installs required Packages or library
    """

    get_pckg = subprocess.check_output([sys.executable, '-m', 'pip', 'freeze'])
    installed_packages = [r.decode().split('==')[0] for r in get_pckg.split()]
    # List of your required packages
    
    required_packages = ['packages_1', 'packages_2', 'packages_3', 'packages_4']    
    for packg in required_packages:
        if packg in installed_packages:
            pass
        else:
            print('installing package %s' % packg)
            os.system('pip3 install ' + packg)
```
