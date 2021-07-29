---
title: "GitLab"
last_modified_at: 2021-05-24T05:28:36-05:00
categories:
  - crums
tags:
  - gitlab
---
### Register Windows GitLab Docker Runner
Having a GitLab server with a private repo to build PowerShell Modules.

```powershell
.\gitlab-runner.exe register --% -n --url "<gitlabURL>" --executor docker --docker-image "<git server url>:5000/gitlab-instance/gitlab-build-containers:latest" --name pwsh-build --registration-token <registration token from gitlab> --tag-list 'powershell-build'
```