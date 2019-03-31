## 本地开发及调试

### 创建应用

到 [https://trello.com/power-ups/admin](https://trello.com/power-ups/admin) 创建一个应用

填写应用相关资料，填写应用`Iframe connector URL`地址为`https://helloworld.com/`

到项目`power-ups`中开启应用

### 下载代码到本地

```shell
cd you_local_folder # 本地目录位置，nginx 配置中也会用到
git clone https://github.com/Micjoyce/power-up-template.git
```

### 修改本地 DNS

```shell
vim /etc/hosts
```

修改本地 127.0.0.1 DNS, 增加 helloworld.com 域名解析到本地

```shell
 127.0.0.1 localhost helloworld.com
```

### 安装 nginx

```shell
brew install nginx
```

#### 配置SSL证书并启用HTTPS

进入nginx目录创建 ssl 目录

```shell
cd /usr/local/etc/nginx
mkdir ssl
cd ssl
```

生成server.key

`openssl genrsa -des3 -out server.key 2048`

以上命令是基于des3算法生成的rsa私钥，在生成私钥时必须输入至少4位的密码。

生成无密码的server.key

`openssl rsa -in server.key -out server.key`

生成CA的crt

`openssl req -new -x509 -key server.key -out ca.crt -days 3650`

基于ca.crt生成csr

`openssl req -new -key server.key -out server.csr`

命令的执行过程中依次输入国家、省份、城市、公司、部门及邮箱等信息。

生成crt（已认证）

`openssl x509 -req -days 3650 -in server.csr -CA ca.crt -CAkey server.key -CAcreateserial -out server.crt`

#### 添加 nginx 配置

```shell
cd /usr/local/etc/nginx/servers
vim helloworld.com.conf
```

将一下内容复制到 helloworld.com.conf 中

```shell
server {
    listen                      80;
    server_name                 helloworld.com;
    listen                      443 ssl;
    ssl_certificate             /usr/local/etc/nginx/ssl/server.crt;
    ssl_certificate_key         /usr/local/etc/nginx/ssl/server.key;
    ssl_session_cache           shared:SSL:1m;
    ssl_session_timeout         5m;
    ssl_protocols               SSLv2 SSLv3 TLSv1.2;
    ssl_ciphers                 HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers   on;
    location / {
        root   you_local_folder/power-up-template;
        index  index.html index.htm;
    }
}
```

#### 启动 nginx

```shell
nginx -t
nginx  # 或者 nginx -s reload
```

#### 信任 ssl

在浏览器中访问 `https://helloworld.com` 信任 ssl 证书

### 刷新 trello 页面接口看到相关应用入口

### Trello 应用初步调研


1.用户创建 power up 应用，并填写相应的应用地址

2.Trello Web 客户端通过 **隐藏** 的 iframe 加载应用

3.应用通过在页面中内嵌 [sdk](https://p.trellocdn.com/power-up.js) 的 initialize 函数注册初始化应用能力，具体有以下几种：

- 'attachment-sections'
- 'attachment-thumbnail'
- 'board-buttons'
- 'card-badges'
- 'card-buttons'
- 'card-detail-badges'
- 'card-from-url'
- 'format-url'
- 'show-settings'
- 'authorization-status'
- 'show-authorization'

应用通过能力向 Trello Web 的应用支持模块添加配置

4.Trello Web 根据约定的应用开放配置在特定的入口显示应用

关于授权：
当 Web 请求第三方应用的数据资源时，有时是需要用户授权的，用户需要向第三方应用授权，并获取 access_token，并存储在 Trello Server 的 pluginData 中，后续 Trello Web 所有向第三方应用发起求均从 pluginData 读取用户的 access_token 数据，发送请求，并获的数据


# Trello Power-Up Template

Hey there 👋

This is a full featured sample Trello Power-Up. What does that mean for you? We hope it's a great starting point for you to fork, and make your own great Power-Up that makes Trello work even better for you.

Want to try this sample out right away without waiting? Go ahead and grab the connector URL (https://trello.github.io/power-up-template/index.html). If you've forked it, you should grab your fork's connector URL. And then head here:

👉  [https://trello.com/power-ups/admin](https://trello.com/power-ups/admin)

Select the Trello team you want to add the Power-Up to. Note: You need to be an admin of the Trello team to add custom Power-Ups to it.

Now click the `Create new Power-Up` button. If this is your first time creating a Power-Up, you'll need to agree to a "Joint Developer Agreement" first. After you have done that, you need to give your cool new Power-Up a name, and paste the connector URL in (the one you created earlier). You can read about what all of the fields are used for at [Managing Power-Ups](https://developers.trello.com/v1.0/docs/managing-power-ups).

Click `Save` and it's time to celebrate 🎉 🎊

Now when you look at the Power-Ups for any board in that team, your awesome new Power-Up will be available under the `Custom` section. All changes you commit and push to the repo will be reflected in the Power-Up!

---

Want more information about Power-Ups? 🤔

👉  [https://developers.trello.com/power-ups/intro](https://developers.trello.com/power-ups/intro)

We even have office hours you can sign up for if you want to talk to a real live person about your Power-Up. Just grab a slot that works for you on this [calendar](https://calendar.google.com/calendar/selfsched?sstoken=UU5DczNLUkNIbk5ifGRlZmF1bHR8YzJmZWM4YWM0NTgxMTE1NmRmMzgxNzMwODRjYzEwZGU). (Remember to add a bit about what you'd like help with when signing up for a slot).

---

Looking for a more _realistic_ example Power-Up? You may find the Trello Card Snooze Power-Up useful. 😴

👉  [Trello Card Snooze Glitch Project](https://glitch.com/edit/#!/trellocardsnooze)