---
layout: base
title: Electron
---

Snapcraft builds on top of the Electron desktop application framework to create snaps for people to install on Linux.

## What problems do snaps solve for Electron applications?

Compared to Mac and Windows, distributing an Electron application for Linux and reaching the widest possible audience was complicated. How applications are packaged and delivered varies from distribution to distribution. With snapcraft it’s just one command to produce a bundle that works anywhere.

Here are some snap advantages that will benefit many Electron projects:

* Simplify installation instructions, regardless of distribution, to `snap install myelectronapp`.
* Directly control the delivery of automatic application updates.

## How long will this guide take to complete?

Typically this guide will take around 20 minutes and will result in a working Electron app in a snap. Once complete, you'll understand how to package Electron applications as snaps and deliver them to millions of Linux users. After making the snap available in the store, you'll get access to installation metrics and tools to directly manage the delivery of updates to Linux users. 

## Getting started

**Note:** We strongly recommend using an Ubuntu 16.04 host, VM or container for this guide. While it is possible to use newer releases of Ubuntu, or other Linux distributions, this may result in incorrect libraries being pulled into the build.

By way of an example, let’s take a look at how a snap is created for the Electron Quickstart app.

### Electron Quickstart

Using `electron-builder` and a few lines of additional configuration in the `package.json` file an Electron project can be adapted to produce snaps. We’ll break this down.

```json
{
  "name": "electron-quick-start",
  "version": "1.0.0",
  "description": "A minimal Electron application",
  "main": "main.js",
  "scripts": {
    "start": "electron .",
    "dist": "build"
  },
  "build": {
    "appId": "io.snapcraft.quick-start",
    "linux": {
      "target": ["snap"]
    }
  },
  "repository": "https://github.com/electron/electron-quick-start",
    "keywords": [
      "Electron",
      "quick",
      "start",
      "tutorial",
      "demo"
    ],
  "author": "GitHub",
  "license": "CC0-1.0",
  "devDependencies": {
    "electron": "~1.7.8",
    "electron-builder": "^19.45.4"
  }
}
```

### Packaging for operating systems

A distribution target (`dist`) needs to be added to the `scripts:` stanza to create package builds for Linux, Windows, and Mac.

```json
  "scripts": {
    "start": "electron .",
    "dist": "build"
  },
```

### Adding a snap package

The distribution target is then made to start the snap build. The `appId` must be set, but is not used on Linux. Refer to the [Electron Builder documentation](https://www.electron.build/configuration/configuration) for an appropriate value if you are also building for Windows and Mac.

```json
  "build": {
    "appId": "io.snapcraft.quick-start",
    "linux": {
      "target": ["snap"]
    }
  },
```

### Building the snap

You’ll first need to [install snap support](https://docs.snapcraft.io/core/install), and then install the snapcraft tool:
```
sudo snap install snapcraft --classic
```

To build Electron applications you also need Node installed, which can be downloaded from [NodeSource](https://github.com/nodesource/distributions).

If you have just installed snap support, start a new shell so your `PATH` is updated to include `/snap/bin`. You can then build this example yourself:
```
git clone https://github.com/snapcraft-docs/electron-quick-start
cd electron-quick-start
npm install
npm run dist
```

The resulting snap can be installed locally. This requires the `--dangerous` flag because the snap is not signed by the Snap Store. The `--devmode` flag acknowledges that you are installing an unconfined application:
```
sudo snap install dist/electron-quick-start_1.0.0_amd64.snap --dangerous --devmode
```

You can then try it out:
```
electron-quick-start
```
Removing the snap is simple too:
```
sudo snap remove electron-quick-start
```

### Share with your friends

To share your snaps you need to publish them in the Snap Store. First, create an account on [dashboard.snapcraft.io](https://dashboard.snapcraft.io/dev/account/). Here you can customize how your snaps are presented, review your uploads and control publishing.

You’ll need to choose a unique “developer namespace” as part of the account creation process. This name will be visible by users and associated with your published snaps.

Make sure the `snapcraft` command is authenticated using the email address attached to your Snap Store account:

```
snapcraft login
```

#### Reserve a name for your snap

You can publish your own version of a snap, provided you do so under a name you have rights to. You can register a name on [dashboard.snapcraft.io](https://dashboard.snapcraft.io/register-snap/), or by running the following command:

```
snapcraft register myelectronsnap
```

Be sure to update the `name:` in your `package.json` to match this registered name, then run `npm dist` again.

#### Upload your snap

Use snapcraft to push the snap to the Snap Store.

```
snapcraft push --release=edge myelectronsnap_*.snap
```

Running `npm dist` creates a `snapcraft.yaml` file in the root of your project. If you’re happy with your snap, you can commit this file to your GitHub repo and [turn on automatic builds](https://build.snapcraft.io) so any further commits automatically get released to edge, without requiring you to manually build locally.

<!--
## Next steps

Congratulations, you have an app in edge ready to share with other developers.

Want to learn more? Continue on to learn how to get your app ready for a wider audience.
-->

## More information

- Tutorial: [Snap a website with Electron](https://tutorials.ubuntu.com/tutorial/snap-a-website?backURL=https://docs.snapcraft.io/build-snaps/electron)
- Video: [Easily distribute Electron apps via Snap](https://www.youtube.com/watch?v=xPtPiaHIghs) (37 minutes)
