# How-to-install-GCC-on-Red-Hat-Enterprise-Linux
How to install newer version of GCC (with C++11 support) on Red Hat Enterprise Linux

# Download and Install Red Hat Enterprise Linux
for free developer version first go to Red Hat Developer and create an account (you will need this id later to register your rhel) then go to Product Downloads and download you prefered version.


>[Red Hat Developer](https://developers.redhat.com/)

>[Red Hat Product Downloads](https://access.redhat.com/downloads/)


You may also search Internet Archive for older release.

>[Internet Archive](https://archive.org)

## Red Hat Enterprise Linux Server 6.10 installation
I have used Red Hat Enterprise Linux Server 6.10 to match my production environment. you can install basic server or even desktop environment during the setup and I assume you know how to setup a linux box.

# Installing GCC via official Red Hat Developer Toolset
The first method is using official Red Hat Repository, for this purpose I am following instruction from this URL

>[Red Hat Developer Toolset](https://developers.redhat.com/products/developertoolset/hello-world#fndtn-rhel-6)

there is also another excelent tutorial at this address, its for rhel 7 but I assume you can use for other version the same way.


>[How to install GCC 8 and Clang/LLVM 6 on Red Hat Enterprise Linux 7](https://developers.redhat.com/blog/2019/03/05/yum-install-gcc-8-clang-6/)
 
## 1. configure your network environment
check that you have connected to internet, and if you should use proxy configure it accordingly.

we will use subscription-manager and yum during this proccess and if you need to configure them to go through proxy you can set it by editing these two file.

I have used polipo to route my trafic from tor network, assuming polipo listening on 192.168.1.105 to 8123 my configuration will be as follow.

first switch to privilaged mode

```sh
su
```

```sh
vi /etc/rhsm/rhsm.conf

# an http proxy server to use
proxy_hostname = 192.168.1.105

# port for http proxy server
proxy_port = 8123

# user name for authenticating to an http proxy, if needed
proxy_user =

# password for basic http proxy auth, if needed
proxy_password =

# host/domain suffix blacklist for proxy, if needed
no_proxy =

```

```sh
vi /etc/yum.conf
proxy=http://192.168.1.105:8123/

```

## 2. Register your Red Hat Enterprise Linux
now use subscription-manager to register your linux box as follow

```sh
subscription-manager register --username user --password pass --auto-attach
```

you will get your registeration ID along the message ```Status: Subscribed``` indicating you have registered successfully.

## 3. Enable necessary software repositories
check access to the Red Hat Software Collections (RHSCL) and Red Hat Developer Toolset (DTS) and Red Hat optional(OPTIONAL) by issuing following command.

```sh
subscription-manager repos --list | grep -P "ID:.+?(rhscl|dts|optional)"
```

Then find your desired repository as follow and enable them as follow:


```sh
subscription-manager repos --enable rhel-server-rhscl-6-rpms
subscription-manager repos --enable rhel-6-server-optional-rpms
subscription-manager repos --enable rhel-server-dts2-6-rpms

```

If you are using different linux version ```6``` may be replaced by your version ```7``` or ```8```.

If you are using a desktop edition of Red Hat Enterprise Linux, change ```-server-``` to ```-desktop-``` .

finally update

```sh
yum check-update
```

The benefit of software collections is that you can have GCC 6, 7, and 8 installed at the same time along with the base GCC 4 that shipped with Red Hat Enterprise Linux. You can easily switch between versions with scl enable.  Note: The latest interpreted languages, such as Python 3, PHP 7, and Ruby 2.5, are also available via Red Hat Software Collections. 

## 4. Setup your development environment 
You can search to find which Developer Toolset is available with this command:

```sh
yum search devtoolset
```

finally we will install our prefered devtoolset by issuing:

```sh
yum install devtoolset-8
```

```sh
gcc -v
...
gcc version 4.4.7 
```

```sh
/opt/rh/devtoolset-8/root/usr/bin/gcc -v
...
gcc version 8.3.1

```

## 5. compile and run with new GCC
Now its time to write our first program and compile it.

```cpp
#include <iostream>
#include <thread>

void func(){
	std::cout << "Hello from thread" << std::endl;
}

int main() {
	std::cout << "Hello from main" << std::endl;

	std::thread t(func);
	t.join();

	return 0;
}
```

to compile this program we will issue this command:

```sh
/opt/rh/devtoolset-8/root/usr/bin/g++ -pthread main.cpp
```

and finally we run our program as follow:
```sh
./a.out
```

## 6. compile and run with new GCC
