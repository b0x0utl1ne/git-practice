# Git 实战教程（随练随记）

> 练习仓库：/home/wsa/git练习　|　笔者环境：Ubuntu 24.04 + zsh，git 2.43.0
> 这份文档是「教材 + 练习本」合体：每学会一条命令就写进来并提交一次，
> 于是 `git log` 本身就是我的学习进度条。

## 文档维护规则（自己给自己定的）

1. **编号只增不改**：章节号用过就固定，插内容用 `0.1a` 或附录，避免引用失效。
2. **一步一提交**：每完成一步立刻提交，信息用中文，如 `教程：步骤1.3 记录 git status 输出`。
3. **阶段结束更新进度表**：只改那张表，不重排章节。
4. **踩的坑当场写进对应步骤的「常见坑」**，不另开笔记散落各处。
5. **危险命令集中放最后「危险区」**，与日常命令物理隔离。
6. **文档里的命令必须能在本仓库直接复现**：统一用绝对路径，不依赖当前目录。
7. 中间产物（半截的、没跑通的）要么删掉，要么显式标注「未验证」。

## 学习进度表

| 阶段 | 主题                                             | 状态   |
| ---- | ------------------------------------------------ | ------ |
| 0    | 环境与身份（version / config）                   | 完成   |
| 1    | 第一次提交（init / status / add / commit / log） | 完成   |
| 2    | 日常循环（改文件再提交）                         | 完成   |
| 3    | 后悔药（restore / amend / reset）                | 完成   |
| 4    | 分支与合并（含冲突实战）                         | 完成   |
| 5    | 看历史（log / show / diff / blame）              | 完成   |
| 6    | 远程与协作（remote / push / pull / clone）       | 完成   |
| 7    | 工具箱（stash / tag / revert / rebase）          | 未开始 |
| 8    | 固化习惯（.gitignore / 对话历史 / tag）          | 未开始 |

## 每步记录模板

- 目的：
- 命令：
- 你会看到：
- 原理（一句话）：
- 常见坑：
- 自检：

---

# 附录 A 阶段 0：环境与身份

## 步骤 0.1 确认 git 版本

- 命令：`git --version`
- 结果：`git version 2.43.0`
- 原理：2.43 支持 switch / restore 等现代命令，教程按现代写法。
- 自检：能打印 `git version` 开头的一行。

## 步骤 0.2 查看全局配置

- 命令：`git config --global --list`
- 结果：已有 user.name 与 user.email，以及 VS Code 装的 credential.helper。
- 常见坑：长输出会进分页器，界面底部出现 `(END)` 时按 q 退出；可用 `--no-pager` 避开。
- 常见坑：一条配置都没有时它无输出且返回非 0，这不叫报错。

## 步骤 0.3 核对 user.email

- 命令：`git config --global user.email | wc -c`
- 结果：18（干净的 QQ 邮箱 17 字符 + 1 个换行）。
- 原理：user.email 是每条提交的作者签名，写错则平台认不出作者，且只能重写历史才能修。
- 常见坑：zsh 里手敲反斜杠会被吃掉；写在引号里才会保留，从而污染配置值。
- 常见坑：终端内容粘贴到别处可能被自动转义（如 `$*` 显示成转义形式），别靠眼睛判断，用 wc -c 这类数字指标。

## 步骤 0.4 设定默认分支名

- 命令：`git config --global init.defaultBranch main`
- 结果：无输出 = 成功。
- 原理：只影响以后新建仓库的初始分支名，不改动任何已有仓库。
- 通用规律：git 的设置类命令成功时沉默（没有消息就是好消息）。

---

# 附录 B 阶段 1：第一次提交

## 步骤 1.1 建立仓库

- 命令：`git init`
- 结果：`Initialized empty Git repository in /home/wsa/git练习/.git/`，没有 master 提示。
- 原理：在当前目录生成隐藏的 `.git/`，仓库的全部历史都在里面；删掉它等于删掉全部历史（工作区文件还在）。
- 自检：zsh 提示符出现 `git:(main)`，说明默认分支名配置生效。

## 步骤 1.2 提交前的基线

