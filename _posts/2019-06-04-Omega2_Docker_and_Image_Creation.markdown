---
layout: post
title:  "Omega2 Docker and Image Creation"
date:   2019-06-04 13:03:27 -0600
tags: SBv2 Omega2
---
First you should install Docker. I am using ubuntu 18.04 LTS and installed docker-ce following [these][docker-docs] directions.

Once installed, you can follow the Omega2 Cross-compiling tutorial found here (though we will do some modification to the image, so don't really follow the linked tutorial).

The basics I follow are:

```bash
$ docker pull onion/omega2-source
$ docker run -it onion/omega2-source /bin/bash
```

This will pull the Omega2 docker image and then create a docker instance. The instance only needs to be created once, e.g. you execute "docker run" for every unique instance you want to create.

To exit docker, just type exit:

```bash
$ exit
```

This will shutdown the docker instance you just created.

If you want to reconnect to the instance you created already, find the docker instance container ID, start that container, then attach the container to access the image.

```bash
$ docker ps -a  #this will look up all the containers on your machine and list them. Copy the container ID you want to load
$ docker start [CONTAINER ID]
$ docker attach [NAME]  #you can use tab complete to auto fill in the container name once it is started.
```

After the attach command, you will be inside your docker image in your command line terminal.
Note: you might have to press enter to see the terminal input.

As of June 2019, the Omega2 docker container initializes the git repo as lede-17.01. We want to be on the openwrt18.06 branch.

```bash
$ git checkout openwrt-18.06
$ git pull
```

This will switch us to the openwrt-18.06 branch as then pull the latest version.

Now we need to pull all the Omega2 specific "feeds" for our OpenWRT build process. Feeds are the way OpenWRT pulls sources files for particular drivers/programs/features that can be compiled into our image. Run these commands from with in the /source folder (default start folder):

First we need to add our own feed to install the Teensyloader commandline tool. Open the `feeds.config` file. You can use whatever terminal editor you like (e.g. `vim`, `nano`, etc.).

```bash
$ vim feeds.configure
```

Now add this line to the end of the file.

```bash
src-git teensy https://github.com/JEB12345/onion-teensyloader.git;master
```

Once added, close the `feeds.config` file and run the bash script below.

```bash
$ bash scripts/onion-feed-setup.sh
```

This command will show you the version and build for the omega2 image:

```bash
$ python scripts/onion-setup-build.py
```

Now we need to configure our image. Run the command below inside the /source folder (should be the default folder you start the container in):

```bash
$ make menuconfig
```

If you get a build dependency error like below:

```bash
Build dependency: Please install GNU 'time' or BusyBox 'time' that supports -f
```

Run the following commands:

```bash
$ apt-get update
$ apt-get -f install  #This should fix the nodejs error you will get if you try to install time right after update
$ apt-get install time
$ make menuconfig  #Run the config command again
```

Now you should have a terminal input window like so:

![main menu](/assets/img/main_menu.png "Main Menu")

> Note: The config menu should be setup for compile for our Omega2 boards. If not, then something didn't initialize well in the previous steps. Either delete your container and create it again. Or try to hack. I recommend starting the container over.

Now navigate to "Network -->", then "Routing and Redirection -->" and select "relayd" twice so that there is an astrix.

![Select Relayd](/assets/img/relayd.png "Select Relayd")

Now, go back to the main menu screen and navigate to "Onion -->" then "Libraries -->". Select the following:
* pyOnionGpio
* pyOnionI2C
* python-spidev
* python3-onioin-i2c
* python3-spidev

Your window should look this this:

![Select Libraries](/assets/img/libraries.png "Select Libraries")

Now, from the main menu navigate to "Languages -->" then " Python -->". Select the follow (you will have to scroll to find them):
* python-pip
* python-pyserial
* python3-pip

I have the pip lines highlighted in the images below:

![Python2](/assets/img/python_pip.png "Select Python2 Libraries")

![Python3](/assets/img/python_pip3.png "Select Python3 Pip")

This should complete our image customization and we can exit the config menu. Make sure you select yes when you exit to save your configuration.

![Exit](/assets/img/exit_menu.png "Make sure to select < Yes >")

Now we can make our image.
Note: this can take quite a long time. Especially on the first compile.

```bash
$ make -j
```

Once this completes, you can run an onion provided script to rename the generated bin file. Run this from the /souce folder:

```bash
$ python scripts/onion-setup-images.py
```

There should now be a file located in /source/bin/targets/ramips/mt76x8/ call "omega2-[version number]-[build number].bin"

To flash this .bin file onto the Omega2 board, you have several options. Since the file is located with in your Docker container, it is separate from your normal  file system. The quickest, though not easiest, would be to use curl's file server feature. You can do this by using the following commands:

```bash
$ curl --upload-file ./[file name] https://transfer.sh/[file name]
```

When writing this guide, my curl command was:

```bash
$ curl --upload-file ./omega2p-v0.3.2-b222.bin https://transfer.sh/omega2p-v0.3.2-b222.bin
```

Here is the output from my Docker terminal:

![Terminal Output](/assets/img/curl_transfer.png "Terminal Output")

Once this is complete, you will see a web address appended to the right of your terminal input line. You can now use wget on another computer to download the file. e.g.:

```bash
$ wget https://transfer.sh/2aIYM/omega2p-v0.3.2-b222.bin  #This is the address I was using during the writing of this guide, copied from the terminal output from the image above.
```

If you have an onion board with a working OS and/or internet access, you can follow [this][onion-manual] tutorial to update the OS to the version you just compiled. Instead of downloading the bin from Onion's website, use wget (demonstrated above) in the /tmp folder (or access the bin file from an SD card or USB drive, if possible).

If you don't have a working OS or internet access on the Omega2 board, you can wget the file onto a personal machine and upload the file using Onion's USB instructions found [here][onion-usb].

> USB instructions only work if you are using the Omega2 expansion board. You might be able to use an SD card, but this will require some hacking since no guide exists (yet).

You should now have an updated Omega2 board with our customized OS running the latest (at the time this guide was written) Omega OS version.


[docker-docs]: https://docs.docker.com/install/linux/docker-ce/ubuntu/
[onion-usb]: https://docs.onion.io/omega2-docs/Firmware-Flashing-from-USB-storage.html
[onion-manual]: https://docs.onion.io/omega2-docs/manual-firmware-installation.html
