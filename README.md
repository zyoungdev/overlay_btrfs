# overlay_btrfs - Initcpio hook

This is a bash script to mount a btrfs subvolume/snapshot as the upper layer of an overlayfs.

## Prerequisites
* Arch Linux
* Installation on btrfs filesystem

## Installation

``` bash
> git clone <this repo>
> cd <this repo>
> cp initcpio/* /usr/lib/initcpio/
> vim /etc/mkinitcpio.conf
> # add overlay and btrfs to modules
> # add overlay_btrfs to hooks after dev
> mkinitcpio
> # add overlay_snapshot=<path to snapshot> to kernel line in bootloader
```

## Why?

This allows you to keep the base installation and your modifications completely separate. You can then easily backup the changes using `btrfs send`, `tar`, or `rsync`. Reverting changes only requires changing snapshots and is separated from software installation.

## How?

By using an empty subvolume, two directories are created to mount the overlayfs. The lower directory can be mounted normally with a separate filesystem or another subvolume/snapshot on the same filesystem.

## Caveats

Updating and installing software should be done by mounting the lower directory separately and chrooting. This is because any updates or installations will go to the upper directory negating the purpose of this configuration.
