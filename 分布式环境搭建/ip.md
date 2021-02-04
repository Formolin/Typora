- /etc/sysconfig/network-scripts/ifcfg-ens**

- 选择nat模式

  ```sh
  TYPE=Ethernet
  PROXY_METHOD=none
  BROWSER_ONLY=no
  BOOTPROTO=static  #!!!!!
  DEFROUTE=yes
  IPV4_FAILURE_FATAL=no
  IPV6INIT=yes
  IPV6_AUTOCONF=yes
  IPV6_DEFROUTE=yes
  IPV6_FAILURE_FATAL=no
  IPV6_ADDR_GEN_MODE=stable-privacy
  NAME=ens33
  UUID=61bdc78c-b456-411d-810a-3d40d6b09dfd
  DEVICE=ens33
  ONBOOT=yes #!!!!!
  IPADDR=192.168.83.170 #!!!!!
  GATEWAY=192.168.83.2 #!!!!!
  DNS1=192.168.83.2 #!!!!!
  
  ```

  