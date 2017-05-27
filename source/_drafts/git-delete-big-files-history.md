---
title: 如何删除不用文件的git的log
date: 2017-03-03 10:44:18
tags:
---

## 删除 git 中不用的 logs 文件
```bash
git filter-branch --force --index-filter 'git rm --cached --ignore-unmatch data/*.*' --prune-empty --tag-name-filter cat -- --all
```
```bash
git filter-branch --force --index-filter 'git rm --cached --ignore-unmatch font/*.*' --prune-empty --tag-name-filter cat -- --all
```
```bash
git filter-branch --force --index-filter 'git rm --cached --ignore-unmatch font/dejavu-serif/*.*' --prune-empty --tag-name-filter cat -- --all
```
```bash
git filter-branch --force --index-filter 'git rm --cached --ignore-unmatch notebooks/*.*' --prune-empty --tag-name-filter cat -- --all
```
```bash
git filter-branch --force --index-filter 'git rm --cached --ignore-unmatch models/*.*' --prune-empty --tag-name-filter cat -- --all
```
  

## 上传分支
```bash
git push origin develop --force
git push origin master --force
```

## 删除本地 .git 内的历史文件
```bash
rm -rf .git/refs/original/
git reflog expire --expire=now --all
git gc --prune=now
git gc --aggressive --prune=now
```