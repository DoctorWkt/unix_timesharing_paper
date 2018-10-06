# The Shell

## General

Communication with UNIX is carried on with the aid of a program
called the Shell. The Shell is a command line interpreter: it
reads lines typed by the user and interprets them as requests to
execute other programs. In simplest form, a command line consists
of the command name followed by arguments to the command, all
separated by spaces:

```
      command arg1 arg2 ... argn
```

The Shell splits up the command name and the arguments into
separate strings. Then a file with name command is sought;
command may be a path name including the "/" character to specify
any file in the system. If command is found, it is brought into
core and executed. The arguments collected by the Shell are accessible
to the command. When the command is finished, the Shell
resumes its own execution, and indicates its readiness to accept
another command by typing the prompt character "@".

If file command cannot be found, the Shell prefixes the string
"/bin/" to command and attempts again to find the file. Directory
"/bin" contains all the commands provided by the system itself.

## Standard I/O

The discussion of I/O given above seems to imply that every file
used by a program must be opened or created by the program in
order to get a file descriptor for the file. In fact, this is not
quite true. There are two files always accessible to every
program without an explicit "open" or "create"; they have file
descriptors O and 1. As a program begins execution, file 1 is
Open for writing, and is best understood as the standard output
file. Except under circumstances indicated below, this file is
the user’s typewriter. Thus programs which wish to write informative
or diagnostic information ordinarily use file descriptor 1.
Conversely, file 0 starts off open for reading, and programs
which wish to read messages typed by the user usually read this file.

The Shell is able to change the standard assignments of these
file descriptors from the user's typewriter printer and keyboard.
If one of the arguments to a command is prefixed by ">", file
descriptor 1 will, for the duration of the command, refer to the
file named after the ">". For example,

```
      ls
```

ordinarily lists, on the typewriter, the names of the files in
the current directory. The command

```
      ls >files
```

creates a file called "files" and places the listing there. Thus
the argument ">files" means, `place output on "files"'. On the
other hand,

```
      ed
```

ordinarily enters the editor, which takes requests from the user
via his typewriter. The command

```
      ed <script
```

interprets "script" as a file of editor commands; thus "<script"
means, `take input from "script"'.

Although the file name following "<" or ">" appears to be an argument
to the command, in fact it is interpreted completely by
the Shell and is not passed to the command at all. Thus no special
coding is needed within each command; the command need
merely use the standard file descriptors O and 1 where appropriate.

## Command Separators

Another feature provided by the Shell is relatively
straightforward. Commands need not be on different lines; instead
they may be separated by semicolons.

```
      ls; ed
```

will first list the contents of the current directory, then enter
the editor.

A related feature is more interesting. If a command is followed
by "&"; the Shell will not wait for the command to finish before
returning with its signal "@"; instead, it is ready immediately
to accept a new command; For example,

```
      as source >output &
```

causes "source" to be assembled, with diagnostic output going to
"output;" however, no matter how long the assembly takes, the
Shell returns immediately. The "&" may be used several times in a
line:

```
      as source >output & ls >files &
```

does both the assembly and the listing in the background. In all
the examples above using "&", an output file other than the
typewriter was provided; if this had not been done, the outputs
of the various commands would have been intermingled.
(Incidentally, the spaces before and after the "&" in the examples
above are not necessary.)

## The Shell as a Command

The Shell is itself a command, and may be called recursively.
Suppose file "tryout" contains the lines

```
      as source
      mv a.out testprog
      testprog
```

The mv command causes the file "a.out" to be renamed "testprog".
"a.out" is the (binary) output of the assembler, ready to be
executed. Thus if the three lines above were typed on the console,
"source" would be assembled, the resulting program named
"testprog", and "testprog" executed. When the lines are in
"tryout", the command

```
      sh <tryout
```

would cause the Shell sh to execute the commands sequentially.
(The Shell has further capabilities, including the ability to interpret
parameters to filed command sequences; see section 8.18).

When the user types the "&" character as part of a command line,
he is explicitly invoking the multitasking facilities of UNIX.
That is, he is creating a process which runs asynchronously from
his normal command stream. Although this ability is quite convenient
for the user directly, it is even more useful to UNIX itself.

## Processes and forking

A process in UNIX is the execution of a program. The evidence of
the existence of a process is a core image. While the processor
is executing on behalf of a process, the core image, quite
naturally, resides in the core memory of the computer; during the
execution of other processes, a core image is kept on the disk.
In order to provide fast response to user’s requests, UNIX, like
most time-sharing systems, swaps the core images of processes
between core and the disk.

Except while UNIX is bootstrapping itself into operation, a new
process can come into existence in only one way: by use of the
fork system call.

```
      processid = fork(label)
```

