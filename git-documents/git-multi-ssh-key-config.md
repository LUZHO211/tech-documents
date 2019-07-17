# 如何配置多个Git ssh-key

## 1 背景
有时候在同一个电脑上需要连接多个`git`远程仓库，例如公司的`gitlab`仓库和个人的的`github`仓库。由于每个`git`远程仓库都单独生成了自己的`id_rsa`和`id_rsa.pub`密钥对，我们需要告诉`git`管理工具该使用哪个`id_rsa`密钥来跟不同的远程仓库进行认证。

## 2 多个ssh-key的配置

假设有两组密钥对：公司和github的，均存放在`.ssh`目录下：

>1. 公司的`ssh`密钥对为：`company_id_rsa`和`company_id_rsa.pub`；
>2. 个人在`github`上的`ssh`密钥对为：`github_id_rsa`和`github_id_rsa.pub`；

在`.ssh`目录下创建`config`文件，用于配置git的ssh-key

```bash
$ touch config
```

编辑`config`文件，添加如下内容：

```bash
# github.com —— 个人github使用的ssh key 配置
Host github.com
  HostName github.com
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/github_id_rsa

# git.company.com —— 公司的gitlab使用的ssh key配置
Host git.company.com
  HostName git.caimi-inc.com
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/company_id_rsa
```

>1. Host：主机标识
>2. HostName：仓库的域名，例如github.com
>3. PreferredAuthentications：认证方式：publickey
>4. IdentityFile：ssh私钥的路径

<br /><br />

>*参考文章*
>
>*1. [Git 最著名报错 “ERROR Permission to XXX git denied to user”终极解决方案](https://juejin.im/post/5c19f802f265da615d729791)*
>
>*2. [了解ssh代理：ssh-agent](http://www.zsythink.net/archives/2407/)*