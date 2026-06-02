# Git 与 GitHub 实战复盘笔记

这份笔记整理的是一次真实的 GitHub 上传实践过程。你在操作中遇到了认证失败、网络重置、SSH Key、远程仓库冲突、Vim 合并提交等问题，最终成功把本地项目 push 到 GitHub。

---

## 1. 这次实践的最终结果

你最后执行：

```powershell
git push -u origin main
```

终端返回：

```text
Enumerating objects: 6, done.
Counting objects: 100% (6/6), done.
Delta compression using up to 16 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (5/5), 537 bytes | 537.00 KiB/s, done.
Total 5 (delta 1), reused 0 (delta 0), pack-reused 0 (from 0)
remote: Resolving deltas: 100% (1/1), done.
To github.com:Youxin166/my_first_project.git
   520f1a8..0d77f28  main -> main
branch 'main' set up to track 'origin/main'.
```

这说明：

```text
本地 main 分支已经成功上传到 GitHub 的 main 分支。
以后可以直接使用 git push / git pull。
```

---

## 2. Git 和 GitHub 的关系

可以这样理解：

```text
Git = 你电脑上的版本管理工具
GitHub = 网上存放代码的远程仓库平台
```

Git 负责在本地记录每一次代码修改；GitHub 负责把这些版本上传到云端，方便备份、协作和展示。

---

## 3. 本地仓库和远程仓库

你的本地项目目录是：

```text
C:\WINDOWS\system32\my_fist_project
```

这个目录现在被 Git 管理，所以它是一个 **本地仓库**。

GitHub 上的仓库是：

```text
git@github.com:Youxin166/my_first_project.git
```

这是 **远程仓库**。

两者的关系是：

```text
本地仓库 main
      ↕
远程仓库 origin/main
```

---

## 4. Git 的三层结构

在本地，Git 有三个重要区域：

```text
工作区：你正在编辑的文件
暂存区：git add 后准备提交的文件
本地仓库：git commit 后真正保存下来的版本
```

完整流程：

```text
修改文件
  ↓
git add .
  ↓
进入暂存区
  ↓
git commit -m "说明"
  ↓
保存到本地仓库
  ↓
git push
  ↓
上传到 GitHub
```

注意：

```text
git commit 只是保存到你自己的电脑。
git push 才是上传到 GitHub。
```

---

## 5. 第一次遇到的问题：用户名或 token 无效

你执行：

```powershell
git push -u origin main
```

遇到：

```text
remote: Invalid username or token. Password authentication is not supported for Git operations.
fatal: Authentication failed for 'https://github.com/Youxin166/my_first_project/'
```

原因是：你当时使用的是 HTTPS 地址：

```text
https://github.com/Youxin166/my_first_project/
```

GitHub 现在不再支持使用账号密码直接 push，需要使用以下两种方式之一：

```text
方式一：HTTPS + Personal Access Token
方式二：SSH Key
```

这次我们选择了更适合长期使用的 **SSH Key**。

---

## 6. 第二次遇到的问题：Connection was reset

你执行 push 时还遇到了：

```text
fatal: unable to access 'https://github.com/Youxin166/my_first_project/': Recv failure: Connection was reset
```

这个错误说明：

```text
你的电脑连接 GitHub 时，网络连接被中途重置。
```

它通常和以下因素有关：

```text
网络不稳定
代理配置
DNS 问题
防火墙
GitHub HTTPS 访问不稳定
```

因此我们后来切换到了 SSH 方式。

---

## 7. SSH Key 是什么？

SSH Key 用来证明：

```text
这台电脑确实属于 GitHub 用户 Youxin166。
```

SSH Key 有两部分：

```text
私钥：保存在你的电脑里，不能给别人
公钥：添加到 GitHub 账号里，可以公开
```

生成 SSH Key 的命令是：

```powershell
ssh-keygen -t ed25519 -C "128950942+Youxin166@users.noreply.github.com"
```

---

## 8. 生成 SSH Key 时遇到的小问题

当终端提示：

```text
Enter file in which to save the key (C:\Users\yuanfang/.ssh/id_ed25519):
```

这里是在问你：

```text
SSH Key 文件保存在哪里？
```

正确操作是：

```text
直接按 Enter
```

你当时误输入了：

```powershell
type $env:USERPROFILE\.ssh\id_ed25519.pub
```

这个命令不是这里用的，而是后面查看公钥时用的。

正确流程：

```text
Enter file in which to save the key (...)：直接回车
Enter passphrase：直接回车
Enter same passphrase again：直接回车
```

---

