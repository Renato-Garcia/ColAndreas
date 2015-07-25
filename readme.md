#ColAndreas

ColAndreas is a plugin for SA-MP which creates a simulation of the San Andreas world. Using the [Bullet Physics library](http://bulletphysics.org/), ColAndreas
gives the server knowledge of the game environment itself.

**Watch it in action:** [https://www.youtube.com/watch?v=aSabQWqQBkI](https://www.youtube.com/watch?v=aSabQWqQBkI)

##Download

Go to the [releases page](https://github.com/Pottus/ColAndreas/releases) and grab the latest *ColAndreas_Precompiled* zip file, it contains the Windows and Linux versions of the plugin, the include file for PAWN and the Wizard executable.

##Linux note

* If you get this error: `Failed (libBulletCollision.so.2.82: cannot open shared object file: No such file or directory)` it means your system doesn't have the required libraries. There are 2 possible solutions for this issue:
	* Use the statically linked version of the plugin (*ColAndreas_static.so*).
	* Install the Bullet libraries using your distro's package manager.
		* **Ubuntu:** `sudo apt-get install libbulletcollision2.82:i386 libbulletdynamics2.82:i386 liblinearmath2.82:i386 libbulletsoftbody2.82:i386`
		* Please note that you must install the *32bit* (i386) version of the libraries, even if you use a 64bit OS.

##Installing

1. If you haven't done it yet, generate the ColAndreas database (*ColAndreas.cadb*). For doing this, run the Wizard executable (*ColAndreasWizard.exe*) and follow the instructions. Save the file in the folder *scriptfiles/colandreas*
2. Add the server plugin:
	* **Windows:** Copy *ColAndreas.dll* to the *plugins* folder of your server and add "ColAndreas" to the *plugins* line in *server.cfg*
	* **Linux:** Copy *ColAndreas.so* or *ColAndreas_static.so* (see the note above) and add "ColAndreas.so" or "ColAndreas_static.so" to the *plugins* line in *server.cfg*
3. Copy *ColAndreas.inc* to the folder *pawno/include*
4. Call `CA_Init()` from `OnGameModeInit` in order to initialize the plugin:
```pawn
public OnGameModeInit()
{
	// Objects need to be removed BEFORE calling CA_Init
	CA_RemoveBuilding();

	// Initialize the plugin
	CA_Init();

	// Create objects and add them to the simulation
	CreateDynamicObject_SC();

	return 1;
}
```

##Building

####Windows

* Use the provided solution files (.sln) for building the project using Visual Studio.

####Linux

#####Requirements
* A C++ compiler with C++11 support ([GCC](https://gcc.gnu.org/)/[Clang](https://clang.llvm.org/))
* [CMake](http://www.cmake.org/) build system.
* [Bullet Physics](http://bulletphysics.org/) development files.
* **Optional:** [git](https://git-scm.com/) version control.

#####Dynamic version
* Install the required packages:
	* **Ubuntu:** `sudo apt-get install libbullet-dev:i386 cmake g++-multilib git`
	* **IMPORTANT:** You require the 32bit version of Bullet, even if you're using a 64bit OS.
* Get the source code:
```bash
# Using git
git clone https://github.com/Pottus/ColAndreas.git
cd ColAndreas

# Or if you prefer the .zip file
wget https://github.com/Pottus/ColAndreas/archive/master.zip
unzip master.zip
rm master.zip
cd ColAndreas-master
```
* Create a build folder and run CMake
```bash
mkdir Linux/build
cd Linux/build

# Replace "Release" with "Debug" if you want debug symbols
cmake -DCMAKE_BUILD_TYPE=Release ..
```
* Build the project
```bash
make -j4
```
* The compiled file will be placed in the folder *Linux/build*

#####Static version
* Install the required packages:
	* **Ubuntu:** `sudo apt-get install cmake g++-multilib git`
* Compile Bullet from source:
```bash
# Getting the source code
wget https://github.com/bulletphysics/bullet3/archive/2.83.5.tar.gz
tar -xzvf 2.83.5.tar.gz
rm 2.83.5.tar.gz
cd bullet3-2.83.5

# Creating a build directory
mkdir build
cd build

# Make sure we generate 32bit code
export CFLAGS="-m32"
export CXXFLAGS="-m32"

# Running CMake
cmake -DBUILD_EXTRAS=OFF -DBUILD_BULLET3=OFF -DBUILD_UNIT_TESTS=OFF -DBUILD_BULLET2_DEMOS=OFF -DBUILD_CPU_DEMOS=OFF -DCMAKE_INSTALL_PREFIX=../output  ..

# Building and installing the project
make -j4
make install

# Going back to where we came from
cd ../..
```
* Compile ColAndreas and tell CMake to use the version of Bullet that we just compiled:
```bash
git clone https://github.com/Pottus/ColAndreas.git
cd ColAndreas

mkdir Linux/build
cd Linux/build

cmake -DCMAKE_BUILD_TYPE=Release -DBULLET_ROOT=$(pwd)/../../../bullet3-2.83.5/output ..
make -j4
```
* The compiled file will be placed in the folder *Linux/build*
