---
title: 删除Github上所有Commit历史的方法
date: 2021-12-12 20:26:00 +0800
categories: [Cheat Sheet]
tags: [git]
---

使用Github的时候偶尔会忘记把一些例如SSH或者API密钥的文件加入`.gitignore`里，导致不小心上传到Github上，这种时候不光要重置密钥，还要把历史Commit删除

## 步骤

```git
git checkout --orphan latest_branch
git add -A
git commit -am "commit message"
git branch -D master
git branch -m master
git push -f origin master
```
{.nolineno}

## 参考资料

stackoverflow : [https://stackoverflow.com/questions/13716658/how-to-delete-all-commit-history-in-github](https://stackoverflow.com/questions/13716658/how-to-delete-all-commit-history-in-github)
