# ARMSR Manifest for RK3399 EVB Target Setup



## 1. Install repo
Follow the instructions under the "Installing Repo" section
[here](https://source.android.com/source/downloading.html).

## 2. Get the source code
```
$ mkdir <New_Dir>
$ cd <New_Dir>
$ repo init -u https://github.com/ankuraltran/ARMSRManifest -m ${TARGET}.xml [-b ${BRANCH}]
$ repo sync -j4 --no-clone-bundle
```

## 3. Get the Toolkits
```
$ cd <New_Dir>/build/
$ make -j2 toolchains
```

## 4. Build the target
```
$ make -j `nproc`
```

## 5.Target Image Flashing Procedure 
```
Install Android Tool v2.71
Address Name Path :
0x0000 Miniloader   — ../miniloader.bin
0x0000 Parameter    — ../paramter_toybrick.txt
0x0040 loader1      — ../idbloader.img
0x4000 loader2      — ../uboot.itb
0x8000 SCT          — ../sct.efi
```
