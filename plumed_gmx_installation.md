# Plumed2.9.3 installation steps (alongwith Gromacs 2023.5) 

###### According to the installation guide: https://www.plumed.org/doc-v2.9/user-doc/html/_installation.html

## 1. Download and unzip

###### Download plumed from: https://github.com/plumed/plumed2/releases
###### Unzip 
`tar -xzvf plumed-2.9.1.tgz`

`cd plumed-2.9.3`

## 2. Configure

###### The following are tips for the ./configure command

###### ./configure --help # help for configure options

###### PLUMED source code already includes a few selected VMD molfile plugins so as to read a small number of additional trajectory formats (e.g., dcd, gromacs files, pdb, and amber files). If you configure PLUMED with the full set of VMD plugins you will be able to read many more trajectory formats, basically all of those supported by VMD.

## 3. Download and install VMD

###### Download from: https://www.ks.uiuc.edu/Development/Download/download.cgi?PackageName=VMD

`tar xvzf vmd-1.9.4a55.bin.LINUXAMD64-CUDA102-OptiX650-OSPRay185-RTXRTRT.opengl.tar.gz`

`cd vmd-1.9.4a55`

`sudo apt-get update`

`sudo apt-get upgrade`

`./configure`

`cd src/`

`sudo make install`


###### ./configure LDFLAGS="-L/pathtovmdplugins/ARCH/molfile" CPPFLAGS="-I/pathtovmdplugins/include -I/pathtovmdplugins/ARCH/molfile"

###### Notice that it might be necessary to add to LDFLAGS the path to your TCL interpreter, e.g.

###### ./configure LDFLAGS="-ltcl8.5 -L/mypathtotcl -L/pathtovmdplugins/ARCH/molfile" CPPFLAGS="-I/pathtovmdplugins/include -I/pathtovmdplugins/ARCH/molfile"
            
###### PLUMED includes some additional modules that by default are not compiled, but can be enabled during configuration. You can use the option --enable-modules to activate some of them, e.g.

###### ./configure --enable-modules=module1name+module2name

###### --enable-modules=all 

###### not enabled modules include: pytorch, opes, funnel

###### To install PLUMED one should first decide the location: 

###### ./configure --prefix=$HOME/opt

###### As of PLUMED 2.5 you cannot anymore change the location during install. If you didn't      # specify the --prefix option during configure PLUMED will be installed in /usr/local.

###### The install command should be executed with root permissions (e.g. "sudo make install") if # you want to install PLUMED on a system directory.

##### FINAL 

./configure --enable-modules=all LDFLAGS="-L/usr/local/lib/vmd/plugins/LINUXAMD64/molfile" CPPFLAGS="-I/usr/local/lib/vmd/plugins/include -I/usr/local/lib/vmd/plugins/LINUXAMD64/molfile"

#################################
#           Compile             #
#################################

# Once configured, PLUMED can be compiled using the following command: 

make -j 4

# You can also check if PLUMED is correctly compiled by performing our regression tests. Be 3 warned that some of them fail because of the different numerical accuracy on different machines. As of version 2.4, in order to test the plumed executable that you just compiled (prior to installing it) you can use the following command 

make check

#  In addition, similarly to previous versions of PLUMED, you can test the plumed executable that is in your current path with 

cd regtest
make

# You can check the exact version they will use by using the command 

which plumed

#################################
#           Install             #
#################################

# It is strongly suggested to install PLUMED in a predefined location. This is done using 

sudo make install

#################################
#        Patch MD code (1)      #
#################################

# A growing number of MD codes can use PLUMED without any modification. If you are using one of these codes, refer to its manual to know how to activate PLUMED. In case your MD code is not supporting PLUMED already, you should modify it. We provide scripts to adjust some of the most popular MD codes so as to provide PLUMED support. At the present times we support patching the following list of codes:

    gromacs-2020-7
    gromacs-2021-7
    gromacs-2022-5
    gromacs-2023-5
    gromacs-2024-3
    namd-2-12
    namd-2-13
    namd-2-14
    qespresso-5-0-2
    qespresso-6-2
    qespresso-7-0
    qespresso-7-2

# To patch your MD code, you should have already installed PLUMED properly. This is necessary as you need to have the command "plumed" in your execution path.

plumed

# There are different options available when patching. You can check all of them using:

plumed patch --help

#################################
# Configure and compile Gromacs #
#################################

# Configure and compile your MD engine (look for the instructions in its documentation).
# Test if the MD code is working properly.

# download gromacs 2023.5 from https://manual.gromacs.org/documentation/2023.5/download.html

tar xfz gromacs-2023.5.tar.gz
cd gromacs-2023.5
mkdir build
cd build

cmake .. -DGMX_BUILD_OWN_FFTW=ON -DREGRESSIONTEST_DOWNLOAD=ON -DGMX_GPU=CUDA -DCUDA_TOOLKIT_ROOT_DIR=/usr/local/cuda -DCMAKE_INSTALL_PREFIX=/usr/local/gromacs-2023 -DOXYGEN_EXECUTABLE=/bin

make
make check

# Go to the root directory for the source code of the MD engine.

cd ..

#################################
#        Patch MD code (2)      #
#################################

# Patch with PLUMED using: 

plumed patch -p

# The script will interactively ask which MD engine you are patching.
# Once you have patched recompile the MD code (if dependencies are set up properly in the MD engine, only modified files will be recompiled)

#################################
#       Recompile Gromacs       #
#################################

cd build

make
make check

#################################
#       Install Gromacs         #
#################################

sudo make install
source /usr/local/gromacs-2023/bin/GMXRC





