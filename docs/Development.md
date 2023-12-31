# Developing RediSearch

Developing RediSearch involves setting up the development environment (which can be either Linux-based or macOS-based), building RediSearch, running tests and benchmarks, and debuugging both the RediSearch module and its tests.

## Cloning the git repository
By invoking the following command, RediSearch module and its submodules are cloned:
```sh
git clone --recursive https://github.com/RediSearch/RediSearch.git
```
## Working in an isolated environment
There are several reasons to develop in an isolated environment, like keeping your workstation clean, and developing for a different Linux distribution.
The most general option for an isolated environment is a virtual machine (it's very easy to set one up using [Vagrant](https://www.vagrantup.com)).
Docker is even a more agile solution, as it offers an almost instant solution:
```
search=$(docker run -d -it -v $PWD:/build debian:buster bash)
docker exec -it $search bash
```
Then, from whithin the container, ```cd /build``` and go on as usual.
In this mode, all installations remain in the scope of the Docker container.
Upon exiting the container, you can either re-invoke the container with the above ```docker exec``` or commit the state of the container to an image and re-invoke it on a later stage:
```
docker commit $search redisearch1
docker stop $search
search=$(docker run -d -it -v $PWD:/build rediseatch1 bash)
docker exec -it $search bash
```

## Installing prerequisites
To build and test RediSearch one needs to install serveral packages, depending on the underlying OS. Currently, we support the Ubuntu/Debian, CentOS, Fedora, and macOS.

If you have ```gnu make``` installed, you can execute
```
cd RediSearch
make setup
```
Alternatively, just invoke the following:
```
cd RediSearch
./deps/readies/bin/getpy2
./system-setup.py
```
Note that ```system-setup.py``` **will install various packages on your system** using the native package manager and pip. This requires root permissions (i.e. sudo) on Linux.

If you prefer to avoid that, you can:

* Review system-setup.py and install packages manually,
* Use an isolated environment like explained above,
* Utilize a Python virtual environment, as Python installations known to be sensitive when not used in isolation.

Next, execute the following, to complete dependency acquisition:
```
make fetch
```

## Installing Redis
As a rule of thumb, you're better off running the latest Redis version.

If your OS has a Redis 5.x package, you can install it using the OS package manager.

Otherwise, you can invoke ```./deps/readies/getredis5```.

## Getting help
```make help``` provides a quick summary of the development features.

## Building from source
```make build``` will build RediSearch.
To enable unit tests, add ```TEST=1```.
Note that RediSearch uses [CMake](https://cmake.org) as its build system. ```make build``` will invoke both CMake and the subsequent make command that's required to complete the build.
Use ```make clean``` to remove built artifacts. ```make clean ALL=1``` will remove the entire ```RediSearch/build``` directory.

### Diagnosing CMake
To get a glimpse into CMake decesion process, add ```WHY=1``` to the build command.
CMake stores its intermediate files in ```RediSearch/build```.
Afterwards, one can use:
```
cd build
make -n
```
or:
```
cd build
make V=1
```
to further diagnose the build process.

## Running Redis with RediSearch
The following will run ```redis``` and load RediSearch module.
```
make run
```
You can open ```redis-cli``` in another terminal to interact with it.

## Running tests
There are several sets of unit tests:
* C tests, located in ```src/tests```, run by ```make c_tests```.
* C++ tests (enabled by GTest), located in ```src/cpptests```, run by ```make cpp_tests```.
* Python tests (enabled by RLTest), located in ```src/pytests```, run by ```make pytest```.

One can run all tests by invoking ```make test```.
A single test can be run using the ```TEST``` parameter, e.g. ```make test TEST=regex```.

## Debugging
To build for debugging (enabling symbolic information and disabling optimization), run ```make DEBUG=1```.
One can the use ```make run DEBUG=1``` to invoke ```gdb```.
In addition to the usual way to set breakpoints in ```gdb```, it is possible to use the ```BB``` macro to set a breakpoint inside RediSearch code. It will only have an effect when running under ```gdb```.

Similarly, Python tests in a single-test mode, one can set a breakpoint by using the ```BB()``` function inside a test.

