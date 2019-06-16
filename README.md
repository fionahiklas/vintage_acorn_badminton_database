## Overview

This is the project I wrote on an Acorn Electron (with Plus1, Plus3 and PRES 
Floppy disc controller with two, one single-sided, one double, 3.5" drives).
This was the computer I had since I was 11-12 and I used it all the way 
through school.  I switch to using an Atari ST when I went to university 
(the course I was on taught 68000 assembler and C using Atari ST 1024 
machines).

I actually still have the Acorn Electron (and the original data recorder!) 
though it's buried in the loft somewhere.  It was an amazing machine and 
I learnt an awful lot on that.

I took AS Computing at school and as part of that you needed to come up 
with a project, I decided to write a program for my Dad who was a 
badminton coach at the time and needed to plan sessions of training.  It's a
fairly rudimentary program in that it allows placement of people on a court
and some text, along with printing all of this out (to a dot matrix printer).

The entire thing is written in 6502 Assembly language!


## Notes

### Extracting Images

I managed to get my hands on an old PC that my Dad has once used (had Windows XP
on it!).  I installed Ubuntu Desktop 16.04 i386 - the newer Ubuntu versions only
support 64bit processors.

I also bought a 3.5" floppy drive (refurbished) and ribbon cable from eBay and 
installed these in the PC - NOTE: make sure you align pin 1 on the motherboard 
and drive connectors to the red stripe on the ribbon cable.  The 34pin IDC
connectors have keys but in the case of my hardware the connectors on the 
motherboard and drive didn't so it's possible to get things the wrong way round.

It might be better to install a low-memory variant of Ubuntu 16.04, my PC only 
seemed to have 512Mb memory and it really struggled, in the end I did 
everything on the console.

I installed `setfdprm` from the Ubuntu repo (package is `fdutils`). 

Inserted a 3.5" disk and ran the following command

```
setfdprm sect=16 head=2 cyl=80 ssize=256 zerobased
```

I was then able to extract an image from the FD using the following command

```
dd if=/dev/fd0 bs=256 count=2560 of=adfs.img
```

I found that, even for single-sided drives (`head=1`) it was sometimes
better to get a double-sided image.  Othertimes this didn't work and I got
errors.

In the end I tried getting both.  I found that B2 (the BBC Emulator) seemed to
work best with a double-sided ADFS image.

All the discs I have are either 640k or 320k ADFS "L" format.

### Extracting Source Code

I compiled B2 on a Mac using Xcode from a project file created with cmake.
This ran fine and I could switch to the `BBC Master 3.5.0` configuration 
which is needed to get the correct ADFS version.

There is a handy menu option under `Edit` called `Copy BASIC listing` that 
will run a `LIST` command and transfer the output text to the clipboard.
This worked for quite a while and then just stopped.

I tried compiling and running under a Linux VM (on same Mac) and that wouldn't
work either.  I'm not sure why this stopped working.  As a work-around I added the following code into `BeebWindow.cpp` in the function 
`void BeebWindow::SetClipboardData(std::vector<uint8_t> data,bool is_text)`

```
    LOGF(OUTPUT, "Clipboard: %s",(const char *)data.data());
```


### Filenames

There is apparently an official file extension of [BBC Basic](https://fileinfo.com/extension/bbc) but I'm not using it as the main program needs to read in 
sections of code to assemble (into screen memory because 32k is just not that 
much space, particularly when some of it is needed for graphics).



## References

### Extracting ADFS images

* [How to read ADFS](http://www.adsb.co.uk/bbc/linux/)

### BBC Emulator

* The excellent [B2](https://github.com/tom-seddon/b2)

### Manuals

* [ADFS](http://chrisacorns.computinghistory.org.uk/docs/Acorn/Manuals/Acorn_ADFSUG.pdf)
* [BBC User Guide](http://central.kaserver5.org/Kasoft/Typeset/BBC/Contents.html)
