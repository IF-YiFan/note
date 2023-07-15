# 编译烧录

  

## 解锁bootloader

  

```shell

avbab  set_device_state  0;setenv  devicestate  unlock;avbab  disable-verity;saveenv;reset

```

  

## 配置调试串口

  

```shell

setprop  persist.xbh.rs232.enable  false

setprop  persist.xbh.app_log.enable  true

setprop  persist.xbh.hidl.loglevel  1

setprop  persist.xbh.app_log.enable  true

setprop  persist.sys.log_debug  true

setprop  persist.sys.bootlog.enable  true

setprop  persist.logd.size.crash  500m

setprop  persist.logd.size.main  500m

setprop  persist.logd.size.system  500m

```

  

# 工厂菜单

  

```shell

dumpsys  window  |  grep  mCurrentFocus

```

  

串口命令

  

```shell

am  start  com.xbh.factory.menu/com.xbh.factory.menu.MainActivity

```

  

adb命令

  

```shell

adb  am  start  com.xbh.factory.menu/com.xbh.factory.menu.MainActivity

```

  

遥控

  

```shell

menu键，1，1，4，7；

source键，左，左，左，左；

上，上，下，下，左，右，Enter；

音量+，下，下，上，上；

```

  

mtk工厂菜单

  

```shell

am  start  mediatek.factorymenu.ui/mediatek.tvsetting.factory.ui.designmenu.DesignMenuActivity

```

  

mtk tv菜单

  

```shell

am  start  -n  com.android.tv.settings/.MainSettings

```

  

# 生成软件

  

CUS配置文件，打开老化。

  

```

DEF AGING VIDEO ENABLE=Y

```

  

## 全编译

  

每次编译代码前，必须执行配置指定客制化脚本：

  

iflytek项目：

  

```shell

export  ENG_NAME=CK;source  Customer/build.sh  Customer/customer/iflytek/KDXF_012_XMM966BD_2048_16G.cus;./Customer/compile.sh  image

```

  

benq2项目：

  

B.E生成软件分支：aosp/factory-benq-m966BE

  

```shell

export  ENG_NAME=CK;source  Customer/build.sh  Customer/customer/benq2/BENQ_101_XMM966BE_4096_32G_RE6503N1.cus;./Customer/compile.sh  image_dual

```

  

sharp项目：

  

```shell

export  ENG_NAME=CK  &&  source  Customer/build.sh  Customer/customer/sharp/SHARP_001_XMM9666C_4096_16G.cus  &&  ./Customer/compile.sh  image

```

  

B.B M3

  

```shell

export  ENG_NAME=CK;source  Customer/build.sh  Customer/customer/boe/BOE_001_XMM966BB_4096_16G.cus;./Customer/compile.sh  image

```

  

A板

  

```shell

export  ENG_NAME=CK;source  Customer/build.sh  Customer/customer/benq/[ENCRYPTED-MESSAGE];./Customer/compile.sh  image

```

  

### 烧录固件

  

将固件out/images/android_11/mt5872/XM_M966B_D.bin拷贝到U盘，然后插入板子。

两种方式升级U盘里面的固件：

方式一：开机按着power键，自动升级U盘里面的固件。

方式二：重启系统进入uboot命令行，串口输入命令：custar

  

## 编译mboot

  

配置环境变量：

  

```shell

export  ENG_NAME=CK

source  ~/.bashrc

source  build/envsetup.sh

lunch  mt5872_an64_edu-userdebug

```

  

**注意：menuconfig不用配置，直接退出即可保存。**

  

编译命令：

  

```shell

Customer/compile.sh  mb

```

  

在out目录下生成如下两个固件：

  

![enter image description here](C:\Users\pc\Desktop\日常手册图片\uboot固件.PNG)

  

### 烧录mboot固件

  

**注意：联发科的USB转串口工具如果识别成两个串口则不能mboot烧录。**

  

#### 断开串口

  

断开mboot串口

  

![](C:\Users\pc\Desktop\日常手册图片\mboot关闭串口.PNG)

  

断开串口调试工具

  

![](C:\Users\pc\Desktop\日常手册图片\断开串口调试工具.PNG)

  

