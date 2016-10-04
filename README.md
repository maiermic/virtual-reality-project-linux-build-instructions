# Virtual Reality Project - Linux Build Instructions

I will explain how to

 - build Boost, OpenSG2, VRPN and inVRs in Linux
 - configure, build and run the initial student project (of lecture [Virtual Reality][vr-lecture] at Ludwig Maximilians University Munich).

[vr-lecture]: http://www.nm.ifi.lmu.de/teaching/Vorlesungen/2016ss/vr/

**!!! Caution !!!**
Follow the instructions carefully and think about each command before you run it. I assume no responsibility if you break your system.


## Project

The initial `cmake` configuration of the student project only allows to build and run your project in Windows or in the cave at V2C/LRZ. I describe how to modify the configuration files and install the required dependencies to be able to run it on your Linux system. I use Linux Mint 18, but you should be able to adjust the instructions to your favorite Linux distribution.


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

I will describe how to build and install them in the given order.


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

Then we go to the extracted source directory:

```sh
cd boost_1_53_0
```

First we have to prepare/configure installation. `--prefix=/usr/local` defines `/usr/local` as installation target directory:

```sh
sudo ./bootstrap.sh --prefix=/usr/local
```

Now we can start installation:

```sh
sudo ./b2 install
```


#### 2. OpenSG2

OpenSG2 depends on a lot of packages. I've grouped them in the following installation command. If a package is not available in your Linux distribution, you have to search for equivalent packages. If you can not find one or if you get a build error open an issue and I'll try to help.

```sh
sudo apt-get install \
  cmake \
  zlib1g zlib1g-dev \
  libfreetype6 libfreetype6-dev \
  libtiff5 libtiff5-dev \
  libgdal1i libgdal-dev \
  libopenexr22 libopenexr-dev \
  freeglut3 freeglut3-dev \
  qt4-default \
  libpython-all-dev \
  doxygen \
  fontconfig libfontconfig1 libfontconfig1-dev \
  libxi6 libxi-dev \
  libxmu6 libxmu-dev \
  libqt4-opengl libqt4-opengl-dev
```

After you installed the required dependencies we need to clone the OpenSG repository:

```sh
git clone git://git.code.sf.net/p/opensg/code opensg
```

Go to the source directory:

```sh
cd opensg
```

and checkout the version of 2015-01-16:

```sh
git checkout edc6d77bf263695d0ca0e11306d1fecd1be18510
```

Next create a build directory and go into it:

```sh
mkdir build && cd build
```

First we generate a build environment using `cmake`:

```sh
cmake ..
```

> ##### Notes
> If you like to change the installation directory use `CMAKE_INSTALL_PREFIX`, for example
>
> ```sh
> cmake -D CMAKE_INSTALL_PREFIX=/sw/opensg/2.0/2015-01-16 ..
> ```
> 
> If you installed Boost in a different directory specify `BOOST_ROOT`, for example
>
> ```sh
> cmake -D BOOST_ROOT=/sw/boost/1.53.0 ..
> ```
>
> Both combined look like this:
>
> ```sh
> cmake -D BOOST_ROOT=/sw/boost/1.53.0 -D CMAKE_INSTALL_PREFIX=/sw/opensg/2.0/2015-01-16 ..
> ```

Build the project:

```sh
make
```

and install OpenSG:

```sh
sudo make install
```


#### 3. VRPN

Download VRPN source (version 7.33):

```sh
wget https://github.com/vrpn/vrpn/releases/download/v07.33/vrpn_07_33.zip
```

Extract source archive:

```sh
unzip vrpn_07_33.zip
```

We don't need the archive anymore. So let's delete it:

```sh
rm -f vrpn_07_33.zip
```

Go to source directory:

```sh
cd vrpn
```

Create a build directory and go into it:

```sh
mkdir build && cd build
```

Generate build environment using `cmake`:

```sh
cmake ..
```

Build project:

```sh
make
```

Install VRPN:

```sh
sudo make install
```


#### 4. inVRs

Install subversion (if not already installed):

```sh
sudo apt-get install subversion
```

Checkout source code revision 2647 (current version in V2C):

```sh
svn checkout -r 2647 https://svn.lrz.de/repos/inVRs/branches/inVRs_OSG
```

Go to source directory:

```sh
cd inVRs_OSG/
```

Open `user.cmake` in your favorite text editor. 

 - enable VRPN support
   - uncomment the line `#set (INVRS_ENABLE_VRPN_SUPPORT ON)`
   - just remove `#` at the beginning; it should look like this `set (INVRS_ENABLE_VRPN_SUPPORT ON)`
 - configure VRPN:
   - replace `#set (vrpn_ROOT_DIR $env(VRPN_HOME) )` with `set (vrpn_ROOT_DIR /usr/local)`
   - uncomment `#set (vrpn_INCLUDE_DIR $(vrpn_ROOT_DIR)/include )` to `set (vrpn_INCLUDE_DIR $(vrpn_ROOT_DIR)/include )`

     > If cmake complains that it can not find `vrpn_INCLUDE_DIR`, uncomment it back and try only with set `vrpn_ROOT_DIR` variable.

 - configure OpenSG:
   - replace `#set (OPENSG_BIN_DIR /bin)` with

     ```cmake
     set (OPENSG_BIN_DIR /path/to/opensg/build/bin)
     set (OPENSG_ROOT /usr/local/)
     ```

     and change `/path/to/opensg` to the source directory (cloned repository) of OpenSG2.

