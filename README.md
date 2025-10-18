# aux-apple-unix-materials
A collection or "dump" of materials, preferably "uuencoded" tar archives, ready for transfer to A/UX Apple Unix v3.1.1, as seen in AUXrunner.

Videos demonstrating my experiments:

https://youtu.be/GHqLQc5kGds

https://youtu.be/nCx0J2H2S_s


8th October 2025

I discovered a "portable" Qemu-package called AUXrunner, a ready-to-run A/UX Apple Unix for macOS systems:

https://mendelson.org/auxrunner.html

Ctrl-Alt-G gets you out of the screen, Ctrl-Alt-F toggles fullscreen.

To run the app, you need to do in the app's directory:

sudo xattr -rc AUXrunner.app

and you also need to allow it to do network connections (first time it attempts it).

I created a couple of binaries in the attached uuencoded (i.e. turned into ASCII) tar files, and specifically, a variant of XLISP, Gnu Common Lisp 1.1 (GCL), and the BWBASIC BASIC interpreter - check auxbins.uue.

The way to transfer these binaries to A/UX is:

Check what your host macOS IP address is:

ifconfig -a

- mine is 10.0.0.27.

Throw over the (internal) network, i.e. wait for a connection:

cat auxbins.uue | nc -l 8888

Then, within the A/UX virtual machine:

Catch the program:

telnet 10.0.0.27 8888 > auxbins.uue

uudecode auxbins.uue

tar xvf auxbins.tar

then you get a nice folder and could, in theory, go there and use the programs, or, e.g. if you wish to compiler XENIX yourself (which works with the A/UX-included K&R C-compiler), type "make".

The programs have been collected and compiled from the sources described below.




General notes:

To compile a Unix System V program - the internal compiler cc is K&R, gcc is better.

Turn the tar.gz, that you download from God knows where, into a tar (copy beforehand, if you will) on the host machine:

gzip -d prog.tar.gz

(or if you need to create a tar out of a folder, if it was no tar.gz but your own files collection:

tar --format=v7 -cvf prog.tar myfolder

)

Turn the tar into ASCII (to "throw" and "catch" as above):

uuencode prog.tar prog.tar > prog.uue

To get things out of A/UX:

In macOS:

nc -l 8888 > whatever.uue

in A/UX:

uuencode whatever.thing whatever.thing > whatever.uue

dd if=whatever.uue | telnet 10.0.0.27 8888

... and when dd reports that it is done in A/UX, Ctrl-C the nc in macOS. (A/UX otherwise continues streaming binary zeroes.)



Check on A/UX fcnvt and setfile

{Dead-end - don't do this, rather transfer into the virtual machine as CD:

hmount "Apple_AUX_2.0_Reference_Archive_v0.96c_1.iso"
hcd "Apple A/UX 2.0:Essential Tools and Software:Utilities:Compression:"
hpwd
hls
hcopy -m BinHex\ 5.0 ./
hcopy -m DD\ Expand ./
humount

hcd "Apple A/UX 2.0:Essential Tools and Software:Utilities:Disk and Image Utilities"
}

How I got StuffIt Expander 4 - OK, I did not, but it DOES work for OTHER bin files that way (here it worked, but StuffIt crashes), so that is a sound method:

From here:

https://macintoshgarden.org/apps/stuffit-expander-402

I got

stuffit_expander_4.0.2.sea_.bin

This I renamed to stuffexp402.bin and then, still on macOS:

uuencode stuffexp402.bin stuffexp402.bin > stuffexp402.uue

cat stuffexp402.uue | nc -l 8888

On the A/UX virtual machine I did:

telnet 10.0.0.27 8888 > stuffexp402.uue

uudecode stuffexp402.uue

fcnvt -i bin stuffexp402.bin StuffExp402

... AND THERE IT WAS, in my folder, the moment I looked at it from the System Finder application.

How I ACTUALLY got it:

Ctrl-Click on AUXrunner, Show Package Contents.

Inside it, place StuffIt-Expander-4-0-2.iso, and edit qemu-aux-single.command, adding two lines at the end:

#!/bin/bash
cd "$(dirname "$0")"

