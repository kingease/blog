---
title: 如何使用hexo创建github page博客
date: 2017-02-21 23:17:58
tags: [教程, hexo]
---
网上遇到很多把博客制作很精美的个人网站，不由眼前一亮。现在有了hexo，我们自己也可以创建自己博客。不需要申请空间，不需要申请域名。很赞的一件事！但搭建hexo还是需要花费一番功夫的，记录一下大体的步骤。还有很多操作并不了解，有些坑还是踩了满脚的，随着使用多了，再慢慢丰富。             

## 第一步 安装 git 和 node

## 第二步 安装 hexo
```bash
npm install -g hexo-cli
```

## 第三步 创建博客目录
```bash
hexo init <blog_dir>
cd <blog_dir>
npm install
# start local server to preview
hexo s
```
如果想预览 draft 下的博客, 关于draft(草稿)参考 [创建新的blog草稿](#第八（二）步-创建新的blog草稿)
```
hexo s --draft
```

## 第四部 配置 github page 的仓库
```YAML
deploy:
  type: git
  repo: git@github.com:kingease/kingease.github.io.git
  branch: master
```
注意要安装npm包`npm install hexo-deployer-git --save`

## 第五步 上传内容
```bash
hexo g
hexo d
```

## 第六步 修改主题
```
git clone https://github.com/iissnan/hexo-theme-next themes/next
```
在根目录下的`_config.yml`文件内修改项：
```YAML
theme: next
```

## 第七步 增加 tag
```bash
hexo new page 'tags'
```
在`source/tags`目录下会有一个`index.md`文件，修改内容为
```markdown
title: tags
date: 2017-02-21 23:03:58
type: "tags"
comments: false
---
```
在`theme`文件夹下`_config.yml`中确保
```
menu:
    tags: /tags
```

## 第八（一）步 创建新的blog
```bash
hexo new "my blog"
```
在`tags`下以YAML的方式增加标签即可，如
```YAML
tags:
    - tag1
    - tag2
```

## 第八（二）步 创建新的blog草稿
八(一)中的操作是创建直接可以发布的blog，但是有些blog我们可能需要时间完善到一定程度才想把它发布出去，这时草稿就可以为我们所用。我们创建的草稿在`deploy`时不会被发布出去，直到我们将它`publish`后它才会被展现出来。
1. 需要创建暂不发布的草稿文件
```bash
hexo new draft "my new draft"
```
2. 如果想发布这个草稿
```bash
hexo publish draft "my new draft"
```
3. 本地预览草稿需要
```
hexo s --draft
```
4. 如果需要撤回某些文章
    - 创建`source/_drafts`文件夹（如果没有的话）
    - 将暂时不发布的文章移到该目录下 

## 支持mathjax
mathjax 在 theme 里设置， 这里next在`_config.yml`里将mathjax下的enable设置成true。在每个单独的blog 头部增加字段`mathjax: true`如
```
title: optimization in computer vision
mathjax: true
tags:
```