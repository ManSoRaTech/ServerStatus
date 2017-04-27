# ServerStatus 中文版

* ServerStatus 中文版是一个酷炫高逼格的云探针, 云监控, 服务器云监控, 多服务器探针~
* 该云监控(云探针)是 ServerStatus ( https://github.com/BotoX/ServerStatus )项目的中文(优化)版.
* 在线演示: https://status.ime.moe

# 目录介绍

* clients  客户端文件
* other   客户端安装脚本 以及启动脚本
* server   服务端文件
* web      网站文件

# 更新说明

* 20170427, 补上原项目的 客户端安装脚本
* 20170407, 91yun 将 psutil 脚本修改为使用 vnstat 获取月流量
* 20170108, 更新支持所有系统
* 20161205, 去掉无用的 IPv6 信息, 增加服务器总流量监控 
* 20161203, 汉化并简化安装教程 

# 安装教程

【克隆代码】
```
git clone https://github.com/ManSoRaTech/ServerStatus.git
```

【服务端配置】

一、编译服务端主程序

```
cd ServerStatus/server
make
./sergate
```
如果没错误提示, Ctrl+C 关闭; 如果有错误提示, 检查 35601 端口是否被占用

命令相关参数

```
Usage: ./sergate [options]

    -h, --help            Show this help message and exit
    -v, --verbose         Verbose output
    -c, --config=<str>    Config file to use
    -d, --web-dir=<str>   Location of the web directory
    -b, --bind=<str>      Bind to address
    -p, --port=<int>      Listen on port
```

二、修改配置文件

修改 config.json 文件, 注意 username, password 的值需要和客户端对应一致

```
{"servers":
	[
		{
			"username": "s01",
			"name": "Mainserver 1",
			"type": "Dedicated Server",
			"host": "GenericServerHost123",
			"location": "Austria",
			"password": "some-hard-to-guess-copy-paste-password"
		},
	]
}       
```

三、拷贝 Web 相关文件到你的网站目录

例如

```
cp -r ServerStatus/web/* /home/wwwroot/status/
```

四、服务端启动脚本

以 Systemd 为例 ( init.d 参考 sergate.initd 文件 )：

```
cp ServerStatus/other/sergate.service /etc/systemd/system/sergate.service 
vim /etc/systemd/system/sergate.service
systemctl daemon-reload
systemctl enable sergate
systemctl start sergate
```
注: -d 参数为 网站目录 酌情修改

【客户端配置】

脚本默认使用 python-psutil 版 可根据 vnstat 来显示月流量 而不是显示网卡流量.

安装依赖

```
#CentOS
yum install -y python-psutil
#Ubuntu
apt install -y python-psutil

rm -rf /var/lib/vnstat
mkdir /var/lib/vnstat
wget http://humdi.net/vnstat/vnstat-1.15.tar.gz
tar zxf vnstat-1.15.tar.gz
rm -rf vnstat-1.15.tar.gz
cd vnstat-1.15
./configure && make && make install
vnstat -u -i eth0
cd ..
rm -rf vnstat-1.15
```

添加 Cron

```
crontab -e

* * * * * /usr/local/bin/vnstat -u >/dev/null 2>&1
```

安装客户端


```
wget https://raw.githubusercontent.com/ManSoRaTech/ServerStatus/master/other/client-setup.sh
bash client-setup.sh
```

以下是安装过程

```
ServerStatus Client Setup Script

Which client implementation do you want to use? [python-psutil, python]
~> #回车即默认 python-psutil
What is your status servers address (DNS or IP)?
~> #服务端的域名或 IP
What is your status servers port? [35601,...]
~> #服务端端口 一般默认即可
Specify the username.
~> #用户名 注意 必须和服务端配置文件内对应
Specify a password for the user.
~> #密码 如上

Summarized settings:

Client implementation:	python-psutil
Status server address:	test
Status server port:	35601
Username:		test
Password:		test

Is this correct? [yes/no]
~> #确认

Magic going on...
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  4501  100  4501    0     0   2266      0  0:00:01  0:00:01 --:--:--  2266

Python-psutil client copied to /home/love4taylor/ServerStatus/other/serverstatus-client-psutil.py
Do you want to autostart the script with your system? This requires sudo. [yes/no]
~> #确认
Trying to detect the init system...
Systemd has been detected, is this correct? [yes/no]
~> #此处是使用 Systemd 确认
Installing script to /usr/local/share/serverstatus-client-psutil.py
Under which user should the script be run? [root, ...]
~> #运行用户 默认 root
Installing systemd service to /etc/systemd/system/serverstatus.service
Starting service...

● serverstatus.service - ServerStatus Client
   Loaded: loaded (/etc/systemd/system/serverstatus.service; disabled; vendor preset: enabled)
   Active: active (running) since 四 2017-04-27 19:15:43 CST; 1s ago
 Main PID: 31975 (python)
    Tasks: 1 (limit: 4915)
   CGroup: /system.slice/serverstatus.service
           └─31975 /usr/bin/python /usr/local/share/serverstatus-client-psutil.py

Should be started. Adding service to autostart...

Created symlink /etc/systemd/system/multi-user.target.wants/serverstatus.service → /etc/systemd/system/serverstatus.service.

Done.

In case you haven't already added the new client to the master server:

		{
			"name": "Change me",
			"type": "Change me",
			"host": "Change me",
			"location": "Change me",
			"username": "test",
			"password": "test",
		},

Have fun.
#安装完毕
```

# 为什么会有 ServerStatus 中文版

* 有些功能确实没用
* 原版本部署, 英文说明复杂
* 不符合中文版的习惯
* 没有一次又一次的轮子, 哪来如此优秀的云探针

# 相关开源项目, 感谢 

* ServerStatus: https://github.com/BotoX/ServerStatus
* mojeda: https://github.com/mojeda 
* mojeda's ServerStatus: https://github.com/mojeda/ServerStatus
* BlueVM's project: http://www.lowendtalk.com/discussion/comment/169690#Comment_169690
