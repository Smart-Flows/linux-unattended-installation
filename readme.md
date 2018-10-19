# Linux Unattended Installation

This project provides all you need to create an unattended installation of a minimal setup of Linux, whereas *minimal* translates to the most lightweight setup - including an OpenSSH service and Python - which you can derive from the standard installer of a Linux distribution. The idea is, you will do all further deployment of your configurations and services with the help of Ansible or similar tools once you completed the minimal setup.

Use the `ubuntu/16.04/custom/build-iso.sh` script to create an ISO file based on the netsetup image of Ubuntu 16.04

For more information, and update see the original repo (Linux Unattended Installtion)[https://github.com/core-process/linux-unattended-installation].

## Features

* Fully automated installation procedure.
* Shutdown and power off when finished. We consider this a feature since it produces a defined and detectable state once the setup is complete.
* Authentication based on SSH public key and not on a password.
* Partition the installation disk using LVM.
* Generates SSH server keys on first boot and not during setup stage. We consider this a feature since it enables you to use the installed image as a template for multiple machines.
* Prints IPv4 and IPv6 address of the device on screen once booted.
* USB bootable hybrid ISO image.
* UEFI and BIOS mode supported.

## Prerequisites

The `build-iso.sh` script software requirements are:

 * `cpio`
 * `dos2unix`
 * `fakeroot`
 * `genisoimage`
 * `gzip`
 * `isolinux`
 * `p7zip-full`
 * `patch`
 * `pwgen`
 * `wget `
 * `wget`
 * `whois`
 * `xorriso`

Run `sudo apt-get install -y cpio dos2unix fakeroot genisoimage gzip isolinux p7zip patch pwgen wget wget whois xorriso` to install them.

To create a bootable USB disk, you can use `usb-creator-gtk` (or ` usb-creator-kde`), that can be installed using `sudo apt-get install -y usb-creator-gtk`.

## Usage

### Build ISO images

You can run the `build-iso.sh` script as regular user. No root permissions required.

```sh
./ubuntu/16.04/build-iso.sh <ssh-public-key-file> <target-iso-file>
```

All parameters are optional.

| Parameter | Description | Default Value |
| :--- | :--- | :--- |
| `<ssh-public-key-file>` | The ssh public key to be placed in authorized_keys | `ssh/id_rsa.pub` |
| `<target-iso-file>` | The path of the ISO image created by this script | `ubuntu-16.04-netboot-amd64-unattended.iso` |

### Create boot USB disk

Use `usb-creator-gtk` by issuing the following command in a terminal:

```sh
sudo usb-creator-gtk
```

Select the generated ISO image (its default name is `ubuntu-16.04-netboot-amd64-unattended.iso`) and a USB disk of at least 64 MB (!), then create the disk.

### Install a new machine

First, boot the machine, to configure the following elements:

 * Disable Windows related options
 * Re-order the boot devices to set USB Floppy, Key, Hard Disk, CD/DVD **before** the first internal hard disk.
 * In the `Power Management Setup`, set the `Restore after AC Power Loss` to ` Power On`.

Plug the installation USB disk in the machine and an ethernet cable (for Internet access), and reboot the machine.
This will trigger hhe unattended installation. Be aware the setup will start within 10 seconds automatically and will reset the disk of the target device completely. The setup tries to eject the ISO/CD during its final stage. It usually works on physical machines, and it works on VirtualBox. It might not function in certain KVM environments in case the managing environment is not aware of the *eject event*. In that case, you have to detach the ISO image manually to prevent an unintended reinstall.

When the installation finishes, the machine stops. Remove the usb disk and power-on the machine. You can (remote) log into it as `root` using the ssh key you selected when creating the ISO. If you did not select a different key, you will have to use the `ssh/id_rsa` to log into it, using:

```sh
ssh -o IdentitiesOnly=yes -i ssh/id_rsa root@<IP ADDRESS>
```
