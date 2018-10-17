# git记录

- git branch -a获取不到远程分支  
  解决方案：先运行一下：`git fetch`，然后再运行`git branch -a`就能看到新的远程分支了；  
  git fetch：取回所有远程分支的更新，感觉有点类似于update一下

- git版本发布，执行命令如下：  
  ``` 
  git tag -a v1.0 -m "v1.0版本发布"
  git push origin v1.0
  ```

- git fix分支合并master
  先切换到master分支上，执行：`git merge fix`，然后再推送到远程上：`git push origin master`即可
  