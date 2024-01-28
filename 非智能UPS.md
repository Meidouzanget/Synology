# Synology

#### 由于部分便宜UPS不支持ESXI虚拟机自动关机，因此只能通过非智能UPS（心跳检测）来实现自动关机

简单来说就是循环Ping某一台机器死了没有，比如路由器或指定连接了UPS PC，来判断是否断电

笔者购买的是施耐德 SPRM2K,只支持Windows系统安装 SchneiderUPS 软件实现关机，

想要使用支持ESXI 的 PowerChute 软件则需要购买更昂贵的机型，需要5，6千（吐槽一个，施耐德的文档真的做的稀烂，不能说几乎没有，简直就是没有。选型，使用，部署根本无从下手。用好一台UPS比用好一台服务器还要困难，夸一下戴尔的文档厚厚的一本不要太良心）

##### 引用群友金句

![image](https://user-images.githubusercontent.com/59044398/218308601-32bdaef2-b698-4979-ab73-0cab4bfd676f.png)

点击下载[代码.zip](https://github.com/Meidouzanget/Synology/files/14074605/default.zip)


``` bash

#!/bin/bash

#断网等待时间
sleep_time="60s"
retry_time="60s"

#米家控制中枢
Route_ip="192.168.100.70"
for ((dmtcai=1;dmtcai>=0;dmtcai++)) do

        if ping -c1 -w1 $Route_ip &> /dev/null 
        then
            echo "Route Power on, ping " + $Route_ip
        else
            echo "Route Power off ，DSM will shut down after 3 failed retry attempts，waiting" $retry_time
            synologset1 sys warn 0x11600036
            date
            sleep $retry_time

            for ((dmtcai=1;dmtcai<=5;dmtcai++)) do
                if [ $dmtcai == 5 ]
                then
                    echo "shutdown execution" 
                    date
                    synologset1 sys warn 0x11600037
                    poweroff
                else
                    if ping -c1 -w1 $Route_ip &> /dev/null 
                    then
                        echo "Route online, ping "+ $Route_ip , shutdown cancel
                        synologset1 sys warn 0x11600035
                        date
                        break
                    else
                        sleep $retry_time
                        echo "Route offline, retry connect..."
                        date
                    fi
                fi
            done
            
        fi
sleep $sleep_time
done

```




