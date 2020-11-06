---
layout: post
title: How to Install KVM
subtitle: How to install KVM on Ubuntu 18.04
cover-img: /assets/img/path.jpg
thumbnail-img: /assets/img/kvm.jpeg
share-img: /assets/img/path.jpg
tags: [ubuntu 18.04, kvm]
---
![kvm](/assets/img/kvm.jpeg)


[Original Post](https://blog.sulaiman.tech/2020/11/ubuntu-1804-how-to-install-kvm.html)

<div align="justify">
Hallo Semua, Pada kesempatan kali ini kami akan share tutorial tentang KVM, seperti biasa kita akan review terlebih dahulu tentang apa itu kvm. Kernel-Based Virtual Machine (KVM) adalah salah satu teknologi virtualisasi (hypervisor) yang dikembangkan oleh Linux. KVM merupakan sebuah solusi untuk melakukan virtualisasi pada Linux dengan perangkat keras type x86 (64-bit). KVM diimplementasikan sebagai modul kernel loadable yang mengubah kernel Linux menjadi bare metal hypervisor. Ada dua prinsip desain utama yang diadopsi oleh KVM dengan tujuan agar KVM menjadi hypervisor dengan kinerja tinggi dan melampaui open source hypervisors lainnya. 
</div>


* Pertama, karena KVM didesain setelah kemunculan teknologi virtualisasi dibantu perangkat keras (hardware assisted virtualization), KVM tidak perlu mengimplementasikan fitur yang telah disediakan oleh perangkat keras. KVM membutuhkan prosesor Intel VT-X atau AMD-V dan menggunakan fitur tersebut untuk virtualisasi CPU. Karena memanfaatkan dukungan perangkat keras yang telah tersedia, KVM mampu merancang solusi hypervisor yang optimal tanpa memerlukan beban yang mendukung perangkat keras dan tidak perlu modifikasi untuk mendukung sistem operasi guest. 

    
* Kedua, tim KVM menerapkan pepatah “don’t reinvent the wheel”. Terdapat banyak komponen yang dibutuhkan oleh hypervisor agar KVM mampu memvirtualisasi CPU dan memori, seperti: pengatur memori, penjadwal proses, I/O stack, device drivers, pengatur keamanan, network stack, dan sebagainya. Faktanya, suatu hypervisor adalah sistem operasi khusus, hanya saja tujuan hypervisor berbeda dengan tujuan umum sistem operasi lainnya. Hypervisor menjalankan mesin virtual, bukan aplikasi-aplikasi. Karena kernel Linux sudah mencakup fitur inti yang diperlukan oleh hypervisor dan Linux telah menjadi platform skala enterprise yang stabil selama lebih dari lima belas tahun, KVM dibangun berdasarkan kernel Linux agar lebih efisien, daripada menulis semua komponen yang dibutuhkan seperti pengatur memori dan penjadwal dari awal. 


<div align="justify">
Dalam hal ini, KVM mendapatkan pelajaran dari pengalaman Xen. Salah satu tantangan utama arsitektur Xen adalah arsitektur pemecahan domain dan hypervisor Xen. Karena hypervisor Xen menyediakan fitur platform inti dalam stack, Xen perlu mengimplementasikan fitur-fitur tersebut, seperti penjadwal dan pengatur memori, dibangun dari nol. Misalnya saat kernel Linux memiliki pengatur memori yang telah teruji dan baik termasuk dukungan untuk NUMA dan sistem skala besar, hypervisor Xen perlu membangun dukungan ini dari awal. Demikian pula fitur seperti manajemen daya di Linux yang sudah matang dan telah terbukti di lapangan harus diimplementasi ulang pada hypervisor Xen.
    
</div>
<div align="justify">
Keputusan penting lainnya yang dibuat oleh tim KVM adalah penggabungan KVM ke dalam hulu kernel Linux. Kode KVM telah disampaikan kepada komunitas kernel Linux pada Desember 2006 dan diterima ke dalam kernel 2.6.20 pada Januari 2007. Pada titik ini, KVM menjadi bagian inti dari Linux dan dapat mewarisi fitur penting dari kernel Linux. Sebaliknya, patch yang dibutuhkan untuk membangun Linux Domain0 untuk Xen masih belum menjadi bagian dari kernel Linux dan memerlukan vendor untuk membuat dan mengelola cabang dari kernel Linux. Hal ini mengakibatkan peningkatan beban pada distributor Xen yang tidak mampu memanfaatkan fitur dari kernel hulu secara mudah. Setiap fitur baru, perbaikan bug, atau penambahan patch yang ditambahkan dalam kernel hulu Linux harus dikonfigurasi ulang agar dapat bekerja dengan setelan patch Xen. KVM telah didukung oleh beberapa vendor terkemuka dalam industri perangkat lunak, termasuk Red Hat, AMD, HP, IBM, Intel, Novell, Siemens, SGI, dan lain-lain. [reference]
</div>

Berikut cara install kvm :
1. install kvm dan dependence

```bash
root@kvm:~# apt-get install -y qemu-kvm libvirt-bin virtinst bridge-utils libosinfo-bin libguestfs-tools virt-top
```

2. Enable vhost-net

```bash
root@kvm:~# modprobe vhost_net
root@kvm:~# lsmod | grep vhost

vhost_net              20480  0
vhost                  32768  1 vhost_net
macvtap                20480  1 vhost_net

root@kvm:~# echo vhost_net >> /etc/modules 
```
3. Konfigurasi network bridge

```bash
root@kvm:~# vi /etc/netplan/01-netcfg.yaml

network:
  version: 2
  renderer: networkd
  ethernets:
    ens3:
      dhcp4: no
      # disable existing configuration for ethernet
      #addresses: [10.20.20.30/24]
      #gateway4: 10.20.20.1
      #nameservers:
        #addresses: [10.20.20.10]
      dhcp6: no

  # add configuration for bridge interface
  bridges:
    br0:
      interfaces: [ens3]
      dhcp4: no
      addresses: [10.20.20.30/24]
      gateway4: 10.20.20.1
      nameservers:
        addresses: [10.20.20.10]
      parameters:
        stp: false
      dhcp6: no

root@kvm:~# reboot
root@kvm:~# ip addr 
```

Yaps, Selesai sudah, sampai bertemu di tutorial berikutnya!