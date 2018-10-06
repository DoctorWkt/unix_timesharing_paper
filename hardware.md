# 2. Hardware

The PDP-11 on which UNIX is implemented is a 16-bit 12K computer,
and UNIX occupies 8K words. More than half of this space,
however, is utilized for a variable number of disk buffers; with
some loss of speed the number of buffers could be cut significantly.

The PDP-11 has a 256K word disk, almost all of which is used for
file system storage. It is equipped with DECTAPE, a variety of
magnetic tape facility in which individual records may be addressed
and rewritten at will. Also available are a high-speed paper
tape reader and punch. Besides the standard Teletype, there are
several variable-speed communications interfaces.
