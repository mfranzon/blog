---
title: "Using a git repository as volume for a Pod"
date: 2022-06-01T23:26:58+02:00
draft: true
thumbnail: "twitter-shared-link.png"
images: ["twitter-shared-link.png"]
---

Volumes in Kubernetes could have several different types. One of this is the `gitRepo`, which is basically an `emptyDir` volume type, that gets populated by the content of a git repository.

One of the most common usage of this is to store static HTML files. Different softwares like Hugo or Jekyll gives you the possibility to get HTML files starting from a Markdown's one. In this case, an always up-to-date shared volumes could be really useful, It keeps your pod sync with the most recent version of yours static files.

### How to create a sync pod

In the following .yaml template there are the main informations to create a pod with 2 containers.

- First we create the git-sync container, It pulls every "x" interval of time (default 3s) from a git repository. The repo to keep synchronised is exported in the `GIT_SYNC_REPO` variable, which is the same folder shared with the web-server.

- Second container is the web-server. It just mounts the folder which contains the up-to-date HTML files.


```
apiVersion: v1
kind: Pod
metadata:
  name: $name
spec:
    containers:
    - name: $containerSyncName
      image: k8s.gcr.io/git-sync:latest
      volumeMounts:
      - name: $volumeNameMd                         # shared volume name
        mountPath: $pathOnTheContainerSync          # path to mount in the container
      env:
      - name: GIT_SYNC_REPO
        value: $volumeHtml
    - name: $containerServiceName

    - name: $webServer
      image: $webServerImage                        # typically nginx or traefik
      volumeMounts:
      - name: $volumeHtml
        mountPath: $pathToHtml                      # path where are served the html

    volumes:
    - name: $volumeHtml
      gitRepo:
        repository: $repoToPull
        revision: $branchRepo
        directory: .
```

You can use also a private repository, but in that case you CAN'T use the `gitRepo` volumes. This because It requires additional configs to pass the correct SSH key. For this reason, using the  `git-sync` sidecar container is the only way to go.
To do that use a `ConfigMap` resource, in which set the ssh-key to pull the private repo, than add as additional config to git-sync container the name of the created `ConfigMap`.

In this way you can have an updated folder (which shouldn't be a `gitRepo` but simply a `emptyDir`) where all files will be up-to-date.
