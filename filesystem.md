# The File System

The most important role of UNIX is to provide a file system. From
the point of view of the user, there are three kinds of files:
ordinary disk files, directories, and special files.

## Ordinary Files

A file contains whatever information the user places there, for
example symbolic or binary (object) programs. No particular
structuring is expected by the system. Files of text ordinarily
consist simply of a string of characters, with lines demarcated
by the new-line character. Binary programs are sequences of words
as they will appear in core memory when the program starts
executing. A few user programs generate and expect files with
more structure; for example, the assembler generates, and the
debugger expects, a name list file in a particular format;
however, the structure of files is controlled solely by the
programs which use them, not by the system.

## Directories

Directories (sometimes, "catalogs"), provide the mapping between
the names of files and the files themselves, and thus induce a
structure on the file system as a whole. Each user has a directory
of his own files; he may also create subdirectories to contain groups
of files conveniently treated together.

A directory is exactly like an ordinary file except that it cannot
be written on by user programs, so that the system controls
the contents of directories. However, anyone with appropriate
permission may read a directory just like any other file.

The system maintains several directories for its own use. One of
these is the root directory. All files in the system can be found
by tracing a path through a chain of directories until the
desired file is reached. The starting point for such searches is
often the root, which contains an entry for each user's master
directory. Another system directory contains all the programs
provided as part of the system; that is, all the commands
(elsewhere, "subsystems"). As will be seen, however, it is by no
means necessary that a program reside in this directory for it to
be used as a command.

Files and directories are named by sequences of eight or fewer
characters. When the name of a file is specified to the system,
it may be in the form of a path name, which is a sequence of
directory names separated by slashes and ending in a file name.
If the sequence begins with a slash, the search begins in the
root directory. The name "/a/b/c" causes the system to search the
root for directory "a"; then to search "a" for "b", and then to
find "c" in "b". "c" may be an ordinary file, a directory, or a
special file. As a limiting case, the name "/" refers to the root
itself.

The same non-directory file may appear in several directories under
possibly different names. This feature is called "linking"; a
directory entry for a file is sometimes called a link. UNIX differs
from other systems in which linking is permitted in that all
links to a file have equal status. That is, a file does not exist
within a particular directory; the directory entry for a file
consists merely of its name and a pointer to the information actually
describing the file. Thus a file exists independently of
any directory entry, although in practice a file is made to
disappear along with the last link to it.

When a user logs into UNIX, he is assigned a default current
directory, but he may change to any directory readable by him. A
path name not starting with "/" causes the system to begin the
search in the user’s current directory. Thus, the name "a/b"
specifies the file named "b" in directory "a", which is found in
the current working directory. The simplest kind of name, for example
"a", refers to a file which itself is found in the working directory.

Each directory always has at least two entries. The name "." in
each directory refers to the directory itself. Thus a program may
read the current directory under the name "." without knowing its
actual path name. The name ".." by convention refers to the
parent of the directory in which it appears; that is, the directory
in which it was first created.

The directory structure is constrained to have the form of a
rooted tree. Except for the special entries "." and "..", each
directory must appear as an entry in exactly one other, which is
its parent. The reason for this is to simplify the writing of
programs which visit subtrees of the directory structure, and
more important, to avoid the separation of portions of the
hierarchy. If arbitrary links to directories were permitted, it
would be quite difficult to detect when the last connection from
the root to a directory was severed.

## Special Files

Special files constitute the most unusual feature of the UNIX
file system. Each I/O device supported by UNIX is associated with
at least one special file. Special files are read and written
just like ordinary disk files, but the result is activation of
the associated device. Entries for all special files reside in
the root directory, so they may all be referred to by "/" followed
by the appropriate name.

The special files are discussed further in section 6 below.

## Protection

The protection scheme in UNIX is quite simple. Each user of the
system is assigned a unique user number. When a file-is created,
it is marked with the number of its creator. Also given for new
files is a set of protection bits. Four of these specify independently
permission to read or write for the owner of the file and
for all other users. A fifth bit indicates permission to execute
the file as a program. If the sixth bit is on, the system will
temporarily change the user identification of the current user to
that of the creator of the file whenever the file is executed as
a program. This feature provides for privileged programs which
may use files which should neither be read nor changed by other
users. If the set-user-identification bit is on for a program,
the accounting file may be accessed during the program’s execution
but not otherwise.

## System I/O Calls

The system calls to do I/O are designed to eliminate the differences
between the various devices and styles of access. There
is no distinction between "random" and sequential I/O, nor is any
logical or physical record size imposed by the system. The size
of a file on the disk is determined by the location of the last
piece of information written on it; no predetermination of the
size of a file is necessary. In UNIX-11, the unit of information
is the 8-bit byte, since the PDP-11 is a byte-oriented machine.

To illustrate the essentials of I/O in UNIX, the basic calls are
summarized below in an anonymous higher level language which will
indicate the needed parameters without getting into the complexities
of machine language programming. (All system calls are also
described in Appendix 1 in their actual form.) Each call to the
system may potentially result in an error return, which for simplicity
is not represented in the calling sequence.

### Open

To read or write a file assumed to exist already, it must be
Opened by the following call:

```
      filep = open(name, flag)
```

Name indicates the name of the file. An arbitrary path name may
be given. The flag argument indicates whether the file is to be
read or written. If the file is to be "updated", that is read and
written simultaneously, it may be opened twice, once for reading
and once for writing.

The returned argument filep is called a file descriptor. It is
used to identify the file in subsequent calls to read, write or
otherwise manipulate the file.

There are no locks in the file system, nor is there any restriction
on the number of users who may have a file open for reading
or writing. Although one may imagine situations in which this
fact is unfortunate, in practice difficulties are quite rare.

