---
title: Minimal Setup for sky130 - Enviroment
date: 2024-07-30 00:30:00 +0900
categories: [Hardware,Open-source-Silicon]
tags: [sky130,magic,netgen,xschem]     # TAG names should always be lowercase, 띄어쓰기도 금지
---
------------------------------------------

**Note:** This tutorial requires sudo access. If you are not the owner of the server or system, you should use a virtual machine or Docker.

**Notation Guides:**

- `$` means Ubuntu shell commands
- `%` means tcl/tk console
- `ln[1]` means interactive Python command
- UPPERCASE words denote user-defined paths

## Install MAGIC

1.  **Install dependent packages**

    **Note:** CentOS uses `yum` instead of `apt-get` for package management. Package names could be slightly different.

    ```bash
    $ sudo apt-get install m4
    $ sudo apt-get install tcsh
    $ sudo apt-get install csh
    $ sudo apt-get install libx11-dev
    $ sudo apt-get install TCL-dev tk-dev
    $ sudo apt-get install libcairo2-dev
    ```

2.  **Clone Magic source**

    Make or go to a directory for the source `DIR_FOR_SOURCE` and clone the source from Git.

    ```bash
    $ cd DIR_FOR_SOURCE
    $ git clone git://opencircuitdesign.com/magic
    ```

    If it doesn't work, clone the GitHub mirror.

    ```bash
    $ git clone https://github.com/RTimothyEdwards/magic
    ```

    **Note:** The command `apt-get install magic` installs an outdated version!

3.  **Compile and Install**

    This step requires sudo access.

    ```bash
    $ cd magic
    $ ./configure
    $ make
    $ sudo make install
    ```

4.  **Test**

    You can run Magic in any directory.

    ```bash
    $ magic &
    ```

    Check Magic's cell search path with the following:

    ```tcl
    % path
    ```

    If `CAD_ROOT` appears after "System search path is", you need to set `CAD_ROOT` as an environment variable pointing to where Magic's basic libraries are installed. The default is '/usr/local/lib'.

## Install XSCHEM, NETGEN, NGSPICE

1.  **XSCHEM**

    Install the following packages using `sudo apt-get install`
    ![xschem_dependencies](./assets/img/xschem_require.png)

    Clone the XSCHEM source and install:

    ```bash
    $ cd DIR_FOR_SOURCE
    $ git clone https://github.com/StefanSchippers/xschem.git_
    $ cd xschem
    $ ./configure --prefix=ROOT_FOR_XSCHEM (default: '/usr/local')
    $ make
    $ make install
    ```

2.  **NETGEN**

    ```bash
    $ cd DIR_FOR_SOURCE
    $ git clone git://opencircuitdesign.com/netgen
    $ cd netgen
    $ ./configure
    $ make
    $ sudo make install
    ```

3.  **NGSPICE**

    NGSPICE uses `wget` instead of `git clone`.

    ```bash
    $ cd DIR_FOR_SOURCE
    $ wget https://sourceforge.net/projects/ngspice/files/ng-spice-rework/37/ngspice-37.tar.gz
    $ tar -zxvf ngspice-37.tar.gz
    $ cd ngspice-37
    $ mkdir release
    $ cd release
    $ ../configure --with-x --enable-xspice --enable-cider --enable-openmp --with-readlines=yes --disable-debug
    $ make
    $ sudo make install
    ```

## Install SKY130-PDK

1.  **Google/Skywater open SKY130-PDK**

    Clone the PDK source from GitHub and activate submodules. Since `laygo2` doesn't use digital standard libraries, we install primitive and IO libraries only.

    ```bash
    $ cd DIR_FOR_SOURCE
    $ git clone https://github.com/google/skywater-pdk
    $ cd skywater-pdk
    $ git submodule init libraries/sky130_fd_io/latest
    $ git submodule init libraries/sky130_fd_pr/latest
    $ git submodule update
    $ make timing
    ```

2.  **Set up PDK using open-pdks**

    The `open-pdks` app installs Skywater-PDK into the `PDK_ROOT` directory and generates Magic library files with them. It also downloads some useful third-party libraries, such as primitives for XSCHEM.

    ```bash
    $ cd DIR_FOR_SOURCE
    $ git clone git://opencircuitdesign.com/open_pdks
    $ cd open_pdks
    $ ./configure --enable-sky130-pdk-DIR_FOR_SOURCE/skywater-pdk --disable-alpha-sky130
    ```

    Before installing, consider these configuration options:

    1.  `--prefix=PDK_ROOT`
        *   PDK files are installed in 'PDK\_ROOT/share/pdk'
        *   Default PDK\_ROOT: '/usr/local'
        *   We use the default path in this tutorial

    2.  `--enable-xschem-sky130[=PATH]`
        *   If not disabled, the third-party Sky130 setup for XSCHEM will be installed as part of the sky130A PDK.
        *   If the path is omitted or the configuration option is not specified, the library will be pulled automatically from the repository and installed.
        *   To disable the package, use `--disable-xschem-sky130`.

    3.  `--enable-alpha-sky130[=PATH]`
        *   The third-party alphanumeric layout library will be installed. Everything else is identical to option 2.

    After choosing and setting the configuration, you can install. **It could take some time.**

    ```bash
    $ make
    $ sudo make install
    ```

## Set Startup Files for MAGIC and XSCHEM

1.  **MAGIC**

    Create a symbolic link mapping sky130 tech-files into the MAGIC system search path.

    ```bash
    $ sudo ln -s SKY130A_INTALL_ROOT_DIR/sky130A/libs.tech/magic/* /usr/local/lib/magic/sys/
    ```

    The default `SKY130A_INTALL_ROOT_DIR` is '/usr/local/share/pdk', which is identical to "PDK\_ROOT/share/pdk".

    Set the `.magicrc` file in your home directory.

    ```bash
    $ cd ~
    $ cat > .magicrc 
    source $CAD_ROOT/magic/sys/sky130A.magicrc
    ```

    Test whether it's connected well.

    ```bash
    $ magic &
    % path
    ```

    It's successful if the result looks like the image below.
    
    ![magic_sky130_integration](./assets/img/magic_sky_path.png)

2.  **XSCHEM**

    **Check xschem_sky130 library**

    The `xschem_sky130` library should have been installed in `SKY130A_INSTALL_ROOT_DIR/sky130A/libs.tech`.

    ```bash
    $ cd SKY130A_INSTALL_ROOT_DIR/sky130A/libs.tech
    $ cd xschem
    $ ls
    ```

    If the following directories don't appear, you should download them manually.
    
    ![xschem_library](./assets/img/xschem_library.png)

    **Set xschemrc**

    ```bash
    $ cd ~/.xschem
    $ cat > xschemrc
    source /usr/local/share/pdk/sky130A/libs.tech/xschem/xschemrc (press ctrl+D to finish writing)
    $ xschem
    ```

    The result should look like the image below. Select the desired cell and press 'e' to check various circuits.
    
    ![xschem_top_image](./assets/img/xschem_top.png)
