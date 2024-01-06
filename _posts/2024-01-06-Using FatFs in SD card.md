---
title: Using FatFs in SD card
date: 2024-01-06 21:21:15 +0800
categories: [STM32, SD card]
tags: [STM32, SD card, FatFs, Project]
math: true
mermaid: true
---

SD card is a convenient solution for storing large amounts of data and many STM32 products include the proper hardware interface. Using a standard file system to write data on an SD card ensures that the data is easily accessible on another device or computer.  
This article shows you how to use FatFs in an SD card and displays some functions that FatFs provides.

## Prerequisites
Before start, make sure you have the following things:  
- Hardware: 
	- An STM32 board with an SD card socket  

> Here we use STM32F407ZGT6 and DAPLink for debugging.
{: .prompt-tip }

- Software:  
	- STM32CubeMX
	- Keil5
	- A serial port assistant

## SD card and FatFs Configuration
In order to enable the chip to communicate with the SD card, you need to turn on `SDIO` in CubeMX.  

1. Select "Connectivity" $\rightarrow$ "SDIO" and choose "SD 1 bit".
2. Enable "SDIO global interrupt".

> You can also choose "SD 4 bits Wide bus" for faster speed, but somehow my device won't work under such option.
{: .prompt-info }

![](https://cdn.jsdelivr.net/gh/flowing-wind/Pic@img/img/Using%20FatFs%20in%20SD%20card_01.png)  

SDIO (Secure Digital Input Output) is a communication interface that allows devices to connect to an SD card and exchange data.
> For more information, check it on [wiki](https://en.wikipedia.org/wiki/SD_card#SDIO_cards).