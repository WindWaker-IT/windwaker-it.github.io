---
title: "Bandit: Level 12 - Level 13"
date: 2024-08-30 12:00:00 -500 
categories: [OvertheWire, Bandit]
tags: [linux,wargames,bandit,overthewire]
author: Christoph K
---

<!-- Change LEVELS -->

### Level 12

**Username:** `bandit12`  
**Password:** `7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4`

### Task:

<!-- PICTURE FROM TASK -->
https://overthewire.org/wargames/bandit/bandit13.html

    The password for the next level is stored in the file data.txt, which is a hexdump of a file that has been repeatedly compressed. For this level it may be useful to create a directory under /tmp in which you can work. Use mkdir with a hard to guess directory name. Or better, use the command “mktemp -d”. Then copy the datafile using cp, and rename it using mv (read the manpages!)

    Commands you may need to solve this level
    grep, sort, uniq, strings, base64, tr, tar, gzip, bzip2, xxd, mkdir, cp, mv, file

<!-- change username bandit!! -->
### Start

Let's begin by connecting to the server `bandit.labs.overthewire.org` and logging in with user `bandit12` on port `2220`.

We have several commands available for this task:


    man (1)              - an interface to the system reference manuals
    grep (1)             - print lines that match patterns
    sort (1)             - sort lines of text files
    uniq (1)             - report or omit repeated lines
    strings (1)          - print the sequences of printable characters in files
    base64 (1)           - base64 encode/decode data and print to standard output
    tr (1)               - translate or delete characters
    tar (1)              - an archiving utility
    gzip (1)             - compress or expand files
    bzip2 (1)            - a block-sorting file compressor, v1.0.8
    xxd (1)              - make a hex dump or do the reverse.


<!-- CHANGE COMMANDS IF NECCESSARY -->


### Solve the level

First we need a working space for creating and compress/decompress files. 

We use `mktemp -d` like mentioned in the Task.

```bash
bandit12@bandit:~$ mktemp -d
/tmp/tmp.CAaIpq72oS
bandit12@bandit:~$ cd /tmp/tmp.CAaIpq72oS
```
Then we copy our file to our folder.


```bash

bandit12@bandit:/tmp/tmp.CAaIpq72oS$ cp ~/data.txt ./data_hex.txt
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ ls
data_compressed.txt

```

Lets have a look into the file here.

```bash

bandit12@bandit:/tmp/tmp.CAaIpq72oS$ cat data_hex.txt |head
00000000: 1f8b 0808 d2e9 9766 0203 6461 7461 322e  .......f..data2.
00000010: 6269 6e00 0141 02be fd42 5a68 3931 4159  bin..A...BZh91AY
00000020: 2653 59ea 2468 ae00 0017 7fff dadb b7fb  &SY.$h..........
00000030: dbff 5ffb f3fb d776 3d6f fffb dbea fdbd  .._....v=o......
00000040: 85db edfc ffa9 7def faaf efdf b001 386c  ......}.......8l
00000050: 1001 a0d0 6d40 01a0 1a00 0006 8006 8000  ....m@..........
00000060: 0000 d034 01a1 a34d 0034 3d43 40d0 0d34  ...4...M.4=C@..4
00000070: d034 34da 9ea1 b49e a7a8 f29e 5106 4326  .44.........Q.C&
00000080: 9a19 1934 d1a0 341a 6234 d018 d468 6834  ...4..4.b4...hh4
00000090: 00c9 a308 6434 0000 0308 d068 0680 1900  ....d4.....h....
bandit12@bandit:/tmp/tmp.CAaIpq72oS$
```

We need to transform the hexdump into data now. Afterwards we have to find out with which programs the data was compressed. 

Lets convert, we use `xxd`. Checking the manual page:

```bash

NAME
       xxd - make a hex dump or do the reverse.

SYNOPSIS
       xxd -h[elp]
       xxd [options] [infile [outfile]]
       xxd -r[evert] [options] [infile [outfile]]
```

The command must be with `-r` and an `infile` and `outfile`.

Therefore: 

```bash
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ xxd -r data_hex.txt data_compressed
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ ls
data_compressed  data_hex.txt

```
We have now a not readable file due to many compressions.

Now the tricky part - we have to decompress this data now. And we have find out which tool compressed the file. Lets check the first line from our hexdump again. 

    00000000: 1f8b 0808 d2e9 9766 0203 6461 7461 322e  .......f..data2.

https://en.wikipedia.org/wiki/List_of_file_signatures

If we search for the fist bytes `1f8b` we find out, that the signature is from `gzip`.

Lets decompress with `gzip`

```bash
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ mv data_compressed data_compressed.gz
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ gzip -d data_compressed.gz
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ ls
data_compressed  data_hex.txt
bandit12@bandit:/tmp/tmp.CAaIpq72oS$
```

Important is, that we need to rename the file into `.gz`otherwise gzip doesnt work. 

Unfortunately is the data still not readable.

Now we have to check the decompressed file from gzip again into an hexdump because we need to know which tool compressed the data after the first compression.

```bash

D��bandit12@bandit:/tmp/tmp.CAaIpq72oS$ xxd data_compressed 
00000000: 425a 6839 3141 5926 5359 ea24 68ae 0000  BZh91AY&SY.$h...
00000010: 177f ffda dbb7 fbdb ff5f fbf3 fbd7 763d  ........._....v=
... 
``` 

