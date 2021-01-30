## 配置
```sh
git config --global "Your Name"
git config --global "Email Address"
git config --global credential.helper store    保存密码(每次要输密码/重复输密码)
```

## 初始化
```sh
git init
```

## 提交修改
```sh
git add <file>
git add -u 提交 work directory 中所有已 track 的文件至 staging area
git commit -m "descriptions"
git commit --amend 对最近一次的提交做内容修改
git commit --amend --author "user_name <user_email>" 修改最近提交用户名和邮箱
```

## 查看状态、比对
```sh
git status
git status -s 文件状态缩略信息, 常见 A: 新增; M: 文件变更; ?: 未 track; D: 删除
git diff <file>
git diff HEAD -- <file>                 查看工作区和版本库里面最新版本的区别
git diff --check <file>                 检查是否有空白错误(regex:' \{1,\}$')
git diff --cached <file>                查看已add的内容(绿M)
git diff branch1 branch2 --stat         查看两个分支差异
git diff branch1 branch2 <file...>      查看分支文件具体差异
```

## 查看历史版本、历史操作
```sh
git log
git reflog
git log -n                  最近 n 条的提交历史
git log <branch_name> -n    分支 branch_name 最近 n 条的提交历史
git log --stat              历次 commit 的文件变化
git log --shortstat         对比 --stat 只显示最后的总文件和行数变化统计(n file changed, n insertions(+), n deletion(-))
git log --name-status       显示新增、修改、删除的文件清单
git log lhs_hash..rhs_hash  对比两次 commit 的变化(增删的主语为 lhs, 如 git log HEAD~2..HEAD == git log HEAD -3)
git log -p                  历次 commit 的内容增删
git log -p -W               历次 commit 的内容增删, 同时显示变更内容的上下文
git log origin/EI-1024 -1 --stat -p -W 查看远端分支 EI-1024 前一次修改的详细内容
git log origin/master..dev --stat -p -W 查看本地 dev 分支比远端 master 分支变化(修改)的详细内容

git log <branch_name> --oneline   对提交历史单行排列
git log <branch_name> --graph     对提交历史图形化排列
git log <branch_name> --decorate  对提交历史关联相关引用, 如 tag, 本地远程分支等
git log <branch_name> --oneline --graph --decorate 拼接一下, 树形化显示历史
git log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen%ai(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit 同上, 建议alais保存

git log --pretty=format 常用的选项(摘自progit_v2.1.9)
%H 提交对象（commit）的完整哈希字串
%h 提交对象的简短哈希字串
%T 树对象（tree）的完整哈希字串
%t 树对象的简短哈希字串
%P 父对象（parent）的完整哈希字串
%p 父对象的简短哈希字串
%an 作者（author）的名字
%ae 作者的电子邮件地址
%ad 作者修订日期（可以用 --date= 选项定制格式）
%ar 作者修订日期，按多久以前的方式显示
%cn 提交者（committer）的名字
%ce 提交者的电子邮件地址
%cd 提交日期
%cr 提交日期，按多久以前的方式显示
%s 提交说明

git log --since --after     显示时间之后的提交
git log --until --before    显示时间之前的提交
git --author                显示指定作者的提交
git --committer             显示指定 committer 的提交(注: committer 不一定是 author)
git log -S [keyword]        仅显示添加或移除了某个关键字的提交(某些场景比单独 git log -p | grep [keyword] 好用很多)
git log origin/b3.3/master --author=yx-ren --since="2019-10-01" --before="2019-11-01" 查看某作者在某发布版本最近一个月的提交, 常见于线上背锅
git log origin/b3.0/master --author=some_leave --since="1 month ago" 查看某刚离职同事过去一个月的提交, 常见于背锅
git log --since=1.weeks     过去一周的提交(写周报的时候可以看看我这一周干了啥)
git log --since=1.days      过去一天的提交(下班的时候可以看看我这一天干了啥)
git log --since="1 weeks 2 days 3 hours 40 minutes 50 seconds ago" 过去 1 周 2 天 3 小时 40 分 50 秒之内的提交
```

