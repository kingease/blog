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
2. 和`manage.py`平级的目录是`project`目录。
3. `app` 目录位于 `project` 目录下。


### url路由