# rf24-serial-docs
Notes about interfacing NRF24L01(+) with USB adapter

## Introduction

NFT24L01(+) is a small and cheap 2.4GHz radio receiver/transmitter
controllable via [Serial Peripheral Interface](https://en.wikipedia.org/wiki/Serial_Peripheral_Interface_Bus) and produced by
[Nordic semiconductor](http://www.nordicsemi.com/eng/Products/2.4GHz-RF/nRF24L01).

![NFR24L01 and USB adapter](/images/adapter.jpg)

## Table of contents

  * [Installation](#installation)
  * [Configuration of the adapter](#configuration-of-the-adapter)
  * [Communication between two serials](#communication-between-two-serials)
  * [Communication between a serial and an MCU](#communication-between-a-serial-and-an-mcu)
    * [Coding](#coding)
  * [References](#references)

## Installation
Mount your NRF24L01(+) on the adapter orienting the antenna in the
opposite direction with respect the USB connector (see image below).
![How to mount the NFR24L01 on USB adapter](/images/how_to_mount.jpg)

The adapter most probably ships a CH340T USB to serial converter. This
chinese chip requires installation of drivers on Windows and Mac OS
operating systems, while is natively supported by the Linux kernel.

You'll find very quickly the most updated drivers for your system on the
Internet.

You'll also need a serial terminal emulator, like
[PuTTY](http://www.putty.org/). Install one of your choice and configure
it for connection at 9600/8-N-1.

Now you're ready for starting using your adapter :wink:

## Configuration of the adapter

## Communication between two serials

## Communication between a serial and an MCU

### Coding

## References

