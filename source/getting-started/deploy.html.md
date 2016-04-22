---
title: Deploying to an App
---

Once you've [created your app on Nanobox](/getting-started/creating-an-app/), you're ready to deploy to it.

## Link
Before deploying your code to an app, you must first link your local codebase with your app on Nanobox. This is done using the Nanobox CLI's `link` command.

```bash
# cd into the root of your projects
$ cd app-dir

# Link the project to your Nanobox app
$ nanobox link app-name
```

For more information about the `link` command, view the ['link' documentation](/cli/commands/link).

## Build
In order to deploy code to Nanobox, you must first create a build package. This is done using the CLI's `build` command.

```bash
$ nanobox build
```

This will run everything associated with your app's [code.build](/app-config/boxfile/code-build/) and generate a build package that can be deployed to your Nanobox app.

## Deploy
The `deploy` command will push you generated build up to Nanobox and deploy it into your production environment.

```bash
$ nanobox deploy
```

## The Deploy Process
*svg Coming Soon*

 