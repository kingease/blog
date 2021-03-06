---
title: Django系列：生产环境下django和wsgi
date: 2018-04-08 14:59:21
tags:
categories:
    - django
    - deployment
---
Django主要的部署平台是WSGI, 在web服务和应用方面的python标准。
## 项目侧准备
Django的`startproject`管理命令生成了一个默认的WSGI配置文件，可以根据project的需要进行修改。

## 使用Apache和**mod_wsgi**
如果你对部署Django是新手，推荐首先使用`mod_wsgi`。在大多数情况下，它是最简单、最快速和最稳定的部署选择。
Django包含一个轻量级的web server，仅仅为了测试而用。所以，如果你不打算部署生产级别的Django，你不需要搭建Apache服务器。

如果你想让Django提供生产级别的服务，就需要使用Apache和[`mod_wsgi`](http://www.modwsgi.org/)。
`mod_wsgi`有两种模式：embeded模式 和 daemon模式。
`mod_wsgi` 是一个 Apache 模块(module)，它可以搭载任何 Python WSGI 应用, 包括 Django。

### 安装和激活**mod_wsgi**

### Apache的基本设置
一旦你安装并激活`mod_wsgi`，就可以编辑你的Apache服务的`httpd.conf`文件，添加以下的配置。如果你使用的Apache版本老于2.4，请将下面配置中**`Require all granted`**换成**`Allow from all`**并在*上*面增加一行**`Order deny,allow`**

``` conf
WSGIScriptAlias / /path/to/mysite.com/mysite/wsgi.py
WSGIPythonHome /path/to/venv
WSGIPythonPath /path/to/mysite.com

<Directory /path/to/mysite.com/mysite>
<Files wsgi.py>
Require all granted
</Files>
</Directory>
```

## 使用Gunicorn
安装
```
pip install gunicorn
```
### 使用异步的workers
```
pip install greenlet
pip install gevent
pip install gunicorn[gevent]
```

## 使用uWSGI


## reference
1. https://docs.djangoproject.com/en/1.11/topics/install/#install-apache-and-mod-wsgi
2. https://docs.djangoproject.com/en/1.11/howto/deployment/wsgi/modwsgi/
3. https://docs.djangoproject.com/en/1.11/howto/deployment/wsgi/
