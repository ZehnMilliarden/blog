---
layout: default
title: GitLab
nav_order: 3
description: ""
guid: e5837de6-dcd8-406e-9556-b7f626bc633f
parent: 053993f5-2abe-48ce-9037-0c4946e7b3c1
permalink: /cs/dev/gitlab
---

# Gitlab
{: .no_toc }

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
- TOC
{:toc}
</details>

---

## 安装依赖
```bash
yum -y install policycoreutils openssh-server openssh-clients postﬁx policycoreutils-python
systemctl enable sshd && sudo systemctl start sshd
systemctl enable postfix && systemctl start postfix
```

## 安装git包
```bash
rpm -ivh gitlab-ce-12.4.2-ce.0.el6.x86_64.rpm
```

## 重新配置gitlab
    vim /etc/gitlab/gitlab.rb
    # 配置gitlab url和开放端口
    external_url 'https://gitlab.xxx.com'
    nginx['listen_port']=80 # 或者 443 端口

## gitlab 进行重新配置，重启gitlab 服务
```bash
gitlab-ctl reconfigure && gitlab-ctl restart
```

## 开放端口 
```bash
firewall-cmd --zone=public --add-port=80/tcp --permanent
```

## 修改密码
```bash
gitlab-rails console -e production
user = User.where(id: 1).first （此User为root用户）
user.password = 'secret_pass' user.password_confirmation = 'secret_pass'
user.save!
exit
```