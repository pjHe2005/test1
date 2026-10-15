# Git 练习总结

日期：2026-10-14

最后更新：2026-10-15

## 文档维护约定

后续协助本仓库进行 Git 操作时，每次调整远程地址、SSH 配置、密钥路径或操作方法，都同步更新本文件。记录可直接复制的命令、修改原因和验证结果；尚未执行或尚未验证的步骤明确标注，不能写成已经成功。

## 日常上传速查

以下命令在 Git Bash 中、当前 `git_ex` 仓库目录内执行。

当前已检查的远程仓库：`git@github.com:pjHe2005/test1.git`。连接方式使用 `ssh.github.com:443` 和已有私钥。

### 一次性修正 SSH 配置

此前配置漏写目标主机，导致连接 `github.com:443` 而非 `ssh.github.com:443`。修正命令如下；正常保存后不需要每次推送都重设。现已检查本地配置完成修正，并通过只读远程查询确认 GitHub 的 `master` 与本地提交一致。

```bash
git config --local core.sshCommand 'ssh -p 443 -o Hostname=ssh.github.com -i "$HOME/.ssh/git_ed25519" -o IdentitiesOnly=yes -o UserKnownHostsFile="$HOME/.ssh/known_hosts"'
```

### 每次有新内容要上传

先检查文件，选择需要保存的变化：

```bash
git status
git diff
```

确认所有变化（包括删除）都需要保存时：

```bash
git add -A
git commit -m "填写这次修改的说明"
git push -u origin master
```

如果只保存指定文件，用 `git add 文件名` 代替 `git add -A`。例如只保存本笔记：

```bash
git add practice.md
git commit -m "更新 Git 练习笔记"
git push -u origin master
```

注意：普通 `git commit` 会提交所有已暂存变化，不仅是刚刚 `add` 的文件；提交前用 `git status` 确认范围。上述示例假设当前在 `master` 分支。成功设置上游后，日常可直接使用 `git push`。

如果修改已经提交，只需要执行推送，不必重复 `add` 和 `commit`。推送成功后用 `git status` 检查本地状态，并到 GitHub 核对内容；发生报错时先保留完整输出，不要直接强制推送。

历史内容提醒：之前的 `aks.docx` 提交中出现过登录凭据。如果仍有效，应更换；删除工作区文件不会清除提交历史中的内容。上传历史前确认这些内容已适当处理。

三轮共完成 18 道题，涵盖状态判断、暂存、提交、撤销、忽略规则和版本回退。部分题只做了口头判断，没有实际执行。下文回退命令均为学习示例，不表示已经执行。

## 第一轮：基础状态与操作（10 题）

| 题号 | 练习 | 结果与反馈 |
|---|---|---|
| 1 | 理解 `MD aks.docx` | 最初混淆字母与区域；学习了第一列描述暂存区、第二列描述工作区 |
| 2 | 理解 `A  a.txt` | 正确：新增已暂存，工作区与暂存区一致 |
| 3 | 修改已暂存的 `a.txt`，观察 `AM` | 操作和解释正确：工作区修改后与暂存区不同 |
| 4 | 暂存 `a.txt` 的最新内容 | `git add a.txt` 操作成功 |
| 5 | 判断普通 `git commit` 会提交哪些变化 | 正确：提交暂存区里的 `a.txt` 新增和 `aks.docx` 修改 |
| 6 | 取消 `aks.docx` 的暂存，保留工作区现状 | `git restore --staged aks.docx` 操作正确，验证通过 |
| 7 | 提交 `a.txt` 并查看最近两次提交 | 成功创建提交 `fa3fd67`，说明为“添加 Git 学习记录” |
| 8 | 撤销 `a.txt` 尚未暂存的修改 | `git restore a.txt` 操作正确，内容和状态验证通过 |
| 9 | 用 `.gitignore` 忽略根目录的 `note.txt` | 状态中不再显示 `note.txt`，规则生效 |
| 10 | 撤销最近一次提交，保留内容并保持暂存 | 正确选择 `--soft`；纠正了目标参数，理解 `HEAD~1`；未实际回退 |

