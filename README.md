# OutGuess 0.2.1

> 2018-11-11 - Joao Eriberto Mota Filho <eriberto@eriberto.pro.br>,

fully based on OutGuess 0.2, by

> 2001-02-12 - Niels Provos <provos@citi.umich.edu>

OutGuess is a universal steganographic tool that allows the insertion
of hidden information into the redundant bits of data sources. The
nature of the data source is irrelevant to the core of OutGuess. The
program relies on data specific handlers that will extract redundant
bits and write them back after modification. In this version the PNM
and JPEG image formats are supported. In the next paragraphs, images
will be used as concrete example of data objects, though OutGuess can
use any kind of data, as long as a handler is provided.

OutGuess is available under the BSD software license. It is
completely free for any use including commercial.

Please see each source file for its respective license.
OutGuess was developed in Germany.

Steganography is the art and science of hiding that communication is
happening. Classical steganography systems depend on keeping the
encoding system secret, but modern steganography are detectable only
if secret information is known, e.g. a secret key. Because of their
invasive nature steganography systems leave detectable traces within a
medium's characteristics. This allows an eavesdropper to detect media
that has been modified, revealing that secret communication is taking
place. Although the secrecy of the information is not degraded, its
hidden nature is revealed, defeating the main purpose of
Steganography.

For JPEG images, OutGuess preserves statistics based on frequency
counts. As a result, no known statistical test is able to detect
the presence of steganographic content. Before embedding data
into an image, the OutGuess system can determine the maximum
message size that can be hidden while still being able to maintain
statistics based on frequency counts.

OutGuess uses a generic iterator object to select which bits in the
data should be modified. A seed can be used to modify the behavior
of the iterator. It is embedded in the data along with the rest of the
message. By altering the seed, OutGuess tries to find a sequence of
bits that minimizes the number of changes in the data that have to be
made.

A sample output from OutGuess is as follows:

```
Reading dscf0001.jpg....
JPEG compression quality set to 75
Extracting usable bits: 40059 bits
Correctable message size: 21194 bits, 52.91%
Encoded 'snark.bz2': 14712 bits, 1839 bytes
Finding best embedding...
    0:  7467(50.6%)[50.8%], bias  8137(1.09), saved:   -13, total: 18.64%
    1:  7311(49.6%)[49.7%], bias  8079(1.11), saved:     5, total: 18.25%
    4:  7250(49.2%)[49.3%], bias  7906(1.09), saved:    13, total: 18.10%
   59:  7225(49.0%)[49.1%], bias  7889(1.09), saved:    16, total: 18.04%
59, 7225: Embedding data: 14712 in 40059
Bits embedded: 14744, changed: 7225(49.0%)[49.1%], bias: 7889, tot: 40032, skip: 25288
Foiling statistics: corrections: 2590, failed: 1, offset: 122.585494 +- 239.664983
Total bits changed: 15114 (change 7225 + bias 7889)
Storing bitmap into data...
Writing foil/dscf0001.jpg....
```

The simple example script `seek_script` uses OutGuess to select an image
that fits the data we want to hide the best, yielding the lowest number
of changed bits. Because we do not care about the actual content of
the cover data we send, this is a very viable approach.

Additionally, OutGuess allows to hide multiple messages in the data.
Thus, it also provides plausible deniability. It keeps track of the
bits that have been modified previously and locks them. A `(23,12,7)`
Golay code is used for error correction to tolerate collisions on
locked bits. Artificial errors are introduced to avoid modifying bits
that have a high bias.

This version of OutGuess can insert only two different messages.
As this is a BETA version, I would like you to give me feedback on
the usefulness of OutGuess. And if you like it, you can support
me via the links on

<http://www.outguess.org/>

Note, by Eriberto: all text shown here was originally written by
Niels Provos for OutGuess =< 0.2. The current site for OutGuess is:

<https://github.com/eribertomota/outguess>

## Installation

### Prepare the `jpeg-6b-steg` library

To do so, you need to choose (and potentially edit)
an appropriate *jconfig.h* file.

To get an idea which one you might want,
have a look at their header comments.

You might do so like this (POSIX only):

```bash
head -n 1 jpeg-6b-steg/jconfig.*
```

The default one is `jconfig.cfg`.
You may use it like this:

```bash
cd jpeg-6b-steg
ln -s jconfig.cfg jconfig.h
cd ..
```

### Build OutGuess

OutGuess has only been tested on OpenBSD, Linux, Solaris and AIX.

#### Supported Systems

OutGuess has been modified to use autoconf.
A normal installation should require only:

1. `./configure && make`

There is an optimization bug in `gcc`,
so you might have to compile with `-O0`.

#### Unsupported Systems

If your system is not supported, try building by hand as follows:

1. Install the *JPEG-6b* `libjpeg.a` library and patch it with
   `jpeg-6b-steg.diff`. You can obtain the source from
   <ftp://ftp.uu.net/graphics/jpeg/jpegsrc.v6b.tar.gz>.
   The Makefile expects the library to be at `../jpeg-6b`.

   > **NOTE** (by Eriberto)\
   > Currently, the jpeg-6b can be found at\
   > <https://www.ijg.org/files/>

2. Edit the `Makefile` and type `make`

## BUGS

None known at the moment.

You may want to check the code from time to time
with a static code checker like [CppCheck](http://cppcheck.sourceforge.net/):

```bash
cppcheck --enable=all *.c &> cppcheck_log.txt
less cppcheck_log.txt
```

## Acknowledgments

OutGuess uses code from the following projects.
Attributions can also be found in the sources.

* Markus Kuhn's Stirmark software,
  see [STIRMARK-README](STIRMARK-README)
* the Independent JPEG Group's JPEG software,
  see [jpeg-6b-steg/README](jpeg-6b-steg/README)
* the Arc4 random number generator for OpenBSD, (c) 1996 by
  _David Mazieres <dm@lcs.mit.edu>_
* free MD5 code by Colin Plumb

For determining the redundant bits out of a JPEG image,
the `jpeg-jsteg-v4` patches by _Derek Upham <upham@cs.ubc.ca>_ were helpful.

Thanks to:

* _Dug Song <dugsong@monkey.org>_ for helping with configure,
* _Andrew Reiter <andrewr@rot26.net>_ for testing on Solaris.
