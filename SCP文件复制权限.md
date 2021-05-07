# Synology

### SCP文件复制权限设置方法



在使用winscp的过程中发现一个问题，使用自行设定的管理员账号密码后台登陆群晖后，对于一些权限敏感的文件夹，想从本地复制文件到群晖中时，winscp的操作会被群晖拒绝，如下图所示：

<img width="340" alt="5f7ff2af394d53745 png_e680" src="https://user-images.githubusercontent.com/59044398/117491256-8a03ce00-afa2-11eb-9313-71f71e7c1469.png">


出现这个问题的原因是winscp没有获取到最高权限，无法对一些高权限文件夹进行操作。因此，我们需要想办法使winscp能够通过root权限登陆群晖，以实现类似“sudo -i”命令的root提权功能。

##### 以前DSM6.0的时候可以通过改root密码的方式，来通过winscp来登录nas，这样可以获得最高权限可以任意修改文件。但是在DSM6.2的时候群晖把这个方式封了，幸好现在找到了另外的方式来解封

#### **一、准备工具**

1、putty

2、WinSCP

#### 二、DSM开启SSH

1.打开群晖管理页面，找到控制面板—》应用程序—》终端机和SNMP

<img width="340" alt="5f7ff5526af518296 png_e680" src="https://user-images.githubusercontent.com/59044398/117491284-94be6300-afa2-11eb-9cd5-6eeb200c0e44.png">


然后选中“启动SSH功能”，点击应用

<img width="340" alt="5f7ff558111307691 png_e680" src="https://user-images.githubusercontent.com/59044398/117491297-99831700-afa2-11eb-9a25-7e4d4796805c.png">

2、配置root账号



1、使用putty连接DSM

主机名称填写群晖的ip地址，端口是22，连接类型是SSH，点击“打开”，会报密匙对话框，点击“是”

<img width="340" alt="5f7ff5da14f394006 png_e680" src="https://user-images.githubusercontent.com/59044398/117491307-9daf3480-afa2-11eb-8aa9-1a4d91447242.png">


3.在弹出的命令行窗口中输入自己的群晖管理用户名，按回车后输入自己的管理员密码，继续回车。

4.输入sudo -i，并回车，将操作权限提升到最高的root级。这时候会要求再次输入一遍刚才的密码。


看到root@……：~#这样的信息就是已经进入到root账号了。

设置root账号密码，输入synouser --setpw root password 这里的password最好和admin密码一样，这样不容易搞错。

![3f821543380079](https://user-images.githubusercontent.com/59044398/117493487-9f2e2c00-afa5-11eb-94db-03daf5f88e67.png)

DSM 6.2还需要做以下操作：

1、输入vi /etc/ssh/sshd_config 修改ssh配置文件，按i键进入insert模式，修改#PermitRootLogin prohibit-password 为 PermitRootLogin yes，然后按ESC键输入 :wq （冒号也是输入的一部分） 保存退出

2、输入reboot重启DSM

-------------------------------------------以下是DSM6.2封锁之后的新操作--------------------------------------------------------------------

#### 三、开启ROOT账号和修改密码


进入root权限。

![捕获 4744PNG](https://user-images.githubusercontent.com/59044398/117491620-04345280-afa3-11eb-95a6-9db643981033.PNG)


5.输入CD /etc并回车，进入etc目录。

![捕获](https://user-images.githubusercontent.com/59044398/117491376-b3bcf500-afa2-11eb-91bb-8022d407c9aa.PNG)


6.输入vi sudoers并回车，浏览sudoers文件。

输入字母e,然后输入i,进入编辑模式，此时注意看图片出现了“[INS](https://pinpai.smzdm.com/64844/)ERT”字样，即可进行编辑了。

7.找到%administrators ALL=(ALL) ALL这一句，改为如下所示：

<img width="340" alt="5f7ffaa5236fa9582 png_e680" src="https://user-images.githubusercontent.com/59044398/117491415-c5060180-afa2-11eb-951c-aa878004d39c.png">


%administrators ALL=(ALL) ALL  改为  %administrators ALL=NOPASSWD: ALL

然后按“ESC”键，输入 :wq （冒号也是输入的一部分） ，再按回车保存编辑。

<img width="340" alt="5f7ffae95b70a4413 png_e680" src="https://user-images.githubusercontent.com/59044398/117491432-c9cab580-afa2-11eb-8058-0aa1589068b2.png">




8.接下来打开winscp，输入群晖地址、用户名和密码后，点击红圈处的“高级按钮”。

<img width="340" alt="5f7ffb5d8c490215 png_e680" src="https://user-images.githubusercontent.com/59044398/117491443-cfc09680-afa2-11eb-905d-e2a3a6ed0f2c.png">


然后如下图所示设置即可。

<img width="340" alt="5f7ffbaeb9aec7736 png_e680" src="https://user-images.githubusercontent.com/59044398/117491455-d3541d80-afa2-11eb-83ce-c3330f71d44e.png">

##### 也可以使用这种方法

1、先到套件中心安装perl插件

2、下载本教程下载列表中的ConfigFileEditor-noarch-16.spk插件，手动安装该插件（该插件不含数字签名，确定就好）

3、打开config file editor，选择Config File Editor，在最后加上 /etc/sudoers, sudoers 保存后重新打开config file editor
![ba6fdbdd36450260ca544f7e885e6f84](https://user-images.githubusercontent.com/59044398/117493238-4f4f6500-afa5-11eb-8f9e-7116db924c67.png)

4、选择sudoers文件，修改 %administrators ALL=(ALL) ALL 这行即可。
![ba6fdbdd36450260ca544f7e885e6f84](https://user-images.githubusercontent.com/59044398/117493361-7312ab00-afa5-11eb-81ed-a5ac71fb245b.png)


PS：其实这个工具也可以直接修改sshd_config文件，直接选择ssh，然后找到 #PermitRootLogin prohibit-password 修改为 PermitRootLogin yes ，然后保存后重启DSM即可。
