# 修复FFmpeg与NVIDIA Pascal架构的兼容性问题

教程搬运自[这里](https://github.com/CachyOS/distribution/issues/542)。是大佬Rsge和deadcoral的成果，仅作分享。

1. 运行以下命令：

   ```bash
   ffmpeg -hide_banner -loglevel error -f lavfi -i testsrc=size=320x240:rate=1 -frames:v 1 -c:v h264_nvenc -f null -
   ```

2. 如果输出与以下内容相仿，修复应该对你有效：

   ```text
   [h264_nvenc @ 0x5587e6769b80] Driver does not support the required nvenc API version. Required: 13.1 Found: 13.0`
   [h264_nvenc @ 0x5587e6769b80] The minimum required Nvidia driver for nvenc is 610.00 or newer
   ```

   如果没有，你可能遇到了其他问题。

3. 从AUR安装`ffnvcodec-headers13.0`：

   - 如果你安装了`paru`，请运行以下命令，并在最后确认移除冲突的软件包`ffnvcodec-headers`。
   - 否则，请用你通常安装AUR软件包的方式进行，同样需要移除冲突。

   ```bash
   paru ffnvcodec-headers13.0
   ```

4. 运行以下命令克隆`ffmpeg`仓库，并进入该目录：

   ```bash
   git clone https://aur.archlinux.org/ffmpeg-git.git
   cd ffmpeg-git
   ```

5. 下载补丁文件：

   ```bash
   wget https://git.dec05eba.com/gpu-screen-recorder/plain/subprojects/packagefiles/ffmpeg-nvenc-runtime-api-version.patch
   ```

6. 替换PKGBUILD文件为deadcoral制作的修复版，并将其行尾符转换为Linux的LF格式：

   （因为Pastebin保存的所有内容都使用的是Windows的CRLF）

   ```bash
   curl https://pastebin.com/raw/MAc3rHQh > PKGBUILD
   sed -i 's/\r$//' PKGBUILD
   ```

7. 运行以下命令构建并安装打过补丁的软件包：

   （稍等片刻，过程中还需要输入密码）

   ```bash
   makepkg -si
   ```

8. 如果系统提示，移除冲突的`ffmpeg`并确认安装。

9. 为了防止`ffmpeg`的后续更新（会取消你的修改），请将该软件包添加到忽略列表中。打开你的pacman配置文件：

   ```bash
   micro /etc/pacman.conf
   ```

10. 找到`#IgnorePkg =`并将其替换为`IgnorePkg = ffmpeg-git`。

11. 再次运行以下命令：

    （如果这次没有任何输出或错误信息，说明你已经成功了）

    ```bash
    ffmpeg -hide_banner -loglevel error -f lavfi -i testsrc=size=320x240:rate=1 -frames:v 1 -c:v h264_nvenc -f null -
    ```

要使用OBS，请从AUR安装`obs-studio-git`：

```bash
paru obs-studio-git
```