#### ISP tools工具烧录

  

具体可以参考烧空片的文档：D:\调试工具软件\ISP_Tool_exe_v5.1.2\烧空片.pdf。

  

配置基本都已经OK了，需要使能

  

![](C:\Users\pc\Desktop\日常手册图片\使能DBBUS.PNG)

  

执行烧录

  

![](C:\Users\pc\Desktop\日常手册图片\烧录mboot.PNG)

  

断电重启上电。

  

## 编译kernel

  

配置环境变量：

  

```shell

export  ENG_NAME=CK

source  build/envsetup.sh

lunch  mt5872_an64_edu-userdebug

```

  

编译命令：

  

```shell

Customer/compile.sh  ke

```

  

### 烧录kernel

  

进入mboot命令行，解锁bootloader

  

```shell

avbab  set_device_state  0;setenv  devicestate  unlock;avbab  disable-verity;saveenv;reset

```

  

进入Android命令行

  

```shell

su

dd  if=/storage/8AF0-2542/boot.img  of=/dev/block/by-name/boot_a

sync

reboot

```

  

## 编译vendor-misdk

  

编译vendor/mediatek/proprietary_tv/apollo/linux_core/misdk

  

```shell

source  build/envsetup.sh

lunch  mt5872_an64_edu-userdebug

```

  

```shell

cd  vendor/mediatek/proprietary_tv/apollo/linux_core/misdk;

m  mi_all  ALLOW_NINJA_ENV=true

```

  

方式一：adb push

  

生成mik.ko路径：

**out/target/product/mt5872/vendor/lib/modules/mik.ko**

可以通过adb push到/vendor/lib/modules。

方式二：U盘拷贝

进入mboot命令行，解锁bootloader：

  

```shell

avbab  set_device_state  0;setenv  devicestate  unlock;avbab  disable-verity;saveenv;reset

```

  

然后

  

```shell

su

remount

cp  /storage/8AF0-2542/mik.ko  /vendor/lib/modules;reboot

```

  

## 编译eway

  

```shell

source  build/envsetup.sh

lunch  mt5872_an64_edu-userdebug

cd  vendor/xbh/middleware/libeywa

mm  -j24

```

  

```shell

uname  -a

```

  

32位eywa库生成路径：

  

out/target/product/mt5872/vendor/lib/libeywa.so

  

64位eywa库生成路径：

  

out/target/product/mt5872/vendor/lib64/libeywa.so

  

## 编译jar包

  

```shell

source  build/envsetup.sh

lunch  mt5872_an64_edu-userdebug

cd  vendor/xbh/middleware/api

mm  -j24

```

  

jar包生成路径：

  

out\target\common\obj\JAVA_LIBRARIES\XbhApi_intermediates\classes.jar

  

# 修改显示通道panel

  

工厂菜单可以修改单双分屏模式。

  

```shell

su

```

  

```shell

sed  -i  '/m_bPanelDualPort*/c\m_bPanelDualPort=0;'  /vendor/tvconfig/config/panel/UD_VB1_8LANE*.ini;sync;reboot

```

  

进入mboot命令行，保存配置：

  

```shell

dbtable_init  1;saveenv;reset

```

  

以上命令中：

m_bPanelDualPort=0：修改为单分区屏

m_bPanelDualPort=1：修改为双分区屏

  

屏参文件：

屏参文件有很多，不知道具体用哪个，就直接用通配符修改所有UD_VB1_8LANE_*.ini文件。

  

/vendor/tvconfig/config/panel/UD_VB1_8LANE_*.ini:

  

公司的4K屏都是单分屏，客户的整机一般情况下，65/75寸为单分屏；86寸为双分屏。

  

查看VBO输出分屏模式

cat /vendor/tvconfig/config/panel/UD_VB1_8LANE_M28DGJ_L30.ini | grep "m_bPanelDualPort"

  

# 串口调试

  

## mboot调试

  

进入mboot命令行执行：

  

```shell

dbg  DEBUG  //打开

dbg  DISABLE  //关闭

ac  loglevel  7

```

  

## kernel的打印信息：

  

