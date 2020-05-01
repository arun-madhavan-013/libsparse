# libsparse
## Linux port of Android's libsparse

This project is an attempt to create android libraries for Android format firmware imge handling on Unix based platforms using CMake.

## Android Source Details  

Source base: https://android.googlesource.com/platform/system/core/+/master/libsparse/

Source revision: bef0b1dbbc860a41712c127702bfe9bbf223ef22

## Dependencies  

* zlib

## Build Steps  

`cmake -DBUILD_SHARED_LIBS=ON -DCMAKE_INSTALL_PREFIX=${Destination_Base_Directory}`  