第 4 题最初涉及尚未学过的 `git diff --cached`，后来调整为已学过的暂存操作，不将未学过的命令计作答错。

## 第二轮：日志、配置、比较和恢复（5 题）

| 题号 | 练习 | 结果与反馈 |
|---|---|---|
| 1 | 查看最近两次提交，找出最新作者和说明 | `git log` 输出正确；当前只有两次提交，明确限制数量可用 `git log -2` |
| 2 | 查询当前仓库生效的用户名和邮箱 | 最初写成设置命令，随后正确查询出 `hpj` 和对应邮箱 |
| 3 | 修改 `a.txt` 后用 `git diff` 识别新增内容 | 正确识别行首 `+`；学习了其他文件的删除输出及 Word 文本转换报错的含义 |
| 4 | 判断暂存后 `git diff -- a.txt` 是否有输出 | 推理正确：未继续修改时，工作区与暂存区一致；未实际执行 |
| 5 | 将暂存区和工作区都恢复到当前提交 | 正确给出先 `git restore --staged a.txt`，再 `git restore a.txt`；未实际执行 |

## 第三轮：reset 与 revert（3 题）

| 题号 | 练习 | 结果与反馈 |
|---|---|---|
| 1 | 撤销已分享的提交，同时保留历史 | 正确选择 `revert`；需要将“替代提交”改为“新增提交来撤销改动” |
| 2 | 撤销最近一次提交，保留内容但取消暂存 | 正确选择 `--mixed`，最初漏写 `HEAD~1`；最终正确理解修改留在工作区 |
| 3 | 解释 `--hard` 与 `--mixed` 的区别 | 理解了三个区域都会恢复；纠正“删除日志和最新版本”的说法 |

这一轮均为口头判断，没有实际执行回退。

## 需要重点纠正的地方

### 1. 状态字母表示变化类型，位置表示区域

最初猜测：“D 是 stage，A 是 working tree。”

正确理解：

```text
XY 文件名
```

| 位置或字符 | 含义 |
|---|---|
| 第一列 X | 暂存区相对当前提交的变化 |
| 第二列 Y | 工作区相对暂存区的变化 |
| `A` | 新增 |
| `M` | 修改 |
| `D` | 删除 |
| `??` | 未跟踪文件 |

常见示例：

```text
A  a.txt       新增已暂存，工作区与暂存区一致
AM a.txt       新增已暂存，但工作区又被修改
MD aks.docx    修改已暂存，但工作区又删除了文件
 D aks.docx    工作区删除尚未暂存
?? note.txt    未跟踪文件
```

### 2. 查询配置时，不要填写新值

下面是设置全局用户名：

```bash
git config --global user.name hpj
```

查询当前仓库实际生效的用户名和邮箱：

```bash
git config user.name
git config user.email
```

不加 `--global` 时，查询结果会考虑当前仓库配置和继承的配置。

### 3. 回退提交时，reset 需要提交目标

曾回答：

```bash
git reset --soft a.txt
git reset --mixed
```

对于“撤销最近一次提交”，正确示例是：

```bash
git reset --soft HEAD~1
git reset --mixed HEAD~1
```

`a.txt` 是文件路径，不是提交目标。省略目标默认使用 `HEAD`，不会回退一次提交。

`HEAD~1` 表示当前提交的第一父提交；在本次直线历史里，就是上一次提交。

| 模式 | 当前分支指向 | 暂存区 | 工作区 |
|---|---|---|---|
| `--soft HEAD~1` | 回退一次提交 | 保持不变 | 保持不变 |
| `--mixed HEAD~1` | 回退一次提交 | 恢复到目标提交 | 保持不变 |
| `--hard HEAD~1` | 回退一次提交 | 恢复到目标提交 | 恢复到目标提交 |

