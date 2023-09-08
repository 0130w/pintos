#Pintos

This repo contains skeleton code for undergraduate Operating System course honor track at Peking University.

[Pintos](http://pintos-os.org) is a teaching operating system for 32-bit x86, challenging but not overwhelming, small
but realistic enough to understand OS in depth (it can run on x86 machine and simulators
including QEMU, Bochs and VMWare Player!). The main source code, documentation and assignments
are developed by Ben Pfaff and others from Stanford (refer to its [LICENSE](./LICENSE)).

## Acknowledgement

This source code is adapted from professor ([Ryan Huang](huang@cs.jhu.edu)) at JHU, who also taught a similar undergraduate OS course. He made some changes to the original
Pintos labs (add lab0 and fix some bugs for MacOS). For students in PKU, please
download the release version skeleton code by `git clone git@github.com:PKU-OS/pintos.git`.

## Start docker

Use alias to run docker quickly:
alias pintos-up='docker run -it --rm --name pintos --mount type=bind,source=/Users/zzq/pintos,target=/home/PKUOS/pintos pkuflyingpig/pintos bash'

## Compile pintos

(docker)

```shell
cd pintos/src/threads
make
cd build
pintos --
```

When work on specific lab, build pintos under its corresponding dir.

## Modify source code

Just modify source code in host machine thanks to the mounting technique.

## Code Guidance

"threads" -> Source code for the base kernel (lab dir)
"userprog" -> Source code for the user program loader (lab dir)
"vm" -> almost empty (implement in p3) (lab dir)
"filesys" -> Source code for a basic file system. Use this file system starting with project2 (modify in p4) (lab dir)
"devices" -> Source code for I/O device interfacing. (keyboard, timer, disk...)
"lib" -> An implementation of a subset of the standard C lib. (use in kernel and user program)
"lib/kernel" -> Parts of C lib only use in kernel
"lib/user" -> Parts of C lib only use in user program
"tests" -> test cases
"examples" -> example user programs
"misc / utils" -> only useful when work with pintos on your own machine

## Build direction

"kernel.o" -> Object file for the entire kernel.(this file generated after linking, contains debug information)

"kernel.bin" -> Memory image of the kernel. (without debug info)

"loader.bin" -> Memory image of the kernel loader. (read the kernel from disk into memory and starts it up. exactly 512 bytes long)

## Run Pintos

there exists a program called pintos (use `which pintos` to get the location) which run Pintos in a simulator.

After build Pintos, just run pintos [arguments]

```shell
# run alarm-multiple test in Pintos
pintos -- run alarm-multiple
```

pintos program offers several options for configuring the simulator

```shell
pintos option1 option2 ... -- arg1 arg2 ...
```

Use `pintos -h` to list available options

## Run all tests

```shell
make check # test implementation
```

Switch simulator

```shell
SIMULATOR=--qemu make check
SIMULATOR=--bochs make check
```

`make check VERBOSE=1` observe the progress of each test

`make check PINTOSOPTS='-j 1'`

## Run single test

```shell
make tests/threads/alarm-multiple.result # use .result file generated in build dir
```

if make says that the test result is up-to-date:
    delete .output file or use `make clean`

## Debug

### ASSERT Macro

Macro: ASSERT(expression)

test the value of expression => if it evaluates to zero, the kernel panics.

<debug.h> 
    provides the function call `debug_backtrace()` which can print a backtrace at any point in your code.
    provides the function call `debug_backtrace_all()` which can print backtraces of all threads.

A tool called `backtrace` translate the address of backtrace into function names and source file line numbers.

it use `kernel.o` as the first argument and the hex numbers corresponding the backtrace(including the 0x prefixes) as the remain args

outputs the function name and source file line numbers that correspond to each address

*If function A is listed above function B but function B doesn't call function A, it's a good sign that you're corrupting a kernel
thread's stack*

When function `A` calls another function `B` that never returns, the compiler may optimize such that an unrelated function `C` appears
in the backtrace instead of `A` and `C` is the function that happens to be in memory just after `A`.

For example:
    `A` -> `B` `C` but `B` never returns
    display: `C`

backtrace example:
    Call stack: 0xc0106eff 0xc01102fb 0xc010dc22 0xc010cf67 0xc0102319 0xc010325a 0x804812c 0x8048a96 0x8048ac8.

```shell
backtrace kernel.o 0xc0106eff 0xc01102fb 0xc010dc22 0xc010cf67 0xc0102319 0xc010325a 0x804812c 0x8048a96 0x8048ac8
```

if we know the user program executable file, we can use

```shell
backtrace tests/filesys/extended/grow-too-big 0xc0106eff 0xc01102fb 0xc010dc22 0xc010cf67 0xc0102319 0xc010325a 0x804812c 0x8048a96 0x8048ac8
```

to see the backtrace in user program

Can use both kernel objective file and user program together

```shell
backtrace kernel.o userfile ...
```

### Run Pintos under GDB debugger

```shell
pintos --gdb -- run mytest
```

this command blocks Pintos program and waits for the GDB the attach to this gdb session

```shell
# another terminal
docker exec -it pintos bash
cd pintos/src/threads/build
pintos-gdb kernel.o
debugpintos # target remote localhost:1234
```




