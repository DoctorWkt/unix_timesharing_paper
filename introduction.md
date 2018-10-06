# 1. Introduction 

There have been three versions of UNIX. The earliest version
(circa 1969-70) ran on the Digital Equipment Corporation PDP-7 and -9 computers.
The second version ran on the unprotected PDP-11/20 computer. This paper
describes only the PDP-11/40 and /45 [1] system since it is more modern and many of the differences
between it and older UNIX systems result from redesign of features
found to be deficient or lacking.
Since PDP-11 UNIX became operational in February
1971, about 40 installations have been put into service;
they are generally smaller than the system described
here. Most of them are engaged in applications such as
the preparation and formatting of patent applications
and other textual material, the collection and processing
of trouble data from various switching machines within
the Bell System, and recording and checking telephone
service orders. Our own installation is used mainly
for research in operating systems, languages, computer
networks, and other topics in computer science,
and also for document preparation.
Perhaps the most important achievement of UNIX
is to demonstrate that a powerful operating system
for interactive use need not be expensive either in
equipment or in human effort: UNIX can run on hardware
costing as little as $40,000, and less than two man-years
were spent on the main system
software. Yet UNIX contains a number of features seldom
offered even in much larger systems.
It is hoped, however, the users
of UNIX will find that the most important characteristics
of the system
are its simplicity, elegance, and ease of use.

Besides the system proper, the major programs available under
UNIX are: assembler, text editor based on QED [2], linking loader, symbolic
debugger, compiler for a language resembling BCPL [3] with types and
structures (C), interpreter for a dialect of BASIC, text
formatting program, Fortran compiler, Snobol interpreter,
top-down compiler-compiler (TMG) [4], bottom-up compiler-compiler (YACC),
form letter generator, macro processor (M6) [5], and permuted index program.
There is also a host of maintenance, utility, recreation, and novelty
programs. All of these programs were written
locally. It is worth noting that the system is
totally self-supporting. All UNIX software is maintained
under UNIX; likewise, UNIX documents are generated
and formatted by the UNIX editor and text formatting
program.
