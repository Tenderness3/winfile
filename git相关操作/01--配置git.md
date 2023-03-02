# git配置GitHub和gitee 同时上传

## 借鉴地址：

- [基本配置地址](https://blog.csdn.net/u011870022/article/details/117883439)
  - [SSH配置](https://blog.csdn.net/u011870022/article/details/117883717)
- [分开配置 gitee和github地址](https://www.bbsmax.com/A/Vx5MGP3gzN/)
- [报错解决地址](https://blog.csdn.net/weixin_46015333/article/details/124627808)

&nbsp;

## 我的总结：

[基本配置地址](https://blog.csdn.net/u011870022/article/details/117883439)

### $\color{#f47983}{ 1、新建一个仓库}$

```js
// gitee上建立一个仓库 winfile，默认就行，默认仓库私有，使用模板后，仓库设为公开，将仓库里面文件 readme 删除
// github 上建立一个仓库 winfile，默认公开，其他不用改
```

&nbsp;

### $\color{#f47983}{2、基本配置 }$

```js
// 1、在本地新建一个文件夹用于存放 github 和 gitee项目


// 2、初始化
git init


// 3、可能要求填写，用户名 和 邮箱
// 配置用户名(用户名自定义随便写)
git config --global user.name "username"
// 配置邮箱("username@email.com"是注册账号时所用的邮箱)
git config --global user.email "username@email.com"

```

&nbsp;

### $\color{#f47983}{ 3、修改初始化后的.git}$

[分开配置 gitee和github地址](https://www.bbsmax.com/A/Vx5MGP3gzN/)

进入 文件夹下面的 `.git` 目录，找到 `config` 文件打开 输入

==注意 url 替换为，仓库 ssh地址==，主页 下载 `code` 中有

```apl
[core]
	repositoryformatversion = 0
	filemode = false
	bare = false
	logallrefupdates = true
	ignorecase = true

[remote "gitee"]
		url= git@gitee.com:liu-rushi/winfile.git
        fetch = +refs/heads/*:refs/remotes/origin/*

[remote "github"]
		url = git@github.com:Tenderness3/winfile.git
        fetch = +refs/heads/*:refs/remotes/origin/*
		
[branch "master"]
	remote = origin
	merge = refs/heads/master

```

&nbsp;

### $\color{#f47983}{ 4、关于 SSH 配置}$

[SSH配置](https://blog.csdn.net/u011870022/article/details/117883717)

#### ①先配置giteee的SSH-Key：

生成一个gitee用的SSH-Key密钥：输入命令：（gitee_id_rsa是生成公钥的名）

```bash
ssh-keygen -t rsa -C '你的邮箱' -f ~/.ssh/gitee_id_rsa
```

点击三次回车，这里什么也不用填。

&nbsp;

#### ②配置github的SSH-Key

生成一个github用的SSH-Key密钥：输入命令：（github_id_rsa是生成公钥的名）

```bash
ssh-keygen -t rsa -b 4096 -C "你的邮箱" -f ~/.ssh/github_id_rsa
```

点击三次回车，这里什么也不用填。

&nbsp;

#### ③将SSH密钥添加到 ssh-agent，输入命令

```bash
eval $(ssh-agent -s)
```

返回类似这样的东西

```bash
Agent pid 595
```

&nbsp;

#### ④将ssh密钥添加到github

```bash
ssh-add ~/.ssh/github_id_rsa
```

&nbsp;

#### ⑤在C:\Users\你的用户名.ssh目录下可以看见升级的密钥文件，并新建一个config文件，添加如下内容

```bash
# gitee
Host  gitee.com
HostName   gitee.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/gitee_id_rsa
# github
Host  github.com
HostName  github.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/github_id_rsa
```

&nbsp;

#### ⑥ssh密钥添加到gitee

```bash
ssh-add ~/.ssh/gitee_id_rsa
```

在任何页面的右上角，单击您的个人资料照片->`设置-`>`左侧菜单栏找到SSH公钥`->将`C:\Users\你的用户名.ssh目录`下`gitee_id_rsa.pub`文件打开复制里面的内容添加到密钥（有可能输入密码，输入就行）。

&nbsp;

#### ⑦ssh密钥添加到github

1、在任何页面的右上角，单击您的个人资料照片，然后单击 `Settings`（设置）。在用户设置侧边栏中，单击 `SSH and GPG keys`（SSH 和 GPG 密钥）。
2、单击 `New SSH key`（新 SSH 密钥）。
3、在 “Title”（标题）字段中，为新密钥添加描述性标签。 将`C:\Users\你的用户名.ssh目录`下`github_id_rsa.pub`文件打开复制里面的内容添加到密钥粘贴到 “Key”（密钥）字段。
4、单击 `Add SSH key`（添加 SSH 密钥）。如有提示，请确认您的 GitHub 密码。

&nbsp;

### $\color{#f47983}{5、测试 }$

本地新建一个文件 `test.txt`

```bash
git status
git add test.txt
git commit -m "提交了测试文件"

// 上传 gitee
git push gitee master

// 上传 github
git push github master
```

&nbsp;

$\color{#f47983}{6、修改错误 }$

[报错解决地址](https://blog.csdn.net/weixin_46015333/article/details/124627808)

出一些乱七八糟的错误

! [rejected] master -> master (non-fast-forward) error: failed to push some refs to ‘xxx’

遇到 `vim`编辑的时候 `esc` 退出编辑模式，再输入 `:wq` 保存退出

基本都是本地文件和云端不匹配的问题。分别执行 下面代码

```bash
// 修改 gitee 文件不匹配问题
git pull --rebase gitee master
git pull gitee master --allow-unrelated-histories


// 修改 github 文件不匹配问题
git pull --rebase github master
git pull github master --allow-unrelated-histories
```

==修改好没问题后，再次重复 第五步 ，直到 单独上传 没问题为止==

&nbsp;

### $\color{#f47983}{6、合并 }$

进入 文件夹下面的 `.git` 目录，找到 `config` 文件打开 输入

```bash
[core]
	repositoryformatversion = 0
	filemode = false
	bare = false
	logallrefupdates = true
	ignorecase = true
[remote "origin"]
		url = git@github.com:Tenderness3/winfile.git
		url= git@gitee.com:liu-rushi/winfile.git
        fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
	remote = origin
	merge = refs/heads/master
```



&nbsp;

### $\color{#f47983}{ 7、结束}$

最后测试几个文件，最后直接这样上传即可

```bash
git status
git add test.txt
git commit -m "提交了测试文件"
git push 
```





大概的总结就是：先弄好基本设置 👉 本地文件先设置成分开传 👉 配置SSH 👉 测试错误 👉 成功后 改回一块传

因为刚开始就一块传，错误修改不了
