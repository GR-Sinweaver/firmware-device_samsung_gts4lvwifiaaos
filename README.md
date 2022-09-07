wget https://dl.google.com/android/repository/platform-tools-latest-linux.zip
unzip platform-tools-latest-linux.zip -d ~

sudo nano ~/.profile
--------------------------------------
# add Android SDK platform tools to path
if [ -d "$HOME/platform-tools" ] ; then
    PATH="$HOME/platform-tools:$PATH"
fi
--------------------------------------

source ~/.profile

sudo apt-get install bc bison build-essential ccache curl flex g++-multilib gcc-multilib git gnupg gperf imagemagick lib32ncurses5-dev lib32readline-dev lib32z1-dev libelf-dev liblz4-tool libncurses5 libncurses5-dev libsdl1.2-dev libssl-dev libxml2 libxml2-utils lzop pngcrush rsync schedtool squashfs-tools xsltproc zip zlib1g-dev openjdk-11-jdk repo brotli python-is-python3

mkdir -p ~/bin
mkdir -p ~/android/lineage

curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod a+x ~/bin/repo

sudo nano ~/.profile
--------------------------------------
# set PATH so it includes user's private bin if it exists
if [ -d "$HOME/bin" ] ; then
    PATH="$HOME/bin:$PATH"
fi
--------------------------------------

source ~/.profile
git config --global user.email "gr.twostep@gmail.com"
git config --global user.name "TWOSTEP"
export USE_CCACHE=1
export CCACHE_EXEC=/usr/bin/ccache
ccache -M 50G

cd ~/android/lineage
repo init -u https://github.com/LineageOS/android.git -b lineage-18.1
repo sync

source build/envsetup.sh
breakfast gts4lvwifi

mkdir ~/android/system_dump/
cd ~/android/system_dump/
wget ----
unzip path/to/lineage-*.zip system.transfer.list system.new.dat*
unzip path/to/lineage-*.zip vendor.transfer.list vendor.new.dat*
brotli --decompress --output=system.new.dat system.new.dat.br
brotli --decompress --output=vendor.new.dat vendor.new.dat.br
git clone https://github.com/xpirt/sdat2img
python sdat2img/sdat2img.py system.transfer.list system.new.dat system.img
mkdir system/
sudo mount system.img system/
python sdat2img/sdat2img.py vendor.transfer.list vendor.new.dat vendor.img
sudo rm system/vendor
sudo mkdir system/vendor
sudo mount vendor.img system/vendor/

cd ~/android/lineage
./extract-files.sh ~/android/system_dump/
source build/envsetup.sh
breakfast gts4lvwifi

cd ~/android/lineage/.repo/local_manifests/
wget https://raw.githubusercontent.com/snappautomotive/firmware-local_manifest/main/snappautomotive.xml
wget https://raw.githubusercontent.com/GRTWOSTEP/files/main/lineage-aaos.xml
sudo nano roomservice.xml
```
REMOVE THESES LINES
<project name="LineageOS/android_device_samsung_gts4lvwifi" path="device/samsung/gts4lvwifi" remote="github" />
<project name="LineageOS/android_device_samsung_gts4lv-common" path="device/samsung/gts4lv-common" remote="github" />
```
cd ~/android/lineage
repo sync -j 4 --force-sync
brunch gts4lvwifi


_________________________________________________________________________________________________________________________________________________________________

WIP MODIFICATION


This is an unofficial Android Automotive OS target for the wifi version of the Samsung Galaxy Tab S5e based on
Lineage 18.1 build.

To create your own build please do the following;

1. [Install the Lineage-18.1 nightly with the Lineage recovery image](https://wiki.lineageos.org/devices/gts4lvwifi/install). Please do not try and use TWRP, I've encountered issues with it unpacking builds which cost me a chunk of time ;).

2. [Follow the Lineage-18.1 build instructions for the S5e (a.k.a. gts4lvwifi)](https://wiki.lineageos.org/devices/gts4lvwifi/build).

Once you're happy you can build and install the Lineage image then it's time to build the AAOS variant;

1. Edit the file `.repo/local_manifests/roomservice.xml` and remove the following lines;

```
<project name="LineageOS/android_device_samsung_gts4lvwifi" path="device/samsung/gts4lvwifi" remote="github" />
<project name="LineageOS/android_device_samsung_gts4lv-common" path="device/samsung/gts4lv-common" remote="github" />
```

2. Download [this additional manifest](https://raw.githubusercontent.com/snappautomotive/firmware-local_manifest/main/snappautomotive.xml) and place it in `.repo/local_manifests/`. 

3. Download [this other additional manifest](https://raw.githubusercontent.com/GRTWOSTEP/files/main/lineage-aaos.xml) and place it in `.repo/local_manifests/`. 

4. Run `repo sync -j 4 --force-sync` from the base of your checkout

5. Create an AAOS build by running `brunch gts4lvwifi`.

6. Update your device with the generated files in the same way as you've already done for your previous lineage build.
