---
layout: post
title:  "在Github上创建自己的博客"
date:   2014-03-23 12:19:49
month:  "03"
day:    23
moto: “要对得起自己吹过得牛B“
---

####1. 在<a href="https://github.com">Github</a>上注册一个账号
####2. 在电脑上安装git
####3. 生成SSH Key

```bash
$ ssh-keygen -t rsa -C "<youremail>"
```

####4. 将SSH Key添加到Github
####5. 在Github上建立仓库blog
####6. 在本地建立blog目录

```bash  
$ mkdir blog
$ cd blog
$ git init
$ git checkout --orphan gh-pages
```

####7. 下载<a href="http://jekyllthemes.org/">jekyll</a>主题模板,把模板根目录下所有文件拷贝到blog
####8. 在<a href="http://www.godaddy.com/">godaddy</a>注册域名
####9. 在<a href="https://www.dnspod.cn/">dnspod</a>上添加两条A记录,记录值参考<a href="https://help.github.com/articles/my-custom-domain-isn-t-working">这里</a>
####10. 去godaddy修改Nameservers为dnspod的NS

```
f1g1ns1.dnspod.net
f1g1ns2.dnspod.net
```

####11. 在blog根目录下建立文件CNAME,在文件中指定你的域名

```
youngjd.com
```

####12. 提交 

```bash 
$ git add .
$ git commit -m "commit blog"
$ git remote add origin https://github.com/<username>/blog.git
$ git push origin gh-pages
```

<br/>等待约10分钟.Bingo!

