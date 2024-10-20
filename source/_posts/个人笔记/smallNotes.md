---
title: smallNotes
date: 2024-10-16 16:59:16
tags: linux
categories: 个人笔记
---

#### 修改Qt日志输出规则

修改环境变量 `QT_LOGGING_RULES`

#### 简单调用一个Dbus方法，并获得返回值

```c++
QDBusPendingReply<bool> r = DDBusSender()
        .interface("com.deepin.SessionManager")
        .path("/com/deepin/SessionManager")
        .service("com.deepin.SessionManager")
        .method("Register")
        .arg(QString(cookie))
        .call();

qDebug() << Q_FUNC_INFO << r.value();
```

#### 设置鼠标跟踪

```c++
setMouseTracking(true);
```

> 设置成功后可以实时跟踪鼠标位置，但是我目前实测就算设置了这个属性还是只能按下跟踪鼠标

#### 使用Qt并发库

```c++
QtConcurrent::run(QThreadPool::globalInstance(), [=] {
    for(int i = 0; i < INT_MAX; i++) {
        ret += i;
    }
    for(int i = 0; i < INT_MAX; i++) {
        ret -= i;
    }
    qInfo() << "finish" << ret;
});
```

> 📌需要在pro文件中添加并发模块 concurrent

#### 代码提交步骤

1. 拉取上游代码, 命名为 \*-upstream
2. clone comunity仓库
3. 上有checkout到对应tag
4. 删除上游本地仓库的.git\*
5. 复制community的本地仓库的.git\*和debian到上游本地仓库
6. dch -v添加changlog
7. git push 提 pr

#### 查询一个目录下包含某个字段的文件

```bash
grep -rin "mmm" path
```

- r 递归查找
- i 忽略大小写
- n 带上行号

#### vim 保存一个需要管理员权限的文件

```bash
:w !sudo tee %

```

- `:`：进入命令行模式。
- `w`：表示写入（保存）文件。
- `!`：表示执行外部命令。
- `sudo`：以超级用户（管理员）的权限执行命令。
- `tee`：将标准输入内容写入文件，并同时将其输出到标准输出。
- `%`：表示当前编辑的文件名。

#### dbus-monitor监听DBus

```bash
sudo dbus-monitor --system "interface=com.deepin.api.LocalHelper"
```

#### 删除dpkg-buildpackage时产生的临时文件

```bash
fakeroot debian/rules clean
```

fakeroot 假装使用root运行命令

#### 服务器编译命令

```bash
# 安装所需工具
sudo yum install yum-utils
# 解压出SPECuostest
rpm -ivh -D "_topdir `pwd`" *.rpm
# 解压出源码
rpmbuild -bp -D "_topdir `pwd`" SPEC/*.spec
# 编译打包
rpmbuild -ba -D "_topdir `pwd`" SPEC/*.spec
```

#### pam问题和修改方案

1. 显示权限拒绝  → 修改pam配置文件确定返回值为7(鉴定错误)，在dde-lock中根据返回值修改展示给用户的信息为密码错误
2. 账户解锁后需要输入两次正确的密码才能够验证通过→暂无解决方案，应该是dde-lock自身的问题
3. 登陆界面没有显示还有多少秒解锁→因为将错误信息固定设置为密码错误，拿不到这个信息
4. 使用pam是不是就没有办法知道当前账户是不是已锁定的状态
5. 账户已经锁定应该也有一个返回值

#### pam认证模块介绍

1. `pam_unix.so`：这是用于基本用户名和密码认证的标准模块。它会检查系统的本地账户数据库来验证用户提供的用户名和密码
2. `pam_env.so`：这个模块用于设置环境变量。它允许在用户登录时设置特定于用户或会话的环境变量。这些环境变量可以影响用户会话中的行为和配置。
3. `pam_faillock.so`：这个模块用于实施登录失败锁定机制。它可以配置为在一定数量的登录失败后锁定用户帐户，以保护系统免受暴力破解攻击。
4. `pam_fprintd.so`：这个模块允许使用指纹进行身份验证。它与指纹识别硬件和相关的软件服务集成，以允许用户使用指纹进行登录验证。
5. `pam_sss.so`：这个模块用于与 System Security Services Daemon (SSSD) 集成，提供对远程用户身份验证和授权的支持。SSSD 是一个用于身份管理的系统守护进程，它允许系统与 LDAP、Active Directory 等身份管理服务进行集成。
6. `pam_succeed_if.so`：这个模块允许在认证过程中根据条件执行成功或失败的操作。它可以用于基于用户、组、主机等属性来确定认证成功或失败的条件。
7. `pam_deny.so`：这个模块用于明确拒绝访问。它可以配置为在满足特定条件时立即拒绝用户访问，而不进行后续认证步骤。
8. `pam_pwhistory.so`：这个模块用于实施密码历史策略。它可以配置为检查新密码是否与先前使用过的密码相似，以强制用户使用不同的密码。

#### pam配置介绍

1. `auth`：这个字段用于配置认证模块，用于验证用户的身份。常见的模块包括 `pam_unix.so`（用于使用系统的本地账户数据库认证）、`pam_ldap.so`（用于与 LDAP 服务器集成认证）、`pam_google_authenticator.so`（用于双因素认证）等。
2. `account`：这个字段用于配置账户管理模块，用于检查用户账户的状态和权限。常见的模块包括 `pam_unix.so`（用于本地账户）、`pam_ldap.so`（用于 LDAP 账户）、`pam_access.so`（用于基于访问控制规则的访问控制）等。
3. `password`：这个字段用于配置密码管理模块，用于验证和修改用户密码。常见的模块包括 `pam_unix.so`（用于本地密码）、`pam_ldap.so`（用于 LDAP 密码）、`pam_cracklib.so`（用于密码复杂性检查）等。
4. `session`：这个字段用于配置会话管理模块，用于在用户登录和注销时执行特定的操作。常见的模块包括 `pam_unix.so`（用于本地会话管理）、`pam_systemd.so`（用于与 systemd 集成的会话管理）、`pam_limits.so`（用于设置用户资源限制）等。

