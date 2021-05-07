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

257-3.png

 

二、安装Transmission

1、在“套件中心”的“社群”找到“Transmission”，点击“安装套件”，会自动下载安装

257-4.png

2、Transmission下载设置

Download diirectory：下载文件存储目录（设置前需要事先建立好文件夹）

Watch directory：保存种子存储目录，留空表示禁用（可以填写下载文件存储目录）

Incomplete directory：未完成下载目录，留空表示禁用（建议不填写，留空）

257-5.png

3、设置用户账号和密码（每次进入TR需要输入的用户名和密码）

257-6.png

4、权限提示，直接“下一步”

257-7.png

5、安装完成后后，打开“套件中心” ---> “Transmission”，找到URL，可以直接打开Web UI（如果你用内网地址安装，则显示内网地址）

257-8.png

6、文件夹权限设置（这个步骤非常重要，否则你的TR下载不了）

打开“控制面板” ---> “共享文件夹” ---> “downloads”（刚才安装Transmission创建的下载文件夹名称），点击“编辑”，选择“本地群组”，选择“sc-download”设置为可读写

257-9.png

打开“File station”，“downloads”文件夹右击，选择“属性”，新增“Everyone”账号，权限选择“读取”和“写入”，勾上“应用到这个文件夹、子文件夹及文件”，确定

257-10.png

257-11.png

