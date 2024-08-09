# Install Python 3.8.10 via source Archive
## Download Python 3.8.10
To begin, visit the official Python download page and obtain the download link for the latest version of the Python release you want. Remember that these instructions should work for any version, as you’ll be compiling it yourself. Once you’ve copied the download link, use the wget command to download the Python 3.8 archive:

```Bash
wget https://www.python.org/ftp/python/3.8.10/Python-3.8.10.tar.xz
```
Note: The download link may change, so be sure to obtain a fresh link. The command above is just an example. After downloading the Python archive, extract it and remember to update the version number if you downloaded a newer release:

```bash
tar -xf Python-3.8.10.tar.xz
```
## Install Required Packages for Python 3.8
Next, you’ll need to install the dependencies required to compile and install Python 3.8:

```bash
sudo apt install build-essential zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev libssl-dev libsqlite3-dev libreadline-dev libffi-dev curl libbz2-dev pkg-config make -y
```
## Configure the Python Build Environment
Navigate to the directory where you extracted the Python source code:
```bash
cd Python3.8.10/
```
Now, run the ./configure command with the --enable-optimizations and --enable-shared options:
```bash
./configure --enable-optimizations --enable-shared
```
The script will perform several checks to ensure that all necessary dependencies are present on your system. Running ./configure with --enable-optimizations will optimize the Python binary by running multiple tests. This can make the build process slower but will result in a faster and more efficient Python installation.

## Compile Python 3.8
After configuring the build environment, it’s time to compile Python using the make command:
```bash
make
```
To significantly increase the compiling speed, you can use the -j <number of CPUs> option, which specifies the number of CPUs you want to utilize. For example, if your server has 6 CPUs, you can use all six or at least 4 to 5 to increase the speed. The command would be make -j 6.

```bash
make -j 6
```

## Install Python Binaries
After finishing the building process, install the Python binaries by running the following command: sudo make altinstall. Using the make altinstall command is recommended to avoid overwriting the default Python 3 binary system.
```bash
sudo make altinstall
```
Finally, configure the dynamic linker run-time bindings by running the ldconfig command after installation:
```bash
sudo ldconfig
```
Example only:
```bash
sudo ldconfig /opt/Python3.8.10
```

## Verify Python 3.8 Installation
Run the following command to confirm that Python 3.8 and the corresponding build version have been successfully installed:
```bash
sudo python3.8 --version
```