```

su

echo 7 > proc/sys/kernel/printk

```

  

gpio相关文件：

hardware/libhardware/include/hardware/mt_gpio.h //定义了CPU的引脚

Customer/toolchains/board/XMM966BD/BD_MT5872_H1V1_B2_S.h //配置CPU引脚功能，比如配置成gpio口，并输出高和低。

  

# adb调试

  

```shell

adb  connect  192.168.20.22

adb  shell

adb  root

adb  remount

adb  push

adb  reboot

```

  

连接报错：adb.exe: device offline，重启一下服务：

  

```shell

adb  kill-server

adb  start-server

```

  

# vscode配置ssh免密远程登录

  

vscode连接不上，清除ssh本地缓存证书

  

```shell

ssh-keygen  -R  192.168.21.237

```

  

生成秘钥

  

```shell

cd  ~/.ssh/  &&  rm  -f  ./vscodekey*  &&  ssh-keygen  -f  vscodekey  -P  ''  &&  cat  vscodekey.pub  >>  authorized_keys

```

  

然后按照开发手册里面<vscode开发环境配置.pdf>配置。

  

# 源码篇

  

源码路径环境变量

  

Customer/toolchains/shell/cus_build.sh

  

# 驱动篇

  

## 源码分析

  

Customer/toolchains/board/XMM966BD/board.h对GPIO的宏定义引用的是下面的文件：hardware/libhardware/include/hardware/mt_gpio.h

  

Customer/toolchains/board/XMM966BD/BD_MT5872_H1V1_B2_S.h

  

例如：PAD_GPIO10_PM(board.h里面具体定义需要看**mt_gpio.h**)

  

```shell

Customer/toolchains/board/XMM966BD$  grep  -rn  PAD_GPIO10_PM

BD_MT5872_H1V1_B2_S.h:276:#define  PAD_GPIO10_PM  GPIO_OUT_LOW  //  GPIO15  OPS-PWR_EN

board.h:148:#define  XBH_BOARD_GPIO_HDMI_SW_B_RST  PAD_GPIO10_PM  //  GPIO70

```

  

## GPIO控制

  

kernel：

  

```shell

cd  /sys/class/gpio

```

  

## 显示

  

m_wPanelOnTiming1:上电时， panel & data之间的时间间隔。

  

m_wPanelOnTiming2:上电时，data & 背光之间的时间间隔。

  

m_wPanelOnTiming3:上电时，PWM与背光之间的时间间隔。

  

m_wPanelOffTiming1：下电时，背光 & data 之间的时间间隔。

  

m_wPanelOffTiming2：下电时，data & panel之间的时间间隔。

  

m_wPanelOffTiming3：下电时，PWM与背光之间的时间间隔。

  

```shell

busybox  vi  vendor/tvconfig/config/panel/UD_VB1_8LANE_M28DGJ_L30.ini

```

  

进入mboot，重新加载ini文件

  

```shell

dbtable_init  1;saveenv;reset

```

  

客制化脚本cus

  

```

TYPE_4K_UI_MODE参数的意义：

0：全FHD

1：白名单方式，也是FHD，部分apk是4K

2：全4K

```

  

调试命令

  

```shell

dumpsys  SurfaceFlinger

```

  

## IIC

  

```

IIC

输入：

su

echo 7 > /proc/sys/kernel/printk

cli

  

然后读数据：

preph.sif.r iic-port(5/9) 100 iic地址(8位) 寄存器字节数(一般是1，2712是2) 寄存器地址 读字节数

例如

读功放mute： preph.sif.r 9 100 0x62 1 0x02 1

读2712当前Port口： preph.sif.r 9 100 0xB0 2 0xff05 1

  

写数据：

preph.sif.w iic-port(5/9) 100 iic地址(8位) 寄存器字节数(一般是1，2712是2) 寄存器地址 需要写入的值

例如

功放解mute： preph.sif.w 9 100 0x62 1 0x02 0x00

切换2712 Port口： preph.sif.w 9 100 0xB0 2 0xff05 1

```

  
  
  

## HDMI

  

HDMI设备信息

  

```shell

/sys/class/hdmirx/hdmirx0

```

  

