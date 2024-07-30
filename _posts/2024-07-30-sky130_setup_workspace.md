---
title: Minimal Setup for sky130 - Workspace
date: 2024-07-30 01:30:00 +0900
categories: [Hardware,Open-source-Silicon]
tags: [sky130,magic,netgen,xschem]     # TAG names should always be lowercase, 띄어쓰기도 금지
---
------------------------------------------

**Step 1: Clone repository**

Clone `laygo2_workspace_sky130` from GitHub:

```bash
$ git clone https://github.com/niftylab/laygo2_workspace_sky130.git
$ cd laygo2_workspace_sky130
$ git submodule init
$ git submodule update
```

**Step 2: Set path variables for magic**

Initialize the Magic template cell search path for this tutorial:

```bash
$ ./mag_set_path.sh
$ ./start_mag.sh
% path
```

These `.sh` scripts initialize the '.maginit_personal' file with the cell search path for 'magic'. Your Magic search path should include the following directories:

*   WORK_DIR/magic_layout/skywater130_microtemplates_dense
*   WORK_DIR/magic_layout/logic_generated

![laygo2_default_path](./assets/img/search_path.JPG)

These paths are the default template paths for the example code in laygo2.

**Step 3: Test laygo2**

**Note:** Several package prerequisites (such as NumPy) should be installed for LAYGO2. The base installation of Anaconda fulfills the package requirements.

```bash
$ source .bashrc
$ ipython profile create
$ cp ipython_config_init.py PROFILE_DIR/ipython_config.py
$ ipython ( or ./start_bag.sh )
```

The '.bashrc' file sets the Python path of laygo2 by setting environment variables. The profile directory is printed to the shell. (Example: WORK_DIR/.ipython/profile_default/ipython_config.py) Overwrite the default 'ipython_config_init.py' to the config file in the repository.

If `PROFILE_DIR` is not output, or it doesn't contain `WORK_DIR`, check if the `.ipython` directory exists in `WORK_DIR`. If so, `PROFILE_DIR = WORK_DIR/.ipython/profile_default`.

```python    
    import sys
    print(sys.path)
```

The printed Python path should include 'WORK_DIR/laygo2'.

![laygo2_python_path_check](./assets/img/laygo2_path.JPG)