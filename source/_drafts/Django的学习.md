---
title: Django的学习
date: 2018-03-27 11:01:51
tags:
    - Django
---
（这个文档可能会有较大的变动）
Django 是目前公司开发的核心框架，需要对它进行深入的学习。

## 教程层面
1. `django-admin` 和 `python manage.py` 是等效的。
1. `django-admin startproject` 生成的根目录（该目录下会有一个`manage.py`文件）是与项目无关的。
1. `django-admin satrtproject <project_name> [.|directory]` 最后一个是指定创建的project放在哪个目录下，如果没有就创建一个目录，目录的名字也是`project_name`，所以，根目录的名称并不重要。
2. 和`manage.py`平级的目录是`project_name`目录。
3. `app` 目录位于 `project_name` 目录下。
3. `python manage.py startapp <app_name>` 创建`app_name`。


### 模板继承(template inheritance)
1. `{ % extends "base.html" % }` 使用父模板
2. `{ % block content % }` 和 `{ % endblock content % }` 在父模板和子模板中定义和重定义块内容


### QuerySets


### 基于function的View和基于class的View


### url路由



## reference
1. https://github.com/codingforentrepreneurs/Try-Django-1.11