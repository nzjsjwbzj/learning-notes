# GIT

1.电脑配置一次GIT和github,gitee    新建SSH密钥

2.配置本地仓库关联到远程仓库（复制远程仓库的SSH) git remote add …… 可以关联多个平台

# add添加到暂存区，commit到本地仓库，pull到远程仓库

两个命令

![image-20251212150254915](./GIT.assets/image-20251212150254915.png)



HEAD是一个指针，指向当前最新一次提交的版本

![image-20251212150427825](./GIT.assets/image-20251212150427825.png)



3个配置文件，优先级3-2-1

![image-20251212152137830](./GIT.assets/image-20251212152137830.png)

# 查看命令



![image-20251212152349380](./GIT.assets/image-20251212152349380.png)



在项目文件里面打开GIT BASH

让GIT知道我是谁 global是全局信息，设置之后整个电脑上都回用这个名字

改为local的话就只是在这个项目里面使用

![image-20251212153104611](./GIT.assets/image-20251212153104611.png)



# git add

![image-20251212155344809](./GIT.assets/image-20251212155344809.png)

# git commit





![image-20251212155037923](./GIT.assets/image-20251212155037923.png)





![image-20251212155537650](./GIT.assets/image-20251212155537650.png)



![image-20251212160154560](./GIT.assets/image-20251212160154560.png)



# git diff

![image-20251212160730369](./GIT.assets/image-20251212160730369.png)

![image-20251212161045755](./GIT.assets/image-20251212161045755.png)

第一次手动加





![image-20251212161158560](./GIT.assets/image-20251212161158560.png)

第二次添加



所以比较的是暂存区和工作区的差异，没有更新暂存区，全部的内容都是+





# git status



![image-20251212162529899](./GIT.assets/image-20251212162529899.png)

![image-20251212162633913](./GIT.assets/image-20251212162633913.png)

暂存区里面放的还是之前add的内容，改动的内容没加进去，modified



# 远程仓库配置

## 选择协议



![image-20251212170250030](./GIT.assets/image-20251212170250030.png)



## SSH协议

先用这个生成密钥对

![image-20251212171027612](./GIT.assets/image-20251212171027612.png)

在C盘用户的.ssh里面

![image-20251212171001182](./GIT.assets/image-20251212171001182.png)



在GITEE上添加公钥

![image-20251212171652653](./GIT.assets/image-20251212171652653.png)

添加完成后测试是否连通



![image-20251212171614868](./GIT.assets/image-20251212171614868.png)



## 远程仓库关联到本地

### 1.克隆远程仓库到本地

也可以选择别人仓库的SSH链接

![image-20251212175005247](./GIT.assets/image-20251212175005247.png)

远程仓库连接

![image-20251212175057233](./GIT.assets/image-20251212175057233.png)



![image-20251212175316312](./GIT.assets/image-20251212175316312.png)





### 2.将已有的本地仓库和远程仓库进行关联

![image-20251212180359108](./GIT.assets/image-20251212180359108.png)





验证是否关联成功

![image-20251212180835794](./GIT.assets/image-20251212180835794.png)





再拉取一下，同步到远程仓库

拉取时要选择平台

![image-20251212182110819](./GIT.assets/image-20251212182110819.png)





# git pull





![image-20251212191632113](./GIT.assets/image-20251212191632113.png)

更新readme文件，拉取到本地



# git push





![image-20251212193238163](./GIT.assets/image-20251212193238163.png)



# 分支

## git branch 来查看分支

*表明当前正处于这个分支

![image-20251214135500558](./GIT.assets/image-20251214135500558.png)

## git branch 分支名字 创建分支





![image-20251214135137960](./GIT.assets/image-20251214135137960.png)



## git checkout/git switch 

切换所在的分支



![image-20251214135814776](./GIT.assets/image-20251214135814776.png)

![image-20251214140428733](./GIT.assets/image-20251214140428733.png)



## 在分支中创建文件

![image-20251214142648861](./GIT.assets/image-20251214142648861.png)

切换到主分支之后，就没有了



只有被commit提交到分支仓库的文件，切换分支时才会不见	



## 合并

git merge

合并之前主分支

![image-20251214143720299](./GIT.assets/image-20251214143720299.png)



合并之后

![image-20251214143838184](./GIT.assets/image-20251214143838184.png)



## 合并冲突



当两个分支修改的是同一个地方时，就有可能出现冲突。两个分支不能改动同一个地方

## 删除分支

git branch -d 如果要删除的分支已经合并回了主分支，说明不再需要，可以用这个，否则GIT就要阻止

git branch -D没合并想强制删除就这个

![image-20251215125744800](./GIT.assets/image-20251215125744800.png)



# git标签

代表一个稳定的版本







![image-20251215133004137](./GIT.assets/image-20251215133004137.png)

指向之前的提交的话就要用git log来查看提交的ID，commit的哈希值是ID的前六位



![image-20251215133400522](./GIT.assets/image-20251215133400522.png)

### git tag

查看已经有的标签

## git show 标签名

查看详细的标签信息

![image-20251215134000421](./GIT.assets/image-20251215134000421.png)



## 本地标签



git标签默认只有自己能看到，还要推送到远程仓库才行

![image-20251215134410373](./GIT.assets/image-20251215134410373.png)





git push origin <>推送单个标签

git push origin --tags推送所有标签





![image-20251215135244114](./GIT.assets/image-20251215135244114.png)



## 删除本地和远程标签

第一个是删除本地标签，第二个是远程

![image-20251215135904061](./GIT.assets/image-20251215135904061.png)





# 撤销修改



## git checkout --

该命令用于本地工作区撤销修改



改了但是没加入到缓存区



![image-20251215155508308](./GIT.assets/image-20251215155508308.png)



## git reset

![image-20251215160331592](./GIT.assets/image-20251215160331592.png)

来撤销提交到暂存区的代码到工作区，但是内容没有撤销，要用checkout –来撤销





如果已经commit到本地，也可以

![image-20251215160714350](./GIT.assets/image-20251215160714350.png)



![image-20251215161059820](./GIT.assets/image-20251215161059820.png)





## git revert 

![image-20251215163530302](./GIT.assets/image-20251215163530302.png)







![image-20251215164050989](./GIT.assets/image-20251215164050989.png)







