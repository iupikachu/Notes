## git



git push origin master 

将文件push到远程的master分支上 如果本机验证过就ok



> git push origin --delete fxx  git删除远程分支





> git 上传新建项目

git init

git add src

git commit -m "first commit"

git remote add origin https://github.com/mw138/TestGit.git





git revert xxxxxx 



c3

c2

c1

git revert c2的版本号

这样会生成新的版本号但是是c1的内容

如果要回到c2的commit 就 git revert c3

> 你要回退到哪一次push 就revert他上一次的commit  revert合并冲突后再commit push



强制回退到当前版本

git reset --hard  想回退的版本号

git push origin master   一定会报冲突 所以加上--force

git push origin master --force

![image-20201205150137235](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201205150137235.png)







git 恢复被删除的文件

```bash
git rm b.txt 

### 对于 删除b.txt 只add 而没有 commit的情况
git reset HEAD b.txt
git checkout b.txt   

### 对于 删除b.txt commit了的情况
git log 查看 提交的sha值
git reset HEAD 08b4f0a8008038098d95630b4e32e29df7548b16
git checkout 08b4f0a8008038098d95630b4e32e29df7548b16 

```

