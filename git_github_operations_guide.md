# Git 和 GitHub 操作入门指南

Git 和 GitHub 可以这样理解：

```text
Git = 本地版本管理工具
GitHub = 放代码的云端平台
```

Git 负责记录你代码每一次修改；GitHub 负责把代码上传到网上，方便备份、协作、展示作品。

---

## 1. Git 和 GitHub 的区别

| 名称 | 是什么 | 类比 |
|---|---|---|
| Git | 本地版本管理工具 | 本地存档系统 |
| GitHub | 远程代码托管平台 | 云盘 + 协作平台 |
| Repository / repo | 仓库 | 一个项目文件夹 |
| Commit | 一次保存记录 | 游戏存档 |
| Branch | 分支 | 平行版本 |
| Push | 上传到 GitHub | 本地 → 云端 |
| Pull | 拉取 GitHub 更新 | 云端 → 本地 |
| Clone | 下载仓库 | 云端复制到本地 |

---

## 2. 第一次使用 Git：安装和配置

### Windows

安装 Git：

```text
https://git-scm.com/download/win
```

安装后打开：

```text
Git Bash
```

### macOS

```bash
brew install git
```

或者安装 Xcode Command Line Tools：

```bash
xcode-select --install
```

### 检查 Git 是否安装成功

```bash
git --version
```

---

### 设置用户名和邮箱

第一次用 Git，一定要配置身份：

```bash
git config --global user.name "你的名字"
git config --global user.email "你的邮箱"
```

查看配置：

```bash
git config --global --list
```

这个名字和邮箱会出现在 commit 记录里。

---

## 3. Git 最核心的工作流程

Git 的基础流程是：

```text
修改文件
  ↓
git add
  ↓
git commit
  ↓
git push
```

可以理解为：

```text
改代码 → 放入暂存区 → 本地存档 → 上传 GitHub
```

---

## 4. 从零创建一个 Git 项目

假设你有一个文件夹：

```bash
mkdir my-first-project
cd my-first-project
```

创建一个文件：

```bash
echo "# My First Project" > README.md
```

初始化 Git 仓库：

```bash
git init
```

查看状态：

```bash
git status
```

把文件加入暂存区：

```bash
git add README.md
```

或者一次添加所有文件：

```bash
git add .
```

提交一次版本：

```bash
git commit -m "first commit"
```

查看提交记录：

```bash
git log
```

简洁版：

```bash
git log --oneline
```

---

## 5. 上传到 GitHub

### 第一步：在 GitHub 新建仓库

进入 GitHub，点击：

```text
New repository
```

填写仓库名，比如：

```text
my-first-project
```

先不要勾选 README，因为你本地已经有了。

---

### 第二步：连接远程仓库

GitHub 会给你一个地址，类似：

```text
https://github.com/你的用户名/my-first-project.git
```

然后在本地执行：

```bash
git remote add origin https://github.com/你的用户名/my-first-project.git
```

查看远程仓库：

```bash
git remote -v
```

---

### 第三步：推送到 GitHub

现在很多 GitHub 仓库默认主分支叫 `main`：

```bash
git branch -M main
git push -u origin main
```

以后再上传，只需要：

```bash
git push
```

---

## 6. 已经有 GitHub 仓库，如何下载到本地？

这叫 **clone**。

```bash
git clone https://github.com/用户名/仓库名.git
```

进入项目：

```bash
cd 仓库名
```

以后你在里面改代码、提交、推送即可。

---

## 7. 日常最常用命令

### 查看当前状态

```bash
git status
```

你应该经常用它。

---

### 添加文件

添加单个文件：

```bash
git add main.py
```

添加全部修改：

```bash
git add .
```

---

### 提交版本

```bash
git commit -m "写清楚这次改了什么"
```

比如：

```bash
git commit -m "add JSON practice example"
```

---

### 推送到 GitHub

```bash
git push
```

第一次可能需要：

```bash
git push -u origin main
```

---

### 拉取 GitHub 最新代码

```bash
git pull
```

如果多人协作，写代码前最好先：

```bash
git pull
```

---

### 查看历史记录

```bash
git log --oneline
```

---

