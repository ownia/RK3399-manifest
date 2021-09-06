# Repo Manifest for Systemready IR compliant rk3399 firmware

The information provided here provides the configuration and build setup for
ARM SystemReady compliant firmware for some Rockchip RK3399 based platforms.

There are three Evaluation Platforms being used as platform for the verification

| Target  | Manifest XML  | Device |
| :------------: |:---------------:| :-----:|
| Toy Brick      | TB-RK3399proD.xml | RK3399 Pro |
| Lenovo Leez     | Leez-P710.xml        |  Leez-p710 |
| Pine 64 | RockPro64.xml |    RockPro 64 |


## 1. Install repo
Follow the instructions under the "Installing Repo" section
[here](https://source.android.com/source/downloading.html).

## 2. Get the source code
```
$ mkdir <New_Dir>
$ cd <New_Dir>
$ repo init -u https://gitlab.arm.com/systemready/firmware-build/rk3399-manifest -m ${TARGET}.xml [-b ${BRANCH}]
$ repo sync -j4 --no-clone-bundle
```

To obtain the same firmware as the one used for Leez certification, use this `repo init` command:

```
$ repo init -u https://gitlab.arm.com/systemready/firmware-build/rk3399-manifest -m Leez-P710.xml -b refs/tags/leez-21.08
```

## 3. Get the Aarch64 toolchain (optional)
```
$ cd <New_Dir>/build/
$ make -j2 toolchains
```

## 4. Build the target
```
$ make -j `nproc`
```

## 5.Target Image Flashing Procedure

The sequence will sync the atf(bl31), uboot(uboot.itb) to the local repo and
make the images for the selected target. Image flashing can be carried out
manually using the rktool, or the `make flash` target will use rkdeveloptool to
copy the images to a board in BOOTROM mode.

```
Install Android Tool v2.71
Address Name Path :
0x0000 Miniloader          — ../miniloader.bin
0x0000 GPT Partition Table — ../paramter_toybrick.txt
0x0040 loader1             — ../idbloader.img
0x4000 loader2             — ../uboot.itb
```
