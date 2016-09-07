## SIMP Packer manifests

#### Table of Contents

* [Overview](#overview)
* [Setup](#setup)
* [Usage](#usage)
	* [Simple build](#simple-build)
* [Notes](#notes)
* [TODO](#todo)
* [DONE](#done)

### Overview

[Packer](https://packer.io) configuration to build a [Vagrant](https://www.vagrantup.com/) box directly from a fresh [SIMP](https://github.com/NationalSecurityAgency/SIMP) ISO. The Vagrant boxes it builds use the [VirtualBox](https://www.virtualbox.org/wiki/Downloads) provider.


### Setup

Requirements:
  - [Packer](https://www.packer.io/downloads.html)
  - [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
  - [Vagrant](https://www.vagrantup.com/downloads.html)

### Usage
#### Simple build
1\. Tweak (or generate) the `vars.json` file with appropriate values
- A `.json` file for each SIMP ISO is generated by `rake build:auto`.

2\. Validate the setup:
```sh
~/bin/packer validate -var-file=vars.json simp.json
```
3\. Run packer:
_(Environment variables and executable path are examples, not suggestions)_
```sh
~/bin/packer build -var-file=vars.json simp.json
```
4\. Alternatively, use some of my favorite [environment variables](https://www.packer.io/docs/other/environmental-variables.html):
```
# PACKER_CACHE_DIR - keeps ginormous tmp files out of /tmp
# PACKER_LOG       - if set with anything, write to a log file
# PACKER_LOG_PATH  - the location of the log file

PACKER_LOG=1 PACKER_LOG_PATH=packerlog.txt PACKER_CACHE_DIR=$PWD/tmp  ~/bin/packer build -var-file=vars.json simp.json
```

5\. Using the default values in `vars.json`, a successful build should drop the new VM under `./OUTPUT`.


### Notes
- The vagrant post-processor writes to `/tmp` unless `PACKER_CACHE_DIR` is set.  If `/tmp` fills up, the following errors occur:

```
==> virtualbox-iso: Running post-processor: vagrant
==> virtualbox-iso (vagrant): Creating Vagrant box for 'virtualbox' provider
    virtualbox-iso (vagrant): Copying from artifact: OUTPUT/packer-virtualbox-iso-1454117110-disk1.vmdk
Build 'virtualbox-iso' errored: 1 error(s) occurred:

* Post-processor failed: write /tmp/packer040362584/packer-virtualbox-iso-1454117110-disk1.vmdk: no space left on device

==> Some builds didn't complete successfully and had errors:
--> virtualbox-iso: 1 error(s) occurred:

* Post-processor failed: write /tmp/packer040362584/packer-virtualbox-iso-1454117110-disk1.vmdk: no space left on device

==> Builds finished but no artifacts were created.
```

### TODO
- [ ] modularize `simp.json` sections
- [x] work on `<wait10>` timings
- [x]  ~~fix sudo tty issue~~
- [x] ~~put paths, credentials in `-var-` or `-var-file`~~
