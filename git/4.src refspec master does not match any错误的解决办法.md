在使用git bash指令将项目上传到github时，总是遇到一些错误无法解决。
下面是我遇到的一个问题


```

error: src refspec master does not match any.
error: failed to push some refs to 'git@github.com:hahaha/ftpmanage.git'
```

 问题的内容是：

错误：SRC ReFSPEC主控器不匹配任何。  
错误：未能将某些引用推到'git @ Github.com：HaHaa/ftpMal.git’

也就是仓库为空。

解决办法：

利用git add xxx.py 指令，将所有的文件全部都添加，然后再进行git commit -m "init"将所有的文件commit,


```git
git commit -m "init"
```

然后在执行
```git
$ git remote add origin xxxxxxxx.git
```
最后使用
```git
$ git push -u origin master
```

大功告成。

# 总结
总结：
其实只需要进行下面几步就能把本地项目上传到Github

     1、在本地创建一个版本库（即文件夹），通过git init把它变成Git仓库；

     2、把项目复制到这个文件夹里面，再通过git add .把项目添加到仓库；

     3、再通过git commit -m "注释内容"把项目提交到仓库；

     4、在Github上设置好SSH密钥后，新建一个远程仓库，通过git remote add origin https://github.com/guyibang/TEST2.git将本地仓库和远程仓库进行关联；

     5、最后通过git push -u origin master把本地仓库的项目推送到远程仓库（也就是Github）上；（若新建远程仓库的时候自动创建了README文件会报错，解决办法看上面）。