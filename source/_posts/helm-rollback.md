---
title: helm-rollback
date: 2026-02-23 15:40:32
tags:
---

## helm rollback

`Error: UPGRADE FAILED: another operation (install/upgrade/rollback) is in progress`

[https://gitlab.com/gitlab-org/gitlab/-/issues/349118](https://gitlab.com/gitlab-org/gitlab/-/issues/349118)

```bash
# helm rollback

# kubectx 确认环境
kubectx 

# 查看并回滚
helm list --all -n NAMESPACE

# 查看历史版本
helm history RELEASENAME -n NAMESPACE

# 回滚版本
helm rollback RELEASENAME 1 -n NAMESPACE
```
