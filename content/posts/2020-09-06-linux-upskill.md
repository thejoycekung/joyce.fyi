---
title: "Linux Upskill Challenge: Setting up on Google Cloud Platform"
category: upskill
date: 2020-09-06T14:02:54-04:00
---

This September[^1], I'm going to participate in the [Linux Upskill Challenge](https://www.linuxupskillchallenge.org/). I read about it a little [on Hacker News](https://news.ycombinator.com/item?id=24380969) yesterday, and it looks like it aligns really well with my goals of working as an SRE.

[^1]: Yes, I know I was working on [Linux from Scratch](http://www.linuxfromscratch.org/) at the end of August, but [my boot issues](https://twitter.com/commitsbyjoyce/status/1301336885734895617) have been very draining - I think this will be a good distraction.

The challenge starts tomorrow, so today is "Day 0" of 20: getting set up. It's possible that I may create new posts related to this challenge - even though the content is listed publicly on Github at all times - but we'll see at the end of this month.

Day 0 asks you to set up a Virtual Private Server (VPS). I chose to use Google Cloud Platform[^2] for my VPS, because I still have credits left from this summer. 

[^2]: The challenge offers instructions for [AWS](https://github.com/snori74/linuxupskillchallenge/blob/master/00-AWS-Free-Tier.md) and [DigitalOcean](https://github.com/snori74/linuxupskillchallenge/blob/master/00-Digital-Ocean.md), and I know AWS/Azure both have free/student tiers available, making them good options as well.

# Creating the Virtual Machine

This setup tutorial assumes that you have a GCP account and access to a terminal (e.g. through WSL, or just being on a Unix system).

I used the `gcloud` CLI to create my VPS. This requires a few things:
* You have the [`gcloud` SDK](https://cloud.google.com/sdk/install) (which includes `gcloud` CLI) installed
* You have the [`gcloud` SDK configured](https://cloud.google.com/sdk/docs/initializing) with your account, with a default region, zone, and project
  * A list of zones and regions can be found by running `gcloud compute zones list`.
* The `gcloud compute` API is enabled for your project

Once you have those, you should be good to run this command in your terminal, taking extra care to replace `[ZONE]` with your chosen `gcloud` zone:

```sh
gcloud compute instances create upskill-host \
--zone=[ZONE] \
--machine-type=e2-micro \
--tags=http-server \
--image=ubuntu-2004-focal-v20200902 \
--image-project=ubuntu-os-cloud \
--boot-disk-size=10GB \
--boot-disk-type=pd-standard \
--boot-disk-device-name=upskill-host
```

Some notes on the command itself:
- `upskill-host` is what I decided to call my instance. Feel free to customize!
- Zone is unchangeable once you've made the instance. Good thing you already picked out your perfect zone!
- Our `machine-type` is `e2-micro`: This is a single vCPU with 1 GB of memory, and is exactly the same size as [AWS's `t2.micro`](https://aws.amazon.com/ec2/instance-types/t2/) (which [the challenge advises you](https://github.com/snori74/linuxupskillchallenge/blob/master/00-AWS-Free-Tier.md) to use).
- The `tag` is a network tag; you'll see why we need it in the next step.
- The `image` is our OS type: here, we use Ubuntu 20.04 which pulls from the Ubuntu OS cloud. [The challenge advises Ubuntu 18.04 or 20.04.](https://github.com/snori74/linuxupskillchallenge/blob/master/00-AWS-Free-Tier.md)
- Our boot disk is 10GB on a persistent disk, which is the default configuration. I also specified my boot disk device name, but I'm not sure if that is necessary.

Note: `gcloud` may issue a warning that because our disk is under 200GB, we may suffer in I/O performance. Feel free to ignore it.

Once `gcloud` has finished creating the instance, it will output a table with the instance name, zone, machine type, internal/external IPs, and status. The status should say `RUNNING` - meaning your machine is ready to use!

# Configuring the Network

We need to run two more commands before accessing it for the first time, though. By default, GCP disallows all incoming HTTP and HTTPS traffic (this makes sense normally). We want to allow this, though, as [we want to practice with a vulnerable server](https://github.com/snori74/linuxupskillchallenge/blob/master/00-AWS-Free-Tier.md#signing-up-with-aws).

To do that, we can run two commands to create new firewall rules, `default-allow-http` and `default-allow-https` to allow inbound traffic for HTTP and HTTPS.

```sh
gcloud compute firewall-rules create default-allow-http \
--direction=INGRESS --priority=1000 --network=default \
--action=ALLOW --rules=tcp:80 --source-ranges=0.0.0.0/0 \
--target-tags=http-server

gcloud compute firewall-rules create default-allow-https \
--direction=INGRESS --priority=1000 --network=default \
--action=ALLOW --rules=tcp:443 --source-ranges=0.0.0.0/0 \
--target-tags=http-server
```

Some notes on the command:
- The `INGRESS` direction means that this is **inbound** traffic. Other people can attack us, but we can't send any data anywhere for now.
- The default `priority` when creating a firewall rule is `1000`. However, this number goes from `0` to `65535`, and the lower it is, the higher priority it is.
- We are using a `default` network because this instance is not registered in a Virtual Private Cloud (VPC).
- The `rules` we use are `tcp:80` and `tcp:443`. These are the default ports used for HTTP and HTTPS, respectively.
- The `source-ranges` is a single address, `0.0.0.0/0`. As a source, this basically means "any IPv4 address".
- The list of `target-tags` is just `http-server` - the tag we set when we first created this instance.

Once you have the firewall rules set, you should get some output with the name, network, direction, priority, allow/deny ports. The last column reads `DISABLED`, and it should be `false`. Yay - now your server is vulnerable!

# Logging in for the first time

Now we have to make sure your access is okay. Login to the instance by using:

```sh
gcloud compute ssh upskill-host
```

Or substituting `upskill-host` with your instance name of choice. If you get a few lines of output like:

```sh
Updating project ssh metadata...⠼
Updating project ssh metadata...done.
Waiting for SSH key to propagate.
Warning: Permanently added 'REDACTED' (ECDSA) to the list of known hosts.
```

That's your SSH connection setting up. You'll know you've successfully logged into the instance when it says `Welcome to Ubuntu 18.04.5 LTS` followed by some system information, followed by a regular terminal prompt.

At the prompt, we're going to test this by updating and upgrading a few packages:

```sh
sudo apt update
sudo apt upgrade
```

`apt` is the package manager on Ubuntu/Debian. `update` pulls the latest versions of your packages from their sources, while `upgrade` actually updates them. You may need to press "Y" to advance through the upgrade.

Note: we run these commands as `sudo`, or the superuser. This is because updating the package database requires higher privileges than our "regular" user.

Once those are complete - we're ready to go! See you on Day 1.
