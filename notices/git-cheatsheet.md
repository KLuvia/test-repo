# Git 常用命令速查表

> 环境：Windows + Git Bash　｜　最后更新：2026-11-27

---

## 0. 如果只记三条

```bash
git status                    # 我在哪
git add -A && git commit -m "改了什么"   # 存档
git push                      # 推到 GitHub
```

其他都是遇到问题再查的。

---

## 1. 每日循环

| 命令 | 干什么 | 备注 |
|---|---|---|
| `git add -A` | 全部改动收进暂存区 | 加 `-n` 预览，不实际执行 |
| `git add 文件名` | 只收指定文件 | 精确控制时用 |
| `git commit -m "说明"` | 存档 | **必须带 `-m`**，否则掉进 vim |
| `git push` | 推到 GitHub | 新分支首次推要 `git push -u origin 分支名` |

**完整流程：**

```bash
cd /d/GitHubRepos/test-repo    # 1. 站到仓库根目录
git status                     # 2. 看有哪些改动
git add -A                     # 3. 全部收进来
git status                     # 4. 再确认收对了
git commit -m "这次改了什么"    # 5. 存档
git push                       # 6. 上传
```

两条 `status` 不是啰嗦，批量操作的错误几乎都出在这一步。

---

## 2. 看状态（纯只读，随时敲）

| 命令 | 干什么 |
|---|---|
| `git status` | 当前状态：改了哪些、暂存了哪些 |
| `git status -sb` | 同上，压到一行，更清爽 |
| `git log` | 提交历史（`q` 退出） |
| `git log --oneline` | 一行一条，最常用 |
| `git log --oneline --graph` | 带分支图形 |
| `git log -5` | 只看最近 5 条 |
| `git log -p 文件` | 看某文件的历史具体改动 |
| `git diff` | 未暂存的改动内容 |
| `git diff --staged` | 已暂存的改动内容 |
| `git blame 文件` | 逐行标出谁、哪次提交改的 |
| `git ls-files` | 列出所有被跟踪的文件 |

`git status` 管"我在哪一层"，`git log` 管"我来过哪"，两个加起来是完整地图。

---

## 3. 远程同步

| 命令 | 干什么 |
|---|---|
| `git clone 地址` | 下载整个仓库（含 .git 和全部历史） |
| `git pull` | 拉远程更新并合并到当前分支 |
| `git push` | 上传本地提交 |
| `git fetch` | 只下载远程变化，不合并 |
| `git remote -v` | 看远程地址（决定 push 推到哪） |
| `git remote set-url origin 新地址` | 改远程地址 |
| `git ls-remote origin` | 直接查远程分支指向哪个提交 |

**`git pull` 是最容易忘、后果最烦的一条。** 换过机器、或在网页上改过文件，动手前先 pull。

**push 到哪只看 remote，跟文件夹名字无关。** 随时用 `git remote -v` 确认。

---

## 4. 分支

| 命令 | 干什么 |
|---|---|
| `git branch` | 列出本地分支（`*` 标当前） |
| `git branch -a` | 列出本地 + 远程所有分支 |
| `git switch -c 名字` | 新建分支并切过去 |
| `git switch 名字` | 切换到已有分支 |
| `git switch -` | 切回上一个分支 |
| `git merge 名字` | 把该分支合并进当前分支 |
| `git branch -d 名字` | 删除已合并的分支 |
| `git push -u origin 名字` | 推新分支并建立跟踪关系 |

> 老教程里用 `git checkout`，新版推荐 `git switch`（专管切分支）和 `git restore`（专管撤文件），职责更清晰。

**典型用法**：想试新东西又怕搞坏主线，就 `git switch -c dev`，玩崩了直接 `git switch main` 扔掉分支。

---

## 5. 撤销（按破坏性从小到大）

