## 常见命令
![[Pasted image 20250715134621.png]]
![[Pasted image 20250715134627.png]]
### git stash
### git rebase

#### 与git merge的区别
`git merge` 和 `git rebase`都能把一个分支的改动整合到另一个分支
- **`merge`**：把两个分支的历史合并，保留原有提交并产生一个 _merge commit_（非线性历史）；
- **`rebase`**：把一条分支的提交“搬家”到另一条分支的最新位置，重写提交历史，得到线性历史（没有 merge commit）。
**主要区别**
1. **历史是否重写**
    - `merge`：**不重写**已有提交，只新增一个合并提交。安全（不会改变已有提交哈希）。
    - `rebase`：**重写**被 rebased 分支的提交（产生新的哈希），不建议对已共享到远端或多人协作的分支执行 rebase。
2. **历史形态**
    - `merge`：保留分支拓扑，能看到分支、合并点（更能反映真实开发过程）。
    - `rebase`：产生线性历史，便于阅读“先后顺序”。
3. **冲突处理**
	两者都会可能产生冲突，但处理流程不同：
    - `merge` 冲突在合并时解决，解决后 `git commit`（或 `git merge --abort` 取消）。
    - `rebase` 冲突在每次重放提交时解决，解决后 `git rebase --continue`（或 `git rebase --abort` 取消，`--skip` 跳过当前补丁）。
4. **提交哈希**
    - `merge` 不变；`rebase` 会改变被搬迁的提交的哈希（因为父提交不同）。
5. **合并提交（merge commit）**
    - `merge` 会产生（除非是 fast-forward）。
    - `rebase` 不会产生合并提交（除非后来用 `merge`）。
6. **Fast-forward 行为**
    - 如果目标分支没有新提交，`merge` 可能做 **fast-forward**（直接把指针移到 feature），不会产生 merge commit。可用 `--no-ff` 强制生成合并提交。
    - `rebase` 会把提交 replay，通常用于把 feature 放到最新 main 上。
**直观示意**（常见场景）
假设 `main` 上有提交 `A`，你从 `A` 分出 `feature` 做了 `B`、`C`；然后 `main` 又有 `D`：
初始：
```mathematica
A --- D   (main)
 \
  B --- C (feature)
```
**git merge main 到 feature** (在 feature 上 `git merge main`)：
```mathematica
A --- D
 \     \
  B --- C --- M   (feature)   <-- M 是 merge commit，父是 C 和 D
```
保留了分叉拓扑 + 生成 M。
**git rebase main onto feature**（在 feature 上 `git rebase main`）：
```mathematica
A --- D --- B' --- C'   (feature)
```
B、C 被复制为 B'、C'，历史变成一条直线（线性历史），没有 merge commit。

## 提交流程
先在本地建出一个分支feat/<你的名字>/
### Comment
格式应为
[init#] : 初始化
[feat#] : 完成某功能开发
[sql#] : 更改数据库表结构
### 创建合并请求
修正提交会修改之前一次的提交而不会创建新的提交