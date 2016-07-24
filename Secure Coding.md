# Debugging and Secure Coding

## Debugging
### Principles

- start small
- top-down
- segmentation fault: trace and backtrace with `coredump`
- optimization after debugging

### Keywords

- breakpoint
- stepping
- resuming
- temporary breakpoint

- watchpoint
- inspection
- expression

- frame hierachy
- stack

- catchpoint


**System Signals**

Name     |    Default Action    |   Description
----|----|----
SIGHUP   |    terminate process  |   terminal line hangup
SIGINT   |    terminate process  |   interrupt program
SIGQUIT  |    create core image  |   quit program
SIGILL   |    create core image  |   illegal instruction
SIGTRAP  |    create core image  |   trace trap
SIGABRT  |    create core image  |  abort program (formerly SIGIOT)
SIGEMT   |    create core image  |  emulate instruction executed
SIGFPE   |    create core image  |  floating-point exception
SIGKILL*  |    terminate process  |  kill program
SIGBUS   |    create core image  |  bus error
SIGSEGV  |    create core image  |  segmentation violation
SIGSYS   |    create core image  |  non-existent system call invoked
SIGPIPE  |    terminate process  |  write on a pipe with no reader
SIGALRM  |    terminate process  |  real-time timer expired
SIGTERM  |    terminate process  |  software termination signal
SIGURG   |    discard signal     |  urgent condition present on socket
SIGSTOP*  |    stop process       |  stop (cannot be caught or ignored)
SIGTSTP  |    stop process       |  stop signal generated from keyboard
SIGCONT  |    discard signal     |  continue after stop
SIGCHLD  |    discard signal     |  child status has changed
SIGTTIN  |    stop process       |  background read attempted from control terminal
SIGTTOU  |    stop process       |  background write attempted to control terminal
SIGIO    |    discard signal     |  I/O is possible on a descriptor (see fcntl(2))
SIGXCPU  |    terminate process  |  cpu time limit exceeded (see setrlimit(2))
SIGXFSZ  |    terminate process  |  file size limit exceeded (see setrlimit(2))
SIGVTALRM |   terminate process  |  virtual time alarm (see setitimer(2))
SIGPROF   |   terminate process  |  profiling timer alarm (see setitimer(2))
SIGWINCH  |   discard signal     |  Window size change
SIGINFO   |   discard signal     |  status request from keyboard
SIGUSR1   |   terminate process  |  User defined signal 1
SIGUSR2   |   terminate process  |  User defined signal 2
* `SIGKILL` and `SIGSTOP` cannot be caught or ignored

    > signal is recorded in the process table by OS; when the process's timeslice comes, the appropriate signal handler function will be executed

### GDB

a terminal debugger for languages: `Assembly`, `C/C++`(gcc), `Python`, `Java`(gcj), `Perl`, etc.

+ Terminal GUI: `gdb -tui`

+ Commands for debugging

    + program lifecycle
    
            //start running
            run, r
            //resuming
            continue, c
            //finishing current function
            finish, fin
            //execute until specific 
            until {line_number} | {function}
    
    + breakpoint management
            
            //breakpoint
            break, b: break {function} | {line_number} | {filename:line_number} | {filename:function} | +/-{offset_from_current_running_line}
            tbreak , tb //temporary break
            rbreak //grep-style breakpoint
            //contional breakpoint
            break if {condition}
            condition, cond {breakpoint_num} {condition}
            //clear breakpoint at specific line / delete specific breakpoint
            clear {line_num}/ delete, d {breakpoint_num}
            //all about breakpoints
            info breakpoints, i b
            //enable / disable breakpoints
            enable (once) / disable, dis
            
    + operations on breakpoints
    
            //inject commands on breakpoints
            commands {breakpoint_num}
            //define macros for convenience
            define {user_defined_command}
            //call scoped functions with scoped arguments on breakpoints
            call {func(scoped_arg...)}
            
    + tracing variables and logic
    
            //stepping
            step, s {count}
            next, n {count}
            //jump to specific line, skipping the lines in between
            jump, j {line_num}
            
            //inspection
            print, p {expression}
            //print expression each time the program stops
            display, disp {expression}
            //list nearby codes
            list, l
            //watch variable changes
            watch {expression}
            
            //print dynamic arrays
            p *ptr@{array_length}
            
            //all local variables 
            info locals
            
            //peek GDB value history
            p *${seq}
            
    + alter variable
    
            set {var} = {value}
            //for convenience variables
            set ${conv_var} = {local_var}
            /* you can choose almost any name for a convenience variable, with some natural ex- ceptions. You cannot, for instance, have a convenience variable named $3, since that is reserved for items in the value history. Also, you should not use register names if you are working in assembly language. For Intel x86–based machines, for instance, one of the register names is EAX, and within GDB it is referred to as $eax; you would not want to choose this name for a convenience variable if you were working at the assembly-language level. */

    + debugger configuration
            
            //make gdb silent about debugging info
            silent
            
+ Startup configuration

    put startup commands in `.gdbinit` file, which will be loaded on gdb startup
    
+ Coredump analysis
    
    > memory map: `vmmap`(OS X), `/proc/{pid}/maps`(LINUX)
    > memory: physical > virtual (pages > page table & access permission)
    > memory partition: kernel > userspace > NULL
    > **coredump**: code and data in _userspace_
        
    > **during your debugging session, you cannot say something like, “This line of source code must be okay, since it didn’t cause a seg fault.”** 
    
    > ex. in C, for a index out of range situation, hardware would not notice until it reaches the end of current memory page (generally 4K)
        
    memory section | storage
    ----|----
    .text | program codes
    .dss | global initialized data
    .bss | uninitialized variables
    heap | objects
    unused | empty common memory area
    stack | local variables, function calls, etc.
    
    + memory sections
    
            //loaded dynamic libs
            info sharedlibrary
            //memory regions
            maintenance info sections
            
    + threads
    
            //threads info
            info threads
            //current thread
            thread
            //switch to another thread
            thread {thread_num}
            //break for thread execution
            break {line_num} thread {thread_num} (if {boolean_expression})
            
            //move to stack frame
            frame {frame_num}
            
    + tracing
            
            //thread raw data: examine app past behaviour
            x/{n}a {address}
    
            //trace stack frames
            bt03
            
            //trace syste calls during execution, out of gdb
            dtrace/dtruss
            
### On DAM

- Memory Leak
- Allocation Request may fail
- Access Errors

#### Detection DAM problems

- electric fence: `libefence`
- MALLOC_CHECK
- mcheck

