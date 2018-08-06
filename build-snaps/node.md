---
layout: base
title: Node
---

<iframe width="720" height="405" class="video-embed" src="https://www.youtube.com/embed/S3YvRALc2C0?rel=0&showinfo=0" allowfullscreen></iframe>

Node has rich tools for packaging, distributing and sandboxing applications. Snapcraft builds on top of these familiar tools such as `npm` and `yarn` to create snaps.

## What problems do snaps solve for Node applications?

Often Linux install documentation for Node applications is complicated, requiring software to be installed via `apt`, `rpm` and `npm` or care taken to run with the right version of Node. With snapcraft it’s just one command to produce a bundle that works anywhere.

Here are some snap advantages that will benefit many Node projects:

  * Bundle all the runtime requirements, including the exact versions of system libraries.
  * Simplify installation instructions, regardless of distribution, to `snap install mynodeapp`.
  * Directly control the delivery of automatic application updates.
  * Extremely simple creation of services.
  
## How long will this guide take to complete?

Typically this guide will take around 20 minutes and will result in a working Node app in a snap. Once complete, you'll understand how to package Node applications as snaps and deliver them to millions of Linux users. After making the snap available in the store, you'll get access to installation metrics and tools to directly manage the delivery of updates to Linux users. 

## Getting started

**Note:** We strongly recommend using an Ubuntu 16.04 host, VM or container for this guide. While it is possible to use newer releases of Ubuntu, or other Linux distributions, this may result in incorrect libraries being pulled into the build.

By way of an example, let's take a look at how a snap is created for the wethr app.

### wethr

Snaps are defined in a single yaml file placed in the root of your project. The wethr example shows the entire `snapcraft.yaml` for an existing project, leveraging the existing `package.json` to satisfy runtime requirements. We'll break this down.

```yaml
name: wethr
version: git
summary: Command line weather tool.
description: |
  Get current weather:-
    $ wethr
  Get current weather in metric units
    $ wethr --metric
  Get current weather in imperial units
    $ wethr --imperial

grade: devel
confinement: devmode

apps:
  wethr:
    command: wethr

parts:
  wethr:
    plugin: nodejs
    source: .
```

#### Metadata

The `snapcraft.yaml` starts with a small amount of human-readable metadata, which usually can be lifted from the GitHub description or project README.md. This data is used in the presentation of your app in the Snap Store. The `summary:` can not exceed 79 characters. You can use a pipe in the `description:` key to declare a multi-line description.

```yaml
name: wethr
version: git
summary: Command line weather tool.
description: |
  Get current weather:-
    $ wethr
  Get current weather in metric units
    $ wethr --metric
  Get current weather in imperial units
    $ wethr --imperial
```

#### Confinement

To get started we won’t confine this application. Unconfined applications, specified with `devmode`, can only be released to the hidden “edge” channel where you and other developers can install them.

```yaml
confinement: devmode
```

#### Parts

Parts define how to build your app. Parts can be anything: programs, libraries, or other assets needed to create and run your application. In this case we have one: the wethr source code. In other cases these can point to local directories, remote git repositories, or tarballs.

The Node plugin will also bundle Node in the snap, so you can be sure that the version of Node you test against is included with your app. Dependencies from your package.json will also be bundled.

```yaml
parts:
  wethr:
    plugin: nodejs
    source: .
```

#### Apps

Apps are the commands and services exposed to end users. If your command name matches the snap `name`, users will be able run the command directly. If the names differ, then apps are prefixed with the snap `name` (`wethr.command-name`, for example). This is to avoid conflicting with apps defined by other installed snaps.

If you don’t want your command prefixed you can request an alias for it on the [Snapcraft forum](https://forum.snapcraft.io/t/process-for-reviewing-aliases-auto-connections-and-track-requests/455). These are set up automatically when your snap is installed from the Snap Store.

```yaml
apps:
  wethr:
    command: wethr
```

If your application is intended to run as a service you simply add the line `daemon: simple` after the command keyword. This will automatically keep the service running on install, update and reboot.

### Building the snap

You’ll first need to [install snap support](/core/install), and then install the snapcraft tool:
```
sudo snap install snapcraft --classic
```

If you have just installed snap support, start a new shell so your `PATH` is updated to include `/snap/bin`. You can then build this example yourself:

```
git clone https://github.com/snapcraft-docs/wethr
cd wethr
snapcraft
```

The resulting snap can be installed locally. This requires the `--dangerous` flag because the snap is not signed by the Snap Store. The `--devmode` flag acknowledges that you are installing an unconfined application:

```
sudo snap install wethr_*.snap --devmode --dangerous
```

You can then try running wethr.

```
wethr
```

Removing the snap is simple too:

```
sudo snap remove wethr
```

## Share with your friends

To share your snaps you need to publish them in the Snap Store. First, create an account on [the dashboard](https://dashboard.snapcraft.io/dev/account/). Here you can customize how your snaps are presented, review your uploads and control publishing.

You’ll need to choose a unique “developer namespace” as part of the account creation process. This name will be visible by users and associated with your published snaps.

Make sure the `snapcraft` command is authenticated using the email address attached to your Snap Store account:

```
snapcraft login
```

### Reserve a name for your snap

You can publish your own version of a snap, provided you do so under a name you have rights to. You can register a name on [dashboard.snapcraft.io](https://dashboard.snapcraft.io/register-snap/), or by running the following command:

```
snapcraft register mynodesnap
```

Be sure to update the `name:` in your `snapcraft.yaml` to match this registered name, then run `snapcraft` again.

### Upload your snap

Use snapcraft to push the snap to the Snap Store.

```
snapcraft push --release=edge mynodesnap_*.snap
```

If you’re happy with the result, you can commit the snapcraft.yaml to your GitHub repo and [turn on automatic builds](https://build.snapcraft.io) so any further commits automatically get released to edge, without requiring you to manually build locally.

## Further customisations

Here are all the Node plugin-specific keywords:

```
    - node-packages:
      (list)
      A list of dependencies to fetch using npm.
    - node-engine:
      (string)
      The version of nodejs you want the snap to run on.
    - npm-run:
      (list)
      A list of targets to `npm run`.
      These targets will be run in order, after `npm install`
    - node-package-manager
      (string; default: npm)
      The language package manager to use to drive installation
      of node packages. Can be either `npm` (default) or `yarn`.
```

You can view them locally by running:

```
snapcraft help nodejs
```

### Extending and overriding behaviour

You can [extend the behaviour](/build-snaps/scriptlets) of any part in your `snapcraft.yaml` with shell commands. These can be run after pulling the source code but before building by using the `prepare` keyword. The build process can be overridden entirely using the `build` keyword and shell commands. The `install` keyword is used to run shell commands after building your code, useful for making post build modifications such as relocating build assets.

Using the wethr example above, we can run the test suite at the end of the build. If this fails, the snap creation will be terminated:

```yaml
parts:
  wethr:
    plugin: nodejs
    source: .
    install: |
      npm test
```

<!--
## Next steps

Congratulations, you have an app in edge ready to share with other developers.

Want to learn more? Continue on to learn how to get your app ready for a wider audience.
-->

## More information

- Tutorial: [Build a nodejs service snap](https://tutorials.ubuntu.com/tutorial/build-a-nodejs-service?backURL=https://docs.snapcraft.io/build-snaps/node)