To fix a compilation error with my GNU C++ compiler, (I had to) edit source file `/path/to/inVRs_OSG/src/inVRs/Modules/Interaction/SharedManipulationMerger.cpp`. Replace `isnan` with `std::isnan` in line 207:
 - **before**

   ```c++
                   if (isnan(result.position[0])) {
   ```
 - **after**

   ```c++
                   if (std::isnan(result.position[0])) {
   ```

Next create a build directory and go into it:

```sh
mkdir build && cd build
```

Generate build environment using `cmake`:

```sh
cmake -C ../user.cmake -D CMAKE_CXX_FLAGS="-std=c++11" ..
```

Build project:

```sh
make
```

Install inVRs:

```sh
sudo make install
```



### Configuration Of Your Project

Go to your student project directory:

```sh
cd /path/to/your/student_project
```

Open `./cmake/Modules/AddProject.cmake` and add

```sh
        target_link_libraries(${ADD_PROJECT_NAME} -lGL -lpthread)
```

after line 149. Result look like this (without line numbers at the beginning):


```
149|  include_directories(${${ADD_PROJECT_NAME}_INCLUDE_DIRS})
150|  target_link_libraries(${ADD_PROJECT_NAME} -lGL -lpthread)
151|  target_link_libraries(${ADD_PROJECT_NAME} ${${ADD_PROJECT_NAME}_LIBRARIES})
```

Next open `./user.cmake`. You find a system differentiation in line 54:

```cmake
54|if(WIN32)
55|  set(BOOST_ROOT "C:/libraries/install/${libPathArchitecture}/boost${boost_ver}"  CACHE PATH ${USER_CMAKE_TEXT})
...
70|else()
71|  set(BOOST_ROOT /sw-5.1.0/boost/1.53.0  CACHE PATH ${USER_CMAKE_TEXT})
...
```

The `else` branch in line 70 defines installation directories of the dependencies for the V2C/LRZ. They are different on our system. Hence, we need to check if we are running `cmake` on our own system. Therefore, I use a environment variable `IS_MY_LINUX_SYSTEM` to identify my system (you might prefer a different variable name). Add before line 70:

```cmake
elseif(IS_MY_LINUX_SYSTEM)
    set(BOOST_ROOT /path/to/installation/of/boost/ CACHE PATH ${USER_CMAKE_TEXT})
    set(OPENSG_ROOT /path/to/installation/of/opensg/ CACHE PATH ${USER_CMAKE_TEXT})
    set(inVRs_ROOT_DIR /path/to/installation/of/inVRs/ CACHE PATH ${USER_CMAKE_TEXT})
    set(CAVESceneManager_DIR ${inVRs_ROOT_DIR}/lib/cmake/CAVESceneManager CACHE PATH ${USER_CMAKE_TEXT})
    set(VRPN_ROOT_DIR /path/to/installation/of/vrpn/ CACHE PATH ${USER_CMAKE_TEXT})
```

Remember to adjust all paths to your system. For example, I installed all dependencies in its own subdirectory of `/sw`:

```cmake
elseif(IS_MY_LINUX_SYSTEM)
    set(BOOST_ROOT /sw/boost/1.53.0/ CACHE PATH ${USER_CMAKE_TEXT})
    set(OPENSG_ROOT /sw/opensg/2.0/2015-01-16/ CACHE PATH ${USER_CMAKE_TEXT})
    set(inVRs_ROOT_DIR /sw/inVRs/rev2647/ CACHE PATH ${USER_CMAKE_TEXT})
    set(CAVESceneManager_DIR ${inVRs_ROOT_DIR}/lib/cmake/CAVESceneManager CACHE PATH ${USER_CMAKE_TEXT})
    set(VRPN_ROOT_DIR /sw/vrpn/7.33/ CACHE PATH ${USER_CMAKE_TEXT})
```

We have to convert our environment variable to `BOOL`. Do this by setting a cmake variable with the same name `IS_MY_LINUX_SYSTEM` by adding the following line at the bottom of the `WHAT YOU CAN EASILY CHANGE` section (line 19):

```cmake
# Set environment variable IS_MY_LINUX_SYSTEM=TRUE on your own Linux system
# and set IS_MY_LINUX_SYSTEM=FALSE or do not define it, when you generate build environment in V2C/LRZ.
set(IS_MY_LINUX_SYSTEM $ENV{IS_MY_LINUX_SYSTEM} CACHE BOOL {USER_CMAKE_TEXT})
```

Further, you need to replace `config/mono.csm` in your student project with the `mono.csm` of this repository. It is a copy of the file `/sw/config/opensg/csm/mono.csm` of the V2C/LRZ system (see `start_demo_Release` script).


### Building Your Project

We do it in a similar way as in the V2C/LRZ (see `linux_compile` script). If it does not already exist, create a build directory in your project root directory:

```sh
mkdir build
```

Go into it:

```sh
cd build
```

When you generate build environment set `IS_MY_LINUX_SYSTEM=TRUE` before (set it to `FALSE` or leave it, when you generate build environment in V2C/LRZ):

```sh
IS_MY_LINUX_SYSTEM=TRUE cmake -C ../user.cmake -D CMAKE_CXX_FLAGS="-std=c++11" ..
```

Building project:

```sh
make
```


### Running Your Project

First we need to start `csmrenderserver` that we build with inVRs:

```sh
/path/to/inVRs_OSG/bin/csmrenderserver -a 127.0.0.1 -g 800x600+100+100 -w front
```

A window opens, but nothing is rendered yet. We need to run our program to see the rendered result in this window. I assume you are still in the build directory (otherwise change paths as needed):

```sh
../exe/MyProject -f ../config/mono.csm
```

A small black window opens where you can input keyboard commands to your program. Your application is rendered in the window of the render server.
