# How to install odoo on MacBook

## Step 01
Open Terminal by pressing "command + space" buttons and type "terminal" the press enter

## Step 02
Install xcode-select using following command:
```bash
xcode-select --install
```

## Step 03
Open brew.sh on browser, you will find the command below 'Install Homebrew" keyword
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

## Step 04
After installing Homebrew, you will get two more command at the end of installation as below:
```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> /Users/<macuser>/.zprofile
```
```bash
eval "$(/opt/homebrew/bin/brew shellenv)"
```

## Step 05
Search for the python, you can run command
```bash
brew search python
```

## Step 06
To install python, run the following command
```bash
brew install python@3.8/python@3.10/python@3.12
```

## Step 07
Install git by following command
```bash
brew install git
```

## Step 08
**Grab the Odoo source code, go to Odoo github on browser
**
Go in Odoo repository for community edition Select the version you want
You can see green button 'Code', click on it to find the link in order to clone the Odoo code
```bash
sudo git clone https://www.github.com/odoo/odoo --depth 1 --branch 16.0 ./odoo
```

## Step 09
To install PostgreSQL run below command
```bash
brew install postgresql
```

## Step 10
To create postgres user
```bash
psql postgres
```
```bash
CREATE USER odoo;
```
```bash
ALTERV USER odoo WITH SUPERUSER;
```

## Step 11
Change Directory to the source code
``` bash
cd ./odoo
```

## Step 12
Create a virtual environment, activate it and install requirements
```bash
python@3/python@3.10/python@3.12 -m venv odoo-venv
```
```bash
source odoo-venv/bin/activate
```
```bash
pip install wheel
```
```bash
pip install -r requirements.txt
```
To fix the error of psycopg2 we will run binary version of the psycopg2, run following command
```bash
pip3 install psycopg2-binary --no-cache-dir
```

## Step 13
Now, its time to open pycharm configure interprater as well as service file and Enjoy...!
