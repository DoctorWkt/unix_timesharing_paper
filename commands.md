# 8. Some Commands

This section summarizes several of the commands available in
UNIX. The list is not exhaustive, but it covers those most frequently
used. The assembler as, the debugger db, the editor ed
and DECTAPE manipulator tap are documented in more detail in
Appendix 2.

Where an argument like name is given, a file name is meant. In
every case, an arbitrary path name may be used to specify any
file in the system, subject to the constraints imposed by the the
protection system.

Arguments enclosed in square brackets are optional.

## 8.1 as -- assemble

As is the assembler for the PDP-11. It is called as follows:

```
      as name1 name2 ... namen
```

The concatenation of the files name1 ... namen is assembled. The
resulting binary output is placed in file "a.out" in the current
working directory; a copy of the name list from the assembly is
placed on "n.out." See Appendix 2 for more information.

## 8.2 b -- compile B program

B [reference] is a new higher-level language with implementations
on the PDP-7, PDP-11, and Honeywell 635. To compile several B
programs,

```
      b -name1 ... -namen
```

Notice that each name should be preceded by a "-" (not part of
the file name). Also, b supplies the conventional suffix ".b".
For example, to concatenate and compile "abc.b" and "def.b", type

```
      b -abc -def
```

The binary output is left on "a.out" and the name list on "n.out"
(just like the assembler). See the B reference manual [reference]
for more information.

## 8.3 cat -- concatenate files

The cat command concatenates several files and copies the result
onto the standard output file.

```
      cat name1 .,. namen
```

Notice that ">" may be used: "cat a b >/ppt" punches the contatenation
of "a" and "b". "cat x" simply lists x on the typewriter.

## 8.4 chdir -- change directories

To change the current directory, use

```
      chdir dirname
```

This command is the only one that does not reside in directory
"/bin"; instead it is part of the Shell. The reason is
interesting. Recall that each ordinary command is executed as a
separate process created by the Shell. If the system’s chdir
primitive were executed in such a process, it would have essentially
no effect, since the process would terminate instantly
without affecting the current directory of the Shell process and
its subsequent offSpring. The Shell itself recognizes the chdir
command and calls the system to change directories without
creating a new process.

## 8.5 chmod -- change mode of file

To change the protection bits for a set of files,

```
      chmod mode name1 ... namen
```

The modes of name1, ..., namen are set to mode. Mode is an octal
number whose bits in the binary representation have the following
meanings:

      1 write, non-owner
      2 read, non-owner
      4 write, owner
     10 read, owner
     20 execute
     40 set user ID on execution

See also section 3.4 on the protection system. Most command
programs create files with mode 17; the assembler's "a.out" file
has mode 37.

## 8.6 chown -- change owner of files
To change the owner of a sequence of files,

```
      chown owner name1 ... namen .
```

Owner is a user number assigned by the system administrators.
Only the owner of a file may donate the file to another user.
Notice that chown does not change the directory in which the link
to the file exists.

## 8.7 cp -- copy file

To make a copy of a file,

```
      cp name1 name2
```

Either name1 or name2 may be special files.

## 8.8 db -- debug

To examine or patch a (usually binary) file,

```
      db [ name [ namelist ] ]
```

The first argument is the file to be examined. The second is a
name list file produced as "n.out" when name was assembled. The
brackets indicate that both arguments are optional. if the first
argument is not given, "core" is assumed. If the second is not
given, "n.out" is assumed. (of course, the first argument alone
cannot be omitted).

Db is discussed in complete detail in Appendix 2.

## 8.9 ed -- edit

ED is the editoro It is essentially a subset of QED [references];
see Appendix 2 for the differences.

## 8.10 ln -- link

To create a link,

```
      ln name1 [ name2 ]
```

A link to file name1 is created. If name2 is given, the link has
name name2, otherwise it has the (last component of) name1. For
example, "ln /a/b /c/d" creates a link named "d" in directory
"/c" to file "/a/b". The user must have permission to write in
directory "/c".

