# 为TN3399_V3构建OpenWRT镜像

## 准备环境

准备Ubuntu或Debian PC一台，笔者使用Ubuntu 20.04 PC

安装工具和依赖：

```
sudo apt install -y ack antlr3 asciidoc autoconf automake autopoint binutils bison build-essential \
bzip2 ccache cmake cpio curl device-tree-compiler fastjar flex gawk gettext gcc-multilib g++-multilib \
git gperf haveged help2man intltool libc6-dev-i386 libelf-dev libglib2.0-dev libgmp3-dev libltdl-dev \
libmpc-dev libmpfr-dev libncurses5-dev libncursesw5-dev libreadline-dev libssl-dev libtool lrzsz \
mkisofs msmtp nano ninja-build p7zip p7zip-full patch pkgconf python2.7 python3 python3-pyelftools \
libpython3-dev qemu-utils rsync scons squashfs-tools subversion swig texinfo uglifyjs upx-ucl unzip \
vim wget xmlto xxd zlib1g-dev
```

## 准备源码

在[此处](https://github.com/openwrt/openwrt/releases/tag/v23.05.0-rc2)下载23.05.0-rc2版本源码并解压

将本仓库提供的补丁复制到源码根目录，开始打补丁：

```
# 提供TN3399_V3基础支持
patch -p1 < openwrt-23.05.0-rc2-add-tn3399_v3-baisc.patch
# 为TN3399_V3提供ALC5640支持，可选
patch -p1 < openwrt-23.05.0-rc2-add-tn3399_v3-speaker.patch
```

## 编译

将`openwrt-23.05.0-rc2-tn3399_v3.config`复制到源码根目录，并重命名为.config。该config使能了AP6255驱动、AP6255固件、ALC5640驱动的选项，如果不使用该config，记得自行使能相关选项

```
# 添加一些常用的软件包源，可选
# https://github.com/kenzok8/openwrt-packages
sed -i '$a src-git kenzo https://github.com/kenzok8/openwrt-packages' feeds.conf.default
sed -i '$a src-git small https://github.com/kenzok8/small' feeds.conf.default
# 更新软件源
./scripts/feeds update -a
# 安装软件源
./scripts/feeds install -a
# 进入编译配置界面
make menuconfig
# 根据配置提前下载需要的软件源码，如果跳过这步直接进行下一步，会变成一边下载一边编译
make download V=s -j32
# 编译
make V=s -j32
```