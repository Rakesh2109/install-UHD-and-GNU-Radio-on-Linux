# Building and Installing the USRP Open-Source Toolchain (UHD and GNU Radio) on Linux

### Before building UHD and GNU Radio, you need to make sure that all the dependencies are first installed.

However, before installing any dependencies, you should first make sure that all the packages that are already installed on your system are up-to-date. You can do this from a GUI, or from the command-line, as shown below.

On Ubuntu systems, run:
```bash
sudo apt-get update
```
Once the system has been updated, then install the required dependencies for UHD and GNU Radio.

On Ubuntu 22.04 systems, run:
```bash
sudo apt-get -y install autoconf automake build-essential ccache cmake cpufrequtils doxygen ethtool fort77 g++ gir1.2-gtk-3.0 git gobject-introspection gpsd gpsd-clients inetutils-tools libasound2-dev libboost-all-dev libcomedi-dev libcppunit-dev libfftw3-bin libfftw3-dev libfftw3-doc libfontconfig1-dev libgmp-dev libgps-dev libgsl-dev liblog4cpp5-dev libncurses5 libncurses5-dev libpulse-dev libqt5opengl5-dev libqwt-qt5-dev libsdl1.2-dev libtool libudev-dev libusb-1.0-0 libusb-1.0-0-dev libusb-dev libxi-dev libxrender-dev libzmq3-dev libzmq5 ncurses-bin python3-cheetah python3-click python3-click-plugins python3-click-threading python3-dev python3-docutils python3-gi python3-gi-cairo python3-gps python3-lxml python3-mako python3-numpy python3-opengl python3-pyqt5 python3-requests python3-scipy python3-setuptools python3-six python3-sphinx python3-yaml python3-zmq python3-ruamel.yaml swig wget
```
After installing the dependencies, you should reboot the system.

If the installation of the dependencies completes without any errors, then you can proceed to build and install UHD and GNU Radio.
### Building and installing UHD from source code

First, make a folder to hold the repository.
```bash
cd $HOME
mkdir workarea
cd workarea
```

Next, clone the repository and change into the cloned directory.
```bash
git clone https://github.com/EttusResearch/uhd
cd uhd
```
Next, checkout the desired UHD version. You can get a full listing of tagged releases by running the command:
``` bash
git tag -l
```

Example truncated output of git tag -l:
```bash
git tag -l
...
release_003_009_004
release_003_009_005
release_003_010_000_000
...
```

After identifying the version and corresponding release tag you need, check it out:


   #### Example: For UHD 4.6.0.0
```bash
git checkout v3.14.0.0
```
Next, create a build folder within the repository.
```bash
cd host
mkdir build
cd build
```
Next, invoke CMake.
```bash
cmake ..
```

##### Error in cmake 

Could NOT find DPDK (missing: DPDK_INCLUDE_DIRS DPDK_LIBRARIES) (Required is at least version "18.11...21.11")


###### UHD disabled components                             

   * DPDK


##### Open New Terminal

```bash 
pip install pyelftools
```

If your facing any issue while install pyelftools  make sure update setuptools

```bash 
pip install --upgrade pip setuptools
```

Install Prerequisites: Ensure that you have necessary build tools and libraries installed:

```bash
sudo apt-get update
sudo apt-get install build-essential linux-headers-$(uname -r) libnuma-dev
```

Download DPDK: You can download DPDK from the official website or use a specific release from its repository. 
##### For example, to download version 21.11:

```bash 
wget https://fast.dpdk.org/rel/dpdk-21.11.tar.xz
```
Extract DPDK: Extract the downloaded DPDK archive:

``` bash
tar -xf dpdk-21.11.tar.xz
cd dpdk-21.11
```

Configure DPDK: Set up DPDK for compilation with the target environment:

```bash 
sudo apt install meson 
meson build
```

