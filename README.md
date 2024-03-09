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

在[此处](https://github.com/openwrt/openwrt/releases/tag/v23.05.2)下载23.05.2版本源码并解压

将本仓库提供的patch复制到源码根目录，打补丁：

```
# 为TN3399_V3提供基础支持
patch -p1 < openwrt-23.05.2-add-tn3399-v3-basic.patch
# 为TN3399_V3提供ALC5640扬声器支持，可选
patch -p1 < openwrt-23.05.2-add-tn3399-v3-speaker.patch
# 为TN3399_V3提供HDMI和1024x600单8 LVDS屏幕支持支持，可选
patch -p1 < openwrt-23.05.2-add-tn3399-v3-display.patch
```
添加一些常用的软件包源，可选：

```
# https://github.com/kenzok8/openwrt-packages
sed -i '$a src-git kenzo https://github.com/kenzok8/openwrt-packages' feeds.conf.default
sed -i '$a src-git small https://github.com/kenzok8/small' feeds.conf.default
```

最后将配置文件`openwrt-23.05.2-tn3399-v3-config`复制到源码根目录

## 编译

```
# 更新软件源
./scripts/feeds update -a
# 安装软件源
./scripts/feeds install -a
# 进入编译配置界面
make menuconfig
```
进入配置界面后，在下面通过`<Load>`加载配置文件`openwrt-23.05.2-tn3399-v3-config`，然后通过`<Save>`保存到`.config`

```
# 根据配置提前下载需要的软件源码，如果跳过这步直接进行下一步，会变成一边下载一边编译
make download V=s -j32
# 编译
make V=s -j32
```