| 命令 | 干什么 | 会丢什么 |
|---|---|---|
| `git restore 文件` | 撤回未暂存的修改 | 工作区改动（**不可恢复**） |
| `git restore --staged 文件` | 退出暂存区 | 不丢，内容还在 |
| `git commit --amend -m "新说明"` | 改最近一次提交说明 | 不丢，改记录 |
| `git revert 哈希` | 用新提交抵消旧提交 | 不丢历史，最安全 |
| `git reset --soft HEAD~1` | 撤销最近一次 commit | 不丢，改动退回暂存区 |
| `git reset HEAD~1` | 撤销 commit 且退出暂存区 | 不丢，改动回工作区 |
| `git reset --hard HEAD` | 强行对齐到当前提交 | ⚠️ 未提交的改动全没 |
| `git reset --hard origin/main` | 强行对齐到远程最新 | ⚠️ 本地未推的全没 |

**`revert` vs `reset`**：reset 改历史，revert 加新历史。已经 push 到公共仓库的提交只能用 revert，否则和别人的历史对不上。

**`HEAD~1` 的含义**：HEAD 是当前提交，`~1` 表示往前一个。`HEAD~3` 就是往前三个。

---

## 6. 救命

| 命令 | 干什么 |
|---|---|
| `git reflog` | 看 HEAD 走过的每一步（**后悔药**） |
| `git stash` | 把当前改动临时收起来 |
| `git stash list` | 看存了几份 |
| `git stash pop` | 放回最近一份并删除记录 |
| `git stash apply` | 放回但不删除记录 |
| `git cat-file -p HEAD` | 查看提交对象的原始内容 |
| `git ls-tree -r HEAD --name-only` | 列出这个提交包含的所有文件 |

**`reset --hard` 手滑之后：**

```bash
git reflog                       # 找到那个提交的哈希
git reset --hard 那个哈希         # 回去
```

reflog 默认保留 90 天，只要没 `git gc` 清理过，基本都能救回来。

---

## 7. 配置

| 命令 | 干什么 |
|---|---|
| `git config --global user.name "名字"` | 设提交作者名 |
| `git config --global user.email "邮箱"` | 设邮箱 |
| `git config --global core.editor "notepad"` | 换掉默认编辑器（摆脱 vim） |
| `git config --global init.defaultBranch main` | 新建仓库默认分支叫 main |
| `git config --global core.autocrlf false` | 关闭换行符自动转换 |
| `git config --list --show-origin` | 看所有配置**来自哪个文件** |
| `git config --show-origin 键名` | 查某个配置的来源 |

---

## 8. 核心概念速记

### 三个区域

```
工作区 ──git add──> 暂存区 ──git commit──> 本地仓库 ──git push──> GitHub
```

- **工作区**：磁盘上你正在改的文件
- **暂存区**：下次提交的清单（`.git/index`）
- **本地仓库**：已存档的提交历史（`.git/objects`）

撤销就是往回退一层，**退到哪层决定丢多少东西**。

### `.git` 目录是什么

仓库本体。删掉它，文件夹立刻变回普通文件夹，历史全没（文件还在）。

| 子项 | 作用 |
|---|---|
| `objects/` | 所有文件内容，按 SHA-1 哈希寻址，压缩存储 |
| `refs/heads/main` | 分支指针，内容就是**一行 40 字符的哈希** |
| `HEAD` | 当前在哪个分支，内容就一行 `ref: refs/heads/main` |
| `index` | 暂存区清单 |
| `config` | remote、用户设置 |
| `logs/` | 操作记录（reflog） |
| `hooks/` | 钩子脚本模板 |
| `COMMIT_EDITMSG` | 最近一次的提交说明草稿 |

**搬仓库要连 `.git` 一起搬。** 只搬外面的文件等于只搬了视图，丢了本体。

### push 传的是什么

传的是**对象和指针**，不是文件夹。

- 本地的 `.git` ＝ 数据 ＋ 工作区视图
- GitHub 服务端也是一个 `.git`，叫**裸仓库**（bare），只有数据没有工作区
- 网页上那个文件列表，是从对象现场渲染出来的界面