Now we have `425a`and a short check in the internet shows, that we need bzip now. 

So again the same thing:


```bash
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ mv data_compressed data_compressed.bz2
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ ls
data_compressed  data_hex.txt
```

Still not readable the file. Check again via `xxd`the hexdump.

```bash
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ xxd data_compressed
00000000: 1f8b 0808 d2e9 9766 0203 6461 7461 342e  .......f..data4.
```

Okay `1f8b`is already known and we have to use `gzip` again.

```bash
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ mv data_compressed data_compressed.gz
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ gzip -d data_compressed.gz
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ ls
data_compressed  data_hex.txt
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ cat data_compressed
data5.bin0000644000000000000000000002400014645764722011261 0ustar  rootrootdata6.bin0000644000000000000000000000033514645764722011267 0ustar  rootrootBZh91AY&SY���a���j@�}�� [#�u!I� �!��h
        FM������!       ���.�p�!_�^�bandit12@bandit:/tmp/tmp.CAaIpq72oS$

```

The output is now very interesting. But still not goot enough. Same procedure.

```bash
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ xxd data_compressed  |head
00000000: 6461 7461 352e 6269 6e00 0000 0000 0000  data5.bin...
```

Now we can see it better. There is an archive `data5.bin`. We have an archive here. Lets try `tar`.

```bash
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ mv data_compressed data_compressed.tar
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ ls
data_compressed.tar  data_hex.txt
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ tar -xf data_compressed.tar
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ ls
data5.bin  data_compressed.tar  data_hex.txt
```

Wow after so much digging ... We extracted actually a file here `data5.bin` :) 
Lets see whats inside:

```bash
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ tar -xf data5.bin
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ ls
data5.bin  data6.bin  data_compressed.tar  data_hex.txt
```

And another file `data6.bin`. Lets check it with xxd.

```bash 
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ xxd data6.bin
00000000: 425a 6839 3141 5926 5359 affc af61 0000  BZh91AY&SY...a..
```

Unfortunate, we have to use `bzip2`again because of the `425a`. If decompressions are needed, I use the steps above. Lets finish this:

```bash

bandit12@bandit:/tmp/tmp.CAaIpq72oS$ mv data6.bin data6.bz2
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ bzip2 -d data6.bz2
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ ls
data5.bin  data6  data_compressed.tar  data_hex.txt
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ cat data6
data8.bin0000644000000000000000000000011714645764722011267 0ustar  rootroo��fdata9.bin
                                                                                      �HU(H,..�/JQ�,Vp�7M)w+N6HNJ����.6*K   q)w��>�2A1bandit12@bandit:/tmp/tmp.CAaIpq72oS$ xxd data6
00000000: 6461 7461 382e 6269 6e00 0000 0000 0000  data8.bin.......
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ mv data6 data6.tar
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ tar -xf data
data6.tar            data_compressed.tar
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ tar -xf data6.tar
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ ls
data5.bin  data6.tar  data8.bin  data_compressed.tar  data_hex.txt
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ xxd data8.bin |head
00000000: 1f8b 0808 d2e9 9766 0203 6461 7461 392e  .......f..data9.

```
and finally:

```bash
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ mv data8.bin data8.gz
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ gz
gzexe  gzip
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ gzip -d data8.gz
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ ls
data5.bin  data6.tar  data8  data_compressed.tar  data_hex.txt
bandit12@bandit:/tmp/tmp.CAaIpq72oS$ cat data8
The password is FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn
bandit12@bandit:/tmp/tmp.CAaIpq72oS$

```

We got it! :) :) 

This level was really long. It is important to understand here, that we always have to find out, which tool compressed it and how we can decompress it. 

The password for next level:

Password: `FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn`



## Explanation: 

#### Hexdump (xxd)
A hexdump is a representation of binary data in hexadecimal format. Each byte of data is converted into two hexadecimal digits, making it easier to analyze the raw contents of a file. The tool xxd can be used to create a hexdump or reverse it, converting the hexadecimal back into the original binary format.

Example Command:

    xxd data_hex.txt
    
This command converts the content of data_hex.txt into its hexadecimal representation.

#### Gzip (gzip)
gzip is a widely-used compression tool that reduces the size of files using the DEFLATE compression algorithm. Files compressed with gzip typically have the .gz extension. The compression process reduces file size by eliminating redundancy in the data, making it more efficient to store and transmit.

Signature in Hexdump: 1f8b
Common Commands:

    Compress a file: gzip filename
    Decompress a file: gzip -d filename.gz

#### Bzip2 (bzip2)
bzip2 is another compression tool, using the Burrows-Wheeler block-sorting compression algorithm and Huffman coding. It's known for achieving higher compression ratios than gzip, although it might be slower. Files compressed with bzip2 have the .bz2 extension.

Signature in Hexdump: 425a
Common Commands:

    Compress a file: bzip2 filename
    Decompress a file: bzip2 -d filename.bz2

#### Tar (tar)
tar is an archiving tool, not a compression tool by itself. It bundles multiple files into a single archive file, often with the .tar extension. Although tar itself does not compress data, it’s commonly used in combination with gzip or bzip2 to create compressed archive files like .tar.gz or .tar.bz2.

Common Commands:

    Create an archive: tar -cf archive.tar file1 file2
    Extract an archive: tar -xf archive.tar

