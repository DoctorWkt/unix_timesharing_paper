# APPENDIX 1

This appendix summarizes all the system calls. To understand the
calls to UNIX, it is fortunately necessary to know only very little
of the structure of the PDP-11. The machine contains several
general registers, of which only two are used for arguments to
the calls, namely R0 and R1. There is also a condition register,
one of whose bits records a carry occurring during an arithmetic
operation. To indicate an error the system sets this carry bit;
it is cleared for successful calls. There is a conditional branch
instruction to test the state of the bit. All registers not used
to communicate explidit arguments are unchanged by calls to the
system.

The instruction used to call the system is known to the assembler
as "sys"; when the processor executes this instruction it is
trapped to a specific location inside UNIX. The address field of
"sys" contains a number indicating which system call is desired.

The arguments for a call are placed either in a register or
immediately following the "sys" instruction.

A number of the calls, principally those dealing with the file
system, take strings as arguments. There is a standard format for
such a string: it consists of a sequence of bytes ending in a
null character. The open call below, contains a complete example
of how to write such a string.

## A1.1 exit

Exit is used to terminate a process as follows:

```
      sys exit
```

There are no arguments, nor is there ever any return from this call.

## A1.2 fork

This is the primitive used to generate new processes.

```
      sys fork
      (old process return)
      (new process return)
```

There are no input arguments. The error bit is set if no space is
available to create a new process, and control returns only to
the old process. R0 contains the process identification of the
new process. See also section 5.

The parent process returns immediately after the sys call; the
new process skips one word; (The label argument mentioned in the
discussion of fork in section 5.5 was a white lie.)

## A1.3 read

To read an open file whose file descriptor is filep, load filep
into R0 and

```
      sys read
      buffer
      nchars
```

Buffer is the address of the place into which information is
read, and nchars is the maximum number of characters desired.
(The actual number, not its address.) The number of characters
actually read returns in R0. If R0 is zero, the end of the file
has been reached. The error bit may be set if, for example, the
file is a tape file and there was a permanent read error, or if
an attempt was made to read into an area not part of the user's
core image.

## A1.4 write

To write an open file with file descriptor filep, load filep into
R0 and

```
      sys write
      buffer
      nchars
```

where buffer and nchars are the same as for read. The number of
characters actually written returns in R0 (ordinarily it is the
same as nchars) and errors are indicated by the error bit.

## A1.5 open

To open an already existing file,

```
      sys open
      name
      mode
      ...
      name:<pathname\0>
```

name is the address of a string of characters constituting a path
name. The name is terminated by a null (all zero) character,
which is indicated by "\O"; the characters "<" and ">" are string
quotes. Mode is 0 or 1 to indicate reading or writing respectively.
The file descriptor returns in R0. If the file cannot be
found or if permission is not granted the error bit is set.

## A1.6 creat

To create or recreate a file,

```
      sys creat
      name
      mode
```

name is the same as for open. Mode is a number encoding the protection
bits as specified under the "chmod" command below (A2.9).
Creation of a file implies opening for writing; the file descriptor
is returned in R0.

## A1.7 close

To close a file, move the file descriptor into R0 and

```
      sys close
```

A program may have only a limited number of files open at one
time (currently, 10). Closing a file allows another file to be
opened in its place. Closing is otherwise unnecessary, for an
automatic close on all files is performed when the process terminates.

## A1.8 wait

To wait for a child process to terminate,

```
      sys wait
```

The identification of the terminated process is returned in R0.
At the present time no further information is returned; in the
future a means of determining the fate of the process will be
provided.

If the process executing a wait has no living children, an error
is returned.

## A1.9 link

To create a link in an arbitrary directory,

```
      sys link
      name1
      name2
```

Name1 and name2 are pointers to names as in open. File name1 is
linked to, and the link has name name2. An error is indicated if
name1 does not exist or if name2 does exist.

## A1.10 unlink

To remove the name of a file from a directory,

```
      sys unlink
      name
```

Name is a pointer to a name. The specified entry is removed from
its directory; if this was the last entry (link) pointing to the
file, the file is destroyed.

## A1.11 exec

To cause execution of a file as a program,

```
      sys exec
      name
      argp
      ...
 argp:arg1
      arg2
       .
       .
       .
      0
```

The first argument is the address of a file name. The second
argument is the address of a list of argument pointers terminated
by a zero pointer. Each argument pointer is the address of a
string to be passed to the command or other program. The first
argument pointer arg1 is, by convention, the name of the file
being invoked. When a program is executed by the Shell, it can
determine the name by which it was called. Thus one may write a
single program with several names which takes various actions
according to the name used.

A file invoked by exec begins execution at its relative location
0. At the start, its stack pointer (one of the general registers)
points to a list of its own arguments as follows:

```
      count
      arg1
      arg2
```

where there are count arguments in the list. Each argi points to
a standard format string. The argi are the same as those specified
to the exec call.

## A1.12 chdir

To change the current directory,

```
      sys chdir
      dirname
```

dirname points to the standard format string describing a directory.

## A1.13 time

The call

```
      sys time
```

returns in the AC and MQ registers the number of sixtieths of a
second since the start of the current year.

## A1.14 mkdir

The call

```
      sys mkdir
      name
```

creates the file whose name is pointed to by name and marks it as
a directory.

## A1.15 chmod

The mode of a file is changed by

```
      sys chmod
      name
      newmode
```

See the "chmod" command (section 8.5) for the interpretation of
the mode. Only the owner of a file may change the mode.

## A1.16 chown

To change the owner of a file,

```
      sys chown
      name
      newowner
```

Only the owner of a file may change its owner.

## A1.17 break

To save time, UNIX does not swap all of the 4K user core area
when exchanging core images. The locations swapped are those from
the beginning of the core image to the initial program break, and
from the top of user core down to the stack pointer. The initial
program break is determined by the size of the file containing
the program. The system’s idea of how much to swap may be altered
by using this call:

```
      sys break
      newbreak
```

Newbreak becomes the first location not swapped. If it points
beyond the stack, or to the very first word in the core image,
the entire core image is swapped.

## A1.18 stat

The user may obtain a copy of the i-node for a named file:

```
      sys stat
      name
      buffer
```

Name is the name of a file, and buffer is the address of 34
sequential bytes into which information concerning the file is
placed. See section 4 for what information is passed; consult a
UNIX programming councillor for its format.

A.19 seek
To move the read or write pointer associated with the open file
with file descriptor filep, load filep into R0 and

```
      sys seek
      base
      offset
```

See also section 3.5.5.

A.20 tell

To discover the position of the read or write pointer associated
with the open file with file descriptor filep, move filep into R0
and

```
      sys tell
      base
      offset
```

The result returns in R0. See also section 3.5.6.

## A1.21 (unassigned)

A1.22 intr

To control the handling of "break" signals sent by the user,

```
      sys intr
```

If R0 is zero on entry, interrupts are disabled; if R0 is non-zero,
they are enabled.
