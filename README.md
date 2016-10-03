# Virtual Reality Project - Linux Build Instructions

I will explain how to

 - build Boost, OpenSG2, VRPN and inVRs in Linux
 - configure, build and run the initial student project (of lecture [Virtual Reality][vr-lecture] at Ludwig Maximilians University Munich).

[vr-lecture]: http://www.nm.ifi.lmu.de/teaching/Vorlesungen/2016ss/vr/


## Project

The initial `cmake` configuration of the student project only allows to develop in Windows and run the project in the cave at V2C/LRZ. We will modify the configuration files and install the required dependencies. Our goal is to be able to run it on our Linux system. I use Linux Mint 18, but you should be able to adjust the instructions to your distribution.


### Dependencies

You need to build the following dependencies:

 - [Boost][boost website] (version 5.13.0)
 - [OpenSG2][opensg website] (Alpha released on 2015-01-16)
 - [VRPN][vrpn website] (version 7.33)
 - [inVRs][invrs website] (revision 2647)

[boost website]: http://www.boost.org/
[opensg website]: http://www.opensg.org/
[vrpn website]: https://github.com/vrpn/vrpn
[invrs website]: http://www.invrs.org/

I will describe how to build and install them in the given order. I choose `/sw` as installation target directory.


#### 1. Boost

First we install (GNU) C++ compiler (not shipped with Linux Mint 18)

```sh
sudo apt-get install g++
```

Then we download Boost 1.53.0 source code (as 7z-archive) into the current directory:

```sh
wget -O boost_1_53_0.7z https://sourceforge.net/projects/boost/files/boost/1.53.0/boost_1_53_0.7z/download
```

Extract source code to `./boost_1_53_0` directory:

```sh
7z x boost_1_53_0.7z
```

We don't need the archive anymore. So let's delete it:

```sh
rm -f boost_1_53_0.7z
```

Then we change to the extracted source directory:

```sh
cd boost_1_53_0
```

First we have to prepare/configure installation. `--prefix=/sw` defines `/sw` as installation target directory:

```sh
sudo ./bootstrap.sh --prefix=/sw
```

Now we can start installation:

```sh
sudo ./b2 install
```


#### 2. OpenSG2




#### 3. VRPN




#### 4. inVRs



