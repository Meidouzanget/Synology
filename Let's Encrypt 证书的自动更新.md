# Synology

前端 群晖 Let's Encrypt 证书的自动更新
xm3 · 2019年12月05日 · 207 次阅读
去年入手了一个群晖的 NAS DS716+II，这玩意儿可以说是垂涎了好久，最后忍不住诱惑，终于是入手了。选了 716+II 这个盘位少但价格不便宜的家伙说白了就是为了一件事——docker。有了 docker，可以说就有了无限的可能性，可以随便折腾，不用担心犯错误把 NAS 的主系统给搞乱了。

不过今天这篇文章我不打算介绍 NAS，也不打算说怎么在上边用 docker，我只想说说在这个上边怎么使用 Let’s Encrypt 的证书，以及怎么自动更新证书。

如果说你幸福的生活在一个运营商没有封 80 端口的国度，那么这篇文章你就不必往下看了，因为群晖的证书管理本身就内置了 Let’s Encrypt 的证书管理方式。我之所以写这篇文章，是因为我家的网络是没有开放 80 端口的，所以群晖自带的管理工具永远都是告诉你“无法连接到 Let's Encrypt。请确认域名有效。”

好在 Let’s Encrypt 提供了 acme 协议的认证方式，可以在没有 80 端口的情形下来签发和更新证书。

感谢伟大的 GitHub 以及无私的开发者们，所有的工具基本上都已经被开发了出来。

Neilpang/acme.sh 这个项目基本上就是我们用到的所有的工具了。

如果你是一个动手能力强的人，那么我在告诉你一下群晖的证书的保存位置/usr/syno/etc/certificate/的话，余下的工作你就应该可以自己搞定了。

下面就开始介绍具体的步骤：

1. 下载并安装 acme.sh
1234
ssh -p your_port your_name@your_hostcurl  https://get.acme.sh | sh
2. 修改配置文档，填入你在指定域名提供商的授权 token
12345678
cd ~/.acme.sh/dnsapivi dns_ali.sh# 修改如下两行配置为你自己的token，注意要去掉前面的#号# #Ali_Key="LTqIA87hOKdjevsf5"# #Ali_Secret="0p5EYueFNq501xnCPzKNbx6K51qPH2"# 保存并退出vi
不同的提供商的 token 的形式和配置方式可能会有不同，需要你到域名管理的后台自己去获取。

3. 准备用于存放安装后的证书的目录
1234567
# 新建一个存放所有证书的根目录mkdir cert_save_path
cd cert_save_path# 为每个子域名创建对应的mkdir sub1.example.commkdir sub2.example.com# ...
4. 生成证书
12345
# 首先加载acme.sh的环境变量
source ~/.acme.sh/acme.sh.env# 执行证书获取命令，我这里的dns_ali是对应阿里云的，其他供应商可以查阅acme的文档acme.sh --issue --dns dns_ali -d sub1.example.comacme.sh --issue --dns dns_ali -d sub2.example.com
5. 安装证书
12345678
acme.sh --installcert -d sub1.example.com         --certpath /cert_save_path/sub1.example.com/cert.pem         --key-file /cert_save_path/sub1.example.com/privkey.pem         --fullchain-file /cert_save_path/sub1.example.com/fullchain.pemacme.sh --installcert -d sub2.example.com         --certpath /cert_save_path/sub2.example.com/cert.pem         --key-file /cert_save_path/sub2.example.com/privkey.pem         --fullchain-file /cert_save_path/sub2.example.com/fullchain.pem
其实这里的安装是指的 acme 将获取的证书安装到之前建立好的目录，并没有安装到 NAS 自己的证书管理下边。

6. NAS 证书安装
控制面板 -> 安全性 -> 证书 -> 新增 -> 添加新证书 -> 导入证书 (描述那里填完整的子域名) -> 导入证书文档 (私钥为 privkey 证书为 cert.pem 中间证书为 fullchain.pem)

这一步将我们从 Let’s Encrypt 获取的证书安装到了 NAS，我们发现有效期是三个月，如果你能够接受三个月走一遍上边的流程，那么到这里就可以结束了，如果想把这个过程自动化起来，请接着看下边的流程。

7. 证书更新命令
因为 Let’s Encrypt 的证书的有效期只有三个月，所有我们必须至少每三个月执行一次更性操作，以防止证书过期。

12345678910
acme.sh/acme.sh --renew --force --dns dns_ali -d sub1.example.comacme.sh/acme.sh --installcert -d sub1.example.com         --certpath /cert_save_path/sub1.example.com/cert.pem         --key-file /cert_save_path/sub1.example.com/privkey.pem         --fullchain-file /cert_save_path/sub1.example.com/fullchain.pemacme.sh/acme.sh --renew --force --dns dns_ali -d sub2.example.comacme.sh/acme.sh --installcert -d sub2.example.com         --certpath /cert_save_path/sub2.example.com/cert.pem         --key-file /cert_save_path/sub2.example.com/privkey.pem         --fullchain-file /cert_save_path/sub2.example.com/fullchain.pem
执行上边的命令，会从 Let’s Encrypt 更新证书，并安装到指定的位置。

