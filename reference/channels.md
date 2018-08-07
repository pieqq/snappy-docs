---
layout: base
title: Channels
---

Channels allow users to subscribe to different release cadences for an app, be it by major version bumps or in-development releases.

You can provide users with automated upgrades for [daily releases](https://build.snapcraft.io/), beta, candidate or stable releases. They also let you manage major software series and provide fixes to users wishing to stay on a specific series.

## Structure of a channel

A release channel is created and named using the following tuple:

```
<track>/<risk level>/<branch>
```

### Syntax

* [__track__](#track) (string): Indicates a release series with a free-form string such as "`1.0`", "`2`", "`trusty`", etc. When omitted, the default track ("`latest`") is assumed.
* [__risk level__](#risk-level) (string, "`stable`", "`candidate`", "`beta`" or "`edge`"): indicates the stability users can expect from the software.
* [__branch__](#branch) (optional, string): indicates a temporary branch derived from a risk level. Example: `fix-for-bug123`.

In practice, you will use this syntax when releasing or installing software from the command-line.

At any time, you can see the channel mapping of your own published snaps with the `snapcraft status <snap>` command.

See the [command-line usage](#command-line-usage) section for practical examples.

### Discoverability of snaps

The snap search API (used by the `snap find` command) only returns results for snaps in channels with **a "stable" risk level and no branch**. This ensures stability for users.

Nevertheless, if you know the name of a snap, the `snap info <snap>` command will give you its complete tracks and risk levels map.

## Track

Tracks allow you to publish different series of your software (1.0, 2.0, etc.) and let users follow and get automated upgrades from a specific one.

### Overview

<iframe width="720" height="405" src="https://www.youtube.com/embed/S3xRFnbVkvs?rel=0&showinfo=0" allowfullscreen></iframe>

* By default, snaps are published to a track called `latest`. This is also the default for users when they install a snap, for example `snap install my-app --beta` implies `--channel=latest/beta`.
* Users do not get automatically moved between tracks. It is the user's decision to not install snaps from the `latest` track, or to move back to it or any other track. Installing a snap from a track is done with the `--channel=<track>/<risk level>` flag.
* To create a track you need to request it and get it approved by the developer community. This process happens in the Snapcraft forum, see the [requests guidelines](https://forum.snapcraft.io/t/process-for-reviewing-aliases-auto-connections-and-track-requests/455).

## Risk level

There are four risk levels available for snaps, that denote the stability of revisions they contain.

### Overview

<iframe width="720" height="405" class="video-embed" src="https://www.youtube.com/embed/-3b9qkl9Z_k?rel=0&showinfo=0" allowfullscreen></iframe>

* Note that in developer discussions and some documentation, the "risk level" is often referred as the "channel", since most snaps only use the default track and no branches.
* By default, the `snap install` command installs snaps from the `stable` level.
* When a channel is explicitly closed with the `snapcraft close` command, users following this channel are automatically moved to the channel with the next safest risk level in the same track. For example, if you run `snapcraft close my-app 1.0/edge`, users following this channel will be moved to the `1.0/beta` channel.

### Risk levels meaning

* `stable`: what most users will consume and as the name suggests, should be your most polished, stable and tested versions.
* `candidate`: used to vet uploads that should require no further code changes before moving to stable.
* `beta`: used to provide preview releases of tested changes.
* `edge`: for your most recent changes, probably untested and with no guarantees attached.

Make sure you follow these guidelines, as they have an impact on the [discoverability of snaps](#discoverability-of-snaps) in search results.

### Risk level restrictions: confinement and grade

In your [`snapcraft.yaml`](/build-snaps/syntax), you can declare the development status of your snap with the `grade` keyword and its confinement policy with the `confinement` one.

Depending on these, your snap can be restricted to certain levels.

|                 | `confinement: strict`  | `confinement: classic` | `confinement: devmode` |
| --------------- | ---------------------- | ---------------------- | ---------------------- |
| `grade: stable` | *all* levels         | *all* levels         | `beta` and `edge` only |
| `grade: devel`  | `beta` and `edge` only | `beta` and `edge` only | `beta` and `edge` only |

## Branch

Branches are temporary and created on demand when releasing a snap. Their purpose is to provide your users with an easy way to test your bug fixes.

### Overview

* Branches are created with the `snapcraft release` command. See [command-line usage](#releasing-a-snap) for examples.
* Branches are automatically closed after 30 days without a new revision being released into them. The expiration date of a branch can be checked at any time by the publisher of a snap using the `snapcraft status <snap>` command, which provides a complete map of tracks, risk levels, branches and their expiration dates.
* Users following a branch will be automatically moved to the risk level the branch is attached to. For example, if you tell users to install your snap with the following command `snap install my-app --channel=beta/fix-for-bug123`, after 30 days, the branch will close and these users will be moved to the `beta` channel, as if they had used `snap install my-app --channel=beta`.
* Branches are not visible in the `snap info` command output unless you are following one.


## Command-line usage

Note: when using tracks and channels from the command-line, when track or channel is omitted, `latest` or `stable` is assumed.

* [Releasing a snap](#releasing-a-snap)
* [Installing a snap](#installing-a-snap)
* [Viewing the channel mapping of a snap](#viewing-the-channel-mapping-of-a-snap)

### Releasing a snap

To release a snap revision to a channel, the command to use is:

    snapcraft release <snap> <revision> <channel>

#### Examples

* Releasing revision 12 of "my-app" to the "beta" level of the "latest" default track:

  Channel syntax: `latest/beta`

         $ snapcraft release my-app 12 beta
      Or $ snapcraft release my-app 12 latest/beta

* Releasing revision 12 of "my-app" to the "stable" level of the "1.0" track:

  Channel syntax: `1.0/stable`

         $ snapcraft release my-app 12 1.0
      Or $ snapcraft release my-app 12 1.0/stable

* Releasing revision 12 of "my-app" to the "fix-for-bug123" branch of the "stable" level of the "latest" track:

  Channel syntax: `latest/stable/fix-for-bug123`

         $ snapcraft release my-app 12 stable/fix-for-bug123
      Or $ snapcraft release my-app 12 latest/stable/fix-for-bug123

### Installing a snap

To install a snap from a specific channel, the command to use is:

       snap install <snap> --channel=<channel>

#### Examples

* Installing the latest stable version of "my-app":

  Channel syntax: `latest/stable`

         $ snap install my-app
      Or $ snap install --stable
      Or $ snap install --channel=latest/stable

* Installing the beta version of "my-app" from the 1.0 track:

  Channel syntax: `1.0/beta`

       $ snap install my-app --channel=1.0/beta

* Installing "my-app" from the fix-for-bug123 branch of the "stable" level of the "latest" track:

  Channel syntax: `latest/stable/fix-for-bug123`

         $ snap install my-app --channel=stable/fix-for-bug123
      Or $ snap install my-app --channel=latest/stable/fix-for-bug123

### Viewing the channel mapping of a snap

To view the map of channels of a snap, the command to use is:

* As a publisher (complete mapping, including branches expiration date):

      snapcraft status <snap>

  For example:

      $ snapcraft status my-app
      Track    Arch    Channel              Version    Revision    Expires at
      latest   amd64   stable               2.2        155
                       candidate            ↑          -
                       beta                 2.3        180
                       edge                 daily      187
                       edge/fix-for-bug123  daily      189         2017-09-16T14:03:06.079634
      1.0      amd64   stable               1.9        88
                       candidate            -          -
                       beta                 -          -
                       edge                 -          -

* As a user (public mapping: no branches):

      snap info <snap>

  For example:

      $ snap info my-app
      [...]
      channels:                               
        stable:          2.2   (155) 43MB -
        candidate:       ↑
        beta:            2.3   (180) 37MB -
        edge:            daily (187) 37MB devmode

        1.0/stable:      1.9   (88) 42MB -
        1.0/candidate:   ↑
        1.0/beta:        -
        1.0/edge:        -