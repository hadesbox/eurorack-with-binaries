=======
# eurorack-with-binaries

This is a mirror repo of the Mutable Instrumetns Eurorack repo, but additionally it contains under the folder BUILD all the latest compiled programs for the modules (including some WAVs).

The reason why I pushed this repo was to provide compiled programs, since Its not super easy to setup all the libraries and binding in some platform (i.e. OSX), because I tried my self. I was quite easy on Ubuntu, so I pushed all this compiled programs for my personal use (and to share them).

=======

# Uploading
(From [flocked](http://mutable-instruments.net/forum/discussion/4344/mac-tutorial-how-to-compile-and-upload-the-firmware-of-mis-eurorack-modules/p1))

There are three ways of uploading a firmware to the modules:

1. Audio input via wav file (very slow, but no programmer is needed and it’s supported by every module, in order to use this method you must already have the "YOURMODULE_bootloader" into the microcontroller, so a blank *new* microcontroller cannot use this method).
2. FTDI (slower than JTAG, but the programmer isn’t as expensive; 10-15$; Yarns has no FTDI port)
3. JTAG (fast, but the programmer is expensive; 50-60$)

## FTDI

If you want to upload a firmware via FTDI, you will need to get an FTDI programmer. I can recommend FTDI friend (you will need to cut the connection between the two 5v VCC pads and solder the pads for 3v). There are a few additional first steps, before you can compile and upload code via FTDI:

1. Download and install FTDI driver
2. Download Pyserial and unpack the tar.gz
3. Open Terminal and enter:
..* cd /PATHTOPYSERIALFOLDER/
..* sudo python setup.py install
..* Enter: ls /dev/cu.*
4. Copy the returned value that looks similar to: /dev/cu.usbserial-XXXX
8. Open /eurorack/stmlib/makefile.inc
9. Paste the copied value to PGM_SERIAL_PORT

Now you can upload via FTDI:

1. Connect FTDI friend to the 6 FTDI pins on your module (the module needs to be connected to your eurorack busboard for power).
2. Press and hold RESET. Press SYSBOOT simultaneously, then release reset while still holding SYSBOOT.
3. Open Terminal and enter: “cd /PATHTOEURORACKFOLDER/”
4. To upload: “make -f /MODULENAME/makefile upload_combo_serial”

# Libraries and configuration

Remember you will have to probably change some configuration on your computer, since avrlib, avrdude and many other tools and libs are installed on different paths on different distros (Win, OSX, Ubuntu, Centos, etc...) here are mine just you can double check yours!

### avrlib/makefile.mk

```
luix@boxita:~/git/eurorack/avrlib$ git diff
diff --git a/makefile.mk b/makefile.mk
index 3c1f5be..7a84990 100644
--- a/makefile.mk
+++ b/makefile.mk
@@ -13,7 +13,7 @@
 # You should have received a copy of the GNU General Public License
 # along with this program.  If not, see <http://www.gnu.org/licenses/>.
 
-AVRLIB_TOOLS_PATH ?= /usr/local/CrossPack-AVR/bin/
+AVRLIB_TOOLS_PATH ?= /usr/bin/
 BUILD_ROOT     = build/
 BUILD_DIR      = $(BUILD_ROOT)$(TARGET)/
 PROGRAMMER     ?= avrispmkI
```

### avrlibx/config.mk

```
luix@boxita:~/git/eurorack/avrlibx$ git diff
diff --git a/config.mk b/config.mk
index d9c0160..0ad41fa 100644
--- a/config.mk
+++ b/config.mk
@@ -13,7 +13,7 @@
 # You should have received a copy of the GNU General Public License
 # along with this program.  If not, see <http://www.gnu.org/licenses/>.
 
-AVRLIBX_TOOLS_PATH = /usr/local/CrossPack-AVR/bin/
-AVRDUDE_PATH       = /usr/local/CrossPack-AVR/bin/
+AVRLIBX_TOOLS_PATH = /usr/bin/
+AVRDUDE_PATH       = /usr/bin/
 PROGRAMMER         = avrispmkII
 PROGRAMMER_PORT    = usb
```

### stmlib/makefile.inc

```
luix@boxita:~/git/eurorack/stmlib$ git diff
diff --git a/makefile.inc b/makefile.inc
index 341bbae..3480e98 100644
--- a/makefile.inc
+++ b/makefile.inc
@@ -26,11 +26,14 @@
 # Files and directories
 # ------------------------------------------------------------------------------
 
-TOOLCHAIN_PATH ?= /usr/local/arm/
+TOOLCHAIN_PATH = /home/luix/apps/gcc/gcc-arm-none-eabi-4_8-2013q4/
 TOOLCHAIN_BIN  = $(TOOLCHAIN_PATH)bin/
 STM32_PATH     = stmlib/third_party/STM
 
-PGM_SERIAL_PORT = /dev/tty.usbserial-A100S09O
+PGM_SERIAL_PORT = /dev/ttyUSB0
 PGM_SERIAL_BAUD_RATE = 115200
 PGM_SERIAL_VERIFY = -v
```
