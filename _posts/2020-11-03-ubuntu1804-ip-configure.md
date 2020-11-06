--- 
layout: post 
title: 'Ubuntu 18.04 : How to configure Ip address' 
date: '2020-11-03T09:21:00.004+07:00' 
author: Sulaiman 
tags: [server,ubuntu 18.04]
modified_time: '2020-11-04T17:10:00.615+07:00' 
thumbnail-img: https://1.bp.blogspot.com/-37PDlsFtbMo/X6C_VRvz1fI/AAAAAAAAGF0/yHhRjS7fZSkUVDH3mLuQl_EmS9-GzFHuQCLcBGAsYHQ/s72-c/logo.png 
blogger_id: tag:blogger.com,1999:blog-5298605766658508146.post-3149919107726433063 
blogger_orig_url: https://blog.sulaiman.tech/2020/11/ubuntu-1804-how-to-configure-ip-address.html
---

<span>    </span>Pada kesempatan ini, kami akan share bagaimana cara konfigurasi Alamat IP, pada Ubuntu 18.04\. Untuk Ubuntu 18.04 ini sendiri dalam Networknya, sudah menggunakan _netplan_ ini memungkinkan kita untuk mengkonfigurasinya dengan _yaml file._ Berikut Langkahnya :

-  Kita login ke dalam Ubuntu 18.04
-  Lalu konfigurasikan IP seperti berikut

```bash  
root@alfaruq:~# vi /etc/netplan/01-netcfg.yaml  
```  

Isi seperti berikut :

```bash  
network:  
  version: 2  
  renderer: networkd  
  ethernets:  
    ens3:  
      dhcp4: no  
      # IP address/subnet mask  
      addresses: [10.0.0.30/24]  
      # default gateway  
      gateway4: 10.0.0.1  
      nameservers:  
        # dns   
        addresses: [10.0.0.10,10.0.0.11]  
      dhcp6: no  

```

- Implentasikan konfigurasi

```bash  
root@alfaruq:~# netplan apply  
root@alfaruq:~# ip addr  
1: lo: <loopback> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000  
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00  
    inet 127.0.0.1/8 scope host lo  
       valid_lft forever preferred_lft forever  
    inet6 ::1/128 scope host  
       valid_lft forever preferred_lft forever  
2: ens3: <broadcast> mtu 1500 qdisc fq_codel state UP group default qlen 1000  
    link/ether 52:54:00:19:e8:09 brd ff:ff:ff:ff:ff:ff  
    inet 10.0.0.30/24 brd 10.0.0.255 scope global ens3  
       valid_lft forever preferred_lft forever  
    inet6 fe80::5054:ff:fe19:e809/64 scope link  
       valid_lft forever preferred_lft forever  
```  
- Disable ipv6, jika tidak membutuhkan

```bash  
root@alfaruq:~# echo "net.ipv6.conf.all.disable_ipv6 = 1" >> /etc/sysctl.conf   
root@alfaruq:~# systcl -p  
net.ipv6.conf.all.disable_ipv6 = 1   
root@alfaruq:~# ip addr  
1: lo: <loopback> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000  
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00  
    inet 127.0.0.1/8 scope host lo  
       valid_lft forever preferred_lft forever  
2: ens3: <broadcast> mtu 1500 qdisc fq_codel state UP group default qlen 1000  
    link/ether 52:54:00:19:e8:09 brd ff:ff:ff:ff:ff:ff  
    inet 10.0.0.30/24 brd 10.0.0.255 scope global ens3  
       valid_lft forever preferred_lft forever  

```  

Sekian dulu untuk postingan hari ini, sampai jumpa di tutorial berikutnya !