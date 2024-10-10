### Run-Time Environment

For any program in execution (seen as the code that the user has actually written), there always is "something" interposed between itself and the processor. That "something" is some other software (obviously also executing), whose duty is to cause all of the abstractions that the program execution needs, to come into existence.

The abstractions that the program execution needs to achieve the effects intended by the programmer are anything that does not exist natively for the processor. For example, classes, threads, streams, containers, network sockets, files, etc.)
The "something" that causes those abstractions to come into existence and "be alive" (that is, to change dynamically as a result of program 's actions) throughout program execution, used to be called "**_runtime environment_**" (RTE). In fact, the RTE is made of two distinct parts: (1) the OS, and (2) the "**_runtime support_**" of the programming language.

The OS' primary purpose is to allow programs themselves to exist and be able to execute simultaneously. To this end, the OS supports abstractions such as files and processes. 
The runtime support instead is "a private property" of the specific programming language (each one has its own). Its duty is to create and manage the abstractions that exist in the programming language itself (and perhaps not in other languages) and that are not directly supported by the OS (for example, classes, streams, certain types of threads, etc.)

The "program" abstraction, which is not primitive to the processor, was needed because the early computers executed one program at a time, until completion, which was terribly slow for users (when they were behind in the wait queue) and scarcely satisfactory for throughput. That mode of execution (one at a time and run to completion) was called "batch".
This "program" abstraction, as defined and handled by the OS, is made of three parts: 
1. The executable code of the program, which has to go to read-only memory for the processor to execute
2. An area of work, which the program can use to make its own computations, without fearing disturbance from other executions; 
3. A run-time environment that knows programs and allows many of them to execute simultaneously. 
In order for multiple programs to progress their execution simultaneously, a mechanism must be provided to create the conditions for alternation of execution across programs. This is where "preemption" comes in.

### Processes

For any OS a _program_ is a "blob" of data in a binary file stored in the file system that has the property of being executable.

When a program gets executed by the OS it becomes a _process_, a dynamic abstraction (at run-time).
This entity has a well defined structure, which varies from different OS', that has to be filled in by the content of the program file.

The process data structure is composed from four data segments:
1. **Text**: static memory where the actual code is stored. It is read-only and executable.
2. **Data**: static memory where global and static variables are stored (might be split into _Data_ for initialised and _BSS_ for uninitialised variables). It is read-write but not executable.
3. **Stack**: dynamic memory that contains stack frames (local variables) for program's routines and grows from the end.
4. **Heap**: dynamic memory that contains dynamically allocated data (eg ```malloc(size)```), it grows from bottom to top against the _stack_.

The _start_ is represented by the instruction at location 0 and it corresponds with the first instruction of the ```main``` function. So when a process is started the _program counter_ will be initialised with the _start_ address.

The processes have the "illusion" of having the _whole_ CPU and memory, this "illusion" is created by the compiler that compiles the program so that it thinks it is the only one executing with exclusive access to the entire resources of the computer.
This abstraction is obviously false, otherwise _multiprogramming_ would be impossible, in fact memory is shared by processed using _virtual memory_ and CPU is shared using _time slicing_.

#### Anatomy of a process (according to C)
![[IMG_C975AFCB3744-1.jpeg]]

<span style="display: block; float: left; padding-right: 30px"><span style="display: block; max-width: 150px">![[IMG_088E34B1252C-1 1.jpeg]]</span></span>
The process is a _dynamic_ concept: this means that it only exists at runtime and its internal state only matter and evolves during its execution, it has then to be described, stored and possibly manipulated by the OS (using a structure called Process Control Block shown in [[IMG_088E34B1252C-1 1.jpeg | here]]).

#### How a process is born (in UNIX)

Except for the first process ("kernel process"), which is spawned at the end of the boot procedure, all the other processes need a _parent_ to come into existence.
The parent has to call the syscall `fork()` which creates a new clone of the calling process, the _child_ is then an exact copy of the _parent_ living on a separate memory space, including open files and memory mappings (`mmap()`) .

The parent then calls `exec(...)`, with the program it wants to execute on the child process just spawned. At this point the child is ready to do its own things.

### On Virtualisation

#### Abstractions

An _abstraction_ is a concept that involves creating an abstract entity implementation and providing a _well defined_ interface to the user in order to let him manipulate the entity without knowing the implementation details, simplifying the high-level view.

The exposed interface is fragile to changes in the run-time environment in which it exists, because they might break its internal implementation.

Software programs realise abstractions via _Abstract Data Types_ (**ADT**) that comprise a data structure describing the abstract entity we want to represent (maybe we use an array to represent the abstract concept of a binary tree), and operations able to operate on this data structure in a transparent way to the user (think about `search(elt)` or `insert(elt)` on a binary tree).

Abstractions are used when we need to represent something that is not a primitive concept to the execution environment, without them we would use machine instructions which are very limited in expressivity (assembly is really boring). While programming we are continuously composing abstractions to create more powerful ones.

### Virtualisation

The goal of virtualisation it to provide an abstract interface to upper layers using it, guaranteeing that, no matter what happens to the execution environment, the interface will work and remain stable.
_Virtualisation_ sits on top of abstraction(s), adding value to them by preserving their interface contract.
We can say that virtualisation provides a _mapping_ between the abstract interface and the actual abstraction implementation below.

The most know virtualisation example is **virtual memory**, which give the abstract illusion to processes to have (almost) unlimited and contiguous memory starting from address 0, while in the backend managing the actual pages and swaps. Processes will never have to know the truth.

#### Difference between abstraction and virtualisation
![[IMG_DCF45BA72DAC-1.jpeg]]

- For _abstraction_ the focus is on the interface provided by the _execution environment_ and building a "higher-level" concept from that. This implies that changes in this environment will eventually break the abstraction.
- For _virtualisation_ the focus in on the interface provided to the _upper layers_, and it promises to them to always hold even after changes to the bottom layers. This obviously requires a great implementation effort.

