## 折腾树莓派的经过

### 首先你得买个树莓派~~废话~~

网上货比三家后买齐了2G裸板，散热片，风扇，壳子，miniHDMI转HDMI线~~不过没有用到罢了~~，闪迪的**Ultra**！SDcard和一个简单的读卡器。一共花费372元RMB。没买显示器，觉得不需要。

### 开箱

- 本体

    ![benti.jpg](https://i.loli.net/2020/06/07/mXJChnUNwLpADas.jpg)

- 全家福

    ![quanjiafu.jpg](https://i.loli.net/2020/06/07/CjQA1eSlHcpkXUn.jpg)

- 运行中！

    ![working.jpg](https://i.loli.net/2020/06/07/IdYrUg4ofXtJZe1.jpg)

### 开机前准备

- 组装

    装好后放进去，OK。

- 掏出你的笔记本电脑。

    在此我用的是一台2012年的上网本。用来SSH链接。

- 格式化SD卡

    我使用的是[SDFormatter](https://www.sdcard.org/chs/downloads/index.html)，快速格式化，过。

- 烧录镜像

    使用[Win32DiskImager](https://win32diskimager.download/)烧录。镜像是[官网](https://www.raspberrypi.org)的[Raspberry Pi OS (32-bit) with desktop](https://downloads.raspberrypi.org/raspios_armhf_latest).

- 提前配置

    - 在`boot`下新建名为`SSH`的无后缀文件，这样树莓派开机就默认开启了SSH。

    - 如果你要WiFi链接的话，就在`boot`下新建一个文件，名为`wpa_supplicant.conf`，打开，在其中输入

        ```
        country=CN
        ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
        update_config=1
        
        network={
        ssid="叫啥"
        psk="密码"
        key_mgmt=WPA-PSK
        priority=1
        }
        ```

        其他情况见[树莓派实验室-无屏幕和键盘配置树莓派WiFi和SSH](https://shumeipai.nxez.com/2017/09/13/raspberry-pi-network-configuration-before-boot.html)

        反正我是有线网络连接，哈哈哈嗝

- 装上树莓派

    插电，开机，3A大电流，不慌。

- 在笔记本上安装[PuTTY](https://www.putty.org/)，去网关看看树莓派的IP，在PuTTY中连接它。

### 配置系统

- 切换国内源

    - aptget：输入`sudo nano /etc/apt/sources.list`，注释（#）掉原有源，改用清华源

    ```
    deb https://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ buster main contrib non-free rpi
    deb-src https://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ buster main contrib non-free rpi
    ```

    - pip：先升级pip

        ```
        pip install -i https://pypi.tuna.tsinghua.edu.cn/simple pip -U
        ```

        然后配置

        ```
        pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
        ```

        

- 修改密码，语言，时区等。

    输入`sudo raspi-config`，回车。

    - 第一个`Change User Password`修改密码，会英文大概都知道。

    - 选择第四个`Localisation Options`，回车。

        - 第一个`Change Locale`换语言。方向键切换，空格选中或取消选中。这里取消选中`en_GB.UTF-8`，就是默认的那个~~我记不清了~~，然后勾选

        ```
        en.US.UTF-8
        zh.CN GB2312
        zh.CN.GB18030
        zh.CN.GBK
        zh.CN.UTF-8
        ```

        回车，选`zh.CN.UTF-8`为默认。

        - 第二个`Change Timezone`切换时区。选择`Asia`中的`Shanghai`，回车。

    - 第七个`Advanced Options`，选择`Expand Filesystem`，拓展分区至整个SD卡。

- 安装中文字体

    ```
    sudo apt-get install front-wqy-zenhei
    ```

    然后重启`sudo reboot`

- 安装中文输入法（如果需要的话）

    ```
    sudo apt-get install fcitx-googlepinyin 
    ```

- 安装VNC来操作图形界面

    ```
    sudo apt-get tightvncserver
    ```

    启动就是

    ```
    tightvncserver
    ```

    输入新密码即可。



至此初步配置完成。


参考资料：

1. [一劳永逸配置树莓派](https://zhuanlan.zhihu.com/p/63275945)

2. 随机附送的树莓派设置书
