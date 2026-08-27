# CachyOS Post Install

我的CachyOS安装后配置流程。

## 显示服务器

我的GTX1060在Wayland下的兼容性实在不太理想，各种显示问题层出不穷，所以目前只能退回到更稳定的X.Org。

1. 安装`Xorg`组件。

   ```shell
   sudo pacman -S --needed plasma-x11-session kwin-x11 xorg
   ```

2. 安装完成后重新登录。

3. 在登录界面的左下角更换会话。

4. 执行以下命令检查，如果出现`x11`则表示设置成功：

   ```shell
   echo $XDG_SESSION_TYPE
   ```

## 字体配置

不知道为什么，CachyOS默认会把汉字渲染成韩文的字形。解决办法：

1. 安装字体。

   ```shell
   sudo pacman -S --needed inter-font adobe-source-serif-fonts noto-fonts-cjk noto-fonts-emoji ttf-sarasa-gothic
   ```

2. 下载字体配置文件。

   ```shell
   wget https://github.com/szclsya/dotfiles/raw/refs/heads/master/fontconfig/fonts.conf
   ```

3. 复制到用户的配置目录。

   ```shell
   mkdir -p ~/.config/fontconfig && cp fonts.conf ~/.config/fontconfig
   ```

4. 然后更新字体缓存即可生效。

   ```shell
   fc-cache -fv
   ```

Flatpak应用运行在沙箱中，宿主系统上的字体配置不一定会被应用直接读取。

```shell
flatpak --user override --filesystem=xdg-config/fontconfig:ro
```

## 家目录

像CachyOS这类用户友好的发行版，会根据你安装时选的语言，自动设置家目录里的文件夹名称。选择中文后，家目录里的文件夹也会变成中文的。

如果经常使用终端，中文目录名可能不太方便。

1. 强制重新设置家目录里的文件夹名称为英文。

   ```shell
   LC_ALL=C.UTF-8 xdg-user-dirs-update --force
   ```

2. 在Dolphin文件管理器的侧边栏中，修改相关目录的位置。

## 系统设置

系统的默认设置不太符合我的使用习惯。以下是我调整过的选项，仅供参考：

- 鼠标和触摸板鼠标：**禁用指针加速度**
- 锁屏，自动锁定屏幕：**不自动锁屏**
- 电源管理，空闲时：**无操作**（防睡死）
- 桌面会话，登录时自动启动应用程序：**启动为空会话**

## ArchLinuxCN

1. 添加[ArchLinuxCN](https://www.archlinuxcn.org/archlinux-cn-repo-and-mirror/)软件源。

   ```shell
   printf '%s\n' '[archlinuxcn]' 'Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch' | sudo tee -a /etc/pacman.conf >/dev/null
   ```

2. 安装`archlinuxcn-keyring`包，以导入GPG密钥。

   ```shell
   sudo pacman -Sy archlinuxcn-keyring
   ```

## 中文输入法

安装并配置[小企鹅输入法5](https://fcitx-im.org/wiki/Fcitx_5/zh-cn)（以下简称fcitx5）。

1. 基本的fcitx5安装包括：主程序、配置程序、输入法模块和输入法引擎。

   ```shell
   sudo pacman -S fcitx5-im fcitx5-rime
   ```

   `fcitx5-im`包组提供主程序、配置程序和输入法模块。

   `fcitx5-rime`包提供[中州韵](https://rime.im/)输入法引擎。

2. 为了让fcitx5能被正确识别并参与输入，需要设置相应的环境变量。

   ```shell
   printf '%s\n' 'export XMODIFIERS=@im=fcitx' 'export GTK_IM_MODULE=fcitx' 'export QT_IM_MODULE=fcitx' | sudo tee -a /etc/profile >/dev/null
   ```

3. 导入[白霜拼音](https://github.com/gaboolic/rime-frost)词库。

   ```shell
   git clone --depth 1 https://github.com/gaboolic/rime-frost Rime
   cp -r Rime/* ~/.local/share/fcitx5/rime
   ```

4. 按`Ctrl+空格`切换输入法。右键任务栏的“中州韵”图标，进入“小地球”的菜单，点击重新部署。

   建议前往“系统设置，输入法，全局选项”修改按键绑定，比如：`Super+空格`。

5. 安装[Mellow](https://github.com/sanweiya/fcitx5-mellow-themes)主题。

   ```shell
   git clone https://github.com/sanweiya/fcitx5-mellow-themes.git
   mkdir -p ~/.local/share/fcitx5/themes
   cp -r fcitx5-mellow-themes/mellow-* ~/.local/share/fcitx5/themes
   ```

   然后前往“系统设置，输入法，配置附加组件，经典用户界面”更换主题。

## NerdFonts

[NerdFonts](https://github.com/ericbaranowski/nerd-fonts/blob/master/readme_cn.md)是包含大量图标的字体。

我推荐使用[Iosevka](https://github.com/be5invis/Iosevka)，中英文完美对齐。

```shell
sudo pacman -S ttf-iosevkaterm-nerd
```

## 文件管理器

[Yazi](https://yazi-rs.github.io/docs/installation/)是一个终端文件管理器。

```shell
sudo pacman -S --needed yazi ffmpeg 7zip jq poppler fd ripgrep fzf zoxide resvg imagemagick xclip
```

## Flatpak

[Flatpak](https://flatpak.org/)是一个Linux桌面程序的构建、分发和沙箱化运行系统。

1. 安装。

   ```shell
   sudo pacman -S flatpak
   ```

2. 配置镜像源。

   ```shell
   sudo flatpak remote-modify flathub --url=https://mirrors.ustc.edu.cn/flathub
   ```

## Minecraft

第三方启动器我比较推荐[PrismLauncher](https://prismlauncher.org/)，它是开源的。可以自动处理前置模组。

1. 安装`PrismLauncher`和`Flatseal`。

   ```shell
   flatpak install flathub org.prismlauncher.PrismLauncher
   flatpak install flathub com.github.tchx84.Flatseal
   ```

2. 打开[Flatseal](https://flathub.org/en/apps/com.github.tchx84.Flatseal)并启用Prism Launcher的**GPU加速权限**。

## GameMode

[GameMode](https://github.com/FeralInteractive/gamemode)是一个Linux下的守护进程和库组合，允许游戏请求一组优化暂时应用于主机操作系统和游戏进程。

1. 安装。

   ```shell
   sudo pacman -S gamemode lib32-gamemode
   ```

2. 将自己添加到`gamemode`用户组。

   ```shell
   sudo gpasswd --add $USER gamemode
   ```

   如果没有这个用户组，GameMode用户守护进程将没有权限更改CPU管理器或进程的优先级。

## 参考

- [Linux下的字体调校指南](https://szclsya.me/zh-cn/posts/fonts/linux-config-guide/)
- [Flatpak程序吃不到系统中文字型设定](https://ivonblog.com/posts/linux-fontconfig/)
- [将家目录下的文件夹改成英文](https://ivonblog.com/posts/linux-xdg-user-dirs/)
- [GameMode——ArchLinux中文维基](https://wiki.archlinuxcn.org/wiki/GameMode)