- 命令：`git status`
- 结果：`On branch main` / `No commits yet` / `nothing to commit`。
- 原理：git 把文件分成三个区 —— 工作区、暂存区、仓库；status 就是三个区的对照表。
- 常见坑：status 永远不改动任何东西，可以当仪表盘随便敲。

## 步骤 1.3 写下教程文档

- 命令：`cat > /home/wsa/git练习/git教程.md <<'GITDOC'`，正文粘贴到结束标记为止，最后单独一行 `GITDOC`。
- 原理：`>` 是覆盖写入，`>>` 是追加；`<<'标记'` 是 heredoc，标记上的单引号让内容原样落地，不做变量替换。
- 常见坑：必须敲到最后单独一行的结束标记才会执行；想反悔按 Ctrl+C，此处不要按 q。

## 步骤 1.4 进暂存区并审清单

- 命令：`git add /home/wsa/git练习/git教程.md`
- 命令：`git status` → `Changes to be committed:` 区块里出现 `new file: git教程.md`
- 命令：`git diff --cached --stat` → `1 file changed, 67 insertions(+)`
- 原理：add 把文件此刻的内容做成快照放进暂存区；add 之后再改文件，新改动不会自动进暂存区。
- 原理：三种 diff —— `git diff` 工作区比暂存区、`git diff --cached` 暂存区比上次提交、`git diff HEAD` 工作区比上次提交。
- 常见坑：不要用 `git add .` 一把梭，先学会明确指名道姓地 add。
- 自检：提交前用 --stat 的数字核对内容完整性（本次 67 行等于原稿行数）。

## 步骤 1.5 第一个提交

- 命令：`git commit -m "docs: 建立 git 教程骨架与阶段 0 记录"`
- 结果：`[main (root-commit) c969115] ... 1 file changed, 67 insertions(+) create mode 100644 git教程.md`
- 原理：`(root-commit)` 是根提交（没有父提交）；`c969115` 是提交指纹，完整 40 位；`100644` 是文件权限模式。
- 常见坑：忘记 -m 会被扔进 vim：按 i 输入，Esc 后 `:wq` 保存退出，或 `:q!` 放弃；不会损坏任何东西。
- 提交信息规矩：第一行简短说清做了什么，中文即可；docs、feat、fix 这类前缀可选；别写「更新一下」这种没信息量的。

## 步骤 1.6 看历史

- 命令：`git log --oneline` → 一行：短哈希加提交信息。
- 命令：`git log -1` → 完整格式：完整哈希、`(HEAD -> main)`、Author、Date、提交信息。
- 原理：`HEAD` 是「我现在站在哪」的指针；`HEAD -> main` 表示 HEAD 指向 main 分支，main 指向最新提交。
- 常见坑：输出超过一屏时 less 会接管屏幕，按 q 退出后内容从视野里消失，看起来像什么都没输出。
- 根治：`git config --global core.pager "less -FRX"`（F 不足一屏不接管、R 保留颜色、X 退出不清屏）。

## 阶段 1 小结：今天新增的五个坑

1. 长输出会进分页器：底部出现 `(END)` 或内容「消失」，先按 q 退出；根治见上面那条 core.pager 配置。
2. 无输出即成功：git 的设置类命令成功时沉默，不代表报错。
3. 粘贴会骗人：终端内容复制到别处可能被自动转义（空格变实体、符号被加反斜杠），判断内容优先用数字指标，如 `wc -c`、`git diff --cached --stat`。
4. 非 ASCII 文件名默认显示成八进制转义；`git config --global core.quotePath false` 可以关掉，只影响显示。
5. 提交前审 diff：如果 diff 里出现我根本没打算改的改动，多半是编辑器「保存时格式化」干的（表格补齐、标题后补空行）。这类噪音让 diff 变脏，要么接受它并在提交信息里写明，要么先关掉自动格式化。

---

# 附录 C 阶段 2：日常循环与提交前审查

## 步骤 2.1 追加与覆写的区别

- 命令：`cat >> /home/wsa/git练习/git教程.md <<'GITDOC'`，正文粘贴到结束标记为止，最后单独一行 `GITDOC`。
- 原理：`>` 是覆盖写入，`>>` 是追加写入；用错 `>` 会把已有内容清空。
- 常见坑：想反悔按 Ctrl+C 取消，此处不要按 q。