## 9. 查看 SSH 公钥

生成完成后，查看公钥：

```powershell
type $env:USERPROFILE\.ssh\id_ed25519.pub
```

输出内容应该以：

```text
ssh-ed25519
```

开头。

把整段内容复制到 GitHub：

```text
GitHub → Settings → SSH and GPG keys → New SSH key
```

---

## 10. 第一次 SSH 连接 GitHub

你执行：

```powershell
ssh -T git@github.com
```

看到：

```text
The authenticity of host 'github.com (20.205.243.166)' can't be established.
ED25519 key fingerprint is SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU.
This key is not known by any other names.
```

这不是报错。

这是 Git 第一次连接 GitHub SSH 服务器时，询问你是否信任这个主机。

正确操作：

```text
输入 yes
然后按 Enter
```

注意：要输入完整的 `yes`，不是 `y`。

成功后通常会看到类似：

```text
Hi Youxin166! You've successfully authenticated, but GitHub does not provide shell access.
```

这说明 SSH 配置成功。

---

## 11. 把远程地址从 HTTPS 改成 SSH

你原来使用的是 HTTPS 地址，后来改成 SSH 地址：

```powershell
git remote set-url origin git@github.com:Youxin166/my_first_project.git
```

查看远程仓库地址：

```powershell
git remote -v
```

应该显示类似：

```text
origin  git@github.com:Youxin166/my_first_project.git (fetch)
origin  git@github.com:Youxin166/my_first_project.git (push)
```

---

## 12. 遇到 rejected：远程仓库有本地没有的内容

后来你执行：

```powershell
git push -u origin main
```

出现：

```text
! [rejected]        main -> main (fetch first)
error: failed to push some refs to 'github.com:Youxin166/my_first_project.git'
hint: Updates were rejected because the remote contains work that you do not
hint: have locally.
```

这说明：

```text
GitHub 远程仓库里已经有内容，但你本地没有这些内容。
Git 不允许你直接 push 覆盖远程内容。
```

常见原因：

```text
你在 GitHub 新建仓库时勾选了 README.md
你在 GitHub 上创建了 .gitignore
你在 GitHub 上添加了 License
```

---

## 13. 解决 rejected：先 pull 再 push

解决命令：

```powershell
git pull origin main --allow-unrelated-histories
```

含义：

```text
从 GitHub 的 main 分支拉取内容
把远程内容合并到本地 main
允许合并两个原本没有共同历史的仓库
```

为什么需要 `--allow-unrelated-histories`？

因为你的本地仓库和 GitHub 远程仓库可能是分别初始化的，它们不是从同一个历史开始的。

---

## 14. 为什么进入了 Vim 编辑器？

执行 pull 后，你看到：

```text
Merge branch 'main' of github.com:Youxin166/my_first_project
# Please enter a commit message to explain why this merge is necessary,
# especially if it merges an updated upstream into a topic branch.
#
# Lines starting with '#' will be ignored, and an empty message aborts
# the commit.
~
~
~
```

这是 Git 打开 Vim，让你确认这次 merge commit 的提交信息。

这不是报错。

正确操作：

```text
1. 按 Esc
2. 输入 :wq
3. 按 Enter
```

含义：

```text
:wq = 保存并退出
```

如果想不保存退出，可以用：

```text
:q!
```

但这次应该使用 `:wq`。

---

## 15. 最终成功 push

合并完成后，再执行：

```powershell
git push -u origin main
```

成功后显示：

```text
To github.com:Youxin166/my_first_project.git
   520f1a8..0d77f28  main -> main
branch 'main' set up to track 'origin/main'.
```

含义：

```text
本地 main 已经上传到 GitHub 的 main
本地 main 已经和 origin/main 建立跟踪关系
以后可以直接 git push 和 git pull
```

---

## 16. 这次完整流程回顾

你这次完整经历的是：

```text
1. 本地创建项目目录
2. 初始化 Git 仓库
3. 添加文件到暂存区
4. commit 到本地仓库
5. 连接 GitHub 远程仓库
6. 第一次 push，遇到 HTTPS 认证失败
7. 改用 SSH Key
8. 添加 SSH 公钥到 GitHub
9. 测试 SSH 连接
10. 把 remote 地址改为 SSH
11. push 时发现远程仓库有内容
12. 使用 pull 合并远程内容
13. 进入 Vim 确认 merge commit
14. 保存退出 Vim
15. 再次 push
16. 成功上传到 GitHub
```

---

## 17. 以后日常使用 GitHub 的流程

以后你不需要再重复配置 SSH Key。

日常只需要：

