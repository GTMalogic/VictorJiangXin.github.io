---
layout: artical
title: github使用相关
tags: Github
categories: 笔记
---
# 常用操作及代码
* 生成SSH key 
	* ssh -keygen -t rsa
	* ssh -T git@github.com  
	
* 克隆版本库 git clone <url>  
* 上传文件  
	* git add <文件名，如果是全部，可以用add .>  
	* git commit -m "文件描述"  
	* git push origin master （把本地代码推送到远程master分支）
	* git pull -u origin master (把远程代码拉下，保证代码同步)  
	
* 将本地项目与Github上的一个项目进行关联。先切换到目的目录，执行下面命令,把远程仓库的名字命名为origin    
` git remote add origin git@github.com:<USERNAME>/<User.resource>.git`  
* 提交项目前，注意！！！一定要先设置用户名与邮箱  
```
git config —global user.name "<your name>"
git config —global user.email "<your email>"
```  
* 创建分支  
`git checkout -b <branch name>`  

# 本地已有版本库，要推送至Github
首先，Github上已有一个Repository, `git@github.com:VictoryJiangXin/mShell.git`
然后，进入目录，比如f:\mShell
然后命令步骤如下：
```
git init
git remote add <Repository's name> git@github.com:VictoryJiangXin/mShell.git
git add <the file you need>
git commit -m "<the description of commit>"
git push -u <Repository's name> master
```

