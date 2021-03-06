### About

This directory contains files to run IoT.js on [TizenRT](https://github.com/Samsung/TizenRT).


### How to build

#### 1. Set up the build environment for Artik053 board

##### Install toolchain

Get the build in binaries and libraries, [gcc-arm-none-eabi-4_9-2015q3-20150921-linux.tar](https://launchpad.net/gcc-arm-embedded/4.9/4.9-2015-q3-update).

Untar the gcc-arm-none-eabi-4_9-2015q3-20150921-linux.tar and export the path like

```
tar xvf gcc-arm-none-eabi-4_9-2015q3-20150921-linux.tar
export PATH=<Your Toolchain PATH>:$PATH
  ```

##### Install genromfs to use romfs

```bash
sudo apt-get install genromfs
```

##### Install kconfig-frontends to use menuconfig

Refer to [Kconfig-frontends Installation](https://github.com/Samsung/TizenRT#appendix)

##### Get IoT.js and TizenRT sources

Clone IoT.js and TizenRT into iotjs-tizenrt directory

```bash
mkdir iotjs-tizenrt
cd iotjs-tizenrt
git clone https://github.com/Samsung/iotjs.git
git clone https://github.com/Samsung/TizenRT.git
```

The following directory structure is created after these commands
```bash
iotjs-tizenrt
  + iotjs
  + TizenRT
```

#### 2. Configure IoT.js

This step is optional. You can edit the build config of IoT.js in `iotjs/build.config`.
For example, edit `iotjs-include-module` to add modules as below.

```
"iotjs-include-module": ["adc", "gpio"]
```

#### 3. Configure TizenRT

```bash
cd TizenRT/os/tools
./configure.sh artik053/iotjs
cd ..
```

#### 4. Copy your app in ROMFS

```bash
cp <your test app> ../tools/fs/contents/
# make ROMFS image
../tools/fs/mkromfsimg.sh
# The contents folder is later flashing into the target's /rom folder
```

NOTE: The romfs image file must not exceed 400kb.

#### 5. Build IoT.js for TizenRT

```bash
make IOTJS_ROOT_DIR=../../iotjs
```

If `IOTJS_ROOT_DIR` isn't given as the argument, IoT.JS codes, which are located in `external/iotjs` of TizenRT, will be used for the build.

> :grey_exclamation: Trouble Shooting: Building IoT.js fails: You may encounter `arm-none-eabi-gcc: Command not found` error message while building IoT.js on a 64-bit system. This may be because the above toolchain you set uses 32-bit libs. For this matter, install the below toolchain as alternative.
> ```
> $ sudo apt-get install -y libc6-i386
> ```

#### 6. Flashing

```bash
make download ALL
```
> :grey_exclamation: Trouble Shooting: Flashing the binary via USB fails: Refer to [add-usb-device-rules](https://github.com/Samsung/TizenRT/blob/master/build/configs/artik053/README.md#add-usb-device-rules). Your `VendorID:ProductID` pair can be found in `lsusb` output as the below instance.
>
>```
>$ lsusb
>Bus 004 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
>Bus 003 Device 005: ID 0403:6010 Future Technology Devices International, Ltd >FT2232C Dual USB-UART/FIFO IC
>```
