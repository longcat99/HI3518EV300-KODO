该例程为视频编码示例

由于开发板自带Flash容量小，需要将该例程放在Ubuntu下的NFS目录下，开发板挂载该目录，步骤如下：

## 一.Ununtu搭建NFS服务器
1、安装并开启 Ubuntu 中的 NFS 服务，使用如下命令安装 NFS 服务：

sudo apt-get install nfs-kernel-server rpcbind

2、等待安装完成，安装完成以后在用户根目录下创建一个名为“NFS”的文件夹（即/home/用户名(根据自己的Ubuntu用户名修改)/NFS），以后所有的东西都放到这个“NFS”文件夹里面。

cd /home/用户名(根据自己的Ubuntu用户名修改)

mkdir NFS

3、使用如下命令打开 nfs 配置文件/etc/exports：

sudo vi /etc/exports

4、打开/etc/exports 以后在最后面添加如下所示内容：

/home/qq(修改为自己的Ubuntu用户名)/NFS *(rw,sync,no_root_squash)

5、重启 NFS 服务，使用命令如下：

sudo /etc/init.d/nfs-kernel-server restart

6、之后使用 ifconfig 查看 Ubuntu的 ip 地址（记住IP地址，后面要用到）：

ifconfig

然后下该例程复制到NFS目录下

## 二、修改开发板配置
7、开发板连接串口，打开串口助手，上电等待开发板进入系统，修改WIFI配置，命令如下：

cd /etc/Wireless

vi wpa_supplicant.conf

找到如下内容：

ctrl_interface=/etc/Wireless/wpa_supplicant

network={

ssid="HI3518E"

scan_ssid=1

key_mgmt=WPA-EAP WPA-PSK IEEE8021X NONE

pairwise=TKIP CCMP

group=CCMP TKIP WEP104 WEP40

psk="12345678"

}

修改：

ssid="WIFI名"

psk="WIFI密码"

保存退出

8、修改开发板的启动参数
开发板连接串口，打开串口助手，上电等待开发板进入系统，修改启动参数来挂载NFS，具体如下：

vi /etc/profile

找到如下内容：
 cd /komod                                                            
insmod cfg80211.ko                                                   
insmod hi3881.ko g_mode=0                                            
ifconfig wlan0 192.168.148.200                      
ifconfig -a                                                    
cd /bin                                                              
wpa_supplicant -iwlan0 -Dnl80211 -c/etc/Wireless/wpa_supplicant.conf&
route add default gw 192.168.148.1 dev wlan0                //设置网关
mount -t nfs -o nolock 192.168.148.102:/home/qq/NFS /mnt    //挂载NFS
#wpa_cli -i wlan0 status

cd /komod/ko                                            
./load3518ev300 -i -sensor0 imx307 -osmem 32M 
cd /mnt

修改其中的：
 
mount -t nfs -o nolock 192.168.148.102:/home/qq/NFS /mnt    //挂载NFS

将 192.168.148.102 修改为前面使用 ifconfig 查询到的 Ubuntu 的 IP 地址

保存退出

9、重启开发板:

reboot

配置正确的话系统启动会自动连接WIFI并挂载NFS文件系统，然后进入/mnt目录下

此时使用 ls 命令就可以看到 Ubuntu 系统 NFS 目录下的文件了
