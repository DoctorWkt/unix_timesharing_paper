# The UNIX Time-Sharing System

## Dennis M. Ritchie and Ken Thompson

## Bell Laboratories

## ABSTRACT

UNIX is a general-purpose, multi-user, interactive operating system
for the Digital Equipment Corporation PDP-11/40 and 11/45 computers. It
offers a number of features seldom found even in larger operating systems,
including:

1. a hierarchical file system incorporating demountable volumes;
2. compatible file, device, and inter-process I/O;
3. the ability to initiate asynchronous processes;
4. system command language selectable on a per-user basis; and
5. over 100 subsystems including a dozen languages.

This paper discusses the usage and implementation of the file
system and of the user command interface.

Key Words and Phrases: time-sharing, operating system, file system,
command language, PDP-11

CR Categories: 4.30, 4.32

Copyright © 1974, Association for Computing Machinery, Inc.
General permission to republish, but not for profit, all or part
of this material is granted provided that ACM's copyright notice
is given and that reference is made to the publication, to its date
of issue, and to the fact that reprinting privileges were granted
by permission of the Association for Computing Machinery.

This is a revised version of a paper presented at the Fourth
ACM Symposium on Operating Systems Principles, IBM Thomas
J. Watson Research Center, Yorktown Heights, New York, October
15-17, 1973. Authors' address: Bell Laboratories, Murray Hill,
NJ 07974.
