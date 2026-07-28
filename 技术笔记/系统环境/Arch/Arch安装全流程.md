setfont ter-132b
cat /sys/firmware/efi/fw_platform_size
ip link

iwctl
station waln0 scan
station waln0 get-networks
station waln0 connect NHK > wifi名称
 ping ping.archlinux.org
 timedatectl
 fdisk -l

 pacstrap -K /mnt base linux linux-firmware intel-ucode networkmanager grub efibootmgr dosfstools mtools vim
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=ARCH

vim /etc/default/grub
1
进行如下修改：

去掉 GRUB_CMDLINE_LINUX_DEFAULT 一行中最后的 quiet 参数
把 loglevel 的数值从 3 改成 5。这样是为了后续如果出现系统错误，方便排错
加入 nowatchdog 参数，这可以显著提高开关机速度
grub-mkconfig -o /boot/grub/grub.cfg

exit # 退回安装环境
umount -R /mnt # 卸载新分区
reboot # 重启

sudo pacman -S plasma sddm
sudo systemctl enable sddm

pacman -Syu

# 安装 sudo（最小化系统默认没有）
pacman -S sudo

# 创建用户（把 yourname 换成你想要的用户名）
useradd -m -G wheel -s /bin/bash yourname

# 设置用户密码
passwd yourname

# 编辑 sudo 配置，允许 wheel 组使用 sudo
EDITOR=vim visudo

systemctl enable --now NetworkManager
nmtui

useradd -m -G wheel -s /bin/bash yourname
passwd yourname
su - yourname

sudo pacman -Syu

# 核心：niri 合成器
sudo pacman -S niri

# 终端、启动器、通知、状态栏、壁纸、锁屏
sudo pacman -S alacritty fuzzel mako waybar swaybg swayidle swaylock

# 运行 X11 程序（微信、Steam 等很多应用需要）
sudo pacman -S xwayland-satellite

# 网络管理托盘图标
sudo pacman -S network-manager-applet

# 音频（PipeWire）
sudo pacman -S pipewire pipewire-audio pipewire-alsa pipewire-pulse wireplumber
systemctl --user enable --now pipewire pipewire-pulse wireplumber

# 字体（避免中文乱码）
sudo pacman -S noto-fonts noto-fonts-cjk noto-fonts-emoji

# 安装正确的 portal 后端
sudo pacman -S xdg-desktop-portal xdg-desktop-portal-wlr xdg-desktop-portal-gtk

# 创建 portal 配置文件
mkdir -p ~/.config/xdg-desktop-portal
cat > ~/.config/xdg-desktop-portal/portals.conf << 'EOF'
[preferred]
default=wlr
org.freedesktop.impl.portal.FileChooser=gtk
EOF

# 1. 安装 Nerd Fonts 图标字体（最轻量，只装图标）
sudo pacman -S ttf-nerd-fonts-symbols

# 2. 再装一个常用的等宽字体（推荐 JetBrains Mono Nerd Font）
sudo pacman -S ttf-jetbrains-mono-nerd

# 3. 刷新字体缓存
fc-cache -fv

sudo pacman -S adwaita-icon-theme hicolor-icon-theme
# 4. 重启 waybar
pkill waybar && waybar &

# 亮度控制
sudo pacman -S brightnessctl

# 音量控制（命令行 + GUI）
sudo pacman -S pamixer pavucontrol

# 确保 PipeWire 在运行
systemctl --user enable --now pipewire pipewire-pulse wireplumber
