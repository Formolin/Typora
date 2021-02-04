 首先获取虚拟机配置的Mac地址
![img](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g2zlxyqrblj31d50u0tjw.jpg)



![img](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g2zlxyfk6pj30u00yn7gb.jpg)



- sudo vi /Library/Preferences/VMware\ Fusion/vmnet8/dhcpd.conf

  ```sh
  subnet 192.168.83.0 netmask 255.255.255.0 {
          range 192.168.83.128 192.168.83.254;
          option broadcast-address 192.168.83.255;
          option domain-name-servers 192.168.83.2;
          option domain-name localdomain;
          default-lease-time 1800;                # default is 30 minutes
          max-lease-time 7200;                    # default is 2 hours
          option netbios-name-servers 192.168.83.2;
          option routers 192.168.83.2;
  }
  host vmnet8 {
          hardware ethernet 00:50:56:C0:00:08;
          fixed-address 192.168.83.1;
          option domain-name-servers 0.0.0.0;
          option domain-name "";
          option routers 0.0.0.0;
  }
  ```

  

打开文件后，subnet（子网中）的range每台机可能不一致。我们的ip范围一定要在之内。我将range范围设定在192.168.83.128-192.168.83.254，

在新建配置的时候，host后面的名字一定要是你虚拟机镜像的文件名[如你的虚拟机文件名为“xxx.vmwarevm”，那么host后面就写xxx]，配置中hardware ethernet 为硬件地址(00:50:56:2E:9F:1A)，即在第三步中查到的MAC地址，fixed-address 为固定ip地址

![img](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g2zm27t26qj311w0qk445.jpg)

添加

```sh
host spring-cloud-aishang {
        hardware ethernet 00:50:56:2E:9F:1A;
        fixed-address 192.168.83.200;
}
```

之后需要重启VMware，只是重启虚拟机是不行的。虚拟机重启之后，选择NAT模式，之后就可以看到我们配置的固定IP地址了，同时如果Mac主机可以访问外网，虚拟机就也可以访问外网。

![spring-cloud-aishang](https://ws1.sinaimg.cn/large/006tNc79ly1g2zmalcjy1j31800rqn3w.jpg)

```sh
host vmnet8 {
	hardware ethernet 00:50:56:C0:00:08;
	fixed-address 192.168.83.1;
	option domain-name-servers 0.0.0.0;
	option domain-name "";
	option routers 0.0.0.0;
}
host spring-cloud-aishang {
        hardware ethernet 00:50:56:2E:9F:1A;
        fixed-address 192.168.83.200;
}
host hadoop {
        hardware ethernet 00:50:56:2F:E8:2C;
        fixed-address 192.168.83.210;
}




host hadoop104 {
        hardware ethernet 00:50:56:3D:0E:46;
        fixed-address 192.168.83.224;
}
host hadoop103 {
        hardware ethernet 00:50:56:37:6E:BE;
        fixed-address 192.168.83.223;
}
host hadoop102 {
        hardware ethernet 00:50:56:29:D4:9C;
        fixed-address 192.168.83.222;
}
host hadoop101 {
        hardware ethernet 00:50:56:21:72:C0;
        fixed-address 192.168.83.221;
}
```

