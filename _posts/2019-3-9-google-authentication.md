---
layout: post_layout
title: 公钥 + Google Authenticator 两部验证
time: 2019年3月9日 星期六
location: 文山
pulished: true
excerpt_separator: "```"
---

### 前言

使用基于公钥加`Google Authenticator`两部验证的方式，增加 ssh（mosh也可以直接使用）远程登陆的安全性。

### 修改配置

#### centos 系配置

+ 安装 `Google Authenticator`


```bash
sudo yum install epel-release; sudo yum makecache all
sudo yum install -y  google-authenticator.x86_64
```

+ 与 pam 相关的 ssh 配置文件

/etc/pam.d/sshd

注释掉`auth substack password-auth`，因为只使用公钥进行验证，不使用密码验证

末尾添加`auth required pam_google_authenticator.so`，启用`Google Authenticator`

配置文件示例(注意缩进)：

```
#%PAM-1.0
auth       required     pam_sepermit.so
#auth       substack     password-auth
auth       include      postlogin
# Used with polkit to reauthorize users in remote sessions
-auth      optional     pam_reauthorize.so prepare
account    required     pam_nologin.so
account    include      password-auth
password   include      password-auth
# pam_selinux.so close should be the first session rule
session    required     pam_selinux.so close
session    required     pam_loginuid.so
# pam_selinux.so open should only be followed by sessions to be executed in the user context
session    required     pam_selinux.so open env_params
session    required     pam_namespace.so
session    optional     pam_keyinit.so force revoke
session    include      password-auth
session    include      postlogin
# Used with polkit to reauthorize users in remote sessions
-session   optional     pam_reauthorize.so prepare
auth       required     pam_google_authenticator.so
```

+ 修改 sshd 配置文件

/etc/ssh/sshd_config

将 `ChallengeResponseAuthentication` 的值改为 `yes`；`PermitRootLogin`的值改为`no`,禁止 root 远程登陆；`PasswordAuthentication`的值改为`no`，禁止使用密码登陆从而使用密钥登陆；末尾添加 `AuthenticationMethods publickey,keyboard-interactive`，设置两步验证的顺序

配置文件示例：

```
...
ChallengeResponseAuthentication yes
Permitrootlogin no
Passwordauthentication no
...
Authenticationmethods publickey,keyboard-interactive
```

重启服务

```bash
sudo systemctl restart sshd
```

+ Googel Authenticator 设置，在需要远程使用两步验证的用户下配置，普通用户及root都可以，但是建议在普通用户下操作，以降低安全风险

```bash
google-authenticator

Do you want authentication tokens to be time-based (y/n) y
下面是二维码及privite key，可以直接扫描二维码导入也可以使用直接添加private key的方式导入移动客户端

Do you want me to update your "/home/skylens/.google_authenticator" file? (y/n) y

Do you want to disallow multiple uses of the same authentication
token? This restricts you to one login about every 30s, but it increases
your chances to notice or even prevent man-in-the-middle attacks (y/n) y

By default, a new token is generated every 30 seconds by the mobile app.
In order to compensate for possible time-skew between the client and the server,
we allow an extra token before and after the current time. This allows for a
time skew of up to 30 seconds between authentication server and client. If you
experience problems with poor time synchronization, you can increase the window
from its default size of 3 permitted codes (one previous code, the current
code, the next code) to 17 permitted codes (the 8 previous codes, the current
code, and the 8 next codes). This will permit for a time skew of up to 4 minutes
between client and server.
Do you want to do so? (y/n) n

If the computer that you are logging into isn't hardened against brute-force
login attempts, you can enable rate-limiting for the authentication module.
By default, this limits attackers to no more than 3 login attempts every 30s.
Do you want to enable rate-limiting? (y/n) y
```

+ 登陆验证

使用 ssh 或者 mosh 远程连接服务器

#### debian 系

debian 系的操作系统设置只是 pam 相关的部分配置文件有一些差异其他部分完全相同

注释掉 `@include common-auth`，如果还需要验证密码则不需要注释；末尾添加 `auth required pam_google_authenticator.so
`，启用两步验证

配置示例：

```
# PAM configuration for the Secure Shell service

# Standard Un*x authentication.
# @include common-auth

# Disallow non-root logins when /etc/nologin exists.
account    required     pam_nologin.so

# Uncomment and edit /etc/security/access.conf if you need to set complex
# access limits that are hard to express in sshd_config.
# account  required     pam_access.so

# Standard Un*x authorization.
@include common-account

# SELinux needs to be the first session rule.  This ensures that any
# lingering context has been cleared.  Without this it is possible that a
# module could execute code in the wrong domain.
session [success=ok ignore=ignore module_unknown=ignore default=bad]        pam_selinux.so close

# Set the loginuid process attribute.
session    required     pam_loginuid.so

# Create a new session keyring.
session    optional     pam_keyinit.so force revoke

# Standard Un*x session setup and teardown.
@include common-session

# Print the message of the day upon successful login.
# This includes a dynamically generated part from /run/motd.dynamic
# and a static (admin-editable) part from /etc/motd.
session    optional     pam_motd.so  motd=/run/motd.dynamic
session    optional     pam_motd.so noupdate

# Print the status of the user's mailbox upon successful login.
session    optional     pam_mail.so standard noenv # [1]

# Set up user limits from /etc/security/limits.conf.
session    required     pam_limits.so

# Read environment variables from /etc/environment and
# /etc/security/pam_env.conf.
session    required     pam_env.so # [1]
# In Debian 4.0 (etch), locale-related environment variables were moved to
# /etc/default/locale, so read that as well.
session    required     pam_env.so user_readenv=1 envfile=/etc/default/locale

# SELinux needs to intervene at login time to ensure that the process starts
# in the proper default security context.  Only sessions which are intended
# to run in the user's context should be run after this.
session [success=ok ignore=ignore module_unknown=ignore default=bad]        pam_selinux.so open

# Standard Un*x password updating.
@include common-password
auth required pam_google_authenticator.so
```
