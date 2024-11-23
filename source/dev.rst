Developer's guide
#################

exaequOS is first and foremost a development platform for easily creating programs that run on every Web browser. It supports an increasing number of programming languages:

1. `C/C++`_
2. `Lua`_
3. `Javascript`_
4. `Tcl`_
5. `Scheme`_
6. `Forth`_
7. `BASIC`_
8. `Uxn`_
9. `bc/dc`_

C / C++ programs need to be compiled on the host OS outside exaequOS (in a Docker or not) whereas the other programming languages can be directly used inside exaequOS.

See `Publishing to the store`_ for sharing your app and `Publishing on itch.io`_.

For editing files inside exaequOS and storing them in the local persistent file systen (/home), several text editors have been integrated:

* Monaco editor: /usr/bin/mc
* Vim: /usr/bin/vim
* microEMACS: /usr/bin/em
* nano: /usr/bin/nano
* ed: /usr/bin/ed

.. note:: You can start them in Havoc terminal by typing mc, vim, em, nano or ed. Or by clicking on the corresponding search result.
	  
.. _C/C++:
1. C/C++
********

Programs written in C/C++ are compiled on your host machine with emscripten-exa toolchain, a modified version of emscripten, in order to generate WebAssembly binaries (and some Javascript). These are the fastest programs that can be run in exaequOS. They can be pure console programs or using frame buffer or using Wayland protocol (buffer mode or OpenGL ES 3 mode).

For using OpenGL ES 3 (i.e WebGL 2), the user can be helped by GLFW3 library or Raylib.

emscripten-exa toolchain
========================

The emscripten-exa toolchain is accessible on `GitHub <https://github.com/baudaux/emscripten-exa>`_.

Installation
^^^^^^^^^^^^
Using git, you can clone the repository into your host machine. In order to use emcc or em++ compiler commands, you also need to install python3, nodejs, clang/llvm toolchain, binaryen, cmake. You can check the content of the Dockerfile for the full list of dependencies (see below).

Dockerfile
^^^^^^^^^^
The Dockerfile is accessible on `GitHub <https://github.com/baudaux/docker-exa>`_. It allows you to directly create a docker image on your host machine and to compile your apps in this docker.

epm - exaequOS package manager
==============================
It is a Python script located in emscripten-exa/third_party/epm. It allows to search, install and create packages.
Example of command for searching a package provided by user exaequOS::

   $ python3 epm.py search @exaequOS

Output should look like this::

  Search package @exaequos in exaequOS store
  4 package(s) found

  ncurses - 6.4.0 - ncurses library - by exaequOS
  glfw - 3.4.0 - GLFW library - by exaequOS
  exa-wayland - 0.0.1 - Wayland client library - by exaequOS
  raylib - 5.0.0 - Raylib game engine - by exaequOS
  ----------

Example of command for installing packages::

  $ python3 epm.py install exa-wayland glfw raylib

Packages are installed in the directory: <current dir>/exapkgs/

Command for listing the installed packages::
  $ python3 epm.py list

pkg-config
==========
You shall set the PKG_CONFIG_PATH with the directory containing the .pc files: <current dir>/exapkgs/pkconfigs

example of command for compiling an app using glfw library::

  $ emcc main.c `pkg-config --libs --cflags exa-wayland glfw` -o exa/test_glfw.js

Running the compiled application
================================
emscripten-exa toolchain will generate (in your host machine) two files: <app name>.js and <app name>.wasm. There shall be accessible to exaequOS.

Local HTTP server
^^^^^^^^^^^^^^^^^
An HTTP server (port 7777) shall run in your host machine in order to serve the application files (exa.html, <app name>.js and <app name>.wasm). Here is an example of file tree that allows exaequOS to find and execute your application:

::

   root server directory
   └── media
       └── localhost
           └── <app name>          
               └── exa
                   ├── exa.html          
                   ├── <app name>.js
                   └── <app name>.wasm
   
exa.html file shall look like::

  <!doctype html>
  <html lang="en-us">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
    <title>Emscripten-Generated Code</title>

    <style>

      html, body {

        margin : 0;
        padding: 0;
        border: none;
        width: 100%;
        height: 100%;
      }

    </style>
  </head>

  <body>

    <script async type="text/javascript" src="havoc.js"></script>
  </body>
  
  </html>

You need to replace "havoc.js" by the generated js file name.
  
/media/localhost
^^^^^^^^^^^^^^^^
In order to execute the application, you need to open a terminal window and type (after $)::

  exaequos:~ $ /media/localhost/<app name>

.. _Lua:
2. Lua
******

Lua 5.4.6 is installed in exaequOS (/usr/bin/lua) and can either interpret Lua files or can interpret user commands on the fly (CLI, command-line interface).

.. note:: See documentation in `lua.org <https://lua.org>`_

.. image:: lua.png
  :width: 800
  :alt: Lua interpreter

**Raylib binding**

`Raylib <https://www.raylib.com>`_ graphics library is available in Lua by adding at the first line of the lua file ::
  
  require "raylib_lua"

