---
title: Network Storage
---

In some cases, it may be necessary to store files in your app's filesystem. In a traditional hosting environment, apps run on a single server with both source code and all auxiliary services using the same server resources and filesystem. In Nanobox however, apps consist of "micro-services" - isolated virtual instances, each with their own resources and filesystems, networked together inside of a virtual machine. In order for written files to be consistent across all services/instances in your app, they must be written to a single filesystem shared with other services. This is where network storage comes in.

## Understanding File Consistency in a Distributed Architecture

In a distributed application architecture, there are multiple isolated filesystems. To ensure consistency across code instances, code instance filesystems are read-only. This prevents them from writing themselves out of sync, but it poses a problem in the case where an app does need to write to the filesystem.

### The Problem
Because each instance has its own isolated filesystem, anything written to the filesystem is inaccessible to other instances.

**For example:** An app allows users to upload images. Images are uploaded through the "web.site" component, but then processed into smaller sizes by "worker.images". Because web.site's filesystem is isolated, worker.images would never be able to access the uploaded images.

<img alt="Network Storage Problem" src="/images/network-storage-problem.svg" width="430" style="display: block; margin: 0 auto;">

### The Solution
Network storage services provide a centralized filesystem shared between code instances. Directories inside of instances to which files must be written/read can be specified as "network directories". These directories are replaced with [network mounts](#network-mounts) on deploy, which route requests to those directories across the network to the filesystem of the network storage service. This allows isolated code services to write to and read from a single writable filesystem.

<img alt="Network Storage Solution" src="/images/network-storage-solution.svg" width="620" style="display: block; margin: 0 auto;">

## How Network Storage Works
Network storage components provide centralized writable filesystems sharable between multiple code services. For each code component, `network_dirs` can be specified in the [Boxfile](/getting-started/boxfile). On deploy, these directories are replaced with network mounts which route any requests to the directory or its contents across the app's private network to the corresponding path inside of the storage component. This allows code components to access files stored in network storage using the same filepaths as if in local filesystem.

### Network Mounts
Network mounts essentially replace those directories in your repo (and their contents) specified as network directories in your Boxfile. Anything inside of a network directory within your repo will not be accessible to your app until uploaded to the matching filepath inside of your storage service.

## Configuring Network Storage
Network storage components are data components that use a filesystem [image](/engines-images/) such as unfs, gluster, etc.

#### Network Storage Component in the Boxfile
```yaml
data.storage:
  image: nanobox/unfs
```

### Network Directories
In order for a code component to be able to store files in a storage component, network directories must be added to the code component's configuration. Network directories are configured per code service using the `network_dirs` config.

In the `network_dirs` config, you must specify the data component on which the directories should be stored by nesting the ID of the desired storage service under `network_dirs` config, then specifying the directory paths.

#### Network Directories in the Boxfile
```yaml
web.site:
  network_dirs:         #| Network Dirs Config
    data.storage:       #| Storage Component Designation
      - public/uploads  #| Network Directory

worker.images:
  network_dirs:         #| Network Dirs Config
    data.storage:       #| Storage Component Designation
      - public/uploads  #| Network Directory

data.storage:           #| Storage Component
  image: nanobox/unfs   #|
```

###### Things to Note:
- Filepaths of `network_dirs` are relative to the root of your app's repo.
- If multiple web/worker components need to share network directories, the `network_dirs` config must be included in all the components.

### Network Directories with Multiple Storage Services
It's possible to have multiple storage services in an app, with specific network directories stored on each.

#### Network Directories for Multiple Storage Services
```yaml
web.site:
  network_dirs:
    data.uploads:
      - public/uploads
    data.processed:
      - public/processed

worker.images:
  network_dirs:
    data.uploads:
      - public/uploads
    data.processed:
      - public/processed

data.uploads:
  image: nanobox/unfs

data.processed:
  image: nanobox/unfs
```

## Managing Network Storage
Network directories and their contents can be managed through an SSH connection into your storage service. More information is available in the [Remote Access doc](/app-management/remote-access).