When fork is executed by a process, it splits into two independently
executing processes. The two processes have core images
which are copies of each other, but they are not precisely
equivalent: one of them is considered the parent process. In the
parent, control does not return directly from the fork, but instead
passes to location label; in the child process, there is a
normal return. The processid returned by the fork call is the
identification of the other, offspring process.

Because the return points in the parent and child process are not
the same, each copy of a program existing after a fork may determine
whether it is the parent or child process.

## Execution of programs

Another system primitive on which the Shell depends heavily is
invoked by

```
      status = execute(file, arq1, argz, ... , argn)
```

which requests the system to read in and execute the program
named by file, passing it arguments arg1, arg2, ... argn.
Ordinarily, arg1 should be the same string as file. If this call
is successful, control never returns to the program which uses
it. That is, the image of the named file replaces the current
program. Only if the call fails, for example because file could
not be found or because its execute-permission bit was not set,
does a return take place from the execute primitive.

The third and last process control system call used by the Shell is

```
      processid, status = wait()
```

This primitive causes its caller to suspend execution until one
of its children has completed execution. Then wait returns the
processid of the terminated process and a status value indicating
how the process died. (Processes which are never waited for die
unnoticed and presumably unmourned).

## Operation of the Shell

The outline of the operation of the Shell can now be understood.
Most of the the time, the Shell is waiting for the user to type a
command. When the new line character is typed, the Shell's read
call returns. The Shell analyzes the command line, putting the
arguments in a form appropriate for execute. Then fork is called.
The child process, whose code of course is still that of the
Shell, then attempts to perform an execute with the appropriate
arguments. If successful, this will bring in and start execution
of the program whose name was given. Meanwhile, the other process
resulting from the fork, which is the parent process, waits for
the child process to die. When this happens, the Shell knows the
command is finished, so it types out "@" and reads the typewriter
to obtain another command.

Given this framework, the implementation of background processes
is trivial; whenever a command line terminates with "&", the
Shell merely refrains from waiting for the process which it
created to execute the command.

Happily, all of this mechanism meshes very nicely with the notion
of standard input and output files. When a process is created by
the fork primitive, it inherits not only the core image of its
parent but also all the files currently open in its parent, including
those with file. descriptors 0 and 1. The Shell, of
course, uses these files to read command lines and to write its
signal "@", and in the ordinary case its children -- the command
programs -- inherit them automatically. When an argument with "<"
or ">" is given however, the offspring process, just before it
performs execute, closes file 0 or 1 respectively and opens the
named file. Because the process in which the command program runs
simply terminates when it is through, the association between a
file specified after "<" or ">" and file descriptor O or 1 is ended
automatically when the process dies. Therefore the Shell need
not know the actual names of the files which are its own standard
input and output, since it need never reopen them.

In ordinary circumstances, the main loop of the Shell never
terminates. (The main loop includes that branch of the return
from fork belonging to the parent process; that is, the branch
which does a wait, then reads another command line). The one
thing which causes the Shell to terminate is discovering an end-of-file
condition on its input file. Thus, when the Shell is executed
as a command with a given input file, as in

```
      sh <comfile
```

the commands in "comfile" will be executed until the end of
"comfile" is reached; then the instance of the Shell invoked by
sh will terminate. Since this Shell process is the child of
another instance of the Shell, the wait executed in the latter
will return, and another command may be processed.

The instances of the Shell to which each UNIX user types commands
are themselves children of another process. The last step in the
initialization of UNIX is the creation of a single process and
the invocation (via execute) of a program called init. The code
for init is kept in a file, like every other command. Its role is
to create one process for each typewriter channel which may be
dialed up by a user. The various subinstances of init open the
appropriate typewriters for input and output. Since when init was
invoked there were no files open, in each process the typewriter
keyboard will receive file descriptor 0 and the printer file
descriptor 1. Each process types out a message requesting that
the user log in and waits, reading the typewriter, for a reply.
At the outset, no one is logged in, so each process simply hangs.
Finally someone types his name or other identification. The appropriate
instance of init wakes up, receives the log-in line,
and reads a password file. If the user is found, and if he is
able to supply the correct password, init changes to the user’s
default current directory, sets the user number to that of the
person logging in, and performs an execute of the Shell. At this
point the Shell is ready to receive commands and the logging-in
protocol is complete.

Meanwhile, the mainstream path of init (the parent of all the
subinstances of itself which will later become Shells) does a
wait. If one of the child processes terminates, either because a
Shell found an end of file or because a user typed an incorrect
name or password, this path of init simply recreates the defunct
process, which reopens the appropriate input and output files and
types another log-in message. Thus a user may log out simply by
typing the end-of-file sequence in place of a command to the
Shell.