## 音频

  

查看所有音频设备：

  

```shell

cat  /proc/asound/cards

cat  /proc/asound/devices

```

  

## 输入设备

  

```shell

cat  /proc/bus/input/devices

```

  

## PM

  

```shell

adb  push  PM.bin  /vendor/tvconfig/config/PM.bin

adb  push  PM.bin  /vendor/tvconfig/config/pm/PM.bin

adb  push  PM.bin  /mnt/vendor/tvservice/glibc/bin/PM.bin

adb  shell  sync

```

  

进入mboot ，上电进入模式选择

  

direct:上电开机模式

  

secondary:上电待机待机模式，进入PM

  

memory：

  

```shell

setenv  factory_poweron_mode  direct

setenv  factory_poweron_mode  secondary

setenv  factory_poweron_mode  memory

  

saveenv;reset

```

  

## 系统属性

  

vendor/build.prop

  

查看源码的tag版本

  

```

vendor/mediatek/proprietary_tv/apollo/linux_mts/build/mak/version/version.txt

```

  

# Gerrit代码管理总结

  

网址：

  

[http://192.168.1.182.8080](http://192.168.1.182.8080)

  

gerrit密码：

  

```

q+zhz4g/bNd46n7ex1BFTxJxni7IfuFl/hcgGaZFNQ

```

  

生成秘钥

  

```shell

ssh-keygen

```

  

将.ssh目录下面的id_rsa.pub内容添加的gerrit秘钥管理里面。

  

```shell

git  config  --global  user.email  ke.chen@lango-tech.cn

git  config  --global  user.name  chenke

```

  

## Xshell git log中文乱码

  

不是Ubuntu git工具的问题，是Xshell使用了Windows的默认编码格式，改Xshell的终端的编码格式。

  

## 同步代码

  

### 保存gerrit配置

  

不用每次同步仓库就输入gerrit密码，有两种方式：

  

方式一：

  

```shell

git  clone  "http://chenke@192.168.1.182:8080/a/modules/gerrit-config"

```

  

方式二：

  

```shell

git  config  --global  credential.helper  store

```

  

### 下载同步代码：

  

```shell

repo  init  -u  http://chenke@192.168.1.182:8080/a/MT9666/manifests

repo  sync

echo  -e  "\n"  |  repo  forall  -v  -p  -c  "git checkout develop-6.0"

echo  -e  "\n"  |  repo  forall  -p  -c  "git pull"

```

  

一路回车确认。

  

### 查看所有git仓库分支：

  

```shell

repo  forall  -p  -c  "git branch -vv"

```

  

### git使用vim作为编辑器

  

```shell

git  config  --global  core.editor  "vim"

```

  

## 代码提交

  

#### 上传代码：

  

```shell

git  push  aosp  HEAD:refs/for/develop-6.0

```

  

#### TAPD添加评论

  

```shell

git  log  --stat  -1 //通过该命令信息添加到TAPD评论中。

```

  

### 提交客制化补丁

  

客制化补丁存放目录：

  

Customer/toolchains/cus_patch/iflytek

  

提交步骤：

  

Customer/toolchains/cus_patch/ReadMe.txt

  

然后上传代码到gerrit。

  

## git常用操作

  

```shell

1、修复补丁里面的行尾空白

git  apply  --whitespace=fix  --reject  ~/0001-6265-driver.patch

2、git  diff忽略行尾^M

git  config  --global  core.whitespace  cr-at-eol

3、切换到指定时间

repo  forall  -c  'commitID=`git log --before "2022-07-14 07:00" -1 --pretty=format:"%H"`; git reset --hard $commitID'

```

  

## 清除所有仓库并同步

  

```shell

echo  -e  "\n"  |  repo  forall  -v  -p  -c  "git checkout ."

echo  -e  "\n"  |  repo  forall  -v  -p  -c  "git clean -xdf"

echo  -e  "\n"  |  repo  forall  -v  -p  -c  "git reset --hard aosp/develop-6.0"

echo  -e  "\n"  |  repo  forall  -v  -p  -c  "git pull"

echo  -e  "\n"  |  repo  forall  -v  -p  -c  "git status"

rm  out  -rf

```

  

# 6265双显调试

  

## 打补丁

  

1、增加编译双显固件的编译指令ENABLE_HWC_DUAL_DISPLAY=true

  

```shell

export  ENG_NAME=CK

source  ~/.bashrc

source  build/envsetup.sh

lunch  mt5872_an64_edu-userdebug

m  -j24  WEEKLY_BUILD_TYPE=PRETEST  ALLOW_NINJA_ENV=true  RLS_CUSTOM_BUILD=true  AB_UPDATE_ENABLE=true  AVB_ENABLE=true  BUILD_DTO=true  ENABLE_HWC_DUAL_DISPLAY=true  mtk_build  #2>&1 |tee m.log

```

  

```shell

export  ENG_NAME=CK;source  Customer/build.sh  Customer/customer/benq2/BENQ_011_XMM966BE_4096_32G_RE6503A_DEBUG.cus;./Customer/compile.sh  image_dual

```

  

2、修改2line 60Hz输出

  

相关文件

  

```shell

vendor/mediatek/proprietary_tv/open/hardware/hwcomposer2/hwc_2_0/ExternalDisplay/HWCExternalDisplayHalMiOsd.cpp

```

  

修改点

  

```

Lango-237 11:12:28 $git diff

diff --git a/proprietary_tv/open/hardware/hwcomposer2/hwc_2_0/ExternalDisplay/HWCExternalDisplayHalMiOsd.cpp b/proprietary_tv/open/hardware/hwcomposer2/hwc_2_0/ExternalDisplay/HWCExternalDisplayHalMiOsd.cpp

index 74235609a..2fed4b116 100755

--- a/proprietary_tv/open/hardware/hwcomposer2/hwc_2_0/ExternalDisplay/HWCExternalDisplayHalMiOsd.cpp

+++ b/proprietary_tv/open/hardware/hwcomposer2/hwc_2_0/ExternalDisplay/HWCExternalDisplayHalMiOsd.cpp

@@ -879,7 +879,7 @@ hwc2_error_t HWCExternalDisplayHalMiOsd::updateDisplayTiming(){

  

case 1920:

default:

- eExtDispDevTiming = E_MI_OSD_DUALPANEL_1920x1080_4LINE;

+ eExtDispDevTiming = E_MI_OSD_DUALPANEL_1920x1080_2LINE;

break;

}

```

  

3、Enable VO分離的方式 /vendor/tvconfig/config/module/Customer_Module.ini

  

相关文件：

  

```shell

vendor/mediatek/proprietary_tv/apollo/linux_core/misdk/mi/mi/platform/mt5872/linux/board_cfg/BD_MT5872-H1V1-B2-S/module/Customer_Module.ini

vendor/mediatek/proprietary_tv/apollo/linux_core/misdk/mi/mi/platform/mt5872/linux/board_cfg/BD_MT5872-H1V1-B2-S-CD/module/Customer_Module.ini

Customer/toolchains/arc/BOE/Customer_Module.ini

下面两个不需修改：

vendor/mediatek/proprietary_tv/apollo/cusdata/mtk/bsp/chip/mt5872/board/BD_MT5872-H1V1-B2-S/module/Customer_Module.ini

vendor/mediatek/proprietary_tv/apollo/cusdata/mtk/bsp/chip/mt5872/board/BD_MT5872-H1V1-B2-S-CD/module/Customer_Module.ini

```

  

修改点：

  

```

Lango-237 11:18:11 $git diff

diff --git a/proprietary_tv/apollo/linux_core/misdk/mi/mi/platform/mt5872/linux/board_cfg/BD_MT5872-H1V1-B2-S-CD/module/Customer_Module.ini b/proprietary_tv/apollo/linux_core/misdk/mi/mi/platform/mt5872/linux/board_cfg/BD_MT5872-H1V1-B2-S-CD/module/Customer_Module.ini

index 7e1f58591..919c055ee 100755

--- a/proprietary_tv/apollo/linux_core/misdk/mi/mi/platform/mt5872/linux/board_cfg/BD_MT5872-H1V1-B2-S-CD/module/Customer_Module.ini

+++ b/proprietary_tv/apollo/linux_core/misdk/mi/mi/platform/mt5872/linux/board_cfg/BD_MT5872-H1V1-B2-S-CD/module/Customer_Module.ini

@@ -12,7 +12,7 @@

# #

########################################################################################

[M_BACKEND]

-F_BACKEND_ENABLE_OSDC = 0;

+F_BACKEND_ENABLE_OSDC = 1;

  

[M_AISR]

# each count is 1ms

```

  

4、Default VO分離的OSD timing是FHD Customer_1.ini

  

修复副屏颜色偏白的问题。

  

相关文件

  

```shell

vendor/mediatek/proprietary_tv/apollo/linux_core/misdk/mi/mi/platform/mt5872/linux/board_cfg/BD_MT5872-H1V1-B2-S/model/Customer_1.ini

vendor/mediatek/proprietary_tv/apollo/linux_core/misdk/mi/mi/platform/mt5872/linux/board_cfg/BD_MT5872-H1V1-B2-S-CD/model/Customer_1.ini

vendor/mediatek/proprietary_tv/apollo/cusdata/mtk/bsp/chip/mt5872/board/BD_MT5872-H1V1-B2-S/model/Customer_1.ini

vendor/mediatek/proprietary_tv/apollo/cusdata/mtk/bsp/chip/mt5872/board/BD_MT5872-H1V1-B2-S-CD/model/Customer_1.ini

```

  

修改点

  

```

--- a/proprietary_tv/apollo/linux_core/misdk/mi/mi/platform/mt5872/linux/board_cfg/BD_MT5872-H1V1-B2-S/model/Customer_1.

ini

+++ b/proprietary_tv/apollo/linux_core/misdk/mi/mi/platform/mt5872/linux/board_cfg/BD_MT5872-H1V1-B2-S/model/Customer_1.

ini

@@ -211,7 +211,7 @@ OC_Mixer_Bypass_En = 0;

OC_Mixer_InvAlpha_En = 1;

OC_Mixer_Hsync_Vfde_Out = 0;

OC_Mixer_Hfde_Vfde_Out = 1;

-OC_Mixer_u16OC_Lpll_type = 45;

+OC_Mixer_u16OC_Lpll_type = 46;

+OC_Mixer_Output_Format = 2;

OC_Mixer_OSDC_Tgen_Type = 2; # 0:manual 1:1366x768 2:1920x1080 3:3840x2160 4:3840x1080 5:1280x720

```

  

```

Customer/toolchains/panel/XMM966BE/001/OsdDefine.ini

  

diff --git a/toolchains/panel/XMM966BE/001/OsdDefine.ini b/toolchains/panel/XMM966BE/001/OsdDefine.ini

index c050a70e..8f0638e9 100755

--- a/toolchains/panel/XMM966BE/001/OsdDefine.ini

+++ b/toolchains/panel/XMM966BE/001/OsdDefine.ini

@@ -30,12 +30,3 @@ FB_WINDOW_FORMAT = 8

FB_IOMMU = 1

FB_DFB_RESERVED = 1

  

-[FBDEV2]

-FB_LAYER_ID = 2

-FB_WINDOW_WIDTH = 3840

-FB_WINDOW_HEIGHT = 2160

-FB_WINDOW_FORMAT = 19

-FB_MMAP_ID = 396

-FB_IOMMU = 0

-FB_DFB_RESERVED = 0

```

  

6、配置双显输出

  

```

vendor/mediatek/common-tv/Graphic.mk

  

@@ -105,7 +103,7 @@ $(error "Dual Display isn't supported in PROJECTOR model, please check your devi

endif

$(warning "ENABLE_HWC_DUAL_DISPLAY enabled.")

PRODUCT_PROPERTY_OVERRIDES += \

- vendor.mstar.dualdisp.mode=1 \

+ vendor.mstar.dualdisp.mode=2 \

vendor.mstar.dualdisp.timing=1920x1080 \

vendor.mstar.dualdisp.user.xdpi=77 \

vendor.mstar.dualdisp.user.ydpi=77

```

  

mboot关闭设置gamma的代码

  

```

diff --git a/bootloader/mboot-mtk/mboot/MstarCore/src/drivers/gop/MsDrvGop.c b/bootloader/mboot-mtk/mboot/MstarCore/src/drivers/gop/MsDrvGop.c

index 4381d07b..361b5a2b 100644

--- a/bootloader/mboot-mtk/mboot/MstarCore/src/drivers/gop/MsDrvGop.c

+++ b/bootloader/mboot-mtk/mboot/MstarCore/src/drivers/gop/MsDrvGop.c

@@ -482,7 +482,7 @@ void MsDrvGOP_Show(MS_U8 u8logoGopIdx, GFX_BufferInfo *dst_info,MS_BOOL bHorStre

EN_GOP_CONSALPHA_BITS enGOPConsAlphaBit;

  

//XBH patch begin - OSD Gamma

- EN_GOP_VOP_PATH_MODE enGOPVopPathMode = E_GOP_VOPPATH_BEF_PQGAMMA;

+// EN_GOP_VOP_PATH_MODE enGOPVopPathMode = E_GOP_VOPPATH_BEF_PQGAMMA;

//XBH patch end

  

MApi_GOP_GWIN_SetForceWrite(TRUE);

@@ -550,7 +550,7 @@ void MsDrvGOP_Show(MS_U8 u8logoGopIdx, GFX_BufferInfo *dst_info,MS_BOOL bHorStre

MApi_GOP_GWIN_SetBlending(u8OsdLayerGwinId, FALSE, 0xFF);

  

//XBH patch begin - set OSD before gamma

- MApi_GOP_SetConfigEx(u8logoGopIdx, E_GOP_VOP_PATH_SEL, &enGOPVopPathMode);

+// MApi_GOP_SetConfigEx(u8logoGopIdx, E_GOP_VOP_PATH_SEL, &enGOPVopPathMode);

//XBH patch end

  

MApi_GOP_GWIN_SetForceWrite(FALSE);

```

  

## 打pattern

  

主屏pattern

  

```shell

echo  pattern=6  enable=1  window=0  B=0x3ff  G=Ox3ff  R=Ox3ff  >  /proc/utopia_mdb/xc

```

  

```

1032_01 bit-13 是副屏 pattemn 开关，02，03，04 是R，G，B颜色

```

  

设置双显系统属性

  

```shell

setprop  vendor.mstar.dualdisp.mode  2

setprop  vendor.mstar.dualdisp.timing  1280x720

setprop  vendor.mstar.dualdisp.timing  1920x1080

setprop  vendor.mstar.dualdisp.timing  3840x1080

setprop  vendor.mstar.dualdisp.timing  3840x2160

  

getprop  vendor.mstar.dualdisp.timing

  

setprop  persist.benq.apps.ui  67

```

  

OSD控制

  

```shell

确定一下白板是OSD1还是OSD2

关OSD1

echo  GOPOn  num=1,0  >/proc/utopia_mdb/gop

关OSD2

echo  GOPOn  num=2,0  >/proc/utopia_mdb/gop

  

开OSD1

echo  GOPOn  num=1,1  >/proc/utopia_mdb/gop

```

  

eywa需要实现接口

  

（1）接口

  

1. 增加HDMIout开关，开启时，HDMIout有画面输出，关闭时，HDMIout无画面输出

  

2. 增加HDMIout声音开关，开启时，HDMIout有声音输出，关闭时，HDMIout无声音输出

  

3. 增加HDMIout分辨率切换，设定分辨率时，HDMIout输出指定分辨率画面。跟小玲确认客户需要那几个分辨率

  

以上接口和功能在XbhFramework.cpp的XbhFramework::setHdmiTxInfo(XBH_U32 u32InfoID, XBH_U32 u32Data) 。注意：与68411做兼容，可用宏XBH_BOARD_HAS_ITE6265来兼容。

  

```shell

it6265_set_video_format_by_vic

it6265_hdcp_enable

  

mute  spdif

_AudMiChannelMute(E_MI_AOUT_PATH_SPDIF,  TRUE);

```

  

6265切换分辨率

  

```shell

  

```
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE1NzE2NTk5XX0=
-->