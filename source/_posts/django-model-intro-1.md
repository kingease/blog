---
title: Django系列：1. Model的介绍
date: 2018-04-04 14:42:46
tags:
categories:
    - django
    - model
---

Model是对你的数据进行定义的源文件。它包含存储数据的基本的字段(Fields)和行为(behaviors)。一般而言，一个Model对应数据库的一张表(database table)。

基本上：
- 每一个Model都是一个python类， 具体的，它是`django.db.models.Model`的子类。
- model的每一个属性(attribute)表示数据库的一个字段(field)。
- Django会根据上述信息自动生成的数据库访问的API; see Making queries.

## 样例
这个例子model定义一个**Person**, 包含一个**first_name**和一个**last_name**：
``` python
from django.db import models

class Person(models.Model):
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=30)
```
**first_name**和**last_name**是model的字段(field)。每一个字段(field)由一个类的属性(attribute)定义, 每个属性(attribute)对应数据库表的一列(column)。

上面**Person**(模型)将和下面的SQL创建表的效果是相同的：
``` SQL
CREATE TABLE myapp_person (
    "id" serial NOT NULL PRIMARY KEY,
    "first_name" varchar(30) NOT NULL,
    "last_name" varchar(30) NOT NULL
);
```

## 使用模型


## 字段(Field)

### 字段类型

### 字段选项

### 自动主键字段

### 字段详细注释(verbose)

## 表的关联(Relationships)

### 多对一(Many-to-one)

### 多对多(Many-to-many)

### Extra field on 多对多

### 一对一(One-to-one)


## 字段名的限制

## **Meta** 选项
用一个**class Meta**内部类来定义模型(Model)的元数据：
``` python
from django.db import models

class Ox(models.Model):
    horn_length = models.IntegerField()

    class Meta:
        ordering = ["horn_length"]
        verbose_name_plural = "oxen"
```
模型的元数据是模型非字段类型的属性，比如
- 排序选项(ordering)
- 表名称(db_table)
- 表的可读的单数或复数名(verbose_name,verbose_name_plural)。#可以理解为表的注释
这些都是不是必须的，**class Meta**完全是可选的。

## Model的属性(attributes)

## Model的方法(methods)
在model上定义自定义方法为你的对象增加自定义的行级别的方法(row-level functionality)。
相比`Manager`方法更倾向于做"表"范围的事，model方法执行在特定的模型对象上。

### 重写预定义的model方法

### 执行自定义的SQL

## Model的继承(inheritance)

### 抽象基类

### 多表(multi-table)继承

### 代理(proxy)模型

### 多(multiple)继承

## 在package中管理Model


## 参考
1. https://docs.djangoproject.com/en/1.11/topics/db/models/