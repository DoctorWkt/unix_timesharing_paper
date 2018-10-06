# APPENDIX 2

This Appendix discusses in more detail the usage of the
assembler, the editor, the debugger, and the DECTAPE manipulation
command.

## A2.1 as

As is based on the DEC-provided assembler PAL-11 [references],
although it was coded locally. Therefore, only the differences
will be recorded.

Character changes are:

```
      for use
       @   *
       #   $
       ;   /
```

In as, the character ";" is a logical new line; several operations
may appear on one line if separated by ";". Several new
expression operators have been provided:

```
      \>   right shift (logical)
      \<   left shift
      *    multiplication
      \/   division
      \    remainder
      !    one's complement (unary)
      []   parentheses for grouping
```

There is a conditional assembly operation code:

```
      .if expression
      .endif
```

If the expression evaluates to non-zero, the section of code
between the ".if" and the ".endif" is assembled; otherwise it is
ignored. ".if"s may be nested.

Temporary labels like those introduced by Knuth [reference] may
be employed. A temporary label is defined as follows:

```
      n:
```

where n is a digit 0 ... 9. Symbols of the form "nf" refer to the
first label "n:" following the use of the symbol; those of the
form "nb" refer to the last "n:". The same "n" may be used many
times. Labels of this form are less taxing both on the imagination
of the programmer and on the symbol table space of the assembler.

The PAL-11 opcodes ".eot" and ".end" are redundant and are omitted.

The symbols

```
      r0 ... r5
      sp
      pc
      ac
      mq
      div
      mul
      lsh
      ash
      nor
      csw
```

are predefined with appropriate values.

The new cpcode "sys" is used to specify system calls. Names for
system calls are predefined. See Appendix 1 for the list of
calls.

Strings of characters may be assembled in a way more convenient
than PAL-11's ".ascii" operation (which is, therefore, omitted).
Strings are included between the string quotes "<" and ">":

```
      <here is a string>
```

Escape sequences exist to enter non graphic and other difficult
characters. These sequences are also effective in single and
double character constants introduced by single and double quotes
respectively:

```
      use for .
      \n  newline (012)
      \0  NULL (000)
      \>  >
      \t  TAB (011)
```

When errors occur, a single-character diagnostic is typed out
together with the line number and the file name in which it
occurred. Errors in pass 1 cause cancellation of pass 2. The
possible errors are:

```
      ) parentheses error
      ] parentheses error
      * Indirection ("*") used illegally
      . . (the location counter) has become undefined
      A error in Address
      B Branch instruction has too remote an address
      E error in Expression
      F error in local ("F" or "b") type symbol
      G garbage (unknown) character
      M Multiply defined symbol as label
      0 Odd-- word quantity assembled at odd address
      P Phase error-- "." different in pass 2 from pass 1 value
      R Relocation error
      U Undefined symbol
      X syntaX error
```

The binary output of the assembler is placed on the file "a.out"
in the current directory. The assembler also generates a file
"n.out" which is a copy of the name list from the assembly, that
is, a table of the names of symbols used and their values.
"n.out" is used by the db, nm, and un commands.

The assembler does not produce a listing of the source program.
This is not a serious drawback; the debugger db discussed below
is sufficiently powerful to render a printed octal translation of
the source unnecessary.

## A2.3 db

Unlike many debugging packages (including DEC's CDT, on which db
is loosely based) db is not loaded as part of the core image
which it is used to examine; instead it examines files. Typically,
the file will be either a core image produced after a
fault (see section 7) or the binary output of the assembler. dnb
is called as follows:

```
      db [ name [ namelist ] ]
```

Name is the file being debugged; if omitted "core" is assumed.
namelist is the "n.out" file produced when name was assembled; if
omitted, "n.out" is assumed. If no appropriate name list file can
be found, db can still be used but some of its symbolic facilities
become unavailable.

The format for most db requests is an address followed by a one
character command.

Addresses are expressions built up as follows:

1. A name has the value assigned to it when the input file was
         assembled. It may be relocatable or not depending on the
         use of the name during the assembly.

2. An octal number is an absolute quantity with the appropriate value.

3. An octal number immediately followed by "r" is a relocatable
         quantity with the appropriate value.

4. The symbol "." indicates the current pointer of db. The
         current pointer is set by many db requests.

5. Expressions separated by "+" or " " (blank) are expressions
         with value equal to the sum of the components. At most one
         of the components may be relocatable.

6. Expressions separated by "-" form an expression with value
         equal to the difference to the components. If the right
         component is relocatable, the left component must be
         relocatable.

7. Expressions are evaluated left to right.

If no address is given for a command, the current address (also
specified by ".") is assumed. In general, "." points to the last
word or byte printed by db.