### 4. --hard 不等于直接删除提交和所有日志

`git reset --hard HEAD~1` 会移动当前分支指向，并重置暂存区和工作区。

原来的最新提交会从当前分支的普通 `git log` 中消失，但通常仍能通过 `git reflog` 找到。尚未提交的已跟踪文件修改会被丢弃，不能依靠 reflog 恢复。

### 5. revert 是新增撤销提交，不是替代原提交

`git revert <提交>` 会新增一个提交，撤销目标提交的改动。原提交仍保留在历史中。

已推送并与同伴共享的提交，如果希望撤销改动且保留历史，本题应选择 `revert`。

### 6. 暂存与提交是不同操作

- `git add`：把工作区内容加入暂存区。
- `git commit`：创建提交。普通 `git commit -m "说明"` 提交暂存区中的变化。

提交说明用普通双引号：

```bash
git commit -m "添加 Git 学习记录"
```

不要在说明里输入用于 Markdown 标记代码的反引号：Git Bash 会将反引号中的内容当作命令执行。

本次带文件名的 `git commit a.txt -m "说明"` 成功了，但指定路径的提交方式有不同语义：它可直接提交该路径的工作区内容，不应把它当作普通提交整个暂存区的写法。

## 已经掌握的关键关系

### diff 比较哪些内容

```bash
git diff -- a.txt
```

比较 `a.txt` 的工作区与暂存区。执行 `git add a.txt` 后，如果没有继续修改，通常没有输出。

```bash
git diff --cached -- a.txt
```

比较 `a.txt` 的暂存区与当前提交。这条是练习中补充介绍的命令。

不指定文件的 `git diff` 会显示所有已跟踪文件尚未暂存的变化，因此本次也显示了 Word 和 Excel 文件的删除。Word 转换工具报错不代表 `a.txt` 修改失败；只检查文本练习时可以指定 `-- a.txt`。

### restore 恢复哪些内容

```bash
git restore a.txt
```

用暂存区内容恢复工作区，暂存区不变。若工作区与暂存区一致，通常没有变化。

```bash
git restore --staged a.txt
```

默认用当前提交的内容恢复暂存区，工作区保持不变。

让暂存区和工作区都恢复到当前提交：

```bash
git restore --staged a.txt
git restore a.txt
```

也可以合成一条命令：

```bash
git restore --source=HEAD --staged --worktree a.txt
```

### .gitignore 的作用

本次忽略根目录 `note.txt` 的规则可以写为：

```gitignore
/note.txt
```

`.gitignore` 本身也需要暂存和提交，才能保存忽略规则。忽略规则不会自动让已经跟踪的文件退出版本管理。

## 后续巩固重点

1. 区分命令中的文件路径与提交目标。
2. 判断操作后分支指向、暂存区和工作区分别发生了什么变化。
3. 在单独的练习仓库中实际验证 `reset` 和 `revert`。
4. 补充 `git init` 的实际练习；本次仓库已初始化，没有单独练习该命令。

## GitHub SSH 连接记录与下次使用指南（2026-10-15）

### 已验证成功的状态

- GitHub 账号：`pjHe2005`。
- 使用 Git Bash 操作。
- 私钥文件：`$HOME/.ssh/git_ed25519`。
- 对应公钥文件：`$HOME/.ssh/git_ed25519.pub`。
- 主机身份记录文件：`$HOME/.ssh/known_hosts`。
- 公钥和私钥继续保留在原位置，不移动。完整路径如下：

| 文件 | Windows 完整路径 | Git Bash 完整路径 |
|---|---|---|
| 私钥 | `C:\Users\冷锋过境\.ssh\git_ed25519` | `/c/Users/冷锋过境/.ssh/git_ed25519` |
| 公钥 | `C:\Users\冷锋过境\.ssh\git_ed25519.pub` | `/c/Users/冷锋过境/.ssh/git_ed25519.pub` |
| 主机身份记录 | `C:\Users\冷锋过境\.ssh\known_hosts` | `/c/Users/冷锋过境/.ssh/known_hosts` |