所以 `.git` 永远不会出现在 GitHub 上——它不是被忽略，是根本没被当成"文件"。

### .gitignore 什么时候需要

判断标准：**项目里会不会出现程序自动生成的文件？**

- 不会（纯 Markdown 笔记）→ 可以不用
- 会（写代码）→ 必需

常见要忽略的：

```gitignore
node_modules/
.venv/
__pycache__/
*.pyc
.env
*.log
.DS_Store
Thumbs.db
build/
*.zip
```

**注意**：`.gitignore` 只对**未跟踪**的文件生效。已经提交过的文件，写进去也没用，得先 `git rm -r --cached 路径`。

---

## 9. 常见报错对照

| 报错 | 原因 | 解决 |
|---|---|---|
| `bash: cd: D:GitHubRepostest-repo: No such file` | 反斜杠被 bash 吃掉 | 用正斜杠 `D:/...` 或 `/d/...` |
| `not a git repository` | 当前目录不是仓库（或没有 `.git`） | `cd` 到仓库根目录 |
| `Permission denied (publickey)` | SSH 没配好 / 地址写错 | 检查是否 `git@github.com:` 开头，测 `ssh -T git@github.com` |
| `src refspec main does not match any` | 本地还没有任何提交 | 先 `git commit` |
| `Updates were rejected...` | 远程有你没有的提交 | 先 `git pull` 再 push |
| `fatal: refusing to merge unrelated histories` | 两边历史不相干 | 加 `--allow-unrelated-histories` |
| `warning: LF will be replaced by CRLF` | Windows 换行符自动转换 | 无害；可 `git config --global core.autocrlf false` |
| 卡在 vim 出不来 | `git commit` 忘了 `-m` | `Esc` → `:q!` → 回车 |
| `this exceeds GitHub's file size limit of 100 MB` | 单文件超限 | 用 Git LFS，或从历史里移除 |
| `error: pathspec '...' did not match` | 文件名写错或路径不对（含大小写） | `git status` 核对真实文件名 |

---

## 10. 本机环境记录

| 项目 | 值 |
|---|---|
| Git 版本 | 2.55.0.windows.3 |
| 安装路径 | `C:\Program Files\Git\mingw64\bin\git` |
| 用户身份 | KLuvia / karryluvia@gmail.com |
| SSH 密钥 | `~/.ssh/id_ed25519`（已绑定 GitHub） |
| Git LFS | 已启用 |
| 仓库位置 | `D:\GitHubRepos\test-repo` |
| 远程地址 | `git@github.com:KLuvia/test-repo.git` |
| 换行符配置 | `core.autocrlf=true`（system 级，来自 PortableGit） |

**路径写法**：在 Git Bash 里一律用正斜杠。

```bash
cd /d/GitHubRepos/test-repo      # Git Bash 风格，最推荐
cd D:/GitHubRepos/test-repo      # 正斜杠，也认
cd "D:\GitHubRepos\test-repo"    # 加引号才行，否则 \ 会被转义吃掉
```

---

## 11. 停止卡住的命令

| 场景 | 操作 |
|---|---|
| 命令卡住（push / clone 等） | `Ctrl + C` |
| 卡在 vim | `Esc` → 输入 `:q!` → 回车 |
| 卡在分页器（log / diff 输出停住） | 按 `q` |
| 卡在 Python / Node 交互提示符 | `Ctrl + D` 或输入 `exit()` |

**查进程并杀掉：**

```bash
ps -W                        # 加 -W 才看得到 Windows 进程
kill -9 268                  # 用第一列 PID
taskkill //F //PID 24588     # 用 WINPID，Git Bash 里双斜杠
taskkill //F //IM node.exe   # 按名字批量杀
```

`Ctrl + Z` 是挂起不是杀，新手别用。
