Introduction
============

A Web platform for learning
---------------------------

The development of exaequOS started in November 2022 in order to become a web platform for learning computer science and programming. Why a web platform ? Because Web is everywhere, on every device. So it was an obvious reason for me to make this new platform the most accessible as possible, in a uniform way. The idea was to be able to run historical Unix and GNU/Linux programs.

Thus I started at getting the emscripten project (emscripten.org) and modifying it for running the ed editor in a terminal (using xterm.js). emscripten outputs printf commmands in the console and does not manage inputs from terminal. So it leads to necessary modifications of the emscripten toolchain. After having successfully integrated ed in a Web page, I did the same with bash, a more complex exercise. I was then able to run the bash commands but not able to start programs as emscripten only manages one process per Web page. Fork/exec are missing in emscripten and they are needed for starting the creation of a new kind of operating system.

Fork/exec
---------

These two system calls are the signature of a Unix-like operating system. Their implementation became possible thanks to the association process-iframe: The parent process (i.e iframe) creates a new iframe (i.e child process) with same url and duplicates its memory array (heap and stack).

Usage of asyncify feature of emscripten is mandatory to allow  asynchronous Web API becoming synchronous libc (musl) functions. When implemented, I had the basis for creating the next step: a Unix-like operating system.

Unix-like microkernel OS
-----------------------

I chose to implement the OS as a microkernel for avoiding a single big process (i.e iframe) handling resource management and drivers. So I decided to have one process for resource management and one process per driver. I started by the console mode (one terminal, no graphics) and added later on graphics (frame buffer, wayland, raylib engine).

Programming and gaming
----------------------

As the purpose of the OS is to learn programming, I started by adding text editors (ed, vim) and interpreters (bwbasic, lua). As there are always games in every machine, I also added small games for fun (GNU Chess, Colossal Cave Adventure, Pacman, Snake, Tetris in console mode and a raylib game, the chaotic dungeon).

As graphics mode came to life, I added a wayland terminal (havoc) with support of pseudo terminals (in tty driver).

Application store
-----------------

One of the prime role of exaequOS is to share its own creations with others. This is made possible because everybody has the same execution environment (i.e. same OS). After developping a new app, a user will be able to publish it on the store. Once shared, the application will be searchable and executable by anybody, with no need of installation. 