## 步骤 2.2 已跟踪文件的修改

- 命令：`git status`
- 结果：`Changes not staged for commit:` 区块里出现 `modified: git教程.md`。
- 原理：已跟踪文件的改动落在「未暂存的修改」区块；新文件落在「未跟踪文件」区块。

## 步骤 2.3 提交前审 diff

- 命令：`git diff` 工作区比暂存区；`git diff --cached` 暂存区比上次提交；`git diff HEAD` 工作区比上次提交。
- 命令：`git diff --cached --stat` 给出「几个文件、增删几行」的摘要。
- 原理：add 存的是内容快照，不是跟随开关；所以同一个文件可以同时出现在「已暂存」和「未暂存」两个区块里。
- 原理：diff 不存储任何东西，每次都是现场比较两份内容。
- 常见坑：同一处改动换个比较基准，增删数字就不同 —— 报数字时必须说清「比的是哪两份」。
- 常见坑：暂存区会被整份覆盖，所以 `--stat` 是重新比较，不是把多次 add 累加。

## 步骤 2.4 第二个提交

- 命令：`git commit -m "docs: 追加阶段 1 附录与五个坑，更新进度表（含 Markdown 格式化）"`
- 结果：`1 file changed, 73 insertions(+), 11 deletions(-)`，历史变成两条，新的在上。
- 原理：没有 `(root-commit)` 说明它有父提交；分支指针随提交一起前移。

## 阶段 2 小结：新增的坑

- 坑 6：编辑器「保存时格式化」会给 diff 掺进我没打算改的改动（表格补齐列宽、标题后补空行）。要么接受并写进提交信息，要么按格式化器的风格写，要么关掉自动格式化。
- 坑 7：diff 数字取决于比较基准，同一个改动在不同比较下增删数不同。

---

# 附录 D 阶段 3：后悔药与 reset

## 步骤 3.1 改写刚提交的那一笔

- 命令：`git commit --amend -m "docs: 追加阶段 2 附录、坑 6/7 与进度表更新"`
- 原理：git 对象不可变，历史不能被修改、只能被重建；amend 会用同一棵树、同一个父提交、新的提交信息造出一个新提交，再把分支指针挪过去。
- 原理：提交信息属于提交内容，所以哈希一定会变（本次练习里 4bdf8b2 变成 6ed9075；哈希是当次记录，历史改写后以 git log 为准）。
- 常见坑：amend 永远只针对 HEAD，只能改最后一次提交；想改更早的提交要动用 rebase -i。
- 常见坑：已经推送到远程的提交不要 amend，否则两边历史分叉，只能强推覆盖。

## 步骤 3.2 reflog 是本地操作日志

- 命令：`git reflog`
- 原理：每行左边是「当时指向哪个提交」，右边是「干了什么动作」，越靠上越新。
- 原理：reflog 只存在本机，不随仓库推送到远程，默认保留 90 天。
- 常见坑：log 看的是当前历史，reflog 看的是我干过什么；东西丢了的第一反应永远是 git reflog。

## 步骤 3.3 把误 add 的文件退回未跟踪

- 命令：`git restore --staged /home/wsa/git练习/tmp_临时笔记.txt`
- 原理：只重置暂存区，让该文件回到与 HEAD 一致的状态；文件内容一根汗毛都不动。
- 常见坑：老写法是 `git reset HEAD <文件>`，现在统一用 restore --staged，语义更清楚。

## 步骤 3.4 丢掉工作区改动（不可逆）

- 命令：`git restore /home/wsa/git练习/git教程.md`
- 原理：用暂存区的内容覆盖工作区；被覆盖掉的字符 git 手里从来没有，reflog 也救不了。
- 常见坑：带不带 --staged 是天壤之别 —— 一个只反悔 add（安全），一个反悔内容（破坏性）。
- 自检：丢完之后用 wc -l 这类数字确认「垃圾没了、其它行完好」，别只看 status 干净。

## 步骤 3.5 reset --hard 与救援

