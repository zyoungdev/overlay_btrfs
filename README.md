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
> cp /etc/mkinitcpio.conf /etc/mkinitcpio.overlay.conf
> vim /etc/mkinitcpio.overlay.conf
> # add overlay and btrfs to modules
> # add overlay_btrfs to hooks after dev
> cp /etc/mkinitcpio.d/linux.preset /etc/mkinitcpio.d/overlay.preset
> vim /etc/mkinitcpio.d/overlay.preset
> # change filename of image, remove fallback
> mkinitcpio -p overlay
> # add new boot configuration to bootloader
> # add overlay_snapshot=<path to snapshot> to kernel line
```

### Example mkinitcpio.overlay.conf
``` conf
MODULES=(overlay btrfs)
BINARIES=()
FILES=()
HOOKS=(base udev autodetect overlay_btrfs modconf block filesystems keyboard fsck)
```

### Example mkinitcpio preset
``` conf
ALL_config="/etc/mkinitcpio.overlay.conf"
ALL_kver="/boot/vmlinuz-linux"
PRESETS=('default')
default_image="/boot/initramfs-linux-overlay.img"
```

### Example kernel line
``` conf
options  "root=UUID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx rootflags=subvol=<path to lower subvolume/snapshot> overlay_snapshot=<path to upper subvolume/snapshot>"
```

## Why?

This allows you to keep the base installation and your modifications completely separate. You can then easily backup the changes using `btrfs send`, `tar`, or `rsync`. Reverting changes only requires changing snapshots and is separated from software installation.

## How?

By using an empty subvolume, two directories (i.e. delta, work) are created to mount the overlayfs. The lower directory currently must be on the same btrfs device.

## Caveats

Updating and installing software should be done by mounting the lower directory separately and chrooting. This is because any updates or installations will go to the upper directory negating the purpose of this configuration.
