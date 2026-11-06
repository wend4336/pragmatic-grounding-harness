# 发布说明

目标仓库：

```text
https://github.com/wend4336/pragmatic-grounding-harness
```

目标可见性：**Public（公开）**。

当前交付物已完成本地 Git 提交，但尚未获得 `wend4336` 账号授权，因此没有执行远程创建或推送。

## 方法一：使用 GitHub CLI

在本仓库根目录执行：

```bash
gh auth login
gh repo create wend4336/pragmatic-grounding-harness \
  --public \
  --source=. \
  --remote=origin \
  --push
```

如果远程仓库已经存在，则执行：

```bash
git remote add origin https://github.com/wend4336/pragmatic-grounding-harness.git
git push -u origin main
```

若已存在 `origin`，先检查其指向，避免推送到错误仓库：

```bash
git remote -v
```

## 方法二：在 GitHub 网页创建仓库

1. 登录 `wend4336` 账号。
2. 创建名为 `pragmatic-grounding-harness` 的公开仓库。
3. 不要自动添加 README、`.gitignore` 或许可证，以免与本地提交产生不必要的差异。
4. 在本地仓库根目录执行：

```bash
git remote add origin https://github.com/wend4336/pragmatic-grounding-harness.git
git push -u origin main
```

## 发布后检查

```bash
git status
git log -1 --oneline
git remote -v
```

公开仓库意味着文档内容可被任何人访问。发布前请确认其中不含隐私、凭据或不愿公开的信息。
