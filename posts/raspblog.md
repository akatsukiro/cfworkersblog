## 前言
~~闲来无事~~在百忙的学业中抽空搞得，所以有些问题并没有时间去搞清楚，只好**大力出奇迹**了。
## 准备
### 我的硬件

　　- Raspberry Pi 4 model B ~~废话~~
　　- SanDisk Ultra microSDcard 32GB
　　- 一台12年的上网本，系统为Win10 1909，SSH用
　　- 读卡器
　　- 一套散热
　　- 网线等

### 我的软件

  - [SD Card Formatter](https://www.sdcard.org/downloads/formatter/)

  - [FileZilla](https://filezilla-project.org/)

  - [PuTTY](https://www.putty.org/)

  - [7Zip](https://www.7-zip.org)

  - [Edge](https://www.microsoft.com/en-us/edge)

    > 其他软件只要功能相仿也可

## 准备系统
- 下载CentOS 8 arm64镜像
在此我使用的是在[树莓派俱乐部](https://raspberrypi.club/366.html)找到的树莓派修改版~~图个心里安稳~~，咱也不知道他修改了啥，但知道的确能用。
- 准备microSD卡
使用SDf格式化SD卡
- 烧录镜像
Win32DiskImager烧录解压出的镜像到SD卡上，用时约3分钟。
- 启动
插上树莓派，插电，开机。

### 配置系统

- 修改密码

      - 在网关处找到树莓派的IP地址。
    
      - 在浏览器导航栏输入
    
        `https://raspberrypiip:9090`
    
        > 注意将raspberrypiIP改成查询到的IP
    
      - 进入Web Console管理界面。此系统默认账户`root`，密码`raspberrypi.club`
    
      - 修改root用户密码

    　　- 点击`账户`，点击root用户，在点击`设置密码`输入密码确定即可。

　　- 

- 修改密码
　　- 在网关处找到树莓派的IP地址。
　　- 在浏览器导航栏输入
　　```
　　https://raspberrypiip:9090
　　```
```

- SSH连接
     　　- 打开PUTTY，输入树莓派IP，连接。
     　　账户`root`，密码为你刚刚修改的。

## 配置LNMP

### 安装Nginx

```
yum install nginx -y
```



- 启动

```
    systemctl start nginx
    ```

- 开机自启

    ```
    systemctl enable nginx
    ```

- 访问树莓派IP，出现Nginx欢迎页，安装成功。

- 安装MariaDB

    ```
    yum install mariadb-server -y
    ```

    - 启动

        ```
        systemctl start mariadb
        ```

        

    - 开机自启

        ```
        systemctl enable mariadb
        ```

### 配置MariaDB

> MySQL命令结束符为分号，故以下命令均带有分号。

- 改密码

    ```
    mysqladmin -uroot password password
    ```

    `password`换成你想要的密码

- 登录

    ```
    mysql -uroot -ppassword
    ```

- 创建用户

    ```mysql
    create usr 'usr'@'localhost' identified by 'password';
    ```

    此用户`usr`只能在本地`localhost`使用密码`password`登录。

- 创建数据库

    ```mysql
    create database data;
    ```

    创建了名为`data`的数据库。

- 用户授权

    ```mysql
    grant all on data.* to 'usr'@'localhost';
    ```

    用户`usr`将拥有`data`数据库中所有表的所有权限，并仅能在`localhost`登录。

- 刷新

    ```mysql
    flush privileges;
    ```

- 退出

    ```mysql
    quit;
    ```

    > 以上创建的数据库等将在安装typecho时使用，请根据自身情况修改填写

### 安装PHP

```
yum install php -y
```

- 发现`httpd`安装失败，不用管。

    - 如果``httpd`也安装了，删除

        ```
        yum remove httpd*
        ```

    - 这会将`php-fpm`一同删除，再装回来

        ```yum
        yum install php-fpm -y
        ```

### 配置Nginx

- 修改`nginx.conf`文件

    ```
    vi /etc/nginx/nginx.conf
    ```

- 伪静态

    ```nginx
        server {
            listen       80 default_server;
            listen       [::]:80 default_server;
            server_name  yourdomain.top;
            root         /usr/share/nginx/html;
    
    		if (!-e $request_filename) {
                rewrite ^(.*)$ /index.php$1 last;
            }
    
            location ~ .*\.php(\/.*)*$ {
                include fastcgi.conf;
                fastcgi_pass  127.0.0.1:9000;
            }
    
            access_log /var/log/nginx/yourdomain.log combined;
    	}
    ```

    使`server`段看起来和这个差不多。

- 重启一下

    ```nginx
    nginx -s reload
    ```

- 如果出错百度一下，`reboot`也行

- 新建`index.php`

    ```index
    echo “<?php phpinfo(); ?>” > /usr/share/nginx/html/index.php
    ```

- 在浏览器打开树莓派IP，应能看到phpinfo页面

## 安装Typecho

- 去[官网](https://typecho.org/)下载typecho

- 用7Zip解压下载的文件，直至解压出

    ```file
    admin
    install
    usr
    var
    index.php
    install.php
    LICENSE.txt
    ```

- 打开FileZilla，填写连接信息

    ```FZ
    主机：sftp://raspberrypiIP
    用户名：root
    密码：password
    端口：22
    ```

    传输刚刚解压出的文件到`/usr/share/nginx/html`

- 浏览器打开树莓派IP，见到typecho安装页面。

### 安装typecho

- 点击`我准备好了`开始安装

- 发现数据库一行空白

    查找得知php7 yum安装时没有安装相应的MySQL 驱动，查找驱动

    ```
    yum search php | grep mysql
    ```

- 发现`php-mysqlnd.aarch64`可安装

- 安装

    ```
    yum install php-mysqlnd.aarch64 -y
    ```

- 填写相关信息

    注意以下空格填写

    ```
    数据库用户名：usr
    数据库密码：password
    数据库名：data
    ```

- 如果创建失败了，应该会让你在网站目录新建一个`config.inc.php`的文件。

    ```php
    <?php
    /**
     * Typecho Blog Platform
     *
     * @copyright  Copyright (c) 2008 Typecho team (http://www.typecho.org)
     * @license    GNU General Public License 2.0
     * @version    $Id$
     */
    
    /** 定义根目录 */
    define('__TYPECHO_ROOT_DIR__', dirname(__FILE__));
    
    /** 定义插件目录(相对路径) */
    define('__TYPECHO_PLUGIN_DIR__', '/usr/plugins');
    
    /** 定义模板目录(相对路径) */
    define('__TYPECHO_THEME_DIR__', '/usr/themes');
    
    /** 后台路径(相对路径) */
    define('__TYPECHO_ADMIN_DIR__', '/admin/');
    
    /** 设置包含路径 */
    @set_include_path(get_include_path() . PATH_SEPARATOR .
    __TYPECHO_ROOT_DIR__ . '/var' . PATH_SEPARATOR .
    __TYPECHO_ROOT_DIR__ . __TYPECHO_PLUGIN_DIR__);
    
    /** 载入API支持 */
    require_once 'Typecho/Common.php';
    
    /** 载入Response支持 */
    require_once 'Typecho/Response.php';
    
    /** 载入配置支持 */
    require_once 'Typecho/Config.php';
    
    /** 载入异常支持 */
    require_once 'Typecho/Exception.php';
    
    /** 载入插件支持 */
    require_once 'Typecho/Plugin.php';
    
    /** 载入国际化支持 */
    require_once 'Typecho/I18n.php';
    
    /** 载入数据库支持 */
    require_once 'Typecho/Db.php';
    
    /** 载入路由器支持 */
    require_once 'Typecho/Router.php';
    
    /** 程序初始化 */
    Typecho_Common::init();
    
    /** 定义数据库参数 */
    $db = new Typecho_Db('Pdo_Mysql', 'typecho_');
    $db->addServer(array (
      'host' => 'localhost',
      'user' => 'usr',
      'password' => 'password',
      'charset' => 'utf8',
      'port' => '3306',
      'database' => 'data',
    ), Typecho_Db::READ | Typecho_Db::WRITE);
    Typecho_Db::set($db);
    ```

    文件类似于以上，新建即可。

- 跟提示走，创建成功。

## 申请域名

- 我在`腾讯云`申请的域名。
- 过程
    - 查询域名
    - 购买
    - 提交资料，包括身份证
    - 审核
- 打开域名解析页，待用。

## 内网穿透

- 原因

    我的年龄没到我们省的备案标准（18周岁及以上），故只能使用内网穿透。

### 内网穿透

我选择的是`Sakura Frp`，免费，还挺好用。

#### 创建内网穿透隧道

- 进入[官网](https://www.natfrp.com/)

    注册账号就好。

- 每日签到

- 创建隧道

    - 国内服务器不能允许未备案的http流量，故选择国外的服务器。

    - 根据提示填写隧道相关信息，并创建

        > 本地地址应填127.0.0.1，毕竟我们是在树莓派上开启服务，树莓派内网地址也可，但若内网地址变动就要重新修改，麻烦。

#### 树莓派服务端配置

- 点击左侧导航栏的`软件下载`

- 找到`Linux arm64`的版本

- 下载（法一）

    - 直接下载

    - 用FZ上传到`/home`

    - 在终端中输入

        ```
        cd /home
        ./frpc_linux_arm64
        ```

    - 提示`permission denied`的话

        ```
        chmod 777 frpc_linux_arm64
        ```

    - 再次运行就没问题了

- 打开终端（法二）

    - 输入

        ```
        wget https://qianqu.me/frp/frpc_linux_amd64
        ./frpc_linux_arm64
        ```

        - 找不到命令就安装`wget`

        ```
        yum install wget -y
        ```

        - 无权报错同上处理

- 输入访问密钥，选择你创建隧道时的服务器，开启成功!

    > 建议开启内网穿透服务时使用Web Console上的终端，因为我无法安装screen，SSH链接一关服务就停止了；而Web Console终端关闭后仍在工作。

- 然后根据提示，去域名解析页修改CNAME解析

## 开启SSL

- 先搞个SSL证书

    腾讯云管理界面可以直接申请TA的，挺快的。

- 配置Nginx

    - 打开`nginx.conf`

        ```
        vi /etc/nginx/nginx.conf
        ```

    - 配置如下

        ```nginx
            server {
                listen       443 ssl http2 default_server;
                listen       [::]:443 ssl http2 default_server;
                server_name  www.yourdomain.top;
                root         /usr/share/nginx/html;
        
                ssl_certificate 1_yourdomain.top_bundle.crt;
                ssl_certificate_key 2_yourdomain.top.key;
                ssl_session_cache shared:SSL:1m;
                ssl_session_timeout  5m;
                ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
                ssl_prefer_server_ciphers on;
                ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        
                # Load configuration files for the default server block.
                include /etc/nginx/default.d/*.conf;
        
                location / {
                root  /usr/share/nginx/html;
                }
        
                error_page 404 /404.html;
                    location = /40x.html {
                }
        
                error_page 500 502 503 504 /50x.html;
                    location = /50x.html {
                }
        
                if (!-e $request_filename) {
                    rewrite ^(.*)$ /index.php$1 last;
                }
        
                location ~ .*\.php(\/.*)*$ {
                    include fastcgi.conf;
                    fastcgi_pass  127.0.0.1:9000;
                }
        
                access_log /var/log/nginx/www.yourdomain.top.log combined;
        
            }
        
        ```

    - debug

        ```
        nginx -t
        ```

        报错自行修改，理论上不会报错，因为我帮你把坑踩了。

    - 重启

        ```
        nginx -s reload
        ```

        

- 访问

    ```
    https://raspberryip
    ```

    无视浏览器警告，继续访问，发现正常进入

- 修改

    - HOST

        ```
        raspberryip yourdomain.com
        raspberryip www.yourdomain.com
        ```

        修改成以上，防止公网上不去。

    - typecho

        - 进入后台

        - 设置-基本

            将`站点地址`更改为

            ```
            https://www.yourdomain.com
            ```

            

    - 内网穿透

        - 创建新隧道

            记得选择`https`隧道，`443`端口哦~

        - 停止树莓派的内网穿透

            由于我不知道开启后怎样停止，于是在终端输入

            ```
            roboot
            ```

        - 删除配置文件

            ```
            cd /home
            rf -f frpc_user.txt
            ```

            我内网穿透跑在`/home`，所以需要进入该文件夹。

        - 开启服务

            ```
            ./frpc_linux_arm64
            ```

        - 选择即可

    - HOST删除相关地址

    - 访问测试

        肯定成功

- 完成

## 拔掉风扇！

- 进入树莓派管理界面

    - 于主页找到`电源选项`,选择`powersave`

- 拔掉树莓派的散热风扇

    我已经受够了你的啸叫啦！！！

## 后记

这篇文章断断续续写了好久，每做一点更新一点，甚至你现在还可以看到有些还没有做。但可惜我们即将期末考试，实在挤不出时间来继续更新了，所以只好有时间再说吧。