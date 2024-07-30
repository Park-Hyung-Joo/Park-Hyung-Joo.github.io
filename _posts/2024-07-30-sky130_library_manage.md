---
title: Minimal Setup for sky130 - Library Mangement
date: 2024-07-30 03:30:00 +0900
categories: [Hardware,Open-source-Silicon]
tags: [sky130,magic,netgen,xschem]     # TAG names should always be lowercase, 띄어쓰기도 금지
---
------------------------------------------

### Personal Library

Create a directory with the same name as the library in the `magic_layout` directory.

![newlib_private](./assets/img/newLib_private.JPG)

Add the new library path in the '.maginit_personal' file in the workspace directory.

![new_library_path](./assets/img/new_lib_path.JPG)

Now you can use this library. See the "Testing the new libraries" section for instructions on how to determine which library your Python code will generate layout files to.

### Public Library

You can also create a library directory outside of the workspace directory.

![commont_library](./assets/img/common_lib.JPG)

Add the new public library path in the `.maginit` file.

![description1](./assets/img/new_common_lib.JPG)

If you are working on a team project, you can use this kind of public library. However, this method requires editing and sharing the tracked '.maginit' on GitHub. In this case, consider using a **forked workspace repository**. When a project master forks the repository and the team members clone the forked repository instead of the default workspace repository, tracked files can be reliably modified and shared via GitHub.

### Testing the New Libraries

To change the generation path, you have to change some variables in the Python code. First, change the `libname` to the new library name (logic_generated -> logic_train).

![description2](./assets/img/set_libname.JPG)

Then, change 'libpath' to where the `logic_train` directory is placed. The 'libpath' is a parameter of the function "laygo2.interface.magic.export". In this tutorial, `libpath = "/WORK/magic_layout"`

![description3](./assets/img/libpath_change.JPG)

Generate TCL and mag files in the same way as in the previous section.

Close Magic and check if the mag file is generated in `logic_train`.

Open Magic again and load the layout itself in the public library.

```
% load logic_train_tinv_small_1x
```

You can see the layout is loaded. Now you can use this library through laygo2.

![description4](assets/img/common_lib_loaded.JPG)