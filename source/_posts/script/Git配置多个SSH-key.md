---
title: Git配置多个SSH-key
tags: [SCRIPT]
category: [SCRIPT,Common]
date: 2020-10-9 18:00:00
---

&emsp;&emsp;该内容主要源于 [Git配置多个SSH key](https://blog.csdn.net/hao495430759/article/details/80673568)。

### 需求
&emsp;&emsp;有时候我们的代码托管在多个平台上，这就需要为每个托管平台设置SSH-key。比如我现在的情况是需要为GitHub和公司的GitLab配置SSH-key。

&emsp;&emsp;以下为创建及配置步骤。

### 1，生成SSH-key
&emsp;&emsp;生成公司用的SSH-key：
```
ssh-keygen -t rsa -C "your email” -f ~/.ssh/popic_rsa
```
&emsp;&emsp;生成GitHub用的SSH-key：
```
ssh-keygen -t rsa -C "your email” -f ~/.ssh/github_rsa
```

&emsp;&emsp;此时，C:/Users/"用户名"/.ssh文件夹下应该有四个新创建的文件，其中“.pub”格式结尾的文件为公钥文件。

### 2. 添加公钥到代码平台
&emsp;&emsp;分别登录github和公司代码托管平台，在“Setting - SSH-keys”中添加公钥。

![](Git配置多个SSH-key/公钥.png)

&emsp;&emsp;填写好名称和内容后，点击“Add SSH key”。

### 3. 添加私钥
```
ssh-add ~/.ssh/popic_rsa
ssh-add ~/.ssh/github_rsa
```

&emsp;&emsp;如果运行后出现“Could not open a connection to your authentication agent.”，执行“ssh-agent bash”后再重新执行添加命令。

![](Git配置多个SSH-key/not_connect.png)

```
ssh-agent bash
ssh-add ~/.ssh/popic_rsa
ssh-add ~/.ssh/github_rsa
```

&emsp;&emsp;通过“ssh-add -l”列出私钥列表，通过“ssh-add -D”来清空私钥列表
```
ssh-add -l
ssh-add -D
```

### 4. 修改config配置文件
&emsp;&emsp;在“.ssh”文件夹下，如果没有“config”文件（无后缀名），可以通过“touch config”命令创建一个。

```
touch config
```

&emsp;&emsp;打开“config”文件进行编辑。

```
# github
Host github.com
Port 22
HostName github.com
PreferredAuthentications publickey
IdentityFile C:/Users/“用户名”/.ssh/github_rsa
User vannyyuan
 
# gitpopic
Host git.popic.com
HostName git.popic.com
PreferredAuthentications publickey
IdentityFile C:/Users/“用户名”/.ssh/popic_rsa
User vanny
```

### 5. 测试
```
ssh -T git@github.com
ssh -T git@git.popic.com
```

&emsp;&emsp;如果提示 <font color='orange'>Are you sure you want to continue connecting (yes/no/[fingerprint])? </font>，则输入“yes”并回车即可。会在“.ssh”目录下生成缺少的known_hosts文件。

&emsp;&emsp;成功！🎉

![](Git配置多个SSH-key/succeed.png)


### 🚏 出现 Permission denied 问题

&emsp;&emsp;在使用命令 ```ssh -T git@git.popic.com``` 时，出现了 <font color='red'>Permission denied (publickey,password,keyboard-interactive). </font> 的错误。

&emsp;&emsp;试过删除密钥重新生成和添加，也不能解决这个问题，最终在网上找到了可用方法：提升 “~/.ssh” 文件夹的权限。

```
chmod 700 ~/.ssh
```
&emsp;&emsp;执行后删除 “~/.ssh” 文件夹中的 “known_hosts” 文件。


&emsp;&emsp;此时重新使用命令就可以成功登陆了！