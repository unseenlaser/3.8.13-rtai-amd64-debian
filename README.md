3.8.13-rtai-amd64-debian
========================

Sources, patches etc to build 3.8.13-rtai kernel for 64 bit Debian
For use with RTAI and MachineKit
******************************************************************

Copy patches and configs to /usr/src

Download the 3.8.13 kernel sources from www.kernel.org

Unpack in /usr/src and create symlink to them called /usr/src/~linux

Enter ~linux

patch -p1 < ../hal-linux-3.8.13-x86-4.patch
patch -p1 < ../sse-hack.patch

cp ../3.8.13-SMP-MG-x86_64-config ./.config

make menuconfig   (change the number of cpus if necessary but nothing else exit and save)

make -jX  (where X is the number of your cpus plus 1)

make modules_install

make install

Then update-grub or manually change grub.cfg and reboot to 3.8.13-rtai kernel

**************************************

git clone https://github.com/ShabbyX/RTAI.git into /usr/src

cp .rtai-config RTAI/

cd RTAI

autogen.sh

make menuconfig (set the number of cpus to the same as you machine and the kernel setting and turn OFF kernel maths support)

./configure

make && make install

cd /usr/src

git clone https://github.com/machinekit/machinekit

cd machinekit/src/rtapi/xeno_math

open submakefile in an editor, top line should read

 'ifeq ($(BUILD_SYS),kbuild)'
 
if not change it to match

cd ../../

autogen.sh

./configure && make -jX && sudo make setuid

cd ../etc/linuxcnc

open rtapi.ini

edit last section to read

#################################################
# _cfg_flavor flavor section
#
[flavor_rtai-kernel]
# These values do not normally need to be changed.
rtapi_app=/usr/src/machinekit/libexec/rtapi_app_rtai-kernel
RTS=/usr/realtime/bin/rtai-config
RTDIR=/usr/realtime/modules
MODULES=rtai_hal rtai_sched xeno_math

ie ensure xeno_math is loaded not rtai_math, which has not been built

cd ../../

. ./scripts/rip_environment

linuxcnc

et voila


