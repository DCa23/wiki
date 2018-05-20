# Using a raspberry pi to unbrick a router

During Battlemesh I learned how to unbrick a TP-Link router using a Raspberry Pi.

## Prerequisites

- Raspberry Pi.
- An EEPROM programmer clip [1](https://www.amazon.es/SOIC8-SOP8-prueba-EEPROM-Circuit-programaci%C3%B3n-adaptador/dp/B012VSGQ0Q) [2](https://es.aliexpress.com/item/High-quality-SOIC8-SOP8-Test-Clip-For-EEPROM-93CXX-25CXX-24CXX-in-circuit-programming-on-USB/32814392856.html?shortkey=rIVFf6fe&addresstype=600)
- Some jumper cable.
- The specific datasheet of your EEPROM.
- Common sense.
- A non-bricked router from which you can make a dump (or a dump, that some of your friends have created).

# Setup your Pi

To enable the SPI GPIO add this to /boot/config.txt:
```
dtparam=spi=on
```

Now run `raspi-config` and enable loading of the spi drivers (its under interfacing options --> spi) at boot. ([source](https://www.raspberrypi.org/documentation/hardware/raspberrypi/spi/README.md))

If you don't want to do this you can manually load your spi drivers using:
```
modprobe spi_bcm2835 # If that fails you may want to try the older spi_bcm2708 module instead
modprobe spidev
```
([source](https://www.flashrom.org/RaspberryPi))

## Setting up your cable

Find a datasheet of your flashchip and connect the right [GPIO](https://www.raspberrypi.org/documentation/usage/gpio/) pins to your programmer clip. 

Apply your clip carefully on the chip. My SSH session to the Raspberry Pi broke when I was doing this, probably because your Pi is powering part of your router, so make sure your USB adapter is adequate. 

## Reading

With your router powered off:
```
flashrom -p linux_spi:dev=/dev/spidev0.0,spispeed=1000 -r out2.img
```
Create 3 dumps & verify with `md5sum` to make sure all images are the same, if not you should try switching your router on.

## Writing

The same applies to writing:
```
flashrom -p linux_spi:dev=/dev/spidev0.0,spispeed=1000 -w out2.img -V
```
## extra: finding the chip to flash

### option 1

To figure out what is the EEPROM on your motherboard I usually look at the techdata available on the OpenWrt wiki. Otherwise you have to figure it out by yourself. Depending on you board there can be several chips that all look the same. I've actually connected my raspberry to a voltage controller chip instead of the eeprom because I was not paying attention. I'm not certain if this will damage your raspberry/router...
Hope this helps you to get you going.

### option 2

First of all: if you're not sure about the electronics, don't connect it; it's a great way to break stuff.

Second: for identification, I usually start with the markings on the IC; a quick search usually gives a pretty good what you are dealing with.
If you're lucky,  it will take you right to the device in question or at least to the manufacturer.
Some manufacturers keep a list or searchable database of top markings for their devices online.

There are a number of standards involved with EEPROM and Flash modules; most are governed by [JEDEC](https://github.com/guifi-exo/wiki/blob/master/howto/spi.md) [1] or [OpenNAND](https://github.com/guifi-exo/wiki/blob/master/howto/spi.md)

EEPROMs are very stupid devices and usually don't offer a lot of information.

Serial Flash modules however, usually implement common command interfaces.

Many of these commands can be found in the Common Flash Memory Interface, described by JEDEC document  jesd68.01.

See the website for more details, you will need to register to get access to the documents, but it's be free.

https://en.wikipedia.org/wiki/Common_Flash_Memory_Interface also has some links to relevant documents.

If you really want to go down the rabbit hole, try https://www.jedec.org/category/technology-focus-area/memory-configurations-jesd21-c , which is the main entry to _all_ memory devices :)