8. 拷贝证书脚本
这一步我认为一定是有其他方法来做的，但是因为搞不明白群晖 NAS 的证书存放逻辑，暂时就想了这么一个折中的办法。

通过观察可以发现，所有证书相关的配置都是在路径/usr/syno/etc/certificate下的，证书存放的具体位置是/usr/syno/etc/certificate/_archive，该目录下的内容形如：

1
2zFLdC  DEFAULT  dXWIy3  h94Uuq  IhSb6T  INFO  kGn0Zn  uTv2EL  vY1OEs  WE3xYE
其中 INFO 的内容是一个 JSON 文档，记录了每个证书的存放位置和应用的范围，DEFAULT 记录了哪一个是默认的证书，其他的目录则是存放一个一个的子域名的证书。

通过观察 INFO 的内容我们可以发现目录名和域名的对应关系，我编写了一个 python 脚本来分析这个对应关系以及将前文的证书拷贝到对应的位置，脚本名称为 update.py.

12345678910111213141516171819202122232425262728293031323334353637383940
# update.py
import json
import os
import shutilSRC_BASE_PATH = '/cert_save_path'   # 这是步骤3里创建的目录
DES_BASE_PATH = '/usr/syno/etc/certificate'
ARC_BASE_PATH = '/usr/syno/etc/certificate/_archive'
# [archive_key: (domain_name, destination_path)]keys = {}cfg_str = open('/usr/syno/etc/certificate/_archive/INFO').read()cfg = json.loads(cfg_str)# name to key
for k in cfg:    for service in cfg[k]['services']:        name = service['display_name']        if name.find('up4dev.com') < 0:            continue
        keys[k] = {'name' : name, 'arc_path' : '%s/%s' %(ARC_BASE_PATH, k), 'des_path' : [], 'src_path': '%s/%s' %(SRC_BASE_PATH, name)}        # des_path = '%s/%s/%s' %(CERT_BASE_PATH, service['subscriber'], service['service'])
        # print name, des_path

for k in cfg:    for service in cfg[k]['services']:        des_path = '%s/%s/%s' %(DES_BASE_PATH, service['subscriber'], service['service'])        if os.path.exists(des_path):            keys[k]['des_path'].append(des_path)
for key in keys:    print keys[key]    shutil.copy2(keys[key]['src_path'] + '/cert.pem', keys[key]['arc_path'] + '/cert.pem')    shutil.copy2(keys[key]['src_path'] + '/privkey.pem', keys[key]['arc_path'] + '/privkey.pem')    shutil.copy2(keys[key]['src_path'] + '/fullchain.pem', keys[key]['arc_path'] + '/fullchain.pem')    for des in keys[key]['des_path']:        shutil.copy2(keys[key]['arc_path'] + '/cert.pem', des + '/cert.pem')        shutil.copy2(keys[key]['arc_path'] + '/privkey.pem', des + '/privkey.pem')        shutil.copy2(keys[key]['arc_path'] + '/fullchain.pem', des + '/fullchain.pem')
9. 重启 web 服务
12
# 我选用的是nginx作为Web服务，如果选择Apache则执行Apache的重启命令/usr/syno/etc/rc.sysv/nginx.sh reload
10. 自动化脚本
我们将 8，9，10 三个步骤的操作串起来，做成一个自动化脚本，保存为 auto_update.sh

123456789101112131415161718
# 更新并安装acme.sh/acme.sh --renew --force --dns dns_ali -d sub1.example.comacme.sh/acme.sh --installcert -d sub1.example.com         --certpath /cert_save_path/sub1.example.com/cert.pem         --key-file /cert_save_path/sub1.example.com/privkey.pem         --fullchain-file /cert_save_path/sub1.example.com/fullchain.pemacme.sh/acme.sh --renew --force --dns dns_ali -d sub2.example.comacme.sh/acme.sh --installcert -d sub2.example.com         --certpath /cert_save_path/sub2.example.com/cert.pem         --key-file /cert_save_path/sub2.example.com/privkey.pem         --fullchain-file /cert_save_path/sub2.example.com/fullchain.pem# 拷贝到NAS的证书路径python update.py# 重启Web服务/usr/syno/etc/rc.sysv/nginx.sh reload
11. 设置定时任务
控制面板 -> 任务计划 -> 新增 -> 计划的任务 -> 用户定义的脚本 -> 计划 (设置成每月执行一次) -> 任务设置 (用户定义的脚本中填入步骤 10 的脚本的完整路径)

[^2018.05.09]: Let’s Encrypt 已经支持 wildcard 类型的证书，可能已经有比本文更好的方法了，待我研究之后再写一篇文章。

[^2018.05.30]: 泛域名的更新方法我已经另写了另一篇文章，大家可以参考：群晖 Let’s Encrypt 泛域名证书自动更新

参考
forum.51nb.com: 群晖安装并自动续期 Let’s Encrypt SSL 证书https://forum.51nb.com/thread-1789843-1-1.html
Neilpang/acme.sh 说明https://github.com/Neilpang/acme.sh/wiki/%E8%AF%B4%E6%98%8E
