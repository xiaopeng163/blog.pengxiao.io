title: YABGP Basic Tutorial 
date: 2015-08-12
categories:
- Python
tags:
- BGP
---

YABGP is a yet another Python implementation for BGP Protocol. It can be used to establish BGP connections with
all kinds of routers (include real Cisco/HuaWei/Juniper routers and some router simulators like GNS3) and
receive/parse BGP messages for future analysis.

YABGP supports RESTAPI, it can be used to get the running status of BGP, and send BGP update/route refresh message.

# Installation

We recommend run ``yabgp`` through python virtual-env from source
code or pip install

## From source code

Use yabgp from source code

```bash
$ virtualenv yabgp-virl
$ source yabgp-virl/bin/activate
$ git clone https://github.com/smartbgp/yabgp
$ cd yabgp
$ pip install -r requirements.txt
$ cd bin
$ python yabgpd -h
```

## From pip

```bash
$ virtualenv yabgp-virl
$ source yabgp-virl/bin/activate
$ pip install yabgp
$ which yabgpd
/home/yabgp/yabgp-virl/bin/yabgpd
$ yabgpd -h
```

For ``virtualenv``, you can install it from pip. And make sure you have installed ``python-dev`` based on
your operation system, for example Ubuntu, you can install it from ``apt-get install python-dev``.
otherwise, you may get error when install requirement from requirements.txt

# Tutorial

## Basic Usage

We can use ``yabgpd`` help.

```bash
$ yabgpd -h
```
The simple way to start a yabgp agent is (There are four mandatory parameters):

```bash
$ yabgpd --bgp-local_addr=10.75.44.11 --bgp-local_as=23650 --bgp-remote_addr=10.124.1.245 --bgp-remote_as=23650
```

## Configuration


The configuration sample can be found at https://github.com/smartbgp/yabgp/blob/master/etc/yabgp/yabgp.ini.sample

## Advanced Usage


If you change the default setting (like change the ``write_dir``), please start the ``yabgp`` use the configuration file:

```bash
$ yabgpd --bgp-local_addr=10.75.44.11 --bgp-local_as=23650 --bgp-remote_addr=10.124.1.245 --bgp-remote_as=23650 --bgp-md5=cisco --config-file=yabgp.ini
```

## Running Mode

By default, the yabgp will be running as standalone mode, and it will write all BGP messages in local disk where as ``write_dir`` point out.
If you run yabgp with ``--nostandalone``,  you must configure mongodb and rabbitmq, then you can control which kind of bgp message will be
sent to rabbitmq queue for further process.

## Logging and Debug

The default setting is loggint to console, if you want to write log files and no console output, please use:

```bash
$ yabgpd --bgp-local_addr=10.75.44.11 --bgp-local_as=23650 --bgp-remote_addr=10.124.1.245 --bgp-remote_as=23650 --bgp-md5=cisco --nouse-stderr --log-file=test.log
```

If you want to change the log level to debug, use `--verbose` option.

```bash
$ yabgpd --bgp-local_addr=10.75.44.11 --bgp-local_as=23650 --bgp-remote_addr=10.124.1.245 --bgp-remote_as=23650 --bgp-md5=cisco --verbose
```