- 命令：`git reset --hard HEAD~1`，再用 `git reflog` 找到旧哈希，最后 `git reset --hard 6ed9075` 挪回去。
- 原理：`~1` 表示沿第一父辈往前一代；reset 把分支指针移到目标提交，这个动作会写进 reflog。
- 原理：reset 只移动指针，提交对象仍在对象库里，所以 reflog 里的旧哈希随时能挪回去 —— 同一条命令既是毁灭也是救援。
- 原理：三种模式的区别是 --soft 只挪 HEAD、--mixed（默认）再清空暂存区、--hard 还会覆盖工作区文件；只有 --hard 会吃掉未提交内容。
- 常见坑：动手前先 git status 确认工作区是 clean，否则 reset --hard 会连未提交的改动一起抹掉。
- 自检：救援后用 git log 和 wc -l 双向确认，日志回到三条、行数回到 165。

## 什么能救、什么救不回

- 工作区改动（改了没 add）：救不回，git 手上只有上一版。
- 新文件且未跟踪：救不回，git 从来没见过它。
- 已 add 但未提交：能救，内容已经写进对象库。
- 已提交（哪怕是被 amend 或 reset 丢掉的）：能救，走 reflog。
- 实用习惯：拿不准要不要留的改动，先提交一次（信息写 WIP 都行）；提交是廉价的，丢东西是不可逆的。

## 阶段 3 小结：新增的坑

- 坑 8：`git restore <文件>` 和 `rm` 都不会问你确认，也没有 undo；带 --staged 的那一半才是安全的。
- 坑 9：reset --hard 是唯一会吃掉工作区内容的 reset 模式，用之前先确认工作区干净。

## 常用命令速查

- 看状态：`git status`
- 看改动：`git diff` 和 `git diff --cached`
- 暂存：`git add 文件`
- 提交：`git commit -m "说明"`
- 看历史：`git log --oneline --decorate --graph --all`
- 后悔药：`git restore`、`git restore --staged`、`git commit --amend`
- 掉东西了：`git reflog`

---

# 附录 E 阶段 4：分支、合并与冲突

## 步骤 4.1 看有哪些分支

- 命令：`git branch -a`
- 原理：分支就是 .git/refs/heads/ 下的一个小文件，内容是一行 40 位哈希；`-a` 连远程分支一起列。
- 常见坑：`*` 表示当前所在分支；不加 `-a` 只列本地分支。

## 步骤 4.2 建分支并切过去

- 命令：`git switch -c feature/outline`
- 原理：`-c` 等于「建分支 + 切过去」两步合一；新分支从当前提交长出来，所以它和 main 一开始指向同一个提交，工作区文件一个字节都不变。
- 常见坑：老写法 `git checkout -b` 现在被拆成 switch（只管切分支）和 restore（只管改文件）两个专职命令。

## 步骤 4.3 在分支上提交

- 命令：`git add 文件`，再 `git commit -m "docs: 增加常用命令速查小节"`
- 结果：提交落在 feature/outline 上，main 原地不动 —— 这时是「领先一条」，还不是分叉。

## 步骤 4.4 切回主线，文件内容跟着变

- 命令：`git switch main`
- 现象：文档从 229 行变回 219 行，分支上写的内容看起来「消失」了。
- 原理：工作区内容由 HEAD 指向的提交决定；切分支只是换一条时间线，内容没丢，只是不在这条线上。

## 步骤 4.5 快进合并

- 命令：`git merge feature/outline`
- 结果：`Updating f2de03b..1b9e25f` 加 `Fast-forward`，没有产生合并提交。
- 原理：main 只是落后、没有分叉，git 把 main 指针直接推到新提交即可。
- 常见坑：快进合并后历史是一条直线，看不出曾经有过分支；想留下痕迹要用 `git merge --no-ff`。

## 步骤 4.6 删除用完的分支

- 命令：`git branch -d feature/outline`
- 原理：删掉的只是那个指针文件，提交本身还在历史里。
- 常见坑：`-d` 在分支还有未合并提交时会拒绝删除；`-D` 强制删，但那些提交仍能用 reflog 找回。

