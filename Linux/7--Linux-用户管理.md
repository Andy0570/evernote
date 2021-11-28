[TOC]

## 拥有者，组成员，和其他人
用户帐户定义在 /etc/passwd 文件里面，用户组定义在 /etc/group 文件里面。当用户帐户和用户组创建以后，这些文件随着文件 /etc/shadow 的变动而修改，文件 /etc/shadow 包含了关于用户密码的信息。 对于每个用户帐号，文件 /etc/passwd 定义了用户（登录）名、uid、gid、帐号的真实姓名、主目录、登录 shell。

#### `/etc/passwd`  存储用户信息

```
7个字段
root:x:0:0:root:/root:/bin/bash
登录名:口令占位符:用户ID号（UID）:默认组ID号（GID）:用户私人信息:用户主目录:登录 shell
```

| 用户组ID（UID） | 账号种类           |
| --------------- | ------------------ |
| 0               | 系统管理员         |
| 1～499          | 系统保留给服务使用 |
| 500～65535      | 普通用户           |



####  `/etc/shadow`  保存用户口令

```
9个字段
root:$1$..../:15092:0:99999:7:::
用户名:用户密码:上次更改密码时间:要过多少天才可以更改密码:密码多少天后到期:密码到期前的警告期限:账号失效期限:账号的生命周期:保留字
```



#### `/etc/group`文件，保存系统中所有组的名称，以及每个组中的成员列表

```
4个字段
root:x:0:
admin:x:115:lewis,rescuer
组名:组口令占位符:组 ID 号（GID）,成员列表，用逗号分开（不能加空格）
```


### `id` 显示用户身份信息
```shell
$ id
uid=0(root) gid=0(root) groups=0(root)
```

### `adduser` 


### `useradd` 新增用户

格式：`useradd [-u UID] [-g GID] [-d HOME] [-M] [-s]`

| 参数 | 含义                                       |
| ---- | ------------------------------------------ |
| -m   | ⭐️自动为新用户建立主目录                    |
| -u   | 自定义 UID， 设定 ID 值时尽量要大于 500    |
| -g   | ⭐️指定用户所属的群组                        |
| -d   | 自定义用户的home目录                       |
| -M   | 不建立home目录                             |
| -s   | ⭐️指定用户登录后所使用的 shell，默认使用 sh |

```shell
$ sudo useradd -m alice    ##添加一个用户名为 alice 的用户，并自动建立主目录
$ sudo passwd alice        ##更改 alice 的登录密码cat
输入新的 Unix 口令:
重新输入新的 Unix 口令:
passwd：已成功更新密码

$ sudo useradd -s /bin/base mike    ##指定新用户的 shell 为 base
```

> 💡建议
>
> 在新建一个用户时，单独创建一个同名的用户组，然后把用户归入这个组中——这正是不带 -g 参数的 useradd 命令的默认行为。

### `userdel` 删除用户

格式：`userdel [-r] username`

-r 选项的作用是删除用户时，连同用户的home目录一起删除。

> 💡 建议
>
> 将被删用户的主目录保留几周，然后再手动删除。

### `groupadd` 新增用户组

格式：`groupadd [-g GID] [-o] [-r] [-f] groupname`

-g 选项可以自定义 gid，不加 -g 则按照系统默认的 gid 创建组，跟用户一样，gid 也是从 500 开始的。

| 参数   | 含义                            |
| ------ | ------------------------------- |
| -g GID | 指定组 ID 号                    |
| -o     | 允许组 ID 号，不必唯一          |
| -r     | 加入组 ID 号，低于 499 系统账号 |
| -f     | 加入已有的组时，发展程序退出    |

### `groupdel` 删除用户组
格式：`groupdel groupname`

### `passwd` 设置/更改用户密码
设置或更改用户密码：`passwd [user]`

```shell
$ sudo passwd root    ## 修改 root 密码
```