```powershell
git status
git add .
git commit -m "说明这次修改"
git push
```

如果远程可能有新内容，先执行：

```powershell
git pull
```

更完整的安全流程：

```powershell
git status
git pull
git add .
git commit -m "update files"
git push
```

---

## 18. 本地和远程谁超前？

### 情况一：你本地改了文件并 commit

```text
本地 main 超前 origin/main
```

需要：

```powershell
git push
```

### 情况二：GitHub 上有新内容，本地没有

```text
origin/main 超前本地 main
```

需要：

```powershell
git pull
```

### 情况三：本地和远程都改了

```text
本地 main 和 origin/main 各自都有新提交
```

需要：

```powershell
git pull
解决冲突或确认 merge
git push
```

---

## 19. 不要把 GitHub 当普通网盘

GitHub 不是简单的文件同步网盘。

它同步的是：

```text
commit 历史
```

所以重点不是：

```text
上传文件
下载文件
```

而是：

```text
本地 commit
远程 commit
两边 commit 是否一致
```

你遇到的 `fetch first`，本质就是：

```text
远程有一个 commit，但你本地没有，所以你需要先 pull。
```

---

## 20. 以后新建项目的推荐方式

### 推荐方式 A：先在 GitHub 创建仓库，再 clone

这是最适合新手的方式。

```powershell
cd $env:USERPROFILE\Desktop
git clone git@github.com:Youxin166/my_first_project.git
cd my_first_project
```

然后在这个文件夹里写代码。

之后正常执行：

```powershell
git add .
git commit -m "add files"
git push
```

这种方式最不容易遇到 `--allow-unrelated-histories`。

---

### 方式 B：先本地创建，再推到 GitHub

也可以，但更容易遇到远程已有 README 的问题。

标准流程：

```powershell
mkdir my_project
cd my_project
git init
echo "# my_project" > README.md
git add .
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:Youxin166/my_project.git
git push -u origin main
```

如果远程仓库已经有 README，可能需要：

```powershell
git pull origin main --allow-unrelated-histories
git push -u origin main
```

---

## 21. 常用命令速查表

| 命令 | 作用 |
|---|---|
| `git status` | 查看当前文件修改状态 |
| `git add .` | 把所有修改加入暂存区 |
| `git commit -m "message"` | 保存一个本地版本 |
| `git push` | 上传到 GitHub |
| `git pull` | 从 GitHub 拉取最新内容 |
| `git remote -v` | 查看远程仓库地址 |
| `git remote set-url origin 地址` | 修改远程仓库地址 |
| `ssh -T git@github.com` | 测试 SSH 是否连接 GitHub |
| `git log --oneline` | 查看简洁提交历史 |

---

## 22. 你这次学到的关键点

这次实践你实际学到了：

```text
Git 和 GitHub 的区别
本地仓库和远程仓库的关系
git add / commit / push / pull 的作用
GitHub 不支持密码 push
SSH Key 的作用
如何生成 SSH Key
如何把 SSH 公钥添加到 GitHub
如何把 remote 从 HTTPS 改成 SSH
为什么 push 会被 rejected
为什么需要先 pull 再 push
Vim 里的 :wq 是保存退出
push 成功后 main 会跟踪 origin/main
```

---

## 23. 最简记忆版

以后只要记住：

```text
写代码
  ↓
git status
  ↓
git add .
  ↓
git commit -m "说明"
  ↓
git pull
  ↓
git push
```

如果你一个人做项目，而且确定 GitHub 没有别人更新，也可以：

```powershell
git add .
git commit -m "update"
git push
```

---

## 24. 项目目录建议

你当前项目目录在：

```text
C:\WINDOWS\system32\my_fist_project
```

不建议长期把项目放在 `system32`，这是 Windows 系统目录。

以后建议把项目放到：

```text
桌面
文档目录
专门的 Code 文件夹
```

例如：

```powershell
cd $env:USERPROFILE\Desktop
mkdir Code
cd Code
```

之后所有项目都放到：

```text
C:\Users\yuanfang\Desktop\Code
```

这样更安全、更清晰。

---

## 25. 下一步练习

建议你下一步练习：

```text
1. 在桌面创建一个新项目文件夹
2. 新建一个 README.md
3. git init
4. git add .
5. git commit
6. 在 GitHub 创建空仓库
7. 设置 SSH remote
8. git push
```

或者更推荐：

```text
1. 在 GitHub 创建仓库
2. 用 git clone 克隆到本地
3. 修改文件
4. git add
5. git commit
6. git push
```

这会让你更熟悉标准协作流程。