在 Windows 文件资源管理器的地址栏输入 `C:\Users\冷锋过境\.ssh`，即可打开所在文件夹。私钥没有 `.pub` 后缀，不要分享或上传；这里仅记录路径，不保存密钥内容。

- 连接地址：`ssh.github.com`，端口：`443`，SSH 用户：`git`。
- 下方显式指定密钥和路径的连接命令已经运行成功，输出：

```text
Hi pjHe2005! You've successfully authenticated, but GitHub does not provide shell access.
```

这表示 SSH 身份认证成功。`does not provide shell access` 是正常提示，不是错误。它不等于浏览器登录，也不会自动上传文件；访问具体仓库仍需相应权限。

### 下次验证连接，直接运行这条命令

打开 Git Bash，复制执行：

```bash
ssh -T -p 443 \
  -i "$HOME/.ssh/git_ed25519" \
  -o IdentitiesOnly=yes \
  -o UserKnownHostsFile="$HOME/.ssh/known_hosts" \
  git@ssh.github.com
```

也可以复制单行版本：

```bash
ssh -T -p 443 -i "$HOME/.ssh/git_ed25519" -o IdentitiesOnly=yes -o UserKnownHostsFile="$HOME/.ssh/known_hosts" git@ssh.github.com
```

不需要每次重新生成密钥或重新添加公钥。若提示输入密钥口令，输入创建密钥时设置的口令；终端不显示输入字符是正常现象。

### 当前仓库的 Git 操作配置（已检查存在，需修正目标主机）

整理本记录时，已通过 `git config --local --get core.sshCommand` 确认当前 `git_ex` 仓库存在以下配置：

```text
ssh -p 443 -i "$HOME/.ssh/git_ed25519" -o IdentitiesOnly=yes -o UserKnownHostsFile="$HOME/.ssh/known_hosts"
```

上面的旧配置指定了密钥和 443 端口，但遗漏了目标主机。如果远程地址是 `git@github.com:pjHe2005/test1.git`，它会连接 `github.com:443`，而 GitHub 的 443 端口 SSH 服务应连接 `ssh.github.com:443`。之前“配置已保存即可正常推送”的判断不完整。

遇到 `Connection closed ... port 443` 时，先执行下面的修正命令，将目标主机显式设置为 `ssh.github.com`。这项设置适用于当前练习仓库连接 GitHub；不要直接套用到连接其他 Git 服务的仓库。

查看配置：

```bash
git config --local --get core.sshCommand
```

当前仓库修正配置，或以后在另一个 GitHub 仓库需要相同设置时，进入该仓库目录后执行：

```bash
git config --local core.sshCommand 'ssh -p 443 -o Hostname=ssh.github.com -i "$HOME/.ssh/git_ed25519" -o IdentitiesOnly=yes -o UserKnownHostsFile="$HOME/.ssh/known_hosts"'
```

这是仓库级配置，不会自动应用到其他仓库，也不会改变直接运行 `ssh -T git@github.com` 的行为。直接测试连接时，使用上面的完整命令。后续检查已确认本地完成此配置，并成功读取远程分支，详情见下方排查记录。

### 已 push 但网页看不到新修改：本次排查结果

2026-10-15，通过 `git ls-remote origin refs/heads/master` 直接查询 GitHub，返回：

```text
e9877b4a97a5d0000c8c68134b08e2168f1a299c refs/heads/master
```