## 版本回退、前进
```sh
git reset --hard HEAD^      回退到上 1 版本
git reset --hard HEAD~5     回退到上 5 个版本
git reset --hard id         回退到指定版本
```

## 撤销修改
```sh
git checkout -- <file>      撤销修改：误修改工作区文件，未 git add/commit
git restore <file>          撤销修改：误修改工作区文件，未 git add/commit
git reset HEAD <file>       撤销 git add：误将文件加入暂存区（git add），未 git commit
git reset --hard HEAD^      撤销 git commit：误将文件提交（一旦提交，只能通过版本回退进行撤销）
```

## 删除与恢复
```sh
git rm/add <file>
git commit -m "remove <file>"   删除版本库中的 <file>：删除工作区文件后，继续删除版本库中相应的文件
git checkout -- <file>          根据版本库中的 <file> 恢复工作区 <file>
```

## 清理工作区未track也未ignore的文件或文件夹(如各种临时.swp, .patch文件等)
```sh
git clean -i    #交互式清理, 不常用
git clean -n    #查看清理文件列表(不包括文件夹), 不执行实际清理动作
git clean -n -d #查看清理文件列表(包括文件夹), 不执行实际清理动作
git clean -f    #清理所有未 track 文件
git clean -df   #清理所有未 track 文件和文件夹, 常用, 但使用前确保新增加的文件或文件夹已 add, 否则新创建的文件或者文件夹也会被强制删除
```

## 关联GitHub远程仓库（本地到远程）
```sh
git remote add origin <remote address>    在本地工作区目录下按照 GitHub 提示进行关联
git remote rm origin                      解除错误关联
git push -u origin master                 第一次将本地仓库推送至远程仓库（每次在本地提交后进行操作）
git push origin master                    以后每次将本地仓库推送至远程仓库（每次在本地提交后进行操作）
<remote address>:
    git@github.com:<username>/<repository>.git
    https://github.com/<username>/<repository>.git
```

## 克隆GitHub远程仓库（远程到本地）
```sh
git clone <remote address>    git协议速度更快但通常公司内网不允许，https协议速度慢
```

## 分支管理：创建、切换、查看、合并、删除
```sh
git branch <branch name>            创建<branch name>分支
git checkout <branch name>          切换至<branch name>分支
git switch <branch name>            切换至<branch name>分支
git checkout -b <branch name>       创建并切换至<branch name>分支
git switch -c <branch name>         创建并切换至<branch name>分支
git branch                          查看已有分支（* 表示当前分支）
git merge <branch name>             合并<branch name>到当前分支（通常在master分支下操作）
git branch -d <branch name>         删除分支
git branch -m oldbranchname newname 删除分支
```

## 解决合并冲突
```sh
合并时报错“分支发生冲突”，首先vim相应文件，修改冲突位置，然后按照git add/commit重新提交，最后删除多余分支即可。
git log --graph --pretty=oneline --abbrev-commit
git log --graph
```

## 分支管理：合并后删除分支也在 log 中保留分支记录
```sh
git merge --no-ff -m "descriptions" <branch name>
```

## 开发流程
```sh
master 分支              发布稳定版本
dev 分支                 发布开发版本
<developer name> 分支    个人开发分支（个人开发完成将该分支并入 dev，同时保留该分支，继续开发）
```

## Bug 分支管理（建立单独分支进行 bug 修复）
```shell
软件开发中，bug 就像家常便饭一样。有了 bug 就需要修复，在 Git 中，由于分支是如此的强大，所以，每个 bug 都可以通过一个新的临时分支来修复，修复后，合并分支，然后将临时分支删除。
git stash                   保存当前工作现场（在 dev 未完成开发，但 master 有 bug 需要修复）
git stash pop               回到 dev 分支后恢复工作现场（list中的现场会同时被删除）
git stash list              查看当前存储的工作现场
git stash apply stash@{#}   回到指定工作现场（list 中的现场不会被删除，需要用 git stash drop）
git stash drop stash@{#}    删除指定工作现场
git cherry-pick <id>        在 master 修复好 bug 后，在 dev 复制一遍 bug 修复流程
```

