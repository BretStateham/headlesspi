# headlesspi

This repo outlines steps for working with a Raspberry Pi from your computer without a monitor, keyboard or mouse attached to the pi.

The instructions in this repo assume you'll be using the Raspbian OS, however these same instructions should work (perhaps with some modification) on other distros for the pi.

Also, so far I've only tested these steps on a Raspberry Pi 2 Model B+ and a Raspberry Pi 3 Model B.  Not sure how it will work on other versions, but I would expect them to work with no or minimal modifications.

## What you DON'T Need

- Monitor attached to the Pi
- Keyboard attatched to the Pi
- Mouse attached to the Pi

## What you DO Need

- Another computer (Windows, macOS, Linux, Unix, really anything you can use to ssh into the Pi and prepare the Micro SD card with).
- A Micro SD card to load the Pi's Raspibian OS onto
- A way to mount the Micro SD card on your computer.  This could be an integrated slot, an external USB reader, whatever.
- A 5V/2.5A or better Micro USB Power Supply to power the Pi
- A USB to TTL Serial FTDI Cable that supports 3.3v pins like this one from Adafruit: [USB to TTL Serial Cable - Debug / Console Cable for Raspberry Pi](https://www.adafruit.com/product/954)

## What you MIGHT Want

- An available Ethernet network and cable if you want to attach the Pi to it
- An available WiFi network and ssid/pwd if you want to attach the Pi to it
- Whatever else it is you want to hookup to your Pi.

## What We'll Cover:

- [Preparing the OS Image and Booting the Pi](#osprep)
- [Complete Adafruits USB to Serial Cable Tutorial](#adafruittut)
- [Enable UART](#enableuart)
- [Boot the Pi](#boot)
- [Connect via Serail](#connectserial)
- [Enable SSH & VNC](#enableinterfaces)
- [Configure the WiFi Network](#wificonfig)

---

<a name="osprep"></a>

## Preparing the OS Image and Booting the Pi

If you already have a Pi up and running with an OS, you don't need to do this task.  You can skip down to and move on.

1. Download the image for the Raspbian OS as a “.zip” (DON'T get NOOBs) from:

    > **Note**: There isn't anything wrong with NOOBS, but we don't need it, and this repo doesn't document the extra steps you'll need to use it, so just download the OS as a ".zip".  Again though if you already have a Pi that you got running using NOOBs, you can certainly use it, just skip down to ... and continue!

    https://www.raspberrypi.org/downloads/raspbian/


1. If you need an older version, checkout: http://downloads.raspberrypi.org/raspbian/images/
For example, the last version of Raspbian “Jessie” is at: http://downloads.raspberrypi.org/raspbian/images/images/raspbian-2017-07-05/2017-07-05-raspbian-jessie.zip

1. Download and install Etcher.io for your platform fromhttps://etcher.io/

1. Insert the Micro-SD card for the pi into your computer

1. Use Etcher.io to flash the OS image “.zip” file to the SD card.  It should be pretty obvious but:

    - Pick the Raspbian OS ".zip" file you downloaded previously as the "Image"
    - Select the Micro SD card you inserted above as the "Drive"
    - Click the "Flash" button to flash the image onto the SD card.  It'll take a few minutes to copy the whole image and verify it so please wait patiently until it is done.

---

<a name="adafruittut"></a>

## Complete Adafruits USB to Serial Cable Tutorial

I had started writing my own documentation to help you get the USB to Serial cable setup on your Pi and computer but I quickly realized I was really just stealing the work that Adafruit has already done so admirably.  Plus, they'll probably be able to keep their docs up to date easier than I can so...

1. Complete "[Adafruit's Raspberry Pi Lesson 5. Using a Console Cable](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-5-using-a-console-cable)" tutorial.  Make sure to get all they way through, and when you're done, return here for more goodness.

    ![Adafruit Tutorial Web Page](images/AdafruitTutorial.png)

---

<a name="enableuart"></a>

## Enable UART

If you completed the Adafruit Tutorial above you should have done this already, this step is just here for clarification purposes and to make sure that you complete it, otherwise you will likely have issues connecting to your Pi over the USB to Serial cable on newer versions of the Pi hardware and Operating System:

The clock speed on the Raspberry Pi 3s messes with the UART baud rate and will cause issues when we attempt to connect to it later with the USB to TTL Serial cable.  To fix the problem, we need to edit the `config.txt` file in the root of the Pi's SD card.

1. Make sure the Micro SD card with your Pi's OS is inserted into your computer, and locate the drive with the `boot` volume label.

1. Locate the `config.txt` file in the root of the `boot` partition and open the the `config.txt` file in the text editor of your choice.

1. Add the following two lines to the bottom of the `config.txt` file and save the changes:

    > **Note**: You should ensure that the `enable_uart` setting isn't being set somewhere else in the `config.txt` file already if this is a pre-existing OS image.

    ```text
    # Enable UART
    enable_uart=1
    ```

1. Now you can safely eject the Micro SD card and insert it into your Raspberry Pi's SD card slot.

---

<a name="boot"></a>

## Boot the Pi

1. Ensure that, in the following order:

    - The Micro SD card is inserted into the SD Card slot on the back of the Pi
    - The USB to Serial cable to the Pi's leads are properly connected to the Pi
        | Lead | Pin            | 
        | ---- | -------------- |
        |   5V | Not Connected  |
        |  GND |  6 (GND)       |
        |   TX |  8 (UART0_TXD) |
        |   RX | 10 (UART0_RXD) |

    - The USB end of the USB to serial cable is connected to your computer
    - If you are on a Raspberry Pi 2 and want to use WiFi, that a WiFi dongle is plugged into a USB port.
    - If you want to use Ethernet, that the Ethernet cable is plugged into the Pi's Ethernet port, and to the network.

    ![USB to Serial cable connection closeup](images/USBToSerialCableCableOnPiCloseUp.png)

1. Once you have ensured all of the above, you can connect the Micro USB power supply to the Pi to boot it.

    ![Connect and Boot the Pi](images/ConnectAndBoot.gif)

---

<a name="connectserial"></a>

## Connect via Serail

Again, you likely completed this already if you completed the Adafruit Tutorial above, but to ensure we are all starting from the same place:

1. Use the steps from the [Test & Configure](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-5-using-a-console-cable/test-and-configure) page of "[Adafruit's Raspberry Pi Lesson 5. Using a Console Cable](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-5-using-a-console-cable)" tutorial to connect to your Raspberry Pi over the USB to Serial cable using a terminal program on your computer. 

1. Login to the Pi:

    > **Note**: You may need to press the `ENTER` or key on your keyboard in the terminal once connected to get to the login prompt.  Also, if you have already changed the credentials on your Pi, use those instead.

    - User Name: `pi`
    - Password: `raspberry`

---

<a name="enableinterfaces"></a>

## Enable SSH & VNC

At this point you can connect to your pi only from a computer that is attached using the USB to Serial cable. If you want to connect to your pi over the network, you will need to enable ssh and/or vnc on the pi.

1. Ensure you are connected to your Pi over the USB to Serial cable in and logged in as described above.  

1. From the command prompt run the following two commands to enable ssh & vnc (or optionally run just the command for the interface you want to enable )

    > **Note**: You can also enable the interfaces interactively by just running `sudo raspi-config`, navigating to `5 Interfacing Options` and iteratively selecting and enabling each interface.  The commands below just allow you to do it more directly and without any manual interaction required.

    ```bash
    # Enable the ssh interface on the pi
    # The "0" means "enable".  "1" would mean "disable"
    sudo raspi-config nonint do_ssh 0

    # Enable the ssh interface on the pi
    # The "0" means "enable".  "1" would mean "disable"
    sudo raspi-config nonint do_vnc 0
    ```
1. You can optionally choose to enable other interfaces now as well, or as mentioned you can interactively run `sudo raspi-config` to configure other options like `Hostname`, `Localization Options`, `Interfacing Options` and more.

    ```bash
    # Enable the ssh camera, i2c and spi interfaces
    # The "0" means "enable".  "1" would mean "disable"
    sudo raspi-config nonint do_camera 0
    sudo raspi-config nonint do_i2c 0
    sudo raspi-config nonint do_spi 0
    ```
---

<a name="wificonfig"></a>

## Configure the WiFi Network

If you will be connecting you pi to a WiFi network using either the built-in WiFi adapter on the Raspberry Pi 3, or a USB WiFi adapter on the Pi 2 or Pi 3, you can configure the WiFi connection a number of ways. We'll cover two options

- Using the `wpa_cli` 
- Manually editing the `wpa_supplicant.conf` file

The default configuration on Raspbian uses the "[wpa_supplicant](https://wiki.archlinux.org/index.php/WPA_supplicant)" to configure and connect your WiFi interface to a wifi network.

What is a "supplicant"? [Vocabulary.com](https://www.vocabulary.com/dictionary/supplicant) explains it as follows:

>_A supplicant can be a fervently religious person who prays to God for help with a problem, and **it can also be someone who begs earnestly for something he or she wants**. A younger brother entreating his sister to be allowed in her tree house could be described as a supplicant. The Latin root word is supplicantem, "plead humbly."_

Note the phrase "_**it can also be someone who begs earnestly for something he or she wants**_".  In our case the `wpa_supplicant` on the raspberry pi will be begging our WiFi Access point to allow it to connect.  To do that we need to give the supplicant a few pieces of information:

- The WiFi Network SSID
- The WiFi Network passphrase / password / Pre-shared key, if there is one



### Using the `wpa_cli`

The `wpa_cli`

---

<a name=""></a>

## 


---

<a name=""></a>

## 


---

<a name=""></a>

## 


---

<a name=""></a>

## 


---

<a name=""></a>

## 


---

<a name=""></a>

## 


---

<a name=""></a>

## 


---

<a name=""></a>

## 


---

<a name=""></a>

## 