### Create

To create a new file, the following call is used.

```
     filep = create(name, mode)
```

Here filep and name are as before. If the file already existed,
it is truncated to zero length. Creation of a file implies
opening for writing as well. The mode argument indicates the permissions
which are to be placed on the file by the protection
mechanism. To create a file, the user must have write permission
in the directory in which the file is being created.

### Write

Except as indicated below, reading and writing are sequential.
This means that if a particular byte in the file was the last
byte written (or read), the next I/O call implicitly refers to
the first following byte. For each Open file there is a pointer,
maintained by the system, which always indicates the next byte to
be read or written. If n bytes are read, the pointer advances by
n bytes.

Once a file is open for writing, the following call may be used.

```
      nwritten = write(filep, buffer, count)
```

Buffer is the address of count sequentially stored bytes (words
in UNIX-7) which will be written onto the file. nwritten is the
number of bytes actually written; except in rare cases it is the
same as count. Occasionally, an error may be indicated; for example
if paper tape is being written, an error occurs if the tape
runs out.

For disk files which already existed (that is, were opened by
open, not create) the bytes written affect only those implied by
the position of the write pointer and the number of bytes
written; no other part of the file is changed.

### Read

To read, the call is

```
      nread = read(filep, buffer, count)
```

Up to count bytes are read from the file into buffer. The number
actually read is returned as nread. Every program must be
prepared for the possibility that nread is less than count. If
the read pointer is so near the end of the file that reading
count characters would cause reading beyond the end, only sufficient
bytes are transmitted to reach the end of the file.
Furthermore, devices like the typewriters work in units of lines.
Suppose, for example, that before anything has been typed a
program tries to read 128 characters from the console. This forces
the program to wait, since nothing has been typed. The user
now types a line consisting, say, of 10 characters and hits the
"new line" key. At this point the read call would return indicating
11 characters read (including the new line). On the
other hand, it is permissible to read fewer characters than were
typed without losing information; for example bytes may be picked
up one at a time.

When the read call returns with nread equal to zero, it indicates
the end of the file. For disk files this occurs when the read
pointer becomes equal to the current size of the file. It is possible
to generate an end-of-file from a typewriter by use of an
escape sequence which depends on the device used.

### Seek

To do "random", that is, direct access I/O it is only necessary
to move the read or write pointer to the appropriate location in
the file.

```
      seek(filep, base, offset)
```

The read pointer (respectively write pointer) associated with
filep is moved to a position offset words from the beginning,
from the current position of the pointer, or from the end of the
file, depending on whether base is O, 1, or 2. Offset may he
negative to move the pointer backwards. For some devices (e.g.
paper tape and typewriters) seek calls are meaningless and are
ignored.

### Tell

The current position of the pointer may be discovered as follows:

```
      offset = tell(filep, base)
```

As with seek, filep is the file descriptor for an open file, and
base specifies whether the desired offset is to be measured from
the beginning of the file, from the current position of the pointer,
or from the end. In the second case, of course, the result
is always zero.

# Implementation of the File System

As mentioned in section 3.2 above, a directory entry contains
only a name for the associated file and a pointer to the file
itself. This pointer is an integer called the i-number (for identification
number) of the file. When the file is accessed, its i-number
is looked up in a system table stored in a known part of
the disk. The entry thereby found (the file's i-node) contains
the description of the file:

1. its owner;
2. its protection bits;
3. the physical disk addresses for the file contents;
4. its size;
5. times of creation and last modification;
6. the number of links to the file; that is, the number of
   times it appears in a directory;
7. bits indicating whether the file is a directory and whether
   it is special (in which case the size and disk addresses
   are meaningless);
8. a bit indicating whether the file is "large" or "small."

There is space in each i-node for eight disk addresses. A file
which fits into eight or fewer 64-word (128-byte) blocks is considered
small; in this case the addresses of the blocks themselves
are stored. For large files, each of the eight disk addresses
may point to an indirect block of 64 words containing the
addresses of the blocks constituting the file itself. Thus files
may be as large as 8*64*128, or 65,536 bytes.

When the number of links to a file drops to zero, its contents
are freed and its i-node is marked unused.

To the user, both reading and writing of files appears to be synchronous
and unbuffered. That is, immediately after return from a
read call the data is available, and conversely after a write the
user's workspace may be reused. In fact the system maintains, unseen
by the user, a rather complicated buffering mechanism.
Suppose a write call is made specifying transmission of a single
byte. UNIX will search its own buffers to see whether the affected
disk block currently resides in its own buffers; if not, it
will be read in from the disk. Then the affected byte is replaced
in the buffer and an entry is made in a list of blocks to be
written on the disk. The return from the write call may then take
place, although the actual I/O may not be completed until a later
time. Conversely, if a single byte is read, the system determines
whether the disk block in which the byte is located is already in
one of the system's buffers; if so, the byte can be returned
immediately. If not, the block is read into a buffer and the byte
picked out. Because sequential reading of a file is so common,
UNIX attempts to optimize this situation by prereading the disk
block following the one in which the requested byte is found.
This strategy tends to minimize and in some cases eliminate disk
latency delays.

A program which reads or writes files in units of 128 bytes has
an advantage over a program which reads or writes a single byte
at a time, but the gain is not immense. As an example, the editor
ed (8.9 and A2.4 below) was originally written, for simplicity,
to do I/O one character at a time; it increased its speed by a
factor of about two when it was rewritten to use 128-byte units.
Because the system attempts to retain copies of the most recently
used disk blocks in core, the speed gain in dealing with large
units comes principally from elimination of system overhead, not
from latency delays.
