# 飞牛OS安装VMWare

##  一. 在建立username文件夹
1. 使用`ssh username@ip`连接到飞牛os
2. 使用`sudo -i`切换到root环境
3. 使用`sudo /home/username`创建文件夹
4. 使用以下命令修改文件夹归属以及权限
	```
	chown -R adorukw:Users /home/adorukw
	chmod 755 /home/adorukw
	exit # 退出root，回到普通用户
	exit # 断开ssh
	```

## 二. 通过scp上传文件
1. 使用`scp -r local_dict_path username@ip:target_path`

## 三.使用KVM
1. ```
rm -rf ~/DebianServer/Debian13Server.vmx.lck
sudo apt update
sudo apt install qemu-kvm libvirt-daemon-system libvirt-clients virt-manager -y
sudo adduser $USER libvirt
sudo adduser $USER kvm
qemu-img convert -f vmdk -O qcow2 Debian13Server-disk1-cl4.vmdk debian13_server.qcow2
sudo cp ~/DebianServer/debian13_server.qcow2 /var/lib/libvirt/images/
sudo chown libvirt-qemu:kvm /var/lib/libvirt/images/debian13_server.qcow2


sudo virsh net-start default
sudo virsh net-autostart default

sudo virt-install \
--name Debian13Server \
--ram 4096 \
--vcpus 2 \
--os-variant debian11 \
--disk path=/var/lib/libvirt/images/debian13_server.qcow2,format=qcow2 \
--network default \
--import \
--graphics none \
--noautoconsole

sudo virt-install \
--name Debian13Server \
--ram 4096 \
--vcpus 2 \
--os-variant debian11 \
--disk path=/var/lib/libvirt/images/debian13_server.qcow2,format=qcow2 \
--network bridge=br0 \
--import \
--graphics none

sudo virsh console Debian13Server
sudo virsh destroy Debian13Server
sudo apt update && sudo apt install libguestfs-tools -y
sudo guestedit -d Debian13Server /etc/default/grub
```


