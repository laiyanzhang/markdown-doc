# Git&Nvm

标签（空格分隔）： 技术栈

---

## 1.工作区、暂存区、版本库
- 工作区：可见文件夹
- 暂存区：.git内的暂存区，通过git add将文件写入暂存区
- 版本库：.git内的版本库，通过git commit -m将文件写入版本库

## 2.日志
- git log：更改日志 commit后是版本号
- git refolg：记载每一次命令，可以看到每次命令的版本号
- git log --graph --pretty=oneline --abbrev-commit：日志的简易版

## 3.版本控制
- git reset --hard 版本号 可回退到任意版本
- git checked --文件名  暂存区已提交后修改则为暂存区版本，暂存区未提交而修改则为版本库版本
- git reset HEAD 文件名 暂存区已提交未修改则回退到上一步的未提交而修改
- git add/rm：在工作区对文件进行删除或修改后都需要指令进行更新

## 4.远程仓库
- 生成SSH连接本地与github账户
- git remote add 远程仓库名字 远程仓库url：连接远程仓库
- git push -u(第一次推送时需要) 远程仓库名字 分支名字(master)(只有远端分支与本地分支相同情况下)
- git clone 远程仓库ssh/http
- git push <远程主机名> <本地分支名>:<远程分支名>

## 5.分支
- git switch -c 分支名称 = git checkout -b 分支名称 = git branch 分支名称 + git checkout 分支名称：创建分支并进入对应分支
- git merge 分支名称：在分支进行修改，merge后将修改内容直接并入主支
- 分支冲突：分支主支分别对某个文件进行修改，merge会产生冲突，手动修改冲突为相同内容提交即可完成修改
- git branch -d 分支名称：删除分支
- git merge --no-ff -m "merge with no-ff" 分支名称：删除分支后保留分支信息
- git stash：将工作区中文件缓存起来，工作区清空；git stash list：查看缓存工作区列表
- git stash apply 指定stash(没有则默认唯一stash) = git stash pop(恢复工作区同时删除stash)
- git cherry-pick 版本号：将对应版本号的修改赋值到当前分支

## 6.多人协作
- 对同一分支进行push时，前面有人已经push过的修改发生冲突，流程：
  - 指定pull的分支： git branch --set-upstream-to=origin/dev(远端分支) dev(本地分支)
  - 本地与远端合并并解决冲突：git pull合并远端本地再解决冲突
  - commit之后再进行push即可(在push之前git rebase可以简化提交路线)
- git pull + 修改冲突 + git add + git commit + git push 可以合并冲突

## 7.标签
- git tag name，name被标志到最新的commit上
- git tag name 版本号，name被标志到对应的版本号上
- git tag -d name：删除标签
- git push origin name：推送标签
    

## 8.提交规范
- feature: 新功能、新特性
- fix: 修改 bug
- perf: 更改代码，以提高性能
- refactor: 代码重构（重构，在不影响代码内部行为、功能下的代码修改）
- docs: 文档修改
- style: 代码格式修改, 注意不是 css 修改（例如分号修改）
- test: 测试用例新增、修改
- build: 影响项目构建或依赖项修改
- revert: 恢复上一次提交
- ci: 持续集成相关文件修改
- chore: 其他修改（不在上述类型中的修改）
- release: 发布新版本
- workflow: 工作流相关文件修改



## 9.公司提交规范
- feature(\#123): 新功能，在原有的功能做扩展也算新增。例如原来支持json 扩展到支持xml也算作新增功能 , （\# 是标记 123对应禅道任务号/bug号,  @是标记需求号）
- fix(#123): bug修复，修复在生产环境发现的bug（补丁包版本分支） , （# 是标记 123对应禅道任务号/bug号/需求号）
- testfix(\#123)：修复迭代过程中测试部发现的bug（master分支） , （# 是标记 123对应禅道任务号/bug号/需求号）
- devfix(\#123)：修复开发过程中发现的bug（master分支） , （# 是标记 123对应禅道任务号/bug号/需求号）
- refactor: 重构
- chore: 构建过程或者辅助工具的变动
- docs: 文档的修改
- other: 其他类型 不属于以上类型 暂时放在这里


## 10.nvm指令
- nvm list：列出所有node版本
- nvm use *：切换使用的node版本
- nvm install *：安装指定的node版本
- nvm version：查看当前的node版本