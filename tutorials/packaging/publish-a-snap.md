---
id: publish-a-snap
summary: We are going to use snapcraft and its dashboard to make our snap available for bigger audience.
categories: packaging
status: published
tags: snapcraft, usage, publishing, beginner
difficulty: 1
published: 2017-12-07
feedback_url: https://github.com/canonical-websites/tutorials.ubuntu.com/issues
author: Marcin Mikołajczak <me@m4sk.in>

---

# How to publish a snap

## Overview
Duration: 1:00

`snapcraft` allows to upload snaps to the Snap Store, making us able to share them with the world. We will learn how to register name for our snap, upload it and release it to the proper channel. We will also use Dashboard to modify snap metadata.


![IMAGE](https://assets.ubuntu.com/v1/074862f8-snaps-hero.png)


### What you’ll learn

  - How to register name for snap.
  - What names are allowed.
  - How to upload snap.
  - How to publish uploaded snap.
  - What release channel is suitable for you snap.
  - How to modify snap metadata in Snap Store dashboard.

### What you’ll need

  - PC running Ubuntu 16.04 (recommended) or newer.
  - Some very basic knowledge of command line use.
  - We expect you to know how to create snaps and you understand the differences between available confinements and grades. The “[create your first snap]” tutorial is a good introduction to this.


Survey
: How will you use this tutorial?
 - Only read through it
 - Read it and complete the exercises
: What is your current level of experience?
 - Novice
 - Intermediate
 - Proficient


## Getting started
Duration: 1:00

If you have completed “Create your first snap” tutorial, you already have all the required tools.

You will need account on [Snap Store dashboard] connected with Ubuntu One account. If you already have Ubuntu One account, you will only have to accept Cannonical Terms of Service and set your snap store username.

Now, log in with `snapcraft login` command using e-mail address connected with your account:

```bash
$ snapcraft login
Enter your Ubuntu One e-mail address and password.
If you do not have an Ubuntu One account, you can create one at https://dashboard.snapcraft.io/openid/login
Email: 
Password:

Login successful.
```

Log in to snap with `snap login <e-mail address>` command:

```bash
$ snap login test@example.com
Password of "test@example.com": 
Login successful
```

postive
: In case you will need, you can log out with `snap logout` and `snapcraft logout`.

## Registering name for our snap
Duration: 4:00

### Using command line

Registering snap name through command line is pretty easy and straightforward. All we need to do is use `snapcraft register <snap_name>` and confirm that we will publish snaps that most people will except under this name. If there is already snap in name that we are going to claim, you can use `<snap_name>-<user-name>` as snap name:

```bash
$ snapcraft register <snap_name>
We always want to ensure that users get the software they expect
for a particular name.

If needed, we will rename snaps to ensure that a particular name
reflects the software most widely expected by our community.

For example, most people would expect ‘thunderbird’ to be published by
Mozilla. They would also expect to be able to get other snaps of
Thunderbird as 'thunderbird-$username'.

Would you say that MOST users will expect '<snap_name>' to come from
you, and be the software you intend to publish there? [y/N]:
Registering <snap_name>.
Congrats! You are now the publisher of '<snap_name>'.
```

### Using Snap Store dashboard

We can also use Snap Store dashboard to register name for our snap. Just open [dashboard.snapcraft.io] and click **Register Snap** or visit the [snap registration page]. You will need to select the store where you want to upload your snap, type its name and explain your rights to entered name. We can also mark package as private.

positive
: We can choose between 5 stores where we can upload snaps. Generally, if you don’t need to make your snap available only for specific platform, you should select “Global store”.

## Uploading the snap
Duration: 4:00

Now, after we registered name for our snap, we can upload our snap to Snap Store. After this, we will be able to publish it and make it available to the whole world.

### Differences between channels

We will need to take a look at your `snapcraft.yaml` file, to ensure that we have made our snap working in proper environment, allowing it to be released in proper channel. It depends of two properties – `grade` and `confinement`.

|                 | `confinement: strict`  | `confinement: classic` | `confinement: devmode` |
| --------------- | ---------------------- | ---------------------- | ---------------------- |
| `grade: stable` | *all* channels         | *all* channels         | `beta` and `edge` only |
| `grade: devel`  | `beta` and `edge` only | `beta` and `edge` only | `beta` and `edge` only |

As you can see, we need to make snap working in strict confinement – in proper isolation, or in classic confinement if we want release a snap in `stable` or `candidate` channel.

### Pushing the snap

In terminal window, we will have to change directory to the folder containg snap file. Now, simply use `snapcraft push <snap-file>.snap`:

```bash
$ snapcraft push <snap-file>.snap
Uploading <snap-file>.snap [====================] 100%
Processing ...
Ready to release!
Revision 1 of '<snap-name>' created.
```

negative
: Ensure that the name of snap you have registered equals name placed in `snapcraft.yaml`. For example, if you have registered `firefox-m4sk1n`, but you have named your snap `firefox`, change name (and exposed command) in `snapcraft.yaml` and rebuild snap.

### Revisions

Each time you upload a snap, the Snap Store will assign a revision number to it, starting at 1. This revision number will be incremented each time you upload a new version of your snap. The revision number also increments when uploading a build for a new architecture. You can see all revisions of your snap using `snapcraft list-revisions <snap-name>`.

Now, let’s release our snap!


## Releasing the snap
Duration: 3:00

The fact that we uploaded snap doesn’t make it available for the world. We will need to release it to the proper channel.

positive
: You can also use `snapcraft push <snap-file>.snap --release <channel>` to push and release snap at once.

### Differences between channesl

We can choose between four channels:
  - `stable` is place for stable and tested versions of snaps
  - `candidate` is place for snaps that will appear in `stable` channel soon, but aren’t properly tested
  - `beta` is place for potentially unstable snaps, snaps with `devmode` flag can be uploaded here
  - `edge` is place for untested and not fully working packages, usually based on development branch, usually regularly uploaded

### Releasing from command line

To release snap from command line, use `snapcraft release <snap-name> <revision> <channel>`. Example:

```bash
$ snapcraft release <snap-name> 1 stable
The 'stable' channel is now open.

Channel    Version    Revision
---------  ---------  ----------
stable     v1.0       1
candidate  -          -
beta       -          -
edge       -          -
```

Now, our snap is available for the whole world. Everyone can see how awesome it is!


## Changing snap metadata

In Snap Store dashboard we can change the way our snap appears in Snap Store. We can set more “friendly” name (with spaces, capital letters etc.), description, logo and even more. You can even set different description for each language!

### Updating snap details

Visit [Snap Store dashboard] and click on ***All snaps***. Select the snap you are going to modify and click on ***Edit name***. You will be able to change its name, summary and set URL where people can report issues related to your snap. In **Discovery** section you can change type of your package (application or service) and change longer description. You can set keywords for your snap.

positive
: Keywords should contain most commonly used phrases that people can use when searching for this kind of snap. For example, it have no sense to place language used to write this app. Does it matter to average user what language was used to create his favourite web browser?

In **Presentation** section you can set icon for your snap, it has to be a 256x256 image file. You can also upload screenshot(s) or provide video showing your snap in action (from Vimeo). In **License** section you can set license under which your snap will be available. You can go back to snap overview by clicking **Overview** button.

For localizing description, find Translation section in overwiew and select language. You can set different name, summary, long description, changelog and even screenshots for each language!


## That’s all folks!
Duration: 1:00

### Easy, wasn’t it?

Congratulations! You made it!


By now you should know how to register name for your snap, upload and publish it. You should understand differences between available distribution channels.

### Next steps

  - Join the snapcraft.io community on the [snapcraft forum].

### Further readings
  - You can use commands from this tutorial with `--help` parameter for more information.
  - [Snapcraft.io documentation] also covered the process of publishing snap.
  - Check how you can [contact us and the broader community].





[create your first snap]: https://tutorials.ubuntu.com/tutorial/create-your-first-snap
[Snap Store dashboard]: https://dashboard.snapcraft.io/
[snapcraft forum]: https://forum.snapcraft.io/
[Snapcraft.io documentation]: http://snapcraft.io/docs/
[contact us and the broader community]: http://snapcraft.io/community/
