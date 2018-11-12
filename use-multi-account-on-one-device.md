## 在一台设备上使用多个Github账户



初次使用多个 Github 账户时，一般都会遇到类似下面权限的问题：

```shell
ERROR: Permission to account-1/sample_repo.git denied to account-2.
fatal: Could not read from remote repository.
```

这个错误的意思是，你正在使用 `account-2` 对 `account-1` 的 `sample_repo` 进行操作，但是没有配置相应的权限，所以操作被拒绝。



我的系统是 mac OS High Sierra, 下面是我的解决办法：

1. 检查 `$HOME` 目录下的 `.ssh/` 目录，是否已存在公钥和私钥文件。一般带`.pub` 后缀的是公钥文件，文件名相同没有`.pub`后缀的是私钥文件。如果没有的话，就通过下面的步骤生成；如果不确定已经存在的公私钥是否已经被其他程序使用，也建议重新一个。
2. 生成公私钥文件。
3. 到 github 账户配置公钥。
4. 使用 `ssh-agent` 或 `.ssh/config` 配置文件管理不同的账户。



### 生成公私钥文件

使用 `ssh-keygen`命令：

```
ssh-keygen -t rsa
```

然后根据提示输入需要存入的文件路径和文件名，一般默认存放在 用户 `~/.ssh/` 目录下，命令中会有提示。`.ssh/` 后面的部分是秘钥的文件名，默认是 `id_rsa`， 你可以修改成其他名字，为了便于区分我们可以输入`~/.ssh/id_rsa_company` 。

之后输入密码，这是查看秘钥文件的密码，可以选择不填。

秘钥生成成功之后出现指纹序列和一张加密图。

之后就可以在`.ssh/`目录下看到生成的 `id_rsa_company` 和 `id_rsa_company.pub`.

使用命令 `pbcopy < ~/.ssh/id_rsa_company.pub` 复制生成的公钥到剪贴板，用于后面 github 账户的配置。



### Github 账户配置

进入账户的 **Settings**，左边侧栏选择 **SSH and GPG keys**，然后点击 **New SSH key** 按钮。在新界面中，填写一个容易区分的 **Title** （ 如 *My Mac* ） ，然后**粘贴**之前复制到剪贴板的公钥字符串，最后点 **Add SSH key** 完成添加。



### 管理使用的账户

假设你有 2 个 github 账户，一个是个人账户，另一个是公司账户。它们对应的公钥分别是 `.ssh/` 目录下的 `id_rsa_personal.pub` 和 `id_rsa_company.pub`. 而且你已经把公钥分别配置到了你的 github 账户里，现在你怎样在不同的项目里使用不同的身份呢？下面两种方式任选其一。

#####使用配置 `.ssh/config`文件

如果 `.ssh/` 目录下没有`config`这个文件，创建一个即可，采用以下配置：

```
# 公司账户
Host company.github.com
  HostName github.com
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/id_rsa_company

# 个人账户
Host github.com
  HostName github.com
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/id_rsa_personal
```

上面每一行的含义：

- Host：解析的地址
- HostName：代表解析之后的地址
- IdentityFile：私钥文件，后面是私钥文件的地址



 











 