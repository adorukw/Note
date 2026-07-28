笔记本原生只能控制内置屏幕的亮度，因为内屏和外接显示器使用的是不同的硬件控制协议。要让 GNOME 桌面能够调节外接显示器的亮度，我们需要借助 DDC/CI 协议以及 Linux 下的 ddcutil 工具，最后通过 GNOME 扩展将其无缝集成到系统菜单中。
以下是在 Debian 13 (Trixie) 下的详细配置步骤：
1.开启显示器的 DDC/CI 功能：在进行系统配置前，你必须先确保显示器硬件允许被系统控制。
按下外接显示器上的实体菜单按键（OSD 菜单）。

在设置中找到 DDC/CI（通常在“系统”、“其他”或“高级设置”中）。

确保该功能处于 开启 (On/Enable) 状态。
2.安装底层控制工具：打开终端（Terminal），安装控制显示器 I2C 总线所需的工具和 GNOME 扩展管理器：
Bash

sudo apt update
sudo apt install ddcutil i2c-tools gnome-shell-extension-manager
3.加载内核模块并配置权限：这一步至关重要，否则只能使用 sudo 调节亮度。加载 I2C 模块：让系统能够与显示器进行底层通信。
Bash

sudo modprobe i2c-dev
设置开机自动加载：
Bash

echo "i2c-dev" | sudo tee /etc/modules-load.d/i2c-dev.conf
授予当前用户权限：将你的用户加入 i2c 用户组，这样 GNOME 桌面（以你的用户身份运行）就有权限修改亮度了。
Bash

sudo usermod -aG i2c $USER
4.重启并验证底层控制：为了让上一步的用户组权限生效，你需要注销当前用户并重新登录，或者直接重启电脑。
重启/重新登录后，打开终端，输入以下命令测试：
Bash

ddcutil detect
如果输出中成功识别到了你的外接显示器型号，说明底层通信已打通。你可以尝试用命令行测试调节亮度（将 50 替换为 0-100 的任意数字）：
Bash

ddcutil setvcp 10 50
（注：10 是亮度的 VCP 寄存器代码，如果此时显示器亮度发生变化，说明核心配置已成功！）
5.安装 GNOME 桌面扩展：现在我们需要把它集成到右上角的 GNOME 控制中心里：
在应用列表中找到并打开刚刚安装的 扩展管理器 (Extension Manager)。

切换到顶部的 浏览 (Browse) 标签页。

搜索 Brightness control using ddcutil。

点击安装。

安装完成后，点击右上角的系统托盘，你应该就能看到一个全新的外接显示器亮度调节滑块了。
💡 避坑提示：
并不是所有的接口都能完美透传 DDC/CI 信号。如果你使用的是某些廉价的 USB-C 拓展坞或非常旧的 HDMI 线缆，可能会导致 ddcutil detect 提示找不到显示器（No displays found）。如果遇到这种情况，尝试将显示器直接插在笔记本机身的 HDMI/DP 接口上。
