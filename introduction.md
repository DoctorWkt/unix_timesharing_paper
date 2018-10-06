# 1. Introduction 

UNIX is a general-purpose, multi-user time sharing system implemented
on several Digital Equipment Corporation PDP series machines.

UNIX was written by K. L. Thompson, who also wrote many of the
command programs. The author of this memorandum contributed
several of the major commands, including the assembler and the
debugger. The file system was originally designed by Thompson,
the author, and R. H. Canaday.

There are two versions of UNIX. The first, which has been in existence
about a year, runs on the PDP-7 and -9 computers; a more
modern version, a few months old, uses the PDP-11. This document
describes UNIX-11, since it is more modern and many of the differences
between it and UNIX-7 result from redesign of features
found to be deficient or lacking in the earlier system.
Although the PDP-7 and PDP-11 are both small computers, the
design of UNIX is amenable to expansion for use on more powerful
machines. Indeed, UNIX contains a number of features very seldom
offered even by larger systems, including

1. A versatile, convenient file system with complete integration
      between disk files and I/O devices;

2. The ability to initiate asynchrously running processes.

It must be said, however, that the most important features of
UNIX are its simplicity, elegance, and ease of use.

Besides the system proper, the major programs available under
UNIX are an assembler, a text editor based on QED, a symbolic
debugger for examining and patching faulty programs, and "B", a
higher level language resembling BCPL. UNIX-7 also has a version
of the compiler writing language TMGL contributed by M. D.
McIlroy, and besides its own assembler, there is a PDP-11
assembler which was used to write UNIX-11. On the PDP-11 there is
a version of BASIC [reference] adapted from the one supplied by
DEC [reference]. All but the last of these programs were written
locally, and except for the very first versions of the editor and
assembler, using UNIX itself.
