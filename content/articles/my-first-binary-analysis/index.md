---
title: "My First Binary Analysis"
date: 2026-03-13
summary: "A short walkthrough of my first binary analysis"
layout: "single"
tags: ["ghidra", "analysis"]
---

<h1>My First Binary Analysis</h1>

This is my first binary analysis, as a first exercise of Reverse Engineering subject from university.

To preserve the experience of my first reverse engineering analysis I didn't run the program because as I imagined, it is very simple an does not have any complexity or complicated functions or data manipulation. I also tried to read the entire assembly before doing some research to try to identify what it was doing, even though I did not understand most of the instructions at first

For our analysis we will be using and open source tool developed by NSA called Ghidra.

<h2>The main objectives are</h2>

<h3>
- Execute automatic analisys<br>
- Identify binary architecture<br>
- Identify main function<br>
- Identify internal functions<br>
- Identify relevant strings
</h3>


<h2>Before we begin analysing</h2>

Right after we create a new project and import our executable, we need to set some initial settings so we can tell Ghidra how the executable should be loaded and interpreted before we can begin the analysis.

{{< figure src="images/project_import-0.png" link="images/project_import-0.png" caption="Format and architecture initial configuration." >}}

Ghidra use these information to know how to read headers, load sections and locate functions and symbols.

This will generate a import results summary, and here we can already see some important information about our binary:

- Language ID: x86:LE:64:default
- Processor: x86
- Endian: little
- Address Size: 64
- Compiler ID: gcc
- Executable Format: ELF

It defines:

- Registers being used
- Instructions format
- Pointers size
- ELF (Executable and Linkable Format, the standard executable format used on Linux systems.)
- Utilized Compiler (gcc suggest that the code was probably written in C or C++)

And other useful information like: 

- ELF Note [required kernel ABI]: Linux 3.2.0

Indicating that the program was compiled to run on Linux systems >= 3.2

- ELF Source File

Appears when the binary is compiled without stripping metadata. It reveals that the code was originally called 'hello_reverse.c' and it can give us some hints about its functioning.


{{< figure src="images/import_summary-1.png" link="images/import_summary-1.png" caption="Import Results Summary." >}}



<h2>Diving into the code</h2>

After our import summary, we can double click our executable to load the automatic analysis, where we can already see our `main` function.


{{< figure src="images/auto-analysis-2.png" link="images/auto-analysis-2.png" caption="Auto analysis view." >}}


Right beside our machine code, we can see a preview of how C code would look like. And just by looking at the assembly, with some programming background we can already understand what the program does even without knowing every instruction, but also is where the fun begins.

Looking at the assembly code, we can already identify some of the most common instructions used by the program.
<br>
<br>
<br>
In the main function, the main assembly instructions used are <strong>MOV</strong>, <strong>LEA</strong>, and <strong>CALL</strong>.<br>
<br>
<strong>MOV</strong> – Used to copy a value from one place to another. Unlike what the name suggests, the value is only ‘copied’ and not ‘moved’ from one place to another.<br>
<br>
<strong>LEA</strong>– Used to load the address of something. It only obtains the memory address of a variable and not its value.<br>
<br>
<strong>CALL</strong> – Used to call a function. In the context of the example, CALL is used several times to execute the `puts` function from *libc*, which prints the string and automatically includes a newline at the end of that string. At the end, the instruction also executes the `mensagem_extra()` function.
<br>
<br>
<br>


By doing some research, we can also understand that there is an execution flow before our program hit the `main` function.

Execution begins at the `_start` entry point provided by the compiler runtime:


{{< figure src="images/start_function-3.png" link="images/start_function-3.png" caption="_start function." >}}


- Prepare the arguments (`argc`, `argv`)
- Prepare environmental variables
- Configure stack
- Call libc (Call the C standard library)

And after that, it calls `__libc_start_main` :


{{< figure src="images/lib_start_main-4.png" link="images/lib_start_main-4.png" caption="`__libc_start_main` function." >}}


- Initialize the C runtime environment
- Register finalization functions (`atexit`)
- Call `main`

It receives our `main` as one of the arguments `__libc_start_main(main, argc, argv, ...)`, we cannot see it because it belongs to an external library.

After our `main`finishes, the control return to `__libc_start_main`, and it calls `exit()` to end the program.



To finish our analysis, if we look at the `main`function, we can see that it also calls a functions named `mensagem_extra()` by having a look inside of it, we can see the same pattern with the instructions to print a new message.


{{< figure src="images/aux_function-5.png" link="images/aux_function-5.png" caption="Additional function being called in `main`" >}}


After printing the last message, it just `return` to our `main` function and the program finishes.


<h2>Conclusion</h2>

Although the binary analyzed in this exercise is very simple, the goal was not complexity but understanding the basic workflow of reverse engineering.

By using Ghidra we were able to:

- Inspect the binary format
- Identify its architecture and compiler
- Understand the program execution flow
- Locate the `main` function
- Analyze how the program interacts with the C standard library

Even a simple program can be useful for learning how binaries are structured and how the execution flow works before reaching the `main` function.

This first exercise helped me become more comfortable navigating assembly instructions and using Ghidra's analysis tools.

In future posts I plan to explore more complex binaries and dive deeper into other topics.