## 8. 分支操作

分支就是开一个新版本，不影响主线。

比如你要开发新功能，不想直接改 `main`：

```bash
git branch feature-login
git switch feature-login
```

或者一步完成：

```bash
git switch -c feature-login
```

查看所有分支：

```bash
git branch
```

切回 main：

```bash
git switch main
```

把分支合并进 main：

```bash
git merge feature-login
```

删除分支：

```bash
git branch -d feature-login
```

---

## 9. GitHub 上的 Pull Request 是什么？

Pull Request，简称 PR。

它的意思是：

```text
我改了一些代码
请你检查
没问题就合并到主分支
```

团队协作常用流程：

```text
clone 项目
  ↓
新建分支
  ↓
修改代码
  ↓
commit
  ↓
push 分支
  ↓
在 GitHub 创建 Pull Request
  ↓
别人 review
  ↓
merge
```

---

## 10. SSH Key 是什么？

你每次 push 到 GitHub，都需要证明“你是你”。

有两种常见方式：

```text
HTTPS + 登录/token
SSH Key
```

SSH Key 更适合长期使用。

生成 SSH Key：

```bash
ssh-keygen -t ed25519 -C "你的邮箱"
```

一路回车即可。

查看公钥：

```bash
cat ~/.ssh/id_ed25519.pub
```

复制输出内容，添加到 GitHub：

```text
GitHub → Settings → SSH and GPG keys → New SSH key
```

测试连接：

```bash
ssh -T git@github.com
```

成功的话会看到类似：

```text
Hi 用户名! You've successfully authenticated...
```

以后 remote 可以用 SSH 地址：

```bash
git remote set-url origin git@github.com:用户名/仓库名.git
```

---

## 11. `.gitignore` 是什么？

`.gitignore` 用来告诉 Git：

```text
这些文件不要上传
```

比如 Python 项目常见：

```gitignore
__pycache__/
.env
.venv/
*.pyc
```

AI/API 项目一定要忽略：

```gitignore
.env
```

因为 `.env` 里可能有 API Key。

---

## 12. 常见错误和解决

### 错误 1：忘记 git add

你改了文件，但 commit 时没有变化。

解决：

```bash
git add .
git commit -m "update files"
```

---

### 错误 2：push 被拒绝

可能是 GitHub 上有更新，你本地没有。

解决：

```bash
git pull
git push
```

如果有冲突，需要手动解决冲突文件。

---

### 错误 3：remote origin already exists

说明你已经添加过远程仓库。

查看：

```bash
git remote -v
```

修改地址：

```bash
git remote set-url origin 新地址
```

删除后重新添加：

```bash
git remote remove origin
git remote add origin 新地址
```

---

### 错误 4：误提交了 API Key

先马上去平台后台删除或重置这个 Key。

然后从 Git 里删除文件：

```bash
git rm --cached .env
echo ".env" >> .gitignore
git add .gitignore
git commit -m "remove env file"
git push
```

注意：如果 key 已经上传过 GitHub，即使后面删除，也可能还在历史记录里，所以必须重置 key。

---

## 13. 练习项目：上传一个 JSON 文件到 GitHub

做一个文件夹：

```bash
mkdir json-practice
cd json-practice
```

创建文件：

```bash
echo '{ "name": "Olivia", "skills": ["JSON", "Git", "Python"] }' > profile.json
```

初始化 Git：

```bash
git init
git add .
git commit -m "add profile json"
```

去 GitHub 创建一个仓库：

```text
json-practice
```

连接远程：

```bash
git remote add origin https://github.com/你的用户名/json-practice.git
git branch -M main
git push -u origin main
```

以后修改 `profile.json` 后：

```bash
git add .
git commit -m "update profile json"
git push
```

---

## 14. 最小命令清单

你先记住这些就够了：

```bash
git init
git status
git add .
git commit -m "message"
git log --oneline
git remote add origin 仓库地址
git branch -M main
git push -u origin main
git pull
git clone 仓库地址
```

一句话记忆：

```text
git init       开始管理
git add        准备保存
git commit     本地保存
git push       上传 GitHub
git pull       拉取更新
git clone      下载项目
```
