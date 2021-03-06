![](https://github.com/fission-suite/PROJECTNAME/raw/master/assets/logo.png?sanitize=true)

# Basquiat - Image Resizing for ipfs

[![Build Status](https://travis-ci.org/fission-suite/PROJECTNAME.svg?branch=master)](https://travis-ci.org/fission-suite/PROJECTNAME)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://github.com/fission-suite/blob/master/LICENSE)
[![Maintainability](https://api.codeclimate.com/v1/badges/44fb6a8a0cfd88bc41ef/maintainability)](https://codeclimate.com/github/fission-suite/PROJECTNAME/maintainability)
[![Built by FISSION](https://img.shields.io/badge/⌘-Built_by_FISSION-purple.svg)](https://fission.codes)
[![Discord](https://img.shields.io/discord/478735028319158273.svg)](https://discord.gg/zAQBDEq)
[![Discourse](https://img.shields.io/discourse/https/talk.fission.codes/topics)](https://talk.fission.codes)

Basquiat is a CLI tool that performs batch image resizing operations and returns an ipfs CID that links to the original,
with metadata pointing to all generated versions using a general-purpose schema.
# QuickStart
### Installing ipfs

See [here](https://docs.ipfs.io/guides/guides/install/).

### Setting up rust
To install rustup on Linux or MacOS 
```shell script
$ curl https://sh.rustup.rs -sSf | sh
```

### Setting up dependencies
The `libvips` library needs to be installed :

On mac os
```shell script
brew install libvips`
```

On Ubuntu
```shell script

apt-get install libvips
```

### Cloning, compiling and running

```shell script
$ git clone git@github.com:fission-suite/basquiat.git
$ cd basquiat
$ ipfs daemon > ipfsd_log.txt #The ipfs daemon must be running!
$ cargo run -q -- <path_to_image>
```

`cargo run` builds and runs in one command. You can always find the executable 
at `target/debug/basquiat`.

### Exploring the DAG

By default, a simple html page presents a list of the available versions at `<root_cid>/thumbnails.html`. This feature
can be disabled by a flag as detailed in the command's `--help` message.

# Proposed metadata schema

The root CID points to the original version with named links to other sizes.
A given size `WxH.jpg` has its CID linked to *three times* by the root node :
```
_xH.jpg
Wx_.jpg
WxH.jpg
```
`W` and `X` are integers in pixel units.

This schema can be subsequently expanded to include transformations other than rescaling,
using an expandable syntax. For a given operation `c`, with parameters `A` and `B`, 
an operation on the given size `WxH.jpg` will also be linked to three times:
```
_xH.c-A-B.jpg
Wx_.c-A-B.jpg
WxH.c-A-B.jpg
```

This syntax is expandable to a sequence of operations by further concatenation.
For instance, the following could represent a file resized to a certain height `H`,
then cropped from the top (operation `ct`) to a certain height `Hp` then cropped from the left (`cl`) to a
certain width `Wp` :
```
_xH.ct-Hp.cl-Wp.jpg
```

This specification will also be used to configure iimir.

# Known issues

- When original file size is too small, its data gets overwritten in the output dag
by a smaller version. This is due to a [documented go-ipfs issue](https://github.com/ipfs/go-ipfs/issues/7190).