./single/qemu-system-m68k \
-M q800 \
-m 128 \
-bios Quadra800.rom \
-g 800x600x8 \
-display sdl \
-nic user,model=dp83932,mac=08:00:07:12:34:56,hostfwd=tcp::2121-:21 \
-drive file=./single/pram-aux.img,format=raw,if=mtd \
-device scsi-hd,scsi-id=0,drive=hd0 \
-drive file=root.img,media=disk,format=raw,if=none,id=hd0  \
-device scsi-hd,scsi-id=2,drive=hd1 \
-drive file=opt.img,media=disk,format=raw,if=none,id=hd1 \
-device scsi-cd,scsi-id=3,drive=cd1 \
-drive file=StuffIt-Expander-4-0-2.iso,media=cdrom,format=raw,if=none,id=cd1

The annoying startup preferences for Macintosh apps inside the virtual machine are here:

/mac/sys/System Folder/Preferences

- but you cannot actually DELETE anything there, and the folder is invisible.

fcnvt has a good manpage, but also see:

https://68kmla.org/bb/index.php?threads/getting-files-on-off-a-ux-in-qemu.41437/

StuffIT Expander as iso:

https://www.macintoshrepository.org/1468-stuffit-expander-4-0-2

A/UX info:

https://aux.preterhuman.net/

HQX packages:

https://info-poland.icm.edu.pl/packages/info-mac/_Development/

https://boileri.nic.funet.fi/pub/mac/info-mac/_Graphic_&_Sound_Tool/

https://archive.info-mac.org/_Development/

https://archive.info-mac.org/dev/

https://ftp.lyx.org/pub/mac/info-mac/dev/

German MacPaint, installs, fails to run properly:

https://www.macintoshrepository.org/1912-macpaint-source-code

Fix-a-Fork, do not see it useful so far:

https://amendhub.com/nulleric/Fix-a-Fork

Microsoft Office:

https://winworldpc.com/product/microsoft-office/2x

"MacBinary":

https://en.wikipedia.org/wiki/MacBinary

mcvert:

https://web.mit.edu/~mkgray/jik/sipbsrc/src/mcvert/mcvert.c
https://www.tuhs.org/Usenet/comp.unix.aux/1991-April/001294.html

p. 118 Macintosh archive formats:

http://www.holodeck.com/curt/mypapers/Thesis.pdf

AUXrunner:

https://mendelson.org/auxrunner.html



Other Addresses:

https://www.nano-editor.org/dist/

https://stackoverflow.com/questions/17817673/7zip-iso-extraction

https://ftp.lip6.fr/pub/minix/distfiles/backup/

https://ftp.uni-sofia.bg/UnixArchive/Applications/Algol_68/

https://macintoshgarden.org/apps/apple-aux-300-and-301-wgs95-reference-archive

unfortunately, that stupid ISO cannot be extracted: 7z x -y -o./auxiso APPLE_AUX_3-0-1_RETAIL.iso

- that fails.

https://68kmla.org/bb/index.php?threads/max-drive-size-for-a-ux.29852/o

https://bitsavers.trailing-edge.com/pdf/apple/mac/a_ux/aux_3.0/AUX_3.0_Programming_Languages_and_Tools_Volume_1_1992.pdf

BWBASIC: https://www.mia-jaap.de/software/programming

useless, but fun: https://mirrors.slackware.com/slackware/slackware-1.01/

CAML: https://caml.inria.fr/pub/distrib/caml-light-0.74/

Bas not reachable: https://www.mmnt.net/db/0/304/gentoo.chem.wisc.edu/gentoo/distfiles/

Bas not reachable: https://www.mmnt.ru/int/get?in=f&sz=191156&st=bas-2.1.tar.gz

JAGUBOX:

https://ftpmirror.your.org/pub/misc/jagubox/

https://web.archive.org/web/19980713122750/https://jagubox.gsfc.nasa.gov/aux/GNU_stuff/

Chasing specifically: https://jagubox.gsfc.nasa.gov/aux/GNU_stuff/gcc-2.6.3v2.bin.tar.gz

GCL 1.1 worked: https://ftp.gnu.org/gnu/gcl/old/
Old, but NOT VERY old GNU stuff, unfortunately: https://ftp.gnu.org/gnu/

AI Languages (including hqx archives for the Macintosh side of A/UX):

https://www.cs.cmu.edu/afs/cs/project/ai-repository/ai/lang/

