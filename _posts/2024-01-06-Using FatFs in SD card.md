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

FatFs is an open-source file system middleware. This is integrated in Cube Libraries.
1. Configure FatFs as SD Card mode in "FATFS" in "Middleware and Software Packs".
2. Select an Input pin for Detect_SDIO.

> The pin can be any available GPIO Input pin if there are no special requirements.
{: .prompt-info}

![](https://cdn.jsdelivr.net/gh/flowing-wind/Pic@img/img/Using%20FatFs%20in%20SD%20card_02.png)

Finally, go to "Project Manager" $\rightarrow$ "Project" $\rightarrow$ "Linker Settings" and increase the Heap and Stack size because we are using FatFs Middleware that requires more memory.

![](https://cdn.jsdelivr.net/gh/flowing-wind/Pic@img/img/Using%20FatFs%20in%20SD%20card_03.png)

Now generate code and open it with your IDE, you may probably find the following files in the project:  

![](https://cdn.jsdelivr.net/gh/flowing-wind/Pic@img/img/Using%20FatFs%20in%20SD%20card_04.png){:  .w-25 .normal}  

The file `ff.c` is particularly important as it defines some commonly used modules of the FAT file system.  

## FatFs Usage
FatFs is a lightweight software library for microcontrollers and embedded systems that implements FAT/exFAT file system support. Most often, FatFs is used in low-power Embedded systems where memory is limited, since the library takes up little space in RAM and program code. In the minimum version, the working code takes from 2 to 10 kB of RAM.  
> For more information, check it on [wiki](https://en.wikipedia.org/wiki/FatFs).  

FatFs offers a range of functions for application use. And now I'm going to demonstrate some basic functions for performing file read and write operations.

> To access all the functions, see FatFs Module Library:   
> http://elm-chan.org/fsw/ff/  
> As it is not an HTTPS site, you need to manually copy the link, and assess the risk before accessing.
{: .prompt-warning}

### Mounting
In general, mounting is a process by which a computer's operating system makes files and directories on a storage device available for users to access via the computer's file system.  

Therefore, we need to mount the SD card so that the file on it can be accessed. Function `f_mount` serves as means to accomplish this task.  

#### Declaration and Parameters
```c
FRESULT f_mount (
  FATFS*       fs,    /* [IN] Filesystem object */
  const TCHAR* path,  /* [IN] Logical drive number */
  BYTE         opt    /* [IN] Initialization option */
);
```
fs
: Pointer to the filesystem object to be registered and cleared. Null pointer unregisters the registered filesystem object.

path
: Pointer to the null-terminated string that specifies the logical drive. The string without drive number means the default drive.

opt
: Mounting option. 0: Do not mount now (to be mounted on the first access to the volume), 1: Force mounted the volume to check if it is ready to work.

#### Code
Firstly, create a work area (filesystem object) for the local driver.
```c
FATFS fs;   /* Work area (filesystem object) for logical drive */
```
Next, specify the path name. You can use any string you like, or just the default configuration.
> For more formatting details, please refer to the online documentation.  
> http://elm-chan.org/fsw/ff/doc/filename.html
{: .prompt-info}

Finally, choose the mounting option. Typically, we opt for 0 (Do not mount now).  

The entire code may look like this:
```c
FATFS fs;   /* Work area (filesystem object) for logical drive */
f_mount (&fs,"0:" ,0);
```
