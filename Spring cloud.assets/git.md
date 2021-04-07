## git 



```bash
chenqipeng@chenqipengdeMacBook-Pro SM9 % git remote add origin https://github.com/iupikachu/SM9-starter.git
fatal: remote origin already exists.
chenqipeng@chenqipengdeMacBook-Pro SM9 % git branch -M main
chenqipeng@chenqipengdeMacBook-Pro SM9 % git remote add origin https://github.com/iupikachu/SM9-starter.git
fatal: remote origin already exists.
chenqipeng@chenqipengdeMacBook-Pro SM9 % git branch -a
* main
  remotes/origin/master
chenqipeng@chenqipengdeMacBook-Pro SM9 % git remote add origin https://github.com/iupikachu/SM9-starter.git

fatal: remote origin already exists.
chenqipeng@chenqipengdeMacBook-Pro SM9 % 
chenqipeng@chenqipengdeMacBook-Pro SM9 % git remote rm origin
chenqipeng@chenqipengdeMacBook-Pro SM9 % git branch -a
* main
chenqipeng@chenqipengdeMacBook-Pro SM9 % git remote add origin https://github.com/iupikachu/SM9-starter.git
chenqipeng@chenqipengdeMacBook-Pro SM9 % git push -u origin main
Enumerating objects: 207, done.
Counting objects: 100% (207/207), done.
Delta compression using up to 8 threads
Compressing objects: 100% (172/172), done.
Writing objects: 100% (207/207), 3.69 MiB | 1.50 MiB/s, done.
Total 207 (delta 26), reused 0 (delta 0)
remote: Resolving deltas: 100% (26/26), done.
To https://github.com/iupikachu/SM9-starter.git
 * [new branch]      main -> main
Branch 'main' set up to track remote branch 'main' from 'origin'.

```



>  git remote rm origin 删除远程分支