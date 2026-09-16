# GitHub Setup Guide

想把代码托管到GitHub，却不知道该从何开始？本教程将带你手把手完成GitHub开发环境搭建。

## 设置Git

1. 设置Git用户名。

   ```shell
   git config --global user.name "Your Name"
   ```

2. 设置Git邮箱。

   ```shell
   git config --global user.email "<you@example.com>"
   ```

## GitHub-CLI

[GitHub-CLI](https://github.com/cli/cli)是GitHub提供的命令行工具。安装后，可以直接在终端中完成登录、创建仓库和管理项目等基本操作，从而节省时间。

```shell
sudo pacman -S github-cli
```

## 生成SSH密钥

1. 打开终端

2. 粘贴以下文本，将`you@example.com`替换为GitHub账户绑定的邮箱。

   ```shell
   ssh-keygen -t ed25519 -C "you@example.com"
   ```

   这将以提供的电子邮件地址为注释生成一对Ed25519密钥：

   - `ssh-keygen`：生成SSH密钥的工具。
   - `-t ed25519`：指定使用Ed25519算法。
   - `-C "you@example.com"`：为密钥添加注释。

   执行后会生成私钥`~/.ssh/id_ed25519`和公钥`~/.ssh/id_ed25519.pub`。私钥应妥善保管，不能泄露；公钥则可以添加到GitHub账户中。

   ```shell
   > Generating public/private ALGORITHM key pair.
   ```

3. 当系统提示“Enter a file in which to save the key（输入要保存密钥的文件）”时，可以按回车键接受默认文件位置。

   ```shell
   > Enter a file in which to save the key (/home/YOU/.ssh/id_ALGORITHM):[Press enter]
   ```

4. 在提示符下，键入安全密码。如果不想设置密码，可以在提示时直接按两次回车键跳过。

   ```shell
   > Enter passphrase (empty for no passphrase): [Type a passphrase]
   > Enter same passphrase again: [Type passphrase again]
   ```

## 将SSH密钥添加到ssh-agent

1. 在后台启动ssh代理。

   ```shell
   eval (ssh-agent -c)
   ```

2. 将SSH私钥添加到ssh-agent中。

   ```shell
   ssh-add ~/.ssh/id_ed25519
   ```

## 身份验证

通过GitHub-CLI进行身份验证。

```shell
gh auth login -c -p ssh -h GitHub.com -w
```

## 测试SSH连接

设置SSH密钥并将其添加到GitHub后，可以测试连接。

1. 打开终端。

2. 执行以下命令：

   ```shell
   ssh -T git@github.com
   ```

   你可能会看到类似如下的警告：

   ```shell
   > The authenticity of host 'github.com (IP ADDRESS)' can't be established.
   > ED25519 key fingerprint is SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU.
   > Are you sure you want to continue connecting (yes/no)?
   ```

3. 验证所看到消息中的指纹是否与[GitHub的公钥指纹](https://docs.github.com/zh/authentication/keeping-your-account-and-data-secure/githubs-ssh-key-fingerprints)匹配。如果是，则键入`yes`：

   ```shell
   > Hi USERNAME! You've successfully authenticated, but GitHub does not
   > provide shell access.
   ```

4. 请确认返回的消息中包含你的用户名。

## 后续步骤

现在，Git和GitHub都已设置完成。现在可以选择创建存储库，可在其中存储项目。将代码保存在存储库中可以备份工作，并在世界各地共享它。

## 创建存储库

为项目创建存储库，并在本地克隆它。

```shell
gh repo create my-project --public --clone
```

## 提交更改

创建项目后，可以开始提交更改。

1. 创建自述文件。

   ```shell
   echo "info about this project" >> README.md
   ```

2. 暂存并提交文件。

   ```shell
   git add README.md && git commit -m "docs: add README"
   ```

3. 首次将更改推送到远程仓库，并设置当前分支的上游分支。

   ```shell
   git push --set-upstream origin main
   ```

设置上游分支后，后续在当前分支提交更改时，直接执行`git push`即可。