## 8.11 ls -- list directory

To list the names of the files in a directory,

```
      ls [ name ]
```

If name is not given, the contents of the current directory are
listed.

## 8.12 mkdir -- make directory

To create a directory,

```
      mkdir name
```

## 8.13 -- move file

To move or rename a file,

```
      mv name1 name2 ...
```

This command does not copy the file. It operates by linking to
name1 by the name name2, then unlinking name1. Mv is often used
to rename a file.

If name2 is a directory, name2 is moved into that directory under
the name which is the last component of name1. For example,

```
      mv x /dirname
```

moves x to /dirname/x.

## 8.14 nm -- get namelist

To get a printed listing of the symbol table (name list) from an
assembly,

```
      nm [ name ]
```

where name is the "n.out" file from some assembly. If name is not
given, "n.out" is listed.

## 8.15 pr -- print

The command

```
      pr name1 name2 ... namen
```

prints the contents of the named files. The output is separated
into pages headed by the file name, the time and date, and the
page number.

## 8.16 rm -- remove file

To unlink one or more files (remove them from directories),

```
      rm name1 ...
```

Recall that removing the last link to a file causes it to go
away.

## 8.17 roff -- run off (format)

Roff is a program similar to the one under GE-TSS which formats
text files under the control of commands embedded in the text.
The command

```
      roff name1 ... namen
```

will run off the concatenation of name1, ... namen. UNIX roff
supports all the features of TSS roff except "merge", tabs, and
footnotes. See [reference] for details.

## 8.18 sh -- Shell

To invoke the Shell,

```
      sh [ name ]
```

Name is interpreted as a file of commands. Name need not be
given, in which case the Shell will read its standard input file.
When called with an argument, the Shell refrains from typing its
prompt character "@". See section 5 above. The Shell has several
features besides those mentioned in section 5.

1. Arguments or parts of arguments to commands enclosed in
         single (’) or double (") quotes are taken literally, so that
         arbitrary character strings can be passed (including spaces,
         "<" or ">" etc.).

2. The character "\" serves to quote the next character. In
         this way a single command may extend over several lines,
         since a new line preceded by "\" is treated like a space.

3. When the Shell is invoked as a command, the character sequences
         "$0", "$1", ... "$9" are treated as parameters. "$0"
         is replaced by the name of the file being interpreted; "$1"
         through "$9" are replaced by the first through ninth argument
         following the file name. For example, when

```
              sh runcom arg1 arg2 arg3
```

         is typed, "$0" inside of "runcom" is replaced by "runcom",
         "$1" is replaced by "arg1", etc.

## 8.19 stat -- get file status

To discover interesting information about one or more files,

```
      stat name1 ...
```

Stat gives the i-number, the mode, the owner, the size, and the
times of creation and last modification for each of name1, ....

## 8.20 tap -- manipulate DECTAPE

Tap is used to load and dump portions of the hierarchy onto
DECTAPE. See Appendix 2 for details.

## 8.21 tm -- time

To discover various information connected with time, the tm command
can be used:

```
      tm [ command arg1 ... argn ]
```

If called without arguments, tm prints out the time of day and
the total times accumulated in several categories:

1. Processor time charged to the user.
2. System overhead time.
3. Time spent waiting for the disk.
4. Idle time.
5. Time spent in the interrupt routines.

Without an argument, tm gives these values both in absolute form
(i.e., totals since creation of the system) and as changes since
the last time tm was called. When called with one or more arguments,
the arguments are assumed to constitute a command to be
timed. Tm executes the given command and prints the times required
for the command in each of the above categories.

## 8.22 un -- undefined symbols

It is sometimes useful, to know the names of all the undefined
symbols in a given assembly. The command

```
      un [ name ]
```

searches the (name list) file name and prints all the symbols undefined
therein. If name is omitted, "n.out" is used.
