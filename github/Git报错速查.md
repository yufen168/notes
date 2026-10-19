# Git 报错速查（小白版）

> 一句话总结：把你学 GitHub 时踩过的坑全记下来，以后报错不用翻聊天记录，对着抄命令就行。

## 正文

下面每条都是「报错原文 → 中文意思 → 怎么解决」。命令里的 `yufennotes` 换成你自己的文件夹名，`yufen168/notes` 换成你自己的用户名和仓库名。

### 1. 仓库地址填错（改过仓库名最容易踩）

**报错原文：**
```
fatal: repository 'https://github.com/yufen168/yufenwiki.git/' not found
```
或 push 时一直失败、提示找不到仓库。

**中文意思：** 你本地的地址还是旧的，GitHub 上仓库改了名，地址对不上了。

**怎么解决：** 在终端进入你的仓库文件夹，把地址改成新的：
```bash
cd yufennotes
git remote set-url origin https://github.com/yufen168/notes.git
```
改完用 `git remote -v` 看一眼，确认地址对了再 push。

---

### 2. 终端进错文件夹

**报错原文：**
```
fatal: not a git repository (or any of the parent directories): .git
```

**中文意思：** 当前这个文件夹不是 git 仓库，git 不认。通常是你在别的目录（比如 `Yufen`）里敲了命令。

**怎么解决：** 先 `cd` 进你真正的仓库文件夹，再执行 git 命令：
```bash
cd yufennotes
git status
```

---

### 3. 连不上 GitHub（网络问题）

**报错原文：**
```
fatal: unable to access 'https://github.com/...': Failed to connect to ... port 443
```
或
```
fatal: unable to access '...': Failure when receiving data from the peer
```

**中文意思：** 网络连不上 GitHub 的服务器（443 是 https 端口）。

**怎么解决（按顺序试）：**
1. 关掉 VPN / 代理，再试一次。
2. 换网络（比如手机热点）。
3. 等几秒重试，GitHub 偶尔抽风。
4. 确认网址没拼错。

---

### 4. push 被拒：远端有你本地没有的内容

**报错原文：**
```
 ! [rejected]        main -> main (fetch first)
error: failed to push some refs to 'https://github.com/...'
hint: Updates were rejected because the remote contains work that you do not have locally.
```

**中文意思：** 别人（或你另一台设备）已经往仓库传了新内容，你本地还没有，所以不让你直接覆盖。

**怎么解决：** 先拉（pull）再推（push）：
```bash
git pull origin main --allow-unrelated-histories --no-rebase
git push -u origin main
```

---

### 5. 两个仓库历史对不上（第一次合并最容易遇）

**报错原文：**
```
fatal: refusing to merge unrelated histories
```

**中文意思：** 本地仓库和 GitHub 上那个是「各起各的炉灶」，git 不敢乱合。

**怎么解决：** pull 时加 `--allow-unrelated-histories` 告诉它「强行合」：
```bash
git pull origin main --allow-unrelated-histories --no-rebase
```

---

### 6. pull 时问你要怎么合并（分叉分支）

**报错原文：**
```
hint: Need to specify how to reconcile divergent branches.
```

**中文意思：** 本地和远端都各自改了，git 不知道该用「变基（rebase）」还是「合并（merge）」来对齐，让你选一个。

**怎么解决（二选一）：**
- 临时解决：pull 时直接指定用合并：
  ```bash
  git pull origin main --no-rebase --no-edit
  ```
- 一劳永逸：设成默认用合并，以后直接 `git pull` 就行：
  ```bash
  git config pull.rebase false
  ```

---

### 7. 拉取带标签也分叉了

**报错原文：** 同第 6 条，但执行的是 `git pull --tags origin main`。

**怎么解决：** 加 `--no-rebase --no-edit`：
```bash
git pull --tags origin main --no-rebase --no-edit
```

---

## 注意事项

- **先看自己在哪个文件夹**：报错先想「我 cd 对地方了吗」，十次有三次是目录进错。
- **先 pull 再 push**：养成习惯，动笔改文件前先 pull，改完再 push，两台设备就不会打架。
- **地址只对一次**：仓库改过名，本地地址就要用 `git remote set-url` 改，不然永远连不上。
- **网络问题别硬刚**：443 / peer 报错，先关 VPN、换网，比反复重试管用。
- **`--no-rebase` 是新手保命参数**：合并用「合并」不用「变基」，不容易把提交历史搞乱。

## 日期与来源

- 创建：2026-10-19
- 来源：自己实操踩坑记录（VS Code + GitHub 第一次传代码全过程）
