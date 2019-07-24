---
layout: post
title:  "NTRT SUPERball Model"
date:   2019-07-24 00:00:22 -0600
tags: SBv2 NTRT Code Controller
---

> This document assumes the reader has a basic understanding of [C++][c++], [Git][git], and [YAML][yaml]. Some functions will be explained, but not all concepts or features will be. If you want more information, please refer to the their respective links.

## NTRT

The NASA Tensegrity Robotics Toolkit (NTRT) is a collection of software modules for the modeling, simulation, and control of Tensegrity Robots. The NTRT Simulator is a tensegrity-specific simulator built to run ontop of the Bullet Physics Engine, version 2.82.

Basic documentation on installation and functionality can be found [here][readthedocs]. I recommend starting here if you have never installed NTRT before.

> NTRT only works on Linux. There is a VM image you can use. However, I find that a local install on a Linux OS increases your development time significantly. Alternatively, you can install NTRT on a windows 10 machine through [Windows Subsystem for Linux (WSL)][WSL] and follow the "installing NTRT without a vertual machine" instructions [here][withoutVM].

> NTRT's source code my be found on GitHub, [here][NTRT].

### SUPERball v2 NTRT model

Once you have NTRT installed and running, you will need to change the git NTRTsim branch to "SBv2_model". You can do this by running the git command within any of the NTRTsim git folders.

```bash
$ git checkout SBv2_model
```

You can check to see which branch you on by using the `git status` command. You should get an output like the one below:

```bash
$ git status
On branch SBv2_model
Your branch is up to date with 'origin/SBv2_model'.

Untracked files:
  (use "git add <file>..." to include in what will be committed)

nothing added to commit but untracked files present (use "git add" to track)
```

Once on the SBv2_model branch, navigate to the model's source code located in `NTRTsim/src/dev/jbruce/SBv2/`. Here you should find the actual files that generate and load the SUPERball v2 NTRT model using the YAML builder.

> Information and documentation on NTRT's YAML builder can be found [here][yaml].

These should be the basic files in the `SBv2/` folder:

```bash
$ ls
AppSBTest.cpp                       SBv2_yaml_files/
CMakeLists.txt                      SingleCableController.cpp
SBv2_model_payload_rotate_x-z.yaml  SingleCableController.h
SBv2_model_rotate_x-z.yaml
```

* `AppSBTest.cpp` - This is main program file that will be built and used to run our NTRT program
* `CMakeLists.txt` - An NTRT required file that tells the builder script what to compiled
* `SBv2_yaml_files/` - Folder containing YAML files used as children for the current level of YAML files
* `SBv2_model_rotate_x.yaml & SBv2_model_payload_rotate_x.yaml` - Main YAML files for generating the SBv2 model without or with a payload.
* `SingleCableController.cpp/.h` - Implementation files for the test controller used on SBv2 model

Now that we know the basic files and folders used for building our simulation model, let's quickly run the simulation.

First let's make sure we built NTRT properly. Navigate to NTRTsim's bin folder and run the build script:

```bash
$ cd NTRTsim/bin
$ ./build.sh
```

Once it has built successfully, navigate to the SBv2 build folder. The folder structure should be the same as the src folders, but instead in build. Thus:

```bash
$ cd NTRTsim/build/dev/jbruce/SBv2/
```

Within this folder, you will see an AppSBTest executable file and some CMake files and folders. Run the simulation by calling the executable followed by the yaml model you would like to load. Thus for loading the SUPERball v2 model with out a payload, use the following command:

```bash
$ ./AppSBTest ../../../../src/dev/jbruce/SBv2/SBv2_model_rotate_x-z.yaml
```

If all is well, the simulation should appear in a new window and some outputs should appear in your terminal.

To load the SUPERball v2 model **with** a pyalod, use the very similar command:

```bash
$ ./AppSBTest ../../../../src/dev/jbruce/SBv2/SBv2_model_payload_rotate_x-z.yaml
```

This shows the power of using YAML. You can load a new modeling into the same simulation environment by just referencing a new YAML model. There are limitation to this, but for small changes to your model YAML should greatly improve development.  

[c++]: http://www.cplusplus.com/doc/tutorial/
[git]: https://git-scm.com/
[yaml]: https://learnxinyminutes.com/docs/yaml/
[readthedocs]: https://ntrtsim.readthedocs.io/en/latest/
[WSL]: https://itsfoss.com/install-bash-on-windows/
[withoutVM]: https://ntrtsim.readthedocs.io/en/latest/setup.html#installing-ntrt-without-a-virtual-machine
[NTRT]: https://github.com/NASA-Tensegrity-Robotics-Toolkit/NTRTsim
[yaml]: https://ntrtsim.readthedocs.io/en/latest/yaml-model-builder.html