##### 行为关键字

1. `required`：它的成功是必须的。如果一个 `required` 模块失败了，整个认证过程会立即失败，并且用户将无法通过认证。这种方式适用于强制性的认证步骤，比如验证密码的正确性。
2. `requisite`：类似于 `required`，但如果模块失败，认证链将立即终止并拒绝认证，不会继续执行后续的模块。
3. `include`：用于包含其他 PAM 配置文件中的模块定义。这使得可以在多个服务或应用程序的配置文件之间共享模块定义。
4. `sufficient`：它的成功不是必须的。如果一个 `sufficient` 模块成功了，后续的认证模块将不再被执行，认证过程将立即成功。若 `sufficient` 模块失败了，认证过程会继续执行
5. `optional`：`optional` 模块表示它的成功是可选的。无论 `optional` 模块成功与否，认证过程都会继续进行。这种方式通常用于提供额外的认证检查
6. 配置前面有`-`：行的开头带有 - 符号表示该模块的执行结果不会影响整个认证链的成功或失败。这种行为通常用于附加的日志记录或其他操作，但不应该影响认证本身的结果。

可以使用中括号细化行为，代替行为关键字

```bash
auth [success=done new_authtok_reqd=ok default=die] pam_unix.so

```

1. `success=module`：指定如果模块成功，则执行后续的模块，如果模块失败，则跳转到指定的 `module` 执行。
2. `success=done` 表示如果模块成功，整个认证链将立即结束（`done`）
3. `fail=module`：指定如果模块失败，则执行后续的模块，如果模块成功，则跳转到指定的 `module` 执行。
4. `new_authtok_reqd=module`：指定如果需要更新认证令牌，则执行后续的模块，如果不需要，则跳转到指定的 `module` 执行。
5. `new_authtok_reqd=ok` 表示如果需要更新认证令牌，模块将返回成功（`ok`）。
6. `default=die` 表示如果模块失败，整个认证链将立即终止并拒绝认证（`die`）。
7. `default=module`：指定模块的默认行为，如果没有特定的条件满足，则跳转到指定的 `module` 执行。
8. `bad`：如果模块执行失败，则认证失败。 &#x20;
9. `ok`：如果模块执行成功，则认证成功。

```bash
[success=done new_authtok_reqd=done default=bad]：定义了条件行为。具体解释如下：
如果模块成功 (success)，认证链将立即结束 (done)。
如果需要更新认证令牌 (new_authtok_reqd)，认证链也将立即结束 (done)。这通常意味着密码过期或需要更改。
如果以上两种情况都不满足，认证链将被标记为失败 (bad)。
```

#### dde-dconfog命令要求

```bash
dde-dconfig --get -a org.deepin.dde.wloutput.daemon -r org.deepin.dde.wloutput.daemon -k brightnessAdjustType
```

#### 查看是否有按键的事件上报

```bash
sudo apt install libinput-tools;sudo libinput debug-events --show-keycode
```

#### 删除当前目录下面的所有文件，但不包括文件夹

```bash
find . -maxdepth 1 -type f -delete

```

- `maxdepth` 最大深度，这里就只匹配当前文件夹
- `type` 匹配类型，这里是文件
- `delete` 将匹配到的内容删除

#### Python 信任阿里源，加快下载速度

```bash
./pip install pdf2image -i http://mirrors.aliyun.com/pypi/simple/ --trusted-host mirrors.aliyun.com
```

配置pip源

打开或创建 `~/.pip/pip.conf`

添加以下内容：

```bash
[global]
index-url = https://mirrors.aliyun.com/pypi/simple/
```

#### 管理有多个版本的python

```bash
sudo update-alternatives --install /usr/bin/python python /usr/bin/python3.8 1

```

#### ssh 不限制时间

```bash
sudo vim /etc/ssh/sshd_config
ClientAliveInterval=0
ClientAliveCountMax=30

```

- ClientAliveInterval 定时向客户端发送请求,设置为0,表示不请求
- ClientAliveCountMax  客户端未响应的最大次数

#### DDE推送翻译

1. 替换tx二进制
2. 一般情况下执行项目根目录下面的`update`脚本即可，推送需要翻译的内容到翻译网站
3. 通知翻译的同事进行翻译
4. `tx pull -a -f --branch m20`拉取翻译

#### github Https 方式克隆项目

```bash
https://cli.github.com/manual/gh_auth_login
```

然后登陆即可

#### 安装virtualBox

```bash
# debian系
# 安装KVM、libvirt和virt-manager
sudo apt install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virt-manager

# 启动libvirtd服务，并将其设置为开机启动
sudo systemctl start libvirtd
sudo systemctl enable libvirtd

# 为了让非根用户管理虚拟机，你需要将用户添加到libvirt组
sudo usermod -aG libvirt $(whoami)

```

#### 两主机之间测速

```bash
1. 安装iperf3
2. 服务端运行 iperf3 -s
3. 客户端运行 iperf3 -c ip<:port>
4. 自动进行测速
```

#### dbus-send 设置属性

```bash
dbus-send --print-reply --dest=org.deepin.dde.Appearance1 /org/deepin/dde/Appearance1 org.freedesktop.DBus.Properties.Set string:"org.deepin.dde.Appearance1" string:"QtScrollBarPolicy" variant:uint32:2
```