## 步骤 4.7 制造分叉

- 命令：建 fix/speed-a 给那一行加 `--all` 并提交；切回 main；建 fix/speed-b 把同一行去掉 `--decorate` 并提交。
- 命令：`git log --oneline --decorate --graph --all`
- 现象：两条并排的历史线，中间出现分叉点 `|/`。

## 步骤 4.8 冲突现场

- 命令：`git switch main`，先 `git merge fix/speed-a`（顺利快进），再 `git merge fix/speed-b`
- 结果：`CONFLICT (content): Merge conflict in git教程.md`，`git status` 显示 `Unmerged paths` 与 `both modified`。
- 原理：两边改了同一行的同一位置，git 不替人做决定，于是把两个版本都写进文件并贴上标签。
- 逃生门：`git merge --abort` 可以一键回到合并前的状态。

## 步骤 4.9 解决冲突

- 命令：`grep -n -A6 '<<<<<<<' /home/wsa/git练习/git教程.md`
- 现象：三明治结构 —— `<<<<<<< HEAD` 到 `=======` 之间是当前分支的版本，`=======` 到 `>>>>>>> 分支名` 之间是被合并分支的版本。
- 做法：先想清楚最终该长什么样，手工写成那个结果，再把三行标记删干净。
- 命令：`git add 文件`（冲突时 add 的含义是「我解决好了」），`git status` 会显示 `All conflicts fixed but you are still merging`，最后 `git commit` 生成有两个父提交的合并提交。
- 常见坑：合并提交不带 -m 会打开编辑器并用默认信息；想直接接受默认信息用 `git commit --no-edit`。

## 步骤 4.10 冲突解决后的双重验收

- 命令：`grep -c '<<<<<<<' /home/wsa/git练习/git教程.md` 得到 0，确认标记清零。
- 命令：`wc -l /home/wsa/git练习/git教程.md` 得到 229，确认结构没被改坏。
- 常见坑：只查标记不够 —— 本次多留了一个空行，把 Markdown 列表拆成了两个列表，靠 wc -l 才抓出来。
- 修正：删掉多余空行后 `git commit --amend --no-edit` 并进合并提交；amend 会保留双亲，改完要用 git log --graph 再确认一次树形没变。

## 阶段 4 小结：新增的坑

- 坑 10：冲突标记要删干净，但「标记清零」不等于「结构正确」，必须配上 wc -l 或 git diff 一起验收。
- 坑 11：switch 和 merge 都会改动工作区文件，动手前先确认工作区干净，否则未提交的改动会跟着你跑到另一条分支上。
- 坑 12：改文件前先看提示符里的分支名；在错误的分支上改东西是分支工作里最常见的事故。

---

# 附录 F 阶段 5：看历史（只读侦探工具）

## 步骤 5.1 看某笔提交干了什么

- 命令：`git show 7eda78e`
- 现象：上半段是元信息（完整哈希、Author、Date、提交信息），下半段是这笔提交相对父提交的 diff。
- 原理：`git show <哈希>` 就是「元信息 + 这笔提交引入的改动」；哈希写前 7 位够用，但别只写 3 到 4 位，提交多了会 ambiguous。

## 步骤 5.2 取出某个历史版本的文件（考古语法）

- 命令：`git show c969115:git教程.md | wc -l`，得到 67。
- 原理：`<提交>:<路径>` 直接打印那个提交里那个文件的内容，不碰工作区、也不切分支。
- 用途：对比任意两个历史版本（第一版 67 行、今天 303 行），确认某段内容是哪个版本引入的。

## 步骤 5.3 查某个文件的历史（这里有个必须知道的坑）

- 命令：`git log --oneline -- git教程.md` 得到 7 条；`git log --oneline --full-history -- git教程.md` 得到 9 条。
- 原理：`--` 之后跟的是路径不是分支名；默认开启历史简化，会剪掉「对当前文件状态没有贡献」的提交（这里少了 7eda78e 和那次合并）。
- 常见坑：查「某个东西是哪次被删掉的」时必须加 --full-history，否则会误判成从来没删过。

## 步骤 5.4 揪出某几行是谁改的

