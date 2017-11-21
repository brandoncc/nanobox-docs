---
title: run.config
seo_title: Configure Runtimes with the boxfile.yml
description: The run.config section of the boxfile.yml allows you to configure the environment in which your app runs.
keywords: configure runtime, configure environment, app environment
---

The `run.config` section allows you to configure the environment your app runs within (this includes local and production). An engine will provide all of the necessary configuration, but you can provide custom configuration as well.

#### Overview of Run Config Settings in the boxfile.yml
```yaml
run.config:

  # Engine
  engine: engine-name

  # Configuration used by the engine
  engine.config:
    runtime: ruby-2.3

  # Contents of these dirs to be cached inside of Nanobox
  cache_dirs:
    - vendor
    - packages

  # Extra Packages (in addition to what the engine installs)
  extra_packages:
    - nodejs
    - newrelic

  # Dev Packages
  dev_packages:
    - psutils

  # Build Triggers - Changes to these files automatically
  # trigger a new build the next time a build is required.
  build_triggers:
    - Gemfile
    - Gemfile.lock
    - package.json

  # Additions to $PATH
  extra_path_dirs:
    - vendor/bin

  # Custom commands to prepare the environment
  extra_steps:
    - npm install

  # Enable filesystem watcher
  fs_watch: true
```

## Engine
The `engine` config allows you to specify which engine to use to build your code's environment.

#### engine
```yaml
run.config:
  engine: engine-name
```

##### Using Non-Nanobox Engines
You can use engines other than official Nanobox engines published on Github with the following patterns:

```yaml
# Other engines published on Github
run.config:
  engine: github-username/repo-name

# An engine on your local machine
run.config:
  engine: ../engine-directory
```


## Config Options
Engines expose configurable options used to tailor your code's environment to your project's specific needs. What options are available wholly depend on the engine and should be documented in the engine's documentation.

#### config
```yaml
run.config:
  engine: engine-name
  engine.config:
    version: 2.2
```

## Cache Directories
You can choose to cache certain directories inside of Nanobox. This can provide a significant performance increase for generated files like shared libraries and dependencies, static assets, build releases, etc.

**HEADS UP**: These directories **are not shared** with your local source code! Be sure that the contents of them are generated automatically (usually part of your build process).

```yaml
run.config:
  # ...
  cache_dirs:
    - vendor
    - node_modules
    - public/assets
 ```

## Extra Packages
In some cases, you may need a package or runtime that your engine does not make available. The `extra_packages` config allows you to specify additional packages that should be loaded into your environment. More information is available in the [Adding Packages](/app-config/adding-packages/) doc.

```yaml
run.config:
  # ...
  extra_packages:
    - nodejs
    - newrelic
```

**Note:** Only packages available in the [Nanobox pkgsrc](http://packages.nanobox.io/2017/11/base/index.html) can be loaded using `extra_packages`.

## Dev Packages
In some cases, you may need a package or runtime that your engine does not make available, but only during development. The `dev_packages` config allows you to specify additional packages that should be available for local development only.  More information is available in the [Adding Packages](/app-config/adding-packages/) doc.

```yaml
run.config:
  # ...
  dev_packages:
    - psutils
    - pip
```

**Note:** Only packages available in the [Nanobox pkgsrc](http://packages.nanobox.io/2017/11/base/index.html) can be loaded using `dev_packages`.

## Build Triggers
Changes to these files will trigger a new build the next time a new build is required. In general, it's recommended that any dependency-management files be added as build triggers. That way, you don't have to manually [build a new runtime](/cli/build-runtime/) when you update your dependencies.

```yaml
run.config:
  # ...
  build_triggers:
    - Gemfile
    - Gemfile.lock
    - package.json
```

**Note:** Only files can be used as build triggers; directories can not. The path of each build trigger should be relative to the root of your project.

## Add Directories to the $PATH
Some tools you may be using include binaries that need to be added to the system $PATH. The `extra_path_dirs` config allows you to specify these directories. Directories should be relative to the root of your project.

```yaml
run.config:
  # ...
  extra_path_dirs:
    - vendor/bin
    - node_modules/.bin
```

## Extra Steps
You can add custom steps to the process of building the environment in which your app runs.

```yaml
run.config:
  # ...
  extra_steps:
    - npm install
```

## Filesystem Watching & Live Reload
Many front-end development frameworks rely on a filesystem watcher that tells the OS when a file is modified and triggers some type of event (usually a browser refresh). Nanobox includes a file-watcher that is disabled by default. You can enable the file-watcher for the duration of `run` using the `fs_watch` setting your boxfile.yml.

```yaml
run.config:
  # ...
  fs_watch: true
```
