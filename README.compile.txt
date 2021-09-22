# $Id: README.compile.txt,v 1.23 2021/01/10 21:22:59 kentd Exp $

General build instructions:
--------------------------

KEGS only supports Mac OS X and Linux compiles for now.  Win32 support will
be added back at a future date.

You need to build with a make utility.  There's a default Makefile, which
should work for nearly any environment.  The Makefile includes a file called
"vars" which defines the platform-dependent variables.  You need to make
vars point to the appropriate file for your machine.

A KEGSMAC.app pre-built application for Mac OS 10.14 (Mojave) or later is
provided.  I think it might work on 10.13 (High Sierra), but I have not
tested it.

A xkegs Linux app pre-built for 64-bit Redhat RHEL 7.2 is also provided.
I'm hoping it works on other Linuxes.

Mac OS X build instructions (the default):
------------------------------------------

KEGS is easy to compile, but you must install XCode first.  Go to the Apple
App store, select Xcode, and install it.  You then must execute the Xcode.app
and agree to terms.  It will want to download additional components, you
need those, too.

Apple only makes the latest XCode available from the App Store, so if you're
on Mojave, for example, you can no longer download that XCode.  You need to
create a free developer account, and then go to
developer.apple.com/download/more/ and download the right version.  For
Mojave (10.14), you want XCode 11.3.1.  The latest list of XCode versions for
each Mac OS version is at en.wikipedia.org/wiki/XCode

Then, cd to the src directory of the KEGS release and type "make -j 20".
KEGS requires perl to be in your path, which is /usr/bin/ on the MAC
(which should be in your $PATH).

Even after installing XCode, the "make" may pop up a dialog saying something
like "The make command requires the command line developer tools.  Would
you like to install the tools now?".  Click "Install".

After the "make" has finished, it will create the application KEGSMAC.

To run, see README.mac.

Linux build instructions:
------------------------

Use the vars_x86linux file with:

rm vars; ln -s vars_x86linux vars
make -j 20

The resulting executable is called "xkegs".

The build scripts assume perl is in your path. If it is somewhere else,
you need to edit the "PERL = perl" line in the Makefile it point
to the correct place.

KEGS by default requires Pulse Audio to compile.  To compile to use /dev/dsp
(not really supported by any current Linux as far as I know), edit
vars to remove pulseaudio_driver.o, the -lpulse of EXTRA_LIBS, and
remove -DPULSE_AUDIO from CCOPTS.

You may not have the required include files on your system to compile BURST.
On RHEL, use "yum provides {type path to file you got an error on}".
For example, pulseaudio requires pulse/pulseadio.h which is indicated by the
error message "pulse/pulseaudio.h: No such file".  All include files are
under /usr/include, so do:

yum provides /usr/include/pulse/pulseadio.h

to see that you need something like pulseaudio-libs-devel-10.0-5.el7.x86_64,
and then do:

su
yum install pulseaudio-libs-devel-10.0-5.el7.x86_64

On my machine, I also had to install the 32-bit libs and headers:
yum install pulseaudio-libs-devel-10.0-5.el7.i686

And I got an error for:
"xdriver.c: fatal error: X11/Xlib.h: No such file or directory".

So:

yum provides /usr/include/X11/Xlib.h

gives libX11-devel-1.6.7-3.el7_9.x86_64, so:

yum install libX11-devel-1.6.7-3.el7_9.x86_64

And /usr/include/X11/extensions/XShm.h is needed, so:

yum install libXext-devel-1.3.3-3.el7.x86_64

Then, /usr/lib64/libpulse.so wasn't found, so:

yum install pulseaudio-libs-devel-10.0-6.el7_9.x86_64


Mac X11 build instructions:
----------------------------

Use the vars_mac_x vars file by:

rm vars; ln -s vars_mac_x vars
make -j 20

The build assumes you've installed XQuartz and run it to initialize itself.

The executable is called xkegs.  KEGS generally does the fastest display
updates in X11 mode--but requires Xshm.  Mac misconfigures the shared memory
limits to be too small to be used as Xshm memory (4MB limit, we need 40MB for
a retina display), so do:

sudo sysctl  kern.sysv.shmmax=67108864          # One segment max: 64MB
sudo sysctl  kern.sysv.shmall=131072            # in 4KB pages Total mem: 512MB


Other platform "C" build instructions:
-------------------------------------

If you are porting to an X-windows and Unix-based machine, it should be
easy.  Start with vars_x86linux.  Remove things causing problems, add things
as needed.  Good luck!

