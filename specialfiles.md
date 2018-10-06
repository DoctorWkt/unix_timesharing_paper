# 6. Census of Special Files

Here is a list of the special files currently implemented. Since
an entry for each resides in the root directory, the file "xyz"
may be referred to by "/xyz". Alternatively, one may link to any
of these files under any name desired.

## 6.1 ppt

When read, "ppt" refers to the paper tape reader; when written,
to the punch. Null characters are ignored for both reading and
writing, so "ppt" is suitable only for ASCII (not binary)
information; on the other hand, the program need not take account
of the leader or trailer. End of file occurs during a read when
the end of the tape passes through the sensors.

## 6.2 bppt

"bppt" also refers to paper tape. The tape is in a blocked format
with checksums. Completely arbitrary information may be written
and recovered unchanged in this mode.

## 6.3 rppt

This is raw input and output for paper tape. Every character is
passed to the program, including nulls, so that the program must
know when the leader ends and information begins during a read.
On the other hand, this mode is suitable when tapes of unusual
format must be read.

## 6.4 tty

This is the console typewriter. Null characters are ignored for
both reading and writing. For reading, the line is a unit of
information; a program reading "tty" will wait until a whole line
has been typed, and at most one line will be passed back to the
program. However, characters may be read one at a time from the line.

On input, erase and kill processing are performed: "#" will erase
the last character typed; "@" kills the entire line.

The ASCII character "EOT" signals an end of file to the program.
The ASCII "new line" character is the standard means of ending an
input line. On the Teletype models 33 and 35 and some other terminals
UNIX must simulate this function by echoing a "return"
character when it receives a "line space" (whose code corresponds
to the ASCII "new line").

The name "tty" refers to the user's own typewriter, no matter
which physical channel he may be using. There are also special
files for each typewriter. They have the names "ctty" (for the
central site terminal), and "tty1", "tty2", ... "ttyn" (for
user's typewriters).

## 6.5 rtty

This is "raw" typewriter I/O. It is identical to "tty" for output,
but on input the program waits only until at least one
character has been typed before a return from the read occurs. No
erase or kill processing is done.

## 6.6 tap0; tap1

These files refer to DECTAPE logical units 0 and 1. When they are
opened, the program waits until a tape is mounted on the appropriate drive.

## 6.7 disk

This file refers to the entire disk in a way independent of the
file system; it reads or writes the physical block corresponding
to the current file pointer.

One use of this file demonstrates convincingly the versatility of
the special file concept. There is a program called check which
scrutinizes the entire file system to determine its consistency
and the number of disk blocks used for various purposes. This
program is in no sense part of the system; it is an ordinary command
invokable by any user. Check operates by reading the file
"disk." In this way it is able to examine the list of i-nodes
(cf. section 4) which define files without depending on ad hoc
system calls to obtain its information.

## 6.8 system

This special file causes the area of core memory occupied by the
system to be treated as a file. Thus the system can be examined
and patched during operation by use of the ordinary debugger db
discussed below.
