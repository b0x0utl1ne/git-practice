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