## Feature 分支管理（建立单独分支添加新功能）
```sh
软件开发中，总有无穷无尽的新的功能要不断添加进来。添加一个新功能时，你肯定不希望因为一些实验性质的代码，把主分支搞乱了，所以，每添加一个新功能，最好新建一个 feature 分支，在上面开发，完成后，合并，最后，删除该 feature 分支。
git branch -D <branch name>    强制删除分支（丢弃未合并分支）
```

## 协作与分支推送

User 1:
```sh
git remote [-v]                        查看远程库信息（-v 查看详细信息）
git remote update origin --prune       更新分支列表(更新远程分支列表)
git remote update origin -p            更新分支列表(更新远程分支列表)
git push origin [master/dev/...]       推送指定分支到远程
```

User 2:
```sh
git clone <remote address>             克隆到本地（只能克隆 master）
git checkout -b dev origin/dev         本地新建分支并关联远程
git add/commit/push                    添加、提交、推送更新
```

User 1:
```sh
git add/commit/push                    推送时报错（与 user2 推送的更新冲突）
git pull <remote> <branch>
git branch --set-upstream-to=origin/<branch> <branch>    本地与远程关联
git pull                               拉取远程文件（并解决冲突）
git commit/push                        重新提交并推送
```

## 标签管理（常用于版本管理）：查看、创建、操作
```sh
git tag                                                     查看标签
git show <tag name>                                         查看指定标签
git log --pretty=oneline --abbrev-commit --decorate=full    在 log 中显示标签
git tag <tag name>                                          为上次 commit 位置打标签
git tag <tag name> <commit id>                              为指定 commit 位置打标签
git tag -a <tag name> -m "descriptions" <commit id>         为指定 commit 打标并添加描述
git tag -d <tag name>                                       删除本地标签
git push origin <tag name>                                  推送指定标签到远程
git push origin --tags                                      推送所有本地标签到远程
git push origin :refs/tags/<tag name>                       删除远程标签（先删除本地标签）
```

## rebase (换基)

rebase 在日常中常用功能主要是两个, 多人协同开发定期 rebase master 以及压缩某分支多个commit
git rebase master 常见于多人开发, 每个开发人员从 master checkout 出自己的分支, 开发一段时间后提交至 master 之前最好 rebase 一下, 防止冲突,
就算真有冲突在本地解决好过强制提交, 开发流程中尽量保证 master 的干净整洁

举个例子:
master 分支上有三个提交C1, C2, C3
某一时刻 usr1 在 C3 的 master 分支上 checkout 出新的分支, 用于开发服务端支持 ipv6 新特性, 并提交了 C4, C5

```sh
git checkout -b ipv6_support
......
git commit -m C4
......
git commit -m C5
```

此时提交状态如下所示

```
      (origin/master branch)
             |
C1 <- C2 <- C3
             \
              \
               \
                C4 <- C5
                       |
                (ipv6_support branch)
```

某同事 usr2 修改了 master 上的内存泄漏错误, 并提交了 C6, C7, C8 三个 commit, 然后直接推送 origin/master (假设这个期间无其他人推新内容到 master)
此时提交状态如下所示

```
                    (origin/usr2/fix_mem_leak branch)
                               |
C1 <- C2 <- C3 <- C6 <- C7 <- C8
             \                 |
              \         (origin/master branch)
               \
                C4 <- C5
                       |
                (ipv6_support branch)
```

如果此时 usr1 希望将 ipv6 的新特性提交至 master, 那么在其直接 push origin master 时会提示 master 需要合并分支 ipv6_support
虽然 C4, C5 的改动内容完全独立于 C6, C7, C8 的改动
但 git 仍会抓取 C5 和 C8 的提交并产生一个新的 C9 commit (因两者分支的 base 不同), 如下图所示

```
C1 <- C2 <- C3 <- C6 <- C7 <- C8
             \                 \
              \                 \
               \                 \
                C4 <- C5 <------ C9
```