Compile DPDK: Compile DPDK using Ninja (you may need to install Ninja if it's not already installed):

```bash 
cd build
ninja
```

Install DPDK: Install DPDK system-wide:
```bash 
sudo ninja install
```
Set Environment Variables: Set the necessary environment variables for DPDK. You can add these lines to your shell profile (e.g., .bashrc) or export them directly in your terminal session:

```
export DPDK_INCLUDE_DIRS=/usr/local/include
export DPDK_LIBRARIES=/usr/local/lib	
```
Install Missing Python Modules: You need to install the missing Python modules (gevent, mprpc, pyudev, and pyroute2) in your Python environment. You can install them using pip, the Python package manager. For example:

```bash 
pip install gevent mprpc pyudev pyroute2
```

#### Run cmake again 
Note: By default, UHD will be installed into the prefix /usr/local. This can be changed by adding -DCMAKE_INSTALL_PREFIX=XXX to install into the prefix XXX.

Note: if the shell PATH is set such that /bin comes before /usr/bin, then this step is likely to fail because cmake will set the FIND_ROOT_PATH to / and this setting will fail as a prefix for Boost headers or libraries. The cmake output will include messages such as
```bash
   --   Boost include directories: /include
   --   Boost library directories: /lib/x86_64-linux-gnu
```
and
```bash 
   CMake Error in lib/CMakeLists.txt:
     Imported target "Boost::chrono" includes non-existent path
       "/include"
     in its INTERFACE_INCLUDE_DIRECTORIES.  Possible reasons include:
     * The path was deleted, renamed, or moved to another location.
     * An install or uninstall procedure did not complete successfully.
     * The installation package was faulty and references files it does not provide.
```
One of the following 3 options should fix this situation:

     1. /usr/bin/cmake ..
     2. PATH=/usr/bin:$PATH cmake ..
     3. cmake -DCMAKE_FIND_ROOT_PATH=/usr ..

Once the cmake command succeeds without errors, build UHD.
```bash 
make
```
Note: Use the -j parameter to use more than the default one CPU core, e.g. make -j8 for 8 CPU cores.

Next, you can optionally run some basic tests to verify that the build process completed properly.
```bash 
make test
```
Next, install UHD, using the default install prefix, which will install UHD under the /usr/local/lib folder. You need to run this as root due to the permissions on that folder.
```bash 
sudo make install
```
Next, update the system's shared library cache.
```bash
sudo ldconfig
```
Finally, make sure that the LD_LIBRARY_PATH environment variable is defined and includes the folder under which UHD was installed. Most commonly, you can add the line below to the end of your $HOME/.bashrc file:
```
export LD_LIBRARY_PATH=/usr/local/lib
```

If the LD_LIBRARY_PATH environment variable is already defined with other folders in your $HOME/.bashrc file, then add the line below to the end of your $HOME/.bashrc file to preserve the current settings.
```
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
```
For this change to take effect, you will need to close the current terminal window, and open a new terminal.

At this point, UHD should be installed and ready to use. You can quickly test this, with no USRP device attached, by running uhd_find_devices. You should see something similar to the following.
```
linux; GNU C++ version 4.8.4; Boost_105400; UHD_003.010.000.HEAD-0-g6e1ac3fc

No UHD Devices Found
```
### Downloading the UHD FPGA Images

You can now download the UHD FPGA Images for this installation. This can be done by running the command uhd_images_downloader.
```bash
sudo uhd_images_downloader
```
Note: Since this installation is being installed to a system level directory (e.g. /usr/local), the uhd_images_downloader command requires sudo privileges.
```
Example ouput for UHD 4.6.0.0:

$ sudo uhd_images_downloader 
Images destination:      /usr/local/share/uhd/images
Downloading images from: http://files.ettus.com/binaries/images/uhd-images_003.010.003.000-release.zip
Downloading images to:   /tmp/tmpm46JDg/uhd-images_003.010.003.000-release.zip
57009 kB / 57009 kB (100%)

Images successfully installed to: /usr/local/share/uhd/images

Example output for UHD 4.6:

$ sudo uhd_images_downloader
[INFO] Images destination: /usr/local/share/uhd/images
[INFO] No inventory file found at /usr/local/share/uhd/images/inventory.json. Creating an empty one.
00006 kB / 00006 kB (100%) usrp1_b100_fw_default-g6bea23d.zip
19484 kB / 19484 kB (100%) x3xx_x310_fpga_default-g494ae8bb.zip
02757 kB / 02757 kB (100%) usrp2_n210_fpga_default-g6bea23d.zip
02109 kB / 02109 kB (100%) n230_n230_fpga_default-g494ae8bb.zip
00522 kB / 00522 kB (100%) usrp1_b100_fpga_default-g6bea23d.zip
00474 kB / 00474 kB (100%) b2xx_b200_fpga_default-g494ae8bb.zip
02415 kB / 02415 kB (100%) usrp2_n200_fpga_default-g6bea23d.zip
05920 kB / 05920 kB (100%) e3xx_e320_fpga_default-g494ae8bb.zip
15883 kB / 15883 kB (100%) n3xx_n310_fpga_default-g494ae8bb.zip
00506 kB / 00506 kB (100%) b2xx_b205mini_fpga_default-g494ae8bb.zip
18676 kB / 18676 kB (100%) x3xx_x300_fpga_default-g494ae8bb.zip
00017 kB / 00017 kB (100%) octoclock_octoclock_fw_default-g14000041.zip
04839 kB / 04839 kB (100%) usb_common_windrv_default-g14000041.zip
00007 kB / 00007 kB (100%) usrp2_usrp2_fw_default-g6bea23d.zip
00009 kB / 00009 kB (100%) usrp2_n200_fw_default-g6bea23d.zip
00450 kB / 00450 kB (100%) usrp2_usrp2_fpga_default-g6bea23d.zip
00142 kB / 00142 kB (100%) b2xx_common_fw_default-g3ff4186b.zip
00460 kB / 00460 kB (100%) b2xx_b200mini_fpga_default-g494ae8bb.zip
00319 kB / 00319 kB (100%) usrp1_usrp1_fpga_default-g6bea23d.zip
00009 kB / 00009 kB (100%) usrp2_n210_fw_default-g6bea23d.zip
11537 kB / 11537 kB (100%) n3xx_n300_fpga_default-g494ae8bb.zip
05349 kB / 05349 kB (100%) e3xx_e310_fpga_default-g494ae8bb.zip
00866 kB / 00866 kB (100%) b2xx_b210_fpga_default-g494ae8bb.zip
[INFO] Images download complete.
```
### Building and installing GNU Radio from source code


Similar to the process for UHD, to build GNU Radio from source code, clone the GitHub repository, check out a branch or tagged release of the repository, and build and install. Please follow the steps below. Make sure that no USRP device is connected to the system at this point.

First, make a folder to hold the repository.
```bash 
cd $HOME
cd workarea
```
Next, clone the repository.
```bash 
git clone --recursive https://github.com/gnuradio/gnuradio
```
Next, go into the repository and check out the desired GNU Radio version.
```bash 
cd gnuradio
```
To checkout the v3.10.7.0 branch:
```bash
git checkout v3.10.7.4
```
Next, update the submodules:
```bash 
git submodule update --init --recursive
```
Next, create a build folder within the repository, invoke CMake, and build GNU Radio:
```bash
mkdir build
cd build
cmake ../
```
##### While cmake ../ in gnuradio you may face this 

```
Configuring VOLK support...
-- Found VOLK: Volk::volk  
--   Found VOLK:
--   * Version: 2.2.0
--   * Libraries: Volk::volk
--   * Includes: /usr/include
CMake Error at CMakeLists.txt:312 (message):
  VOLK version 2.2.0 < 2.4.1
```

Download VOLK: You can download VOLK from the official website or use a specific release from its repository. For example, to download version 3.1.2

[Vector-Optimized Library of Kernels](https://www.libvolk.org/release-v312.html)


First, make a folder to hold the repository.
```bash 
cd $HOME
cd workarea
```
Next, clone the repository

```bash 
git clone --recursive https://github.com/gnuradio/volk.git
```
Next, go into the repository.
```bash 
cd volk
```
Next, create a build folder within the repository, invoke CMake, and build volk:
```bash
mkdir build
cd build
cmake ..
make
make test
sudo make install
```
Note : You might want to explore "make -j[SOMEVALUE]" options for your multicore CPU.
Perform post-installation steps

Linux OS: Link and cache shared library
```bash
sudo ldconfig
```

volk_profile will profile your system so that the best kernel is used

```bash 
volk_profile
```

Now
```bash 
cd workarea/gnuradi/build
cmake ../
make
```

Next, you can optionally run some basic tests to verify that the build process completed properly.
``` bash 
 make test
```
#### One Test may be failed due to version of volk 

##### Just replace that error python py  file from latest version of gnuradio github link 


Next, install GNU Radio, using the default install prefix, which will install GNU Radio under the /usr/local/lib folder. You need to run this as root due to the permissions on that folder.
```bahs
sudo make install
```
Finally, update the system's shared library cache.
```bash
sudo ldconfig
``` 
At this point, GNU Radio should be installed and ready to use. You can quickly test this, with no USRP device attached, by running the following quick tests.
```bash
gnuradio-config-info --version
gnuradio-config-info --prefix
gnuradio-config-info --enabled-components
```
There is a simple flowgraph that you can run that does not require any USRP hardware. It's called the dialtone test, and it produces a PSTN dial tone on the computer's speakers. Running it verifies that all the libraries can be found, and that the GNU Radio run-time is working.
```bash
python $HOME/workarea/gnuradio/gr-audio/examples/python/dial_tone.py
```
You can try launching the GNU Radio Companion (GRC) tool, a visual tool for building and running GNU Radio flowgraphs.
```bash
gnuradio-companion
```
