---
title: "Uploading Docker Images to Private GitLab Registry"
last_modified_at: 2021-05-23T22:38:42-05:00
categories:
  - recipes
tags:
  - GitLab
  - Docker
---
## Prerequisites
Update the dockerd configuration file so that it will push the entire windows image to the docker registry.
This file is located at C:\ProgramData\Docker\config\daemon.json
```json
{
  "registry-mirrors": [],
  "insecure-registries": [],
  "debug": false,
  "experimental": false,
  "allow-nondistributable-artifacts": [
    "<gitlab server url>:5000"
  ]
}
```
Upon saving and updating the file restart docker.

## Process
1. On a machine with internet access pull down the image you want on your private repo
    ```docker
    PS C:\> docker pull mcr.microsoft.com/windows/servercore:ltsc2019
    ```
2. Add a tag for the private GitLab server
    ```docker
    PS C:\> docker tag mcr.microsoft.com/windows/servercore:ltsc2019 <gitlab server url>:5050/gitlab-instance/gitlab-build-containers
    ```
3. Push the docker image up to the private registry (ensure you login first)
    ```docker
    PS C:\> docker push <gitlab server url>:5050/gitlab-instance/gitlab-build-containers
    Using default tag: latest
    The push refers to repository [<gitlab server url>:5050/gitlab-instance/gitlab-build-containers]
    30275bef3136: Pushed
    a7ba3db29ebb: Pushed
    latest: digest: sha256:1c0c16038e8f755df7e80196d5f61d2bfdc4ee7941e455b9b3fbcd507a4eea4a size: 743
    ```
4. You should see the image on the registry now!