- 命令：`git blame -L 226,230 --date=short git教程.md`
- 现象：每行左边是短哈希、作者、日期、行号，右边是内容；那一行 --all 被归到 d5ed71f，也就是已经删掉的那条分支上的提交。
- 原理：blame 显示的是「最后一次改动这一行的提交」，不是「这行的原创者」。
- 常见坑：不加 -L 会把整个文件刷出来；大规模重排格式之后 blame 会一片指向那次重排，有些项目用 .git-blame-ignore-revs 解决。

## 步骤 5.5 按内容片段搜「这行字哪次进来的」

- 命令：`git log --oneline -S 'core.pager' -- git教程.md`，得到 eabe32e。
- 原理：pickaxe 找的是「这个字符串出现次数发生变化」的提交，不是「内容里含这个词」的提交。
- 常见坑：值以 - 开头时必须粘连写，例如 `-S--decorate`；写成 `-S '--decorate'` 会报 `switch 'S' requires a value`。
- 常见坑：pickaxe 同样受历史简化影响，查「哪次被删掉」要配 --full-history。

## 步骤 5.6 合并提交的 show 为什么是空白

- 命令：`git show 8786b35`
- 现象：只有元信息（含 `Merge: d5ed71f 7eda78e`）和提交信息，没有任何 diff。
- 原理：合并提交默认显示「相对所有父提交都不同」的组合差异；这次的解决结果恰好等于其中一个父提交，所以什么都不显示。
- 命令：想看合并带来的改动，用 `git show -m 8786b35`（对每个父分别显示）或 `git diff 8786b35^1 8786b35`。
- 原理：Merge 那行是双亲的证据，顺序为「第一父 = 当时所在分支，第二父 = 被合并进来的分支」，`^1` 与 `^2` 就按这个顺序。

## 阶段 5 小结：新增的坑

- 坑 13：`git log -- 路径` 默认简化历史，查「谁改的、谁删的」要加 --full-history。
- 坑 14：-S 与 -G 的值以 - 开头时必须粘连写（-S值）。
- 坑 15：合并提交的 `git show` 可能是空的，不代表这次合并没改东西。

---

# 附录 G 阶段 6：远程与协作（GitHub）

## 步骤 6.1 接一个远端

- 命令：`git remote add origin https://github.com/b0x0utl1ne/git-practice.git`，再用 `git remote -v` 核对。
- 原理：remote 只是给一个地址起名字，origin 是惯例叫法；fetch 与 push 可以指向不同地址，所以会显示两行。
- 常见坑：建远端仓库时不要勾 Add README、Add .gitignore、Choose a license，否则远端先有一笔提交，首次 push 必然被拒。

## 步骤 6.2 HTTPS 认证为什么失败

- 现象：push 时报 `Cannot find module '/tmp/vscode-remote-containers-<uuid>.js'`，随后退回问用户名与密码，GitHub 回 `Password authentication is not supported`。
- 原因：全局配置里的 credential.helper 指向 VS Code 上一次远程会话留下的临时文件，那个 uuid 随会话变化，文件早已被清掉。
- 结论：终端里的 HTTPS 认证不可靠时不要猜密码，直接换 SSH；GitHub 早已不接受账号密码认证。

## 步骤 6.3 用 SSH 代替 HTTPS

- 命令：`cat ~/.ssh/id_ed25519.pub`，把整行加到 GitHub 的 Settings 里 SSH and GPG keys 处。
- 原理：公钥（.pub）可以公开，私钥（id_ed25519）绝不能外传；私钥权限必须是 600，否则 ssh 会拒绝使用。
- 命令：`ssh -T git@github.com`
- 原理：首次连接要核对主机指纹（GitHub 官方 ED25519 为 SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU），确认后输入 yes，指纹写进 known_hosts。
- 常见坑：看到 `Hi 用户名! You've successfully authenticated, but GitHub does not provide shell access.` 就是成功，后半句不是报错。
- 命令：`git remote set-url origin git@github.com:b0x0utl1ne/git-practice.git`
- 原理：SSH 地址里的 git@ 是固定用户名不是账号名，GitHub 靠公钥识别你是谁。

