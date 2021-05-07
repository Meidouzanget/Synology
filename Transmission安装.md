![thum-b7e31533520749](https://user-images.githubusercontent.com/59044398/117425849-c78e3a00-af55-11eb-94e5-a57899e7d1ec.png)
# Synology

一、添加第三方源

1、用管理员admin账号登录DSM桌面，打开“套件中心”，点击“设置” ---> “常规”，选择“任何发行者”；

2、“套件中心”，“设置” ---> “套件来源”，点击“新增”，位置填入第三方源的网址，完成即可完成第三方源的添加

![thum-88251533521006](https://user-images.githubusercontent.com/59044398/117425712-a3325d80-af55-11eb-8b18-5e9ba2131e89.png)


第三方源网址：

http://packages.synocommunity.com            （建议选择这个，支持HTTPS）

http://packages.pcloadletter.co.uk

http://www.cphub.net

http://synology.sysco.ch

http://packages.quadrat4.de

http://synology.acmenet.ru

http://cytec.us/spk

http://spk.naefmarco.ch/spkrepo/packages/

http://spk.nas-mirror.de/spkrepo/packages

http://spk.unzureichende.info/

http://packages.synocommunity.com/?beta=1

3、刷新“套件中心”，找到“社群”。这个就是第三方源

![thum-b7e31533520749](https://user-images.githubusercontent.com/59044398/117425866-ce1cb180-af55-11eb-946b-fb78c686c6f9.png)


 

二、安装Transmission

1、在“套件中心”的“社群”找到“Transmission”，点击“安装套件”，会自动下载安装

![0c1c1533524991](https://user-images.githubusercontent.com/59044398/117427433-9dd61280-af57-11eb-96fd-661f4a2b5172.png)



2、Transmission下载设置

Download diirectory：下载文件存储目录（设置前需要事先建立好文件夹）

Watch directory：保存种子存储目录，留空表示禁用（可以填写下载文件存储目录）

Incomplete directory：未完成下载目录，留空表示禁用（建议不填写，留空）

![51511533524991](https://user-images.githubusercontent.com/59044398/117427210-636c7580-af57-11eb-8c4b-cc376e362a6f.png)


3、设置用户账号和密码（每次进入TR需要输入的用户名和密码）

设置好并记住，否则无法进入网页，要卸载重装

![thum-78d51533532065](https://user-images.githubusercontent.com/59044398/117427404-96af0480-af57-11eb-9e28-702a6cf979fd.png)



4、权限提示，直接“下一步”


5、安装完成后后，打开“套件中心” ---> “Transmission”，找到URL，可以直接打开Web UI（如果你用内网地址安装，则显示内网地址）

旧版安装后会显示桌面图标，点击可进入网址，更新后需要在套件中心查看

![fe2e1533532355](https://user-images.githubusercontent.com/59044398/117427321-826b0780-af57-11eb-91a4-5a587d87d6f7.png)


6、文件夹权限设置（这个步骤非常重要，否则你的TR下载不了）

打开“控制面板” ---> “共享文件夹” ---> “downloads”（刚才安装Transmission创建的下载文件夹名称），点击“编辑”，选择“本地群组”，选择“sc-download”设置为可读写

![thum-91a41533536525](https://user-images.githubusercontent.com/59044398/117427373-8eef6000-af57-11eb-87c8-3c318efc38f5.png)


打开“File station”，“downloads”文件夹右击，选择“属性”，新增“Everyone”账号，权限选择“读取”和“写入”，勾上“应用到这个文件夹、子文件夹及文件”，确定


三、界面汉化

方法一

1、Transmission安装完成后打开界面如下，是英文界面，你可以把它进行汉化

![thum-01aa1533537168](https://user-images.githubusercontent.com/59044398/117431044-56ea1c00-af5b-11eb-814b-4f047bd5609d.png)

2、汉化包项目地址：https://github.com/ronggang/transmission-web-control

3、解压文件参考官网的安装教程 https://github.com/ronggang/transmission-web-control/wiki/Windows-Installation-CN

4、用WinSCP以root登录群晖，进入/volume1/@appstore/transmission/share/transmission/web，如果你的套件安装在盘1则地址是/volume1，以此类推。或者 /usr/local/transmission/share/transmission/web，不同的方法进入相同的文件夹

5、把群晖上的web文件夹改成web-bak

6、把解压出来的web文件复制到群晖

7、电脑浏览器打开http://群晖IP:9091，按下Ctrl+F5，强制刷新浏览器

8、中文界面就出来了

![38598199-0d2e684c-3d8e-11e8-8b21-3cd1f3c7580a](https://user-images.githubusercontent.com/59044398/117431066-5e112a00-af5b-11eb-8756-6588fc080bab.png)



方法二

在套件中心，关闭transmission。
1、到套件中心，停用Transmission
2、开启群晖SSH功能。打开“控制面板”-“应用程序：终端机和SNMP”，勾选“启动SSH功能”。

![20201007-Transmission9](https://user-images.githubusercontent.com/59044398/117431842-45554400-af5c-11eb-95a2-f34a1f78e794.png)


3、下载运行putty软件。打开后，输入群晖的内网IP，端口默认22，选择SSH，点击连接。

![20201007-Transmission10](https://user-images.githubusercontent.com/59044398/117431820-3f5f6300-af5c-11eb-8419-6a5847e79d5c.png)


4、root,输入你群晖root的密码，再次输入代码：sudo -i ，密码，获取root权限

![20201007-Transmission11](https://user-images.githubusercontent.com/59044398/117431887-52723300-af5c-11eb-81ed-21dc71b6d9c9.png)


5、输入以下代码，安装脚本

![20201007-Transmission12](https://user-images.githubusercontent.com/59044398/117431900-556d2380-af5c-11eb-8fc3-d9a8e99ed5b1.png)

代码：wget https://github.com/ronggang/transmission-web-control/raw/master/release/install-tr-control-cn.sh


6、继续执行中文安装脚本，然后选择“1”进行安装最新发布版（如果无法正常显示中文，需设置putty的编码为UTF-8）

代码：sudo bash install-tr-control-cn.sh

![20201007-Transmission13](https://user-images.githubusercontent.com/59044398/117431925-5bfb9b00-af5c-11eb-90ba-a3c1a59050eb.png)


7、等待安装完成

![20201007-Transmission14](https://user-images.githubusercontent.com/59044398/117431946-5f8f2200-af5c-11eb-9d97-5456f5c43e43.png)


到套件中心重新启动transmission

刷新网页，或者按Ctrl+F5强制刷新页面，看到下面的效果，OK!

![20201007-Transmission15 (1)](https://user-images.githubusercontent.com/59044398/117432622-299e6d80-af5d-11eb-9fbc-9a88d0e47650.png)





