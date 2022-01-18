# RootKit By 0xSV
> Rootkit Written in C-Sharp, revamp/make it better as you wish, thank's.

[![NPM Version][npm-image]][npm-url]
[![Build Status][travis-image]][travis-url]
[![Downloads Stats][npm-downloads]][npm-url]

A rootkit allows someone to maintain command and control over a computer without the computer user/owner knowing about it. Once a rootkit has been installed, the controller of the rootkit has the ability to remotely execute files and change system configurations on the host machine. A rootkit on an infected computer can also access log files and spy on the legitimate computer owner’s usage.

![](header.png)

## Installation

OS X & Linux:

```sh
git clone https://github.com/0xSV/C-Sharp-RootKit.git
```

Windows:

```sh
git clone https://github.com/0xSV/C-Sharp-RootKit.git
```

## Usage

Drag And Drop or git clone the repo into ur server or targets server, run the file via gedit, have fun.

## Help if you dont know how to setup
```
sudo apt update
sudo apt install mono-complete
mcs -out:Rootkit.exe RootKit.cs
mono RootKit.exe
```
```
run: sudo apt-get install gedit
```
if that doesn't work create a bash file with this code, (edit it obvs)
```
if [ ! -f "$1" ]; then
dmcs_args=$1
shift
else
dmcs_args=""
fi
script=$1
shift
input_cs="$(mktemp)"
output_exe="$(mktemp)"
tail -n +2 $script > $input_cs
dmcs $dmcs_args $input_cs -out:${output_exe} && mono $output_exe $@                                                                          
rm -f $input_cs $output_exe
```
LMFAO im dumb omg, YOU CAN RUN CS FILES LIKE THIS!
```
./RootKit.cs
```
## Release History

* 0.2.1
    * CHANGE: Update docs (module code remains unchanged)
* 0.2.0
    * CHANGE: Removed `TimeVoid(), Time Function, its usless` was making a OS detection system/time zone system
* 0.1.1
    * FIXED: Crash's when `typing in 1`, it was calling a unknown function for some reason

## Social's

Twitter – (https://twitter.com/SyntaxVoid)
Discord - (SyntaxVoid#3980)

Distributed under the XYZ license. See ``LICENSE`` for more information.

https://github.com/0xSV

## Contributing

1. Fork it (<https://github.com/0xSV/C-Sharp-RootKit/fork>)
2. Create a new Pull Request

<!-- Markdown link & img dfn's -->
[npm-image]: https://img.shields.io/npm/v/datadog-metrics.svg?style=flat-square
[npm-url]: https://npmjs.org/package/datadog-metrics
[npm-downloads]: https://img.shields.io/npm/dm/datadog-metrics.svg?style=flat-square
[travis-image]: https://img.shields.io/travis/dbader/node-datadog-metrics/master.svg?style=flat-square
[travis-url]: https://travis-ci.org/dbader/node-datadog-metrics
[wiki]: https://github.com/0xSV/C-Sharp-RootKit
