# Repo Manifest for Systemready IR compliant rk3399 firmware

The information provided here provides the configuration and build setup for
ARM SystemReady compliant firmware for some Rockchip RK3399 based platforms.

There are three Evaluation Platforms being used as platform for the verification

| Target  | Manifest XML  | Device |
| :------------: |:---------------:| :-----:|
| Toy Brick      | TB-RK3399proD.xml | RK3399 Pro |
| Lenovo Leez     | Leez-P710.xml        |  Leez-p710 |
| Pine 64 | RockPro64.xml |    RockPro 64 |

```
Note: The guide below has been extensively tested with the TB_RK3399ProD but is expected 
to work with all of the above platforms.
```

## 1. Prepare Host machine
The following package installations and steps are required to be completed to build and install the firmware binaries.

```
sudo apt-get install -y cmake python3 python-is-python3 git gnupg bison flex
sudo apt-get install -y libssl-dev device-tree-compiler

# Configure git with your details
git config --global user.email "you@example.com"
git config --global user.name "Your Name"

# Install repo
#   Option 1 - package manager
sudo apt-get install repo

##   Option 2 - manual download
##  curl https://storage.googleapis.com/git-repo-downloads/repo > /opt/repo
##  chmod +x /opt/repo
##  export PATH=$PATH:/opt
```


## 2. Clone the repositories
```
mkdir <New_Dir>
cd <New_Dir>
repo init -u https://github.com/ownia/RK3399-manifest -m TB-RK3399proD.xml
repo sync -j4 --no-clone-bundle
```

## 3. Get the Toolchains
If you dont have an aarch64-linux-gnu cross compiler already installed on your host machine, there are 2 ways of suppling the toolkits required to build the firmware and u-boot binaries.

* Option 1: Package install the cross-compilers on the host machine OS.
* Option 2: Use the toolchain installer thats provided by the makefile

### Option 1: PREFERRED

```
sudo apt-get install gcc-aarch64-linux-gnu arm-none-eabi-gcc
which aarch64-linux-gnu-gcc
```
Copy the path reported by the above command (excluding the tailing `bin/aarch64-linux-gnu-gcc`) and paste it after the command below:

```
export AARCH64_PATH=
```
For example:

```
$ which aarch64-linux-gnu-gcc
/usr/bin/aarch64-linux-gnu-gcc
$ export AARCH64_PATH=/usr
$ echo $AARCH64_PATH
/usr
```

### Option 2: ALTERNATIVE (skip this if using option 1)

```
cd build/
make -j2 toolchains
```

## 4. Build u-boot binaries for target
```
cd build/
make -j `nproc`
```
Amongst other things the above command will generate the following files of interest in the `../out/bin/u-boot/` directory:

```
u-boot.itb
idbloader.img
```
Check the above by running:
```
ls -ltr ../out/bin/u-boot/
```



## 5. Building the FW binaries
```
cd ../rkbin
./tools/boot_merger  ./RKBOOT/RK3399PROMINIALL.ini
```
The above command will generate the miniloader file: eg. `rk3399pro_loader_v1.25.126.bin`

## 6. Flashing the FW binaries and U-Boot

### 6-a: Preparing the host-machine
To succesgfully detect and flash the board the following packages are required to be installed on the host machine

```
sudo apt-get install pkg-config libudev-dev libusb-1.0-0-dev libusb-1.0
```


### 6-b: Put the board in MaskROM mode
1. Power-off the board
2. Keep the Maskrom button pressed for 10 seconds
3. Power on the board (either by long-pressing the power button OR plugging in the Power connector)
4. Release maskrom button after 5 seconds
5. The device should now be in Maskrom mode ready for flashing
6. Check if the board has entered MaskROM mode:

```
# Check if the following device is detected:
#    Fuzhou Rockchip Electronics Company RK3399 in Mask ROM mode
lsusb
```


### 6-c: Writing the binaries to device
```
sudo ./tools/rkdeveloptool db rk3399pro_loader_v1.25.126.bin && sleep 2
sudo ./tools/rkdeveloptool wl 0x40 ../out/bin/u-boot/idbloader.img && sleep 2
sudo ./tools/rkdeveloptool wl 0x4000 ../out/bin/u-boot/u-boot.itb && sleep 2
sudo ./tools/rkdeveloptool rd
```

The TB_RK3399ProD board should restart and boot into u-boot

If you have a bootable storage device with LINUX installed connected to USB or installed in MMCslot the board should start booting the linux kernel.