## 步骤 6.4 首次推送与跟踪关系

- 命令：`git push -u origin main`
- 现象：Enumerating、Counting、Compressing 一堆，加 `* [new branch] main -> main`，最后 `branch 'main' set up to track 'origin/main'`。
- 原理：-u 把本地分支与远端分支绑成跟踪关系，之后 push 与 pull 不用再写参数；`[new branch]` 表示远端原本没有这条分支，之后的推送显示 `旧哈希..新哈希` 表示在已有分支上向前推进。
- 原理：push 送的是提交对象不是工作区文件，未提交的改动推不上去；git 只送远端没有的对象（首次 36 个对象 14 KiB，之后一笔只推 4 个对象 511 字节）。

## 步骤 6.5 远程跟踪分支与离线的 git

- 命令：`git branch -a` 会看到 remotes/origin/main；`git log --oneline --decorate -3` 会看到 `(HEAD -> main, origin/main)` 挤在同一行。
- 原理：origin/main 是你最后一次联网时远端状态的只读快照，不是你本地的工作分支，切不过去、也不该在上面干活。
- 常见坑：status 里的 up to date、ahead by N、have diverged 三种措辞，描述的都是「本地 main 与你本地那份快照」的关系；快照过期，措辞就过期。git 从不偷偷联网，推之前先 fetch。

## 步骤 6.6 克隆

- 命令：`git clone git@github.com:b0x0utl1ne/git-practice.git /home/wsa/git练习-第二台`
- 原理：clone 等于 init 加 remote add 加 fetch 加 checkout 四件事一次做完，所以克隆出来的仓库天生带 origin 和完整历史。

## 步骤 6.7 push 被拒绝

- 命令：两端各提交一笔后执行 `git push`
- 现象：`! [rejected] main -> main (fetch first)` 加一段 hint。
- 原理：远端有你没有的提交，git 拦住你是为了不覆盖别人的工作；括号里的原因词（fetch first、non-fast-forward、stale info）比整段 hint 更有信息量。
- 常见坑：此时千万不要 `git push -f`，那会把远端那笔从 main 上踢掉；真需要重写历史重推时用 `git push --force-with-lease`。

## 步骤 6.8 fetch 与 pull 的区别

- 命令：`git fetch`
- 现象：输出 `2a2c400..f9b2533 main -> origin/main`，只有快照前移；你的 main、工作区、本地提交都不动。
- 命令：`git status` 此时才改口成 `Your branch and 'origin/main' have diverged, and have 1 and 1 different commits each`。
- 原理：fetch 只同步信息；pull 等于 fetch 加 merge，会改动工作区并生成双亲合并提交。

## 步骤 6.9 分叉时 pull 要先表态

- 现象：新版 git 直接 fatal，提示 `Need to specify how to reconcile divergent branches`，并列出三种可选策略。
- 原理：merge 会多加一个接头提交、不改已有哈希；rebase 会把本地提交重放到远端之上、会改哈希，等于改写历史。
- 命令：`git config --global pull.rebase false` 选择 merge 作为默认，等于回到 git 2.27 之前的老默认；只想临时换可用 `git pull --rebase`、`--no-rebase`、`--ff-only`。

## 步骤 6.10 完整闭环

- 顺序：push 被拒，git fetch，git status 看到 diverged，git pull 合并，git push 成功。
- 结果：`f9b2533..0e44f50 main -> main`，图形变成双亲合并提交，`(HEAD -> main, origin/main)` 挤在同一行。
- 常见坑：`git log --graph` 不加 `--all` 只走 HEAD 能到达的提交，远端那条线（不是你的祖先）根本不显示，容易误以为没分叉。

## 阶段 6 小结：新增的坑

- 坑 16：status 与 log 全是离线判断，快照过期会给出误导的 up to date；推之前先 fetch。
- 坑 17：`git log --graph` 必须配 `--all` 才能看到不在 HEAD 祖先链上的分支线。
- 坑 18：VS Code 凭据助手的路径随会话变化（指向 /tmp 的过期文件），终端里 HTTPS 认证会失败；长期方案用 SSH，而 `ssh -T git@github.com` 的成功输出长得像报错。