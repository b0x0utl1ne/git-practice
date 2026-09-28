# git 从零练习记录

> 项目：/home/wsa/git练习　|　教材兼练习本：git教程.md
> 规则：同一主题往下追加，新主题另建文件；每完成一轮练习就补一段。

## 2026-09-16～17 阶段 0～2：建仓库、第一次提交、日常循环

- 环境：笔者的 Ubuntu 24.04 加 zsh，git 2.43.0；仓库 /home/wsa/git练习。
- 全局身份 user.name 与 user.email 原本就有，只补了 init.defaultBranch main 和 core.quotePath false。
- 关键决策：不动已有的 user.name 与 user.email（会影响本机所有仓库），只调「新建仓库默认分支名」和「路径显示方式」。
- 产出：附录 A（阶段 0）、附录 B（阶段 1）、附录 C（阶段 2）写进 git教程.md，各自一个提交。
- 学到的坑：分页器卡住按 q；无输出即成功；粘贴会转义、判断内容要用数字；中文文件名默认显示成八进制；保存时格式化会掺进 diff 噪音；diff 数字取决于比较基准。
- 收尾状态：3 个提交，git教程.md 165 行。

## 2026-09-17～24 阶段 3：后悔药

- 产出：amend 改提交信息、reflog 找旧提交、restore --staged 退暂存、restore 丢工作区改动、reset --hard 危险演示与救援，记进附录 D。
- 关键实验：reset --hard HEAD~1 把提交挪掉（日志 3 条变 2 条、文件 165 行变 129 行），再用 reflog 里的哈希挪回来，全部复原。
- 结论：git 对象不可变，历史只能被重建；只要东西曾经提交过，reflog 都能救回来；没被 git 见过的东西救不回。
- 收尾状态：4 个提交，git教程.md 219 行。

## 下一步

- 阶段 4：分支与合并，含一次冲突实战。

## 2026-09-28 阶段 4：分支、合并与冲突

- 产出：附录 E 写进 git教程.md；学会 branch、switch -c、merge（快进与真合并）、冲突解决、branch -d。
- 关键实验：feature/outline 上改文档并提交，切回 main 后文件从 229 行变回 219 行，亲眼看到工作区内容随 HEAD 变化。
- 冲突实战：fix/speed-a 与 fix/speed-b 改同一行，合并第二条时报 CONFLICT，手工取并集解决，产生第一个双亲合并提交。
- 关键教训：冲突解决后要「标记清零 + 结构核对」双重验收；本次多留一个空行把列表拆成两段，靠 wc -l 抓出来，再用 git commit --amend --no-edit 并进合并提交。
- 收尾状态：git log --oneline 共 10 条（含两条分支上的提交），git教程.md 303 行。
- 2026-09-28：在第二台（clone 副本）里追加，用来制造远端领先。

## 2026-09-28 阶段 6：远程与协作（真 GitHub）

- 产出：本地仓库接到 https://github.com/b0x0utl1ne/git-practice.git（公开仓库），全过程记进附录 G。
- 踩坑：VS Code 凭据助手指向 /tmp 里上一次会话的过期文件，HTTPS 认证失败（GitHub 已禁用密码认证）；改用 SSH 私钥 id_ed25519，公钥加到 GitHub 后 ssh -T 通过。
- 关键实验：clone 出 /home/wsa/git练习-第二台 冒充同事机器，两端各提交一笔制造 push 被拒（! [rejected] fetch first），再 fetch 看快照前移、status 显示 diverged、pull 合并、push 成功。
- 新增决策：全局设 pull.rebase false，分叉时用 merge，不改写已有提交的哈希。
- 收尾状态：本地与远端一致，教程 430 行。

## 2026-09-28 阶段 7：stash、revert 与 tag

- 产出：附录 H 写进 git教程.md；学会 stash（抽屉）、revert（撤销已推送的提交）、tag（版本标签）。
- stash 实验：往教程追加一行、git stash 收走（工作区变干净）、stash list 与 pop 拿回来，最后用 git restore 丢掉 —— 抽屉用完即清，不留历史包袱。
- revert 实战：撤销已推送的 0b6560b，中途撞上冲突（冲突块 78 行、实际只删 1 行），手工解决后 git revert --continue 完成，生成 66dd404 并直接 push，全程没用 force。
- 收尾细节：末尾换行丢失让 wc -l 报 428 而真实内容 429 行；补回换行、再删掉 VS Code 自动续出的空列表项，共两笔小提交（86759f1、345ba8f）。
- 收尾状态：教程 477 行，本地与远端一致。

## 2026-09-28 阶段 8：.gitignore 与忽略规则

- 产出：附录 I 写进 git教程.md；学会 .gitignore 语法与优先级、status --ignored、check-ignore -v、git rm --cached。
- 现场：造假文件 build/output.txt、debug.log、important.log（例外演示）、token.txt（假 token）、data/raw.csv，并故意把 data/raw.csv 提交推送（4344bc3），再补规则补救。
- 关键实验：补上 14 行 .gitignore 后 status 只剩 .gitignore 与 important.log；status --ignored 列出 build/、debug.log、token.txt，而 data/ 不在其中（因为已被跟踪）。
- 关键教训：.gitignore 只对未跟踪文件生效，对已跟踪文件完全无效（往 data/raw.csv 追加一行照样显示 modified）；补救要用 git rm --cached，只摘索引、不删磁盘文件。
- 关键教训：补救不等于消灭历史，4344bc3 里的文件仍在历史中、仓库体积不减小；真瘦身要 git filter-repo 或 BFG 重写历史。
- 顺手修正：阶段 7 的进度表漏改（提交信息写了「并更新进度表」，但表里第 7 行仍是未开始），本次一起补上并在提交信息里注明。
- 收尾状态：教程 561 行（附录 A 到 I 共 9 篇），进度表阶段 0 到 8 全部完成，本地与远端一致（337683c）。
