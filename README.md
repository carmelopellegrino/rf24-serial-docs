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
Internet. Anyhow, here is a list of links to download the drivers from:
  * [Linux](http://bsfrance.fr/documentation/10661_NRF24L01_ADAPTER/CH341SER_LINUX.ZIP) (generally not required)
  * [Windows](http://bsfrance.fr/documentation/10661_NRF24L01_ADAPTER/CH341SER.ZIP)
  * [Mac OS](http://bsfrance.fr/documentation/10661_NRF24L01_ADAPTER/CH341SER_MAC.ZIP)
  * [Android](http://bsfrance.fr/documentation/10661_NRF24L01_ADAPTER/CH341SER_ANDROID.ZIP)

You'll also need a serial terminal emulator, like
[PuTTY](http://www.putty.org/). Install one of your choice and configure
it for connection at 9600/8-N-1.

Now you're ready for starting using your adapter :wink:

## Configuration of the adapter
The adapter allows to set up only a subset of configuration parameters
supported by the NRF24L01. In particular it is **not** possible to set:

  * the emission power (set to 0 dBm)
  * the address length (set to 5 bytes -> 40 bits)
  * the data length (32 bytes, 31 for user data)
  * more than one destination address

The configuration can be done via AT commands on the serial terminal.
A list of available AT commands follows:

  * `AT?`: return the current configuration
  * `AT+BAUD=n`: set the baud rate of the serial connection; `n`=1-6:
    * 1 -> 4800
    * 2 -> 9600 (default)
    * 3 -> 14400
    * 4 -> 19200
    * 5 -> 38400
    * 6 -> 115200
  * `AT+RATE=n`: set the data link speed; `n`=1-3:
    * 1 -> 250Kbps
    * 2 -> 1Mbps
    * 3 -> 2Mbps (default)
  * `AT+RXA=0Xnn,0Xnn,0Xnn,0Xnn,0Xnn`: set the local receiving address (default 0xff,0xff,0xff,0xff,0xff) in hexadecimal format
  * `AT+TXA=0Xnn,0Xnn,0Xnn,0Xnn,0Xnn`: set the target address (default 0xff,0xff,0xff,0xff,0xff) in hexadecimal format
  * `AT+FREQ=2.abcG`: set the operating frequency; `abc` can be any integer number between 400 and 525 (default 2.400G). **NOTE:** in other contextes this parameter is called *channel*; the correspondance is linear such that 400 and 525 correspond to channels 0 and 125, respectively (e.g.: 409 corresponds to channel 9). **NOTE:** to avoid interferences with WiFi use frequencies above 2.500GHz.
  * `AT+CRC=n`: set the CRC length, in bits; n = 8/16 (default: 16 bit)

**NOTE:** all AT commands are written using capital letters, without
spaces and using the english typeset.

**NOTE:** don't be afraid if you see dumb chars on the serial terminal ad
output. Probably they are UTF-16 chinese chars not well displayed by your
locale.

## Communication between two serials
Using two adapters is straight forward. You have to open a serial
connections to each of your adapters and configure them such that the
remote address of one is the local address of the other. All other
communication parameters such as the data speed, the frequency and the CRC
must coincide.

What you write to a serial terminal is immediately sent to the other.

## Communication between a serial and an MCU
The microcontroller on the adapter uses 32-bytes-wide fixed-length
messages. The first byte must contain the actual user data length so only
31 bytes are available to the user. Even if the message is shorter, 32
bytes must be used.
If the data to transmit is longer than 31 bytes it must be split into
several messages and rebuilt after the transmission.
A 32-bytes message will always be sent and expected to be received by the
adapter.

Thus, an MCU connected via SPI to its own NRF24L01(+) must be programmed
fullfilling these constraints in order to correctly communicate with the
adapter.

Schematic of a message:

    +-------+-------+-------+-------+-------+-------+-------+
    |       |       |       |       |       |       |       |
    |   0   |   1   |   2   |  ...  |   N   |  ...  |   31  |
    |       |       |       |       |       |       |       |
    +-------+-------+-------+-------+-------+-------+-------+
     Length | User                          | Empty

### Coding
Example code of message serialization, in C++:

```cpp
char const msg[] = "Hello, World!";

char packet[32];

packet[0] = sizeof(msg);
memcpy(packet + 1, msg, sizeof(msg));

nrf24.send(packet); // replace with the function of your choice/need
```

## References