Most of the draw functions are available but not all the functions of the library. Do not hesitate to ask. You can check some examples in the store (minesweeper, geom).

.. _Javascript:
3. Javascript
*************

QuickJS 2024-02-14 is installed in exaequOS (/usr/bin/qjs) and allows you to create Javscript programs.

.. note:: See documentation in `bellard.org/quickjs <https://bellard.org/quickjs/>`_

**Raylib binding**

`Raylib <https://www.raylib.com>`_ graphics library is available in QuicksJS by adding at the first line of the lua file ::
  
  import * as ray from 'raylib_qjs.so'

.. _Tcl:
4. Tcl
******

Jim Tcl 0.84 is installed in exaequOS (/usr/bin/jimsh).

.. note:: See documentation in `jim.tcl.tk <https://jim.tcl.tk/index.html/doc/www/www/index.html>`_

.. _Scheme:
5. Scheme
*********

TinyScheme 1.42 is installed in exaequOS (/usr/bin/scheme).

.. note:: See documentation in `tinyscheme.sourceforge.net <https://tinyscheme.sourceforge.net/home.html>`_

.. _Forth:
6. Forth
********

pForth v2.0.1 is installed in exaequOS (/usr/bin/pforth).

.. note:: See documentation in `www.softsynth.com/pforth/ <https://www.softsynth.com/pforth/index.php>`_

You need to use a dictionary for launching pForthas follows::

    $ pforth -d/usr/local/share/pforth/pforth.dic

.. _BASIC:
7. BASIC
********

BywaterBASIC 3.20 is installed in exaequOS (/usr/bin/bwbasic).

.. note:: See project page `sourceforge.net/projects/bwbasic <https://sourceforge.net/projects/bwbasic/>`_

.. image:: bwbasic.png
  :width: 800
  :alt: BWBASIC interpreter

.. _Uxn:
8. Uxn
******

Uxn is a stacked-based virtual machine created by Hundredrabbits (`100r.co <https://100r.co>`_).

exaequOS includes:

 * Uxn assembler (/usr/bin/uxnasm) for creating rom file for a Uxntal file,
 * Uxn cli (/usr/bin/uxncli) for running Uxn rom files in console,
 * Wayvara (/usr/bin/wayvara), a Wayland Varvara emulator for running Uxn rom files with graphics.

.. note:: See project pages `100r.co/site/uxn.html <https://100r.co/site/uxn.html>`_, `wiki.xxiivv.com/site/uxn.html <https://wiki.xxiivv.com/site/uxn.html>`_ and `wiki.xxiivv.com/site/varvara.html <https://wiki.xxiivv.com/site/varvara.html>`_

.. _bc/dc:
9. bc/dc
********

GNU bc 1.01.1 and GNU dc 1.4.1 are installed in exaequOS (/usr/bin/bc and /usr/bin/dc). GNU bc is an arbitrary precision numeric processing language. GNU dc is an arbitrary precision reverse-polish desk calculator.

.. note:: See project pages `GNU bc <https://www.gnu.org/software/bc/>`_ and `GNU dc <https://www.gnu.org/software/bc/manual/dc-1.05/html_mono/dc.html>`_


.. _Publishing to the store:
Publishing to the store
#######################

Register and connect to exaequos.com
************************************

In order to publish an app on the store, you need to first signup and connect to the platform.

.. image:: signup.png
  :width: 800
  :alt: signup


exapkg
******
.. note:: Not applicable for app compiled on your host machine using emcc. Use epm in that case. See below

Once connected, you have to change the current working directory to the app directory (/media/localhost/<app name> or /home/<app path>) and to start the command 'exapkg'::

  exaequos:~ $ cd /media/localhost/<app name>
  exaequos:/media/localhost/<app name> $ exapkg
  
The following window appears. You have to fill it and click on create. The app is then published on the store.

.. image:: exapkg.png
  :width: 800
  :alt: exapkg

epm
***
You can also publish (from your host machine) an app or a library using epm with the following command (launched in the root directory of the app or lib)::

  $ python3 epm.py create

The directory structure for a library shall look like::
  
   library root directory
   ├── include
   ├── lib
   └── pkgconfig


exaequOS store
**************

The published app will be found in the following directory::
  
  /usr/store/<user name>/<package name>

And will be found on hte store: 

.. image:: exastore.png
  :width: 800
  :alt: exastore

Link to your app
^^^^^^^^^^^^^^^^

You can then provide to others a direct link to your app::

  https://exaequos.com/?a=@<user_name>/<package_name>

or, more generally::
  
  https://exaequos.com/?a=/usr/store/<user_name>/<package_name>


.. _Publishing on itch.io:
Publishing on itch.io
*********************

You first need to publish your app on the exaequOS store and then you can create an index.html file as follows and upload it on itch.io. When clicking the "Run" button on itch.io, exaequOS will be loaded and will automatically start the previously published package::

  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <title>exaequOS</title>
  </head>
  <body>
    <script>
     location.replace("https://exaequos.com/start.php?a=@Benoit/axel_flies");
    </script>
  </body>
  </html>
