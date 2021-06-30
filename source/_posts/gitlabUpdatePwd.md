---
title: gitlab修改root密码
top: false
cover: false
toc: true
mathjax: true
date: 2021-06-30 22:37:22
password:
summary:
tags: Linux
categories: Gitlab
---

### gitlab修改root密码
```sh
gitlab-rails console -e production

// 查询root用户
> user = User.where(username:"root").first
=> #<User id:1 @root>

// 修改密码
> user.password = "password"
=> "password"

// 保存
> user.save!
Enqueued ActionMailer::MailDeliveryJob (Job ID: 1baf8589-c259-4287-bb21-345c862c1935) to Sidekiq(mailers) with arguments: "DeviseMailer", "password_change", "deliver_now", {:args=>[#<GlobalID:0x00007f821c58df68 @uri=#<URI::GID gid://gitlab/User/1>>]}
=> true

exit
```


![getlab-token](getlab-token.png)
