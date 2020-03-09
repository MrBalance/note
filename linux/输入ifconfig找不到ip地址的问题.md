# 输入ifconfig找不到ip地址的问题

1. 打开虚拟机设置，网络适配器中的网络连接选择自定义VMnet8(NAT模式)
2. 输入查看ip的命令`ifconfig`，或者`ip addr`
3. 若查不到ip，则查看ens33网卡的配置，输入`vi /etc/sysconfig/network-scripts/ifcfg-ens33`（vi后加空格）将ONBOOT的权限改为yes
   ![201907010839528](..\img\linux\201907010839528.jpg)
4. 按Esc退出，再输入:wq，再按Enter
5. 重启网络服务，输入`sudo service network restart`
6. 输入`ifconfig`（或`ip addr`）查看ip即可

