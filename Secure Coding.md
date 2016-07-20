# Debugging and Secure Coding

## Debugging
### Principles

- start small
- top-down
- segmentation fault: trace and backtrace with `coredump`

### Keywords

- breakpoint
- stepping
- resuming
- temporary breakpoint

- watchpoint
- inspection
- frame hierachy
- stack

### GDB

+ Terminal GUI: `gdb -tui`

+ Commands

            //start running
            run, r
            //breakpoint
            break, b
            //contional breakpoint
            break if {condition}
            condition {breakpoint_num} {condition}
            //stepping
            step, s
            next, n
            //resuming
            continue, c
            //clear breakpoint
            clear