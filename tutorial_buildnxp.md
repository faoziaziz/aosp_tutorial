```bash
sudo apt-get install uuid uuid-dev zlib1g-dev liblz-dev liblzo2-2 liblzo2-dev lzop git curl u-boot-tools mtd-utils \
android-sdk-libsparse-utils device-tree-compiler gdisk m4 bison flex make libssl-dev gcc-multilib libgnutls28-dev \
swig liblz4-tool libdw-dev dwarves bc cpio tar lz4 rsync ninja-build clang libelf-dev build-essential libncurses5
```

config git nya

```bash
git config --global user.name "Aziz Faozi"
git config --global user.email "aziz.faozi@prasimax.com"
```

extract source code dari nxp 
```bash
tar xzvf imx-android-14.0.0_1.0.0.tar.gz
```


clone code dari repo
```bash
mkdir ~/bin
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod a+x ~/bin/repo
export PATH=${PATH}:~/bin
source ~/imx-android-14.0.0_2.0.0/imx_android_setup.sh
# By default, after preceding command finishes execution, current working directory changed to the i.MX Android source code root directory.
# ${MY_ANDROID} will be refered as the i.MX Android source code root directory in all i.MX Andorid release documentation.
export MY_ANDROID=`pwd`
```

download the toolchain untuk target arm32
```
wget https://developer.arm.com/-/media/Files/downloads/gnu/12.3.rel1/binrel/arm-gnu-toolchain-12.3.rel1-x86_64-arm-none-eabi.tar.xz
```
download toolchain untuk aarch64

```bash
wget https://developer.arm.com/-/media/Files/downloads/gnu/12.3.rel1/binrel/arm-gnu-toolchain-12.3.rel1-x86_64-aarch64-none-linux-gnu.tar.xz

```

extract toolchain untuk host arm32
```bash
sudo tar -xvJf arm-gnu-toolchain-12.3.rel1-x86_64-arm-none-eabi.tar.xz -C /opt
```
extract toolchain untuk aarch64 target host
```bash
sudo tar -xvJf arm-gnu-toolchain-12.3.rel1-x86_64-aarch64-none-linux-gnu.tar.xz -C /opt
```
export path
```bash
export AARCH32_GCC_CROSS_COMPILE=/opt/arm-gnu-toolchain-12.3.rel1-x86_64-arm-none-eabi/bin/arm-none-eabi-
export AARCH64_GCC_CROSS_COMPILE=/opt/arm-gnu-toolchain-12.3.rel1-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu-

```
clone kernel build tool
```bash 
sudo git clone -b main-kernel-build-2024 --single-branch --depth 1 \
https://android.googlesource.com/platform/prebuilts/clang/host/linux-x86 /opt/prebuilt-android-clang
cd /opt/prebuilt-android-clang
sudo git fetch origin 3bd47139ac0e3593d4707ac0eeb2d45aa7411b67
sudo git checkout 3bd47139ac0e3593d4707ac0eeb2d45aa7411b67
export CLANG_PATH=/opt/prebuilt-android-clang
export LIBCLANG_PATH=/opt/prebuilt-android-clang/clang-r510928/lib

sudo git clone -b main-kernel-build-2024 --single-branch --depth 1 \
https://android.googlesource.com/kernel/prebuilts/build-tools /opt/prebuilt-android-kernel-build-tools
cd /opt/prebuilt-android-kernel-build-tools
sudo git fetch origin ae85d23af20f61220b114fc3f7bb6f77cc140365
sudo git checkout ae85d23af20f61220b114fc3f7bb6f77cc140365
export PATH=/opt/prebuilt-android-kernel-build-tools/linux-x86/bin:$PATH

sudo git clone -b main-kernel-build-2024 --single-branch --depth 1 \
https://android.googlesource.com/platform/prebuilts/rust /opt/prebuilt-android-rust
cd /opt/prebuilt-android-rust
sudo git fetch origin 442511af884f074018466f85b4daadd4b0ac0050
sudo git checkout 442511af884f074018466f85b4daadd4b0ac0050
export PATH=/opt/prebuilt-android-rust/linux-x86/1.73.0b/bin:$PATH

sudo git clone -b main-kernel-build-2024 --single-branch --depth 1 \
https://android.googlesource.com/platform/prebuilts/clang-tools /opt/prebuilt-android-clang-tools
cd /opt/prebuilt-android-clang-tools
sudo git fetch origin 1634c6a556d1f2c24897bf74156c6449486e8941
sudo git checkout 1634c6a556d1f2c24897bf74156c6449486e8941  
export PATH=/opt/prebuilt-android-clang-tools/linux-x86/bin:$PATH
```

lets start to build
```bash
cd ${MY_ANDROID}
source build/envsetup.sh
lunch evk_8mm-trunk_staging-userdebug
./imx-make.sh -j10 2>&1 | tee build-log.txt
```

kalau ada error gak ditemukan gcc
```bash
sudo ln -s /opt/arm-gnu-toolchain-12.3.rel1-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu-ld /opt/arm-gnu-toolchain-12.3.rel1-x86_64-aarch64-none-linux-gnu/bin/ld

sudo ln -s /opt/arm-gnu-toolchain-12.3.rel1-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu-gcc /opt/arm-gnu-toolchain-12.3.rel1-x86_64-aarch64-none-linux-gnu/bin/gcc

sudo ln -s /opt/arm-gnu-toolchain-12.3.rel1-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu-ar /opt/arm-gnu-toolchain-12.3.rel1-x86_64-aarch64-none-linux-gnu/bin/gcc-ar

sudo ln -s /opt/arm-gnu-toolchain-12.3.rel1-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu-objdump /opt/arm-gnu-toolchain-12.3.rel1-x86_64-aarch64-none-linux-gnu/bin/objdump

sudo ln -s /opt/arm-gnu-toolchain-12.3.rel1-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu-objcopy /opt/arm-gnu-toolchain-12.3.rel1-x86_64-aarch64-none-linux-gnu/bin/objcopy

sudo ln -s /opt/arm-gnu-toolchain-12.3.rel1-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu-readelf /opt/arm-gnu-toolchain-12.3.rel1-x86_64-aarch64-none-linux-gnu/bin/readelf

sudo ln -s /opt/arm-gnu-toolchain-12.3.rel1-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu-cpp /opt/arm-gnu-toolchain-12.3.rel1-x86_64-aarch64-none-linux-gnu/bin/cpp

```

for compiler in aarch64-none-linux-gnu-*; do
    sudo ln -s aarch64-none-linux-gnu-* compiler#aarch64-none-linux-gnu-
done

for compiler in aarch64-none-linux-gnu-*; do
    link_name="${compiler#aarch64-none-linux-gnu-}"  # Hapus prefix
    sudo ln -s "$compiler" "$link_name"  # Buat symbolic link
done
