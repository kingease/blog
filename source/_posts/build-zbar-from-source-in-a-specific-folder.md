---
title: build zbar from source in a specific folder
date: 2018-05-04 14:07:43
tags:
---


1. download the zbar source code
2. build the source and output the specific folder `/code`
    ``` bash
    $ ./configure --prefix=/code --with-gtk=no --with-qt=no --enable-video=no
    $ make
    $ make install
    ```
    we would find folders `bin`, `include` , `lib` in the `/code`
3. install zbar package, specified with `include` and `lib`
    ``` bash
    pip install --global-option=build_ext --global-option="-I/code/include/" --global-option="-L/code/lib" zbar
    ```
4. use zbar should spec the lib path
    ``` bash
    LD_LIBRARY_PATH=/code/lib python
    ```