如果是为了保证 master 提交记录的"干净完整"
或者是某分支不着急提交, 仍需要更多的测试与开发, 但又不想分支开发周期结束后"偏离"当初 checkout 的 master 分支太久远(容易造成更多的冲突)
可以考虑(定期)利用 rebase 来进行变基
即上面提到过的多人协同开发, 定期 rebase master 是个好习惯

```sh
git checkout ipv6_support
git rebase master
```

结果提交状态如下所示
```
            (origin/master origin/usr2/fix_mem_leak branch)
                               |
C1 <- C2 <- C3 <- C6 <- C7 <- C8
                                \
                                 \
                                  \
                                   C4' <- C5'
                                           |
                                    (ipv6_support branch)
```
									
这种 rebase 在功能上类似将某分支所有的改动做成多个 patch 并依次打在指定的新 base 上
此时再提交 master 就不会产生抓取效果, 会将 C4' 和 C5' 直接提交至 master, 即 `can be fast-forwarded`, 同时也保证了 master 提交记录的整洁性
(注: 虽然 C4' 和 C5' 的内容和 C4, C5 完全一致, 但两者 base 不同, `commit hash code` 也完全不同)

```shell
git rebase --noto <branch_lhs> <branch_rhs> #重放, 用于变基在分支branch_lhs中而不在branch_rhs中的commit
```

某项目状态分支如下所示, 其中 Cn 的数字代表提交时间顺序
 - T1 某员工 urs1 从 C2 (master 分支) checkout 出一个新的分支用于开发某基础公共组件功能
 - T2 员工 usr1 开发完毕提交 C3, 然后继续在该分支上(或 `checkout -b server`) 开发服务端相关功能, 并提交 C4
 - T3 master 分支有更新, 其他同事 usr2 提交了 C5, C6 并推送到了 origin master
 - T4 员工 usr1 从 server 分支切回到 C3 公共基础的提交, 并创建新分支 client, 用于开发客户端功能, 并提交 C8, C9
 - T5 员工 usr1 从 client 分支切回到 server 分支继续开发服务端功能, 并提交 C10

```
            (master branch)
                   |
C1 <- C2 <- C5 <- C6
         \
          \
           \
            C3 <- C4 <- C10
               \         |
                \ (server branch)
                 \
                  C8 <- C9
                         |
                  (client branch)
```

此时该员工希望将客户端相关的功能合并到主分支并发布，但暂时并不想合并 server 中的修改，因为它们还需要经
过更全面的测试。 这时可以使用 `git rebase` 命令的 **--onto** 选项，选中在 client 分支里但不在
server 分支里的修改（即 C8 和 C9），将它们在 master 分支上重放：

```sh
git rebase --noto client server
```

得到如下图所示的提交状态
注: 其中 C3', C8', C9' 与 C3, C8, C9 的提交内容完全一样, 但是 hash id 是完全不同的

```
            (master branch)(client branch)
                   |            |
C1 <- C2 <- C5 <- C6 <- C8' <- C9'
         \
          \
           \
            C3 <- C4 <- C10
               \         |
                \ (server branch)
                 \
               [#####disable######]
               [  C8 <- C9        ]
               [         |        ]
               [  (client branch) ]
```

can be fast-forwarded

```sh
git checkout master
git merge client
```

提交后分支状态如下

```
                                (client branch)
                                       |
C1 <- C2 <- C5 <- C6 <- C3' <- C8' <- C9'
         \                             |
          \                     (master branch)
           \
            C3 <- C4 <- C10
                         |
                  (server branch)
```

```sh
git rebase -i HEAD~n   # 压缩当前分支的 n 个 commit 并合并为 1 个 commit, 常见第一行为 pick, 剩下的 n-1 行为 squash

git rebase --abort     # rebase 过程中发生错误, 可以利用该命令终止整个 rebase 过程
git rebase --continue  # rebase 过程中发生冲突, 在解决冲突后可以利用该命令进行后续过程
```