There are db commands for examining locations interpreted as
octal numbers, machine instructions, ASCII characters, and
addresses. For numbers and characters, either bytes or words may
be examined. The following commands are used to examine the
Specified file.

      / The addressed word is printed in octal.

      \ The addressed byte is printed in octal.

      " The addressed word is printed as two ASCII characters.

      ' The addressed byte is printed as an ASCII character.

      ? The, addressed word is interpreted as a machine instruction
        and a symbolic form of the instruction, including symbolic
        addresses, is printed. Usually, the result will appear
        exactly as it was written in the source program.

      & The addressed word is interpreted as a symbolic address and
        is printed as the name of the symbol whose value is closest
        to the addressed word, possibly followed by a signed
        offset.

      <n1> (i.e., the character "new line") This command advances
        the current location counter "." and prints the resulting
        location in the mode last specified by one of the above
        requests.

      ^ This character decrements "." and prints the resulting
        location in the mode last selected one of the above
        requests. It is a converse to <n1>.

It is illegal for the word-oriented commands to have odd
addresses. The incrementing and decrementing of "." done by the
<nl> and ^ requests is by one or two depending on whether the
last command was word or byte oriented.

The address portion of any of the above commands may be followed
by a comma and then by an expression. In this case that number of
sequential words or bytes specified by the expression is printed.
"." is advanced so that it points at the last thing printed.
There are two commands to interpret the value of expressions.

      = When preceded by an expression, the value of the expression
                 is typed in octal. When not preceded by an
                 expression, the value of. "." is indicated. This
                 command does not change the value of ".".

             :   An attempt is made to print the given expression as a
                 symbolic address. If the expression is relocatable,
                 that symbol is found whose value is nearest that of
                 the expression, and the symbol is typed, followed by a
                 sign and the appropriate offset. If the value of the
                 expression is absolute, a symbol with exactly the
                 indicated value is sought and printed if found; if no
                 matching symbol is discovered, the octal value of the
                 expression is given.

          The following command may be used to patch the file being
          debugged.

             !   This command must be preceded by an expression. The
                 value of the expression is stored at the location
                 addressed by the current value of "." . The opcodes do
                 not appear in the symbol table, so the user must
                 assemble them by hand.


          The following command is used after a fault has caused a core
          image file to be produced.

             $   causes the contents of the general registers and
                 several other registers to be printed both in octal
                 and symbolic format. The values are as they were at
                 the time of the fault.

                  The only way to exit from db is to generate an end of file
                  on the typewriter (EOT character).

## A2.4 ed

Ed is nearly a subset of QED [reference]. When called by

```
      ed name
```

ed performs an automatic "r" (read) command on file name. The
major differences between ed and QED are:

1. There is no "\f" character; input mode is left by typing
         "." alone on a line.

2. There are no buffers and hence no "\b" stream directive.

3. The commands are limited to:

             a c d i p q r s w = !

4. The only special characters in regular expressions are:

             * ^ $ [ .

         which have the' usual meanings. However, "^" and "s" are
         only effective if they are the first or last character
         respectively of the regular expression. Otherwise
         suppression of special meaning is done by preceding the
         character by "\", which is not otherwise special.

5. In the substitute command, only the leftmost occurrence of
         the matched regular expression is substituted.

## A2.5 tap

The tap command is used as follows:

```
      tap [O1][crxdt][s][v] name1 ... namen -name1 ... -namen
```

The first argument consists of characters which indicate what is
to be done. Subsequent arguments specify a set of files.

A digit (0 or 1) in the first argument indicates the logical unit
number on which the tape is mounted. C, r, x, d and t are
mutually exclusive:

      c indicates the creation of a new tape. Files name1, ...,
        namen are placed on the tape. If any of these are directories,
        all files and subdirectories therein are placed on
        the tape as well. Arguments preceded by "-" indicate files
        or directories which are not to be placed on the tape even
        though implied by one of the other arguments.

      r indicates that the files specified (exactly as for c) will
        be added to the tape. If there was a file of the same name
        as one of the specified files already on the tape, it will
        be replaced.

      x indicates that the specified files are to be extracted from
        the tape and copied onto the disk. If any directory needed
        does not exist, it will be created.

      d indicates that the specified files are to be deleted from
        the tape.

      t causes a partial table of contents of the tape to be produced,
        including all files implied by the following arguments.
        (E.g., "tap t /dmr" gives the names of all files on
        the tape in directory "/dmr").

Argument "v" (for "verify") may be used in addition to the preceding
arguments. Before each file is dealt with as indicated by
one of the preceding arguments, the "v" option causes tap to
pause, type the name of the affected file, and request the user
to decide whether the file should be treated. The reply "y"
means "yes"; an empty line means "no"; a "q" means "no, and exit
from the tap command". For example, by the use of "xv", files can
be selectively restored.

Argument "s" may be used alone or in addition to one of c, r, x,
t, d. It causes tap to examine the tape, verify that it can be
read properly, and produce statistics on the contents of the
tape.
