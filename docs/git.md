# Github

### 常见面试题

#### 1. 主要用哪些Git版本管理操作?
1. 基础操作
- `git clone` 拉去项目
- `git add / git commit` 提交代码
- `git status` 查看工作状态
- `git log` 查看提交历史

2. 分支管理
- `git branch`：创建分支
- `git checkout / git switch`：切换分支
- `git merg`e：合并分支

3. 远程仓库操作
- `git pull`：拉取远程更新
- `git push`：推送代码
- `git fetch`：只拉取不合并（更安全）


#### 2. rebase
`git rebase` 的本质是：把**当前分支的提交“移动”到另一个分支的最新提交之后**，从而让提交历史变成**线性**的。

**merge**（默认方式）
```
A---B---C (main)
     \
      D---E (feature)
           \
            M (merge commit)
```
 特点：

- 会产生 merge commit
- 历史是“分叉的”
- 安全，适合团队协作

**rebase**（线性历史）
A---B---C---D'---E' (feature)

- 没有 merge commit
- 提交历史更清晰
- 看起来像“顺序开发”

##### 1. 多个commit如何合并
**核心方法**：`git rebase -i`
>合并多个 commit 本质是通过交互式 rebase 修改提交历史，把多个提交 squash 成一个，从而保持提交记录整洁。