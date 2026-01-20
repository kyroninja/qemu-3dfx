# QEMU MESA GL/3Dfx Glide Pass-Through
Copyright (C) 2018-2025<br>
KJ Liew \<liewkj@yahoo.com\>
## Content
    qemu-0/hw/3dfx      - Overlay for QEMU source tree to add 3Dfx Glide pass-through device model
    qemu-1/hw/mesa      - Overlay for QEMU source tree to add MESA GL pass-through device model
    scripts/sign_commit - Script for stamping commit id
    wrappers/3dfx       - Glide wrappers for supported guest OS/environment (DOS/Windows/DJGPP/Linux)
    wrappers/mesa       - MESA GL wrapper for supported guest OS/environment (Windows)
## Patch
    00-qemu92x-mesa-glide.patch - Patch for QEMU version 9.2.x (MESA & Glide)
    01-qemu82x-mesa-glide.patch - Patch for QEMU version 8.2.x (MESA & Glide)
    02-qemu72x-mesa-glide.patch - Patch for QEMU version 7.2.x (MESA & Glide)
## QEMU Windows Guests Glide/OpenGL/Direct3D Acceleration
Witness, experience and share your thoughts on modern CPU/GPU prowess for retro Windows games on Apple Silicon macOS, modern Windows and Linux. Most games can be installed and played in pristine condition without the hassle of hunting down unofficial, fan-made patches to play them on modern Windows or Linux/Wine.
- YouTube channel (https://www.youtube.com/@qemu-3dfx/videos)
- VOGONS forums (https://www.vogons.org)
- Wiki (https://github.com/kjliew/qemu-3dfx/wiki)
## Building QEMU
Following instructions are based on `MSYS2/mingw-w64` BASH shell environment on modern Windows. It is meant to be simple and minor variations are inevitable due to different flavors of Linux distributions.

Simple guide to apply the patch:<br>
(using `03-qemu102x-mesa-glide.patch`)

(Setup MSYS2 and use MINGW64 environment)

    $ pacman -S mingw-w64-i686-toolchain mingw-w64-i686-glib2 mingw-w64-i686-pixman mingw-w64-i686-gtk3 mingw-w64-i686-SDL2 mingw-w64-i686-python mingw-w64-i686-ninja mingw-w64-i686-meson mingw-w64-i686-pkg-config mingw-w64-i686-libslirp
    $ mkdir ~/myqemu && cd ~/myqemu
    $ git clone https://github.com/kjliew/qemu-3dfx.git
    $ cd qemu-3dfx
    $ wget https://download.qemu.org/qemu-10.2.0.tar.xz
    $ tar xf qemu-10.2.0.tar.xz
    $ cd qemu-10.2.0
    $ rsync -r ../qemu-0/hw/3dfx ../qemu-1/hw/mesa ./hw/
    $ patch -p1 -i ../03-qemu102x-mesa-glide.patch
    $ bash ../scripts/sign_commit
    $ mkdir ../build && cd ../build
    $ ../configure --enable-whpx --enable-sdl --enable-gtk --disable-werror --enable-slirp --audio-drv-list=dsound,sdl --target-list=i386-softmmu

## Building Guest Wrappers
**Requirements:**
 - `base-devel` (make, sed, xxd etc.)
 - `gendef, shasum`
 - `mingw32` cross toolchain (`binutils, gcc, windres, dlltool`) for WIN32 DLL wrappers
 - `Open-Watcom-1.9/v2.0` or `Watcom C/C++ 11.0` for DOS32 OVL wrapper
 - `{i586,i686}-pc-msdosdjgpp` cross toolchain (`binutils, gcc, dxe3gen`) for DJGPP DXE wrappers
<br>

    $ cd ~/myqemu/qemu-3dfx/wrappers/3dfx
    $ mkdir build && cd build
    $ bash ../../../scripts/conf_wrapper
    $ make && make clean

    $ cd ~/myqemu/qemu-3dfx/wrappers/mesa
    $ mkdir build && cd build
    $ bash ../../../scripts/conf_wrapper
    $ make && make clean

**New Instructions**
sudo apt update
sudo apt install build-essential make sed xxd git curl wget perl libdigest-sha-perl mingw-w64-tools gcc-mingw-w64-i686 binutils-mingw-w64-i686 mingw-w64

---setup djgpp---
cd ~
wget https://github.com/andrewwutw/build-djgpp/releases/download/v3.4/djgpp-linux64-gcc1220.tar.bz2

# Extract
sudo tar xjf djgpp-linux64-gcc1220.tar.bz2 -C /usr/local

sudo ln -s /usr/local/djgpp/i586-pc-msdosdjgpp/bin/dxe3gen /usr/local/djgpp/bin/i586-pc-msdosdjgpp-dxe3gen
sudo ln -s /usr/local/djgpp/i586-pc-msdosdjgpp/bin/dxe3res /usr/local/djgpp/bin/i586-pc-msdosdjgpp-dxe3res
sudo ln -s /usr/local/djgpp/i586-pc-msdosdjgpp/bin/dxe3gen /usr/local/djgpp/bin/dxe3gen
sudo ln -s /usr/local/djgpp/i586-pc-msdosdjgpp/bin/dxe3res /usr/local/djgpp/bin/dxe3res

cat >> ~/.bashrc << 'EOF'
export PATH="/usr/local/djgpp/bin:$PATH"
export DJDIR="/usr/local/djgpp/i586-pc-msdosdjgpp"
EOF

source ~/.bashrc

----setup openwatcom----
wget https://github.com/open-watcom/open-watcom-v2/releases/download/2024-09-03-Build/open-watcom-2_0-c-linux-x64

chmod +x open-watcom-2_0-c-linux-x64
sudo ./open-watcom-2_0-c-linux-x64

export WATCOM=/usr/bin/watcom
export PATH=$PATH:$WATCOM/binl64

## Installing Guest Wrappers
**For Win9x/ME:**  
 - Copy `FXMEMMAP.VXD` to `C:\WINDOWS\SYSTEM`  
 - Copy `GLIDE.DLL`, `GLIDE2X.DLL` and `GLIDE3X.DLL` to `C:\WINDOWS\SYSTEM`  
 - Copy `GLIDE2X.OVL` to `C:\WINDOWS`  
 - Copy `OPENGL32.DLL` to `Game Installation` folders

**For Win2k/XP:**  
 - Copy `FXPTL.SYS` to `%SystemRoot%\system32\drivers`  
 - Copy `GLIDE.DLL`, `GLIDE2X.DLL` and `GLIDE3X.DLL` to `%SystemRoot%\system32`  
 - Run `INSTDRV.EXE`, require Administrator Priviledge  
 - Copy `OPENGL32.DLL` to `Game Installation` folders
 
## Donation
If this project helps you relive the nostalgic memory of Good Old Windows Games, you can now donate in the course of supporting **Games Preservation** with QEMU. Your donation also motivates and encourages further research in making QEMU the ultimate platform for Retro Windows Games.

For $89.99 donation, you will deserve the following donor's privileges:
- QEMU binary package built for platform of your choice (choose **ONE**: Windows 10/11, Ubuntu, etc.)
- QEMU-enhanced OpenGLide **Host-side wrappers** built for platform of your choice (choose **ONE**: Windows 10/11, Ubuntu, etc.)
- QEMU-enhanced [**WineD3D libraries for Win98/2K/ME/XP VMs**](https://www.winehq.org) for DirectDraw/Direct3D games up to DirectX 9.0c
- Game controllers support with [**QEMU USB Gamepad**](https://github.com/kjliew/qemu-3dfx/wiki/QEMU-USB-Gamepad)
- SDL2 clipboard sharing through built-in [**QEMU vdagent**](https://www.kraxel.org/blog/2021/05/qemu-cut-paste/)
- OpenGLide **Guest-side wrappers** for Windows
- Elect up to 5 games for priority support and your name as the honorary sponsor in the supported & tested list of games.

[![paypal](https://www.paypalobjects.com/en_US/i/btn/btn_donateCC_LG.gif)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=XE47KTASERX4A)
### A Note to Donation
The purpose of the donation is for preserving retailed CD/DVD games in their originality. It may be used to purchase the game online or from local thrift shops. The donation is **NOT** the ticket for one to learn how to use QEMU Virtual Machine in general. Sometimes, it may be difficult to get virtualization acceleration working and that would result in serious degradation of game experience with QEMU. It is a **willing donation pledge and non-refundable**. Many classic Windows games also have re-releases from GOG/Steam that work on modern Windows and Linux. It can be an option to consider before making a donation.

Donations without leaving notes on **Platform of Choice** are regarded as upper-class donors who have no desire in exercising donor's privileges. A measure to avoid unneccessary spamming on emails. Donors are expected to proactively follow up the communication to exercise donor's privileges as wished. All donations are tracked in PayPal transaction history. Only **"ONE"** platform of choice per donation. Upgrades eligibility are limited to the **SAME** platform of choice.
### About Game Election
The game election serves the purpose of allocating additional focus and resources to make them work. Sometimes, it means considerable efforts in researching, debugging and tracing the games to root cause the failures and come up with solutions. It is **OPTIONAL** to make game election upon donation. My YouTube channel has video demos of games which already worked and more may be showing up periodically. It is typically a safe assumption that games using the same engine (IdTech1/2/3, LithTech, Unreal etc.) would work, too. The _N_ counts of eligibility would only be accounted once the game were made to work. If upgrades were neccessary, it would be a **FREE upgrade** for QEMU binary packages.
