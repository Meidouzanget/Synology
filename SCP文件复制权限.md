# Synology

### SCP文件复制权限设置方法



但是在使用winscp的过程中发现一个问题，使用自行设定的管理员账号密码后台登陆群晖后，对于一些权限敏感的文件夹，想从本地复制文件到群晖中时，winscp的操作会被群晖拒绝，如下图所示：

<img width="340" alt="5f7ff2af394d53745 png_e680" src="https://user-images.githubusercontent.com/59044398/117491256-8a03ce00-afa2-11eb-9313-71f71e7c1469.png">


出现这个问题的原因是winscp没有获取到最高权限，无法对一些高权限文件夹进行操作。因此，我们需要想办法使winscp能够通过root权限登陆群晖，以实现类似“sudo -i”命令的root提权功能。

以前DSM6.0的时候可以通过改root密码的方式，来通过winscp来登录nas，这样可以获得最高权限可以任意修改文件。但是在DSM6.2的时候群晖把这个方式封了，幸好现在找到了另外的方式来解封

#### 操作步骤

1.打开群晖管理页面，找到控制面板—》应用程序—》终端机和SNMP

<img width="340" alt="5f7ff5526af518296 png_e680" src="https://user-images.githubusercontent.com/59044398/117491284-94be6300-afa2-11eb-9cd5-6eeb200c0e44.png">


然后选中“启动SSH功能”，点击应用

<img width="340" alt="5f7ff558111307691 png_e680" src="https://user-images.githubusercontent.com/59044398/117491297-99831700-afa2-11eb-9a25-7e4d4796805c.png">


2.下载一个putty软件，打开putty，在红框中填入群晖的局域网地址，然后点击下方的“OPEN”按钮。

<img width="340" alt="5f7ff5da14f394006 png_e680" src="https://user-images.githubusercontent.com/59044398/117491307-9daf3480-afa2-11eb-8aa9-1a4d91447242.png">


3.在弹出的命令行窗口中输入自己的群晖管理用户名，按回车后输入自己的管理员密码，继续回车。

4.输入sudo -i，并回车，将操作权限提升到最高的root级。这时候会要求再次输入一遍刚才的密码。



可以看到已经变成了root权限。

![捕获 4744PNG](https://user-images.githubusercontent.com/59044398/117491620-04345280-afa3-11eb-95a6-9db643981033.PNG)


5.输入CD /etc并回车，进入etc目录。

![捕获](https://user-images.githubusercontent.com/59044398/117491376-b3bcf500-afa2-11eb-91bb-8022d407c9aa.PNG)


6.输入vi sudoers并回车，浏览sudoers文件。

输入字母e,然后输入i,进入编辑模式，此时注意看图片出现了“[INS](https://pinpai.smzdm.com/64844/)ERT”字样，即可进行编辑了。

7.找到%administrators ALL=(ALL) ALL这一句，改为如下所示：

<img width="340" alt="5f7ffaa5236fa9582 png_e680" src="https://user-images.githubusercontent.com/59044398/117491415-c5060180-afa2-11eb-951c-aa878004d39c.png">


%administrators ALL=(ALL) ALL  改为  %administrators ALL=NOPASSWD: ALL

然后按“ESC”键，输入:wq，再按回车保存编辑。

<img width="340" alt="5f7ffae95b70a4413 png_e680" src="https://user-images.githubusercontent.com/59044398/117491432-c9cab580-afa2-11eb-8058-0aa1589068b2.png">




8.接下来打开winscp，输入群晖地址、用户名和密码后，点击红圈处的“高级按钮”。

<img width="340" alt="5f7ffb5d8c490215 png_e680" src="https://user-images.githubusercontent.com/59044398/117491443-cfc09680-afa2-11eb-905d-e2a3a6ed0f2c.png">


然后如下图所示设置即可。

<img width="340" alt="5f7ffbaeb9aec7736 png_e680" src="https://user-images.githubusercontent.com/59044398/117491455-d3541d80-afa2-11eb-83ce-c3330f71d44e.png">
