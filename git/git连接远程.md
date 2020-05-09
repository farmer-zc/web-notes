# Git

## 1. git安装

下载地址：https://npm.taobao.org/mirrors/git-for-windows/

安装完成后在命令行输入以下命令:

```
git config --global user.name "zoucong"
git config --global user.email "845794204@qq.com"
```

因为Git是分布式版本控制系统，所以需要填写用户名和邮箱作为一个标识

`git config --global` 有了这个参数，表示你这台机器上所有的Git仓库都会使用这个配置，当然你也可以对某个仓库指定的不同的用户名和邮箱



## 2. 连接远程仓库

1. 创建 SSH Key，执行一下命令行，会在 user/.shh 生成下`id_rsa`和`id_rsa.pub`这两个文件

   ```
   ssh-keygen -t rsa -C '845794204@qq.com'
   ```

2. 登录 github ，打开 settings 中的 SSH Keys 页面，然后点击 Add SSH Key，填上任意的 Title 在Key文本框里黏贴 id_rsa.pub 文件的内容。

3. 创建版本库，在需要创建仓库的目录下运行命令行：git init

4. 根据GitHub的提示，在仓库下运行命令

   ```
   git remote add origin 'xxxxx'
   ```
   
   
   
5. 第一次 push 在命令行手动输入(ide很麻烦)
