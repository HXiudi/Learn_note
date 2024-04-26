# GitHub 工作流程

## 1. 克隆仓库

首先，你需要将远程仓库克隆到本地计算机上：

```bash
git clone <repository-url>
```

## 2. 创建分支

为了开展工作，你应该从`main`分支创建一个新的特性分支：

```bash
git checkout -b feature-branch
```

## 3. 进行更改

在特性分支上进行代码更改或添加新功能。

## 4. 添加更改

将更改添加到暂存区：

```bash
git add .
```

## 5. 处理主分支更新

在提交更改之前，检查主分支是否有新的更新：

```bash
git checkout main
git pull origin main
git checkout feature-branch
git rebase main
```

## 6. 提交更改

提交更改并附上相应的描述信息：

```bash
git commit -m "Add new feature"
```

## 7. 推送分支

将特性分支推送到远程仓库：

```bash
git push origin feature-branch
```

## 8. 创建 Pull Request

在 GitHub 上打开你的仓库，并创建一个 Pull Request（PR），将你的特性分支合并到`main`分支。

## 9. Code Review

其他团队成员将审查你的代码，并提供反馈或建议修改。

## 10. 合并代码

经过审查并解决必要的更改后，将代码合并到`main`分支。

## 11. 删除分支

一旦特性分支的代码已经合并，可以删除该分支：

```bash
git branch -d feature-branch
```

## 12. 拉取最新代码

定期拉取远程仓库的最新代码，以保持本地代码同步：

```bash
git pull origin main
```

## 13. 维护更新

持续重复这个流程，确保代码库保持更新和维护。

以上是基本的 GitHub 工作流程。你可以根据团队的实际需求和项目规模进行调整和扩展。