### 打 patch (补丁)
```sh
# 生成 diff patch 文件(git 可以识别 diff 文件)
git <branch> log -n -p > diff.patch  # 生成某分支过去 n 个 commit 的文件 diff 信息至单个 diff 文件
git diff <--cached> diff.patch       # 针对当前缓存区的内容生成 diff 文件

# 利用 apply 打 patch
git apply --check diff.patch    # 检查是否可以正常应用, 无回显证明无冲突
git apply --stat diff.patch     # 查看应用 diff 文件后的文件变化
git apply diff.patch            # 打 patch, 仅仅改变文件信息, 无 commit 信息, 仍然需要add, commit

# 利用 --format-patch 生成 patch, 带 commit 信息
git format-patch <branch> -n 　 # 生成分支 <branch> 最近的 n 次 commit 的 patch
git format-patch <r1>..<r2>     # 生成两个 commit 间的修改的 patch（包含两个 commit. <r1> 和 <r2> 都是具体的 commit 号)
git format-patch -1 <r1>        # 生成单个 commit 的 patch
git format-patch <r1>           # 生成某 commit 以来的修改 patch（不包含该commit）
git format-patch --root <r1>　　# 生成从根到 r1 提交的所有 patch

# 利用 am 打 patch
git apply --check 0001-update-bash.sh.patch  # 检查 patch 是否冲突可用
git apply --stat 0001-update-bash.sh.patch   # 检查 patch 文件变更情况, 无回显证明无冲突
git am 0001-update-bash.sh.patch             # 将该 patch 打上到当前分支, 带 commit 信息
git am ./*.patch                             # 将当前路径下的所有 patch 按照先后顺序打上
git am --abort                               # 终止整个打 patch 的过程, 类似 rebase --abort
git am --resolved                            # 解决冲突后, 可以执行该命令进行后续的 patch, 类似 rebase --continue
```

## bundle (打包)

该命令会将 git 工程打包, 默认情况下会打包所有 commit 记录和 track 的文件
不同于简单粗暴 `tar.gz` 打包整个文件夹, bundle 只打包那些 push 过的记录
如某 git 工程下存在 `.build` 构建后的目录, 而 `.gitignore` 又忽略了该文件夹
如果利用 `tar.gz` 打包则会将那些忽略的文件文件夹一并打包, 可能会造成压缩包极大的臃肿
而又不想仅仅为了打个包就删除整个 build 目录(如重新 build 时间成本太大)
那么就可以使用 bundle 进行打包, 该命令只打包 track 过的文件
并且像 url 那样直接调用 `git clone` 来重建

```sh
git bundle create awesome-cheatsheets.bundle HEAD master #打包重建master分支的所有数据
git clone awesome-cheatsheets.bundle # 重建工程
```

bundle 也可以打包指定的区间, 至于提交区间有多种表示方式

```sh
git bundle create awesome-cheatsheets.bundle HEAD~10
git bundle create awesome-cheatsheets.bundle HEAD~10..HEAD
git bundle create awesome-cheatsheets.bundle lhs_commit_md5..rhs_commit_md5
git bundle create awesome-cheatsheets.bundle origin/master..master
git bundle create awesome-cheatsheets.bundle master ^origin/master
```

## 使用 GitHub
```sh
fork --> clone --> add/commit/push --> pull request
```

## 其他配置
```sh
git config --global color.ui true    显示颜色
```

## 配置 .gitignore 文件
```
/<dir name>/                    忽略文件夹
*.zip                           忽略 .zip 文件
/<dir name>/<file name>         忽略指定文件
```

## 文件 .gitignore 生效后
```sh
git add -f <file>               强制添加
git check-ignore -v <file>      查看生效规则
```

## 配置别名
```sh
git config [--global] alias.<alias> '<original command>'    为所有工作区/当前工作区配置别名
.git/config             当前工作区的配置文件
~/.gitconfig            当前用户的配置文件
```

## References
- https://www.liaoxuefeng.com/wiki/896043488029600
- https://git-scm.com/book/en/v2

## 子模块
```sh
git submodule foreach git pull    子模块更新
```