这与本地 `master` 的 `e9877b4`（整理 Git 练习和学习笔记）一致，说明这次提交已经在 GitHub 上。网页应查看 [test1 仓库的 master 分支](https://github.com/pjHe2005/test1/tree/master)。

但本地当时还有以下已暂存、未提交的变化：

```text
A  AGENTS.md
M  practice.md
```

因此再次执行 `git push` 不会上传这两项最新变化：push 上传的是提交，而不是暂存区或工作区。要让它们出现在 GitHub，需要先提交再推送：

```bash
git status
git add AGENTS.md practice.md
git commit -m "更新推送说明和文档维护约定"
git push
```

本记录写入时，上面这次新的提交和推送尚未执行；本记录自身也需要随下一次提交一起上传。

### 认证成功以后，如何连接具体仓库

整理记录时，`git remote -v` 没有输出，说明当前仓库尚未配置远程地址。SSH 认证成功和配置远程仓库是两件事。

在 GitHub 创建或选定一个有权限访问的仓库后，在当前本地仓库添加 SSH 地址。下面的 `REPOSITORY` 必须替换为实际仓库名：

```bash
git remote add origin git@github.com:pjHe2005/REPOSITORY.git
git remote -v
```

确认地址无误后，如果远程仓库为空且准备上传当前 `master` 分支，可以执行：

```bash
git push -u origin master
```

之后在该分支可使用 `git push` 和 `git pull`。未提交和被忽略的文件不会因为推送而自动上传。若远程仓库已有 README 等提交，需要先检查双方历史，不要直接强制推送。

如果 `origin` 已经存在，需要修改地址时使用 `git remote set-url origin <SSH地址>`，不要重复执行 `git remote add origin ...`。HTTPS 地址不使用这里的 SSH 配置。

### 今天遇到的报错与原因

| 提示 | 原因或判断 | 本次处理方式 |
|---|---|---|
| `Could not create directory ... No such file or directory` | 默认中文用户路径出现编码兼容问题 | 使用带引号的 `$HOME` 显式指定路径 |
| `Saving key ... failed: Is a directory` | 把 `id_ed25519` 创建成了目录，而密钥需要保存为文件 | 换用文件名 `git_ed25519`，没有必要删除原目录 |
| `Connection closed ... port 22` | 默认 22 端口连接被关闭；具体网络原因未完全确认 | 改用 GitHub 提供的 `ssh.github.com:443`，随后连通 |
| `Failed to add the host to the list of known hosts` | 默认中文路径导致主机记录无法保存 | 通过 `UserKnownHostsFile` 显式指定路径 |
| `Permission denied (publickey)` | 当时提供的密钥未通过认证，可能未正确选中私钥；不能仅据此断定公钥缺失 | 显式指定 `-i` 和 `IdentitiesOnly=yes` 后认证成功 |

连接日志中的 `198.18.0.x` 属于特殊用途地址段，推测与代理的 Fake IP 模式有关，未进一步验证代理设置。它不是本次最终认证成功的障碍。

### 首次连接的服务器身份确认

首次连接或主机记录不存在时，可能提示：

```text
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

今天显示的 Ed25519 主机指纹与 GitHub 官方公布的值一致：

```text
SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU
```

确认与官方指纹一致后才输入 `yes`。今天已成功写入主机记录，正常情况下下次不会重复询问。若遇到主机密钥变化警告，先核对官方信息，不要直接删除记录绕过检查。

私钥 `git_ed25519` 自己保管，不要上传到仓库或发送给他人；需要配置 GitHub 时使用对应的 `.pub` 公钥。本记录没有保存私钥或密钥口令。

参考资料：

- [GitHub：通过 HTTPS 端口使用 SSH](https://docs.github.com/en/authentication/troubleshooting-ssh/using-ssh-over-the-https-port)
- [GitHub：测试 SSH 连接](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/testing-your-ssh-connection)
- [GitHub：SSH 主机密钥指纹](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/githubs-ssh-key-fingerprints)
- [IANA：IPv4 特殊用途地址段](https://www.iana.org/assignments/iana-ipv4-special-registry/)
