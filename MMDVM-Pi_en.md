# Enable serial port in Raspberry Pi 3 or Pi Zero W

This this necessary only if you are installing a fresh copy of Raspbian OS. Images like "Pi-Star" are already OK.

Edit /boot/cmdline.txt:

    sudo nano /boot/cmdline.txt

(remove the text: console=serial0,115200)

Disable services:

    sudo systemctl disable serial-getty@ttyAMA0.service
    sudo systemctl disable bluetooth.service

Edit /boot/config.txt:

    sudo nano /boot/config.txt

and add the following lines at the end of /boot/config.txt:

    enable_uart=1
    dtoverlay=pi3-disable-bt

Reboot your RPi:

    sudo reboot

# Build the firmware and upload to MMDVM-Pi

## Installation of necessary software (only once)

If you are using Pi-Star, expand filesystem (if you haven't done before):

    sudo pistar-expand
    sudo reboot

Enable RW filesystem if you are using Pi-Star:

    rpi-rw

Update list of packages:

    sudo apt-get update

Install toolchain and necessary packages:

    sudo apt-get install git gcc-arm-none-eabi gdb-arm-none-eabi libstdc++-arm-none-eabi-newlib autoconf libtool pkg-config libusb-1.0-0 libusb-1.0-0-dev

Download and compile serial flashing utilities:

    cd ~
    git clone https://github.com/jsnyder/stm32ld
    cd stm32ld
    make
    sudo cp stm32ld /usr/local/bin

Remove libi2c-dev and stm32flash packages if you are using Pi-Star:

    sudo apt-get remove libi2c-dev
    sudo apt-get remove stm32flash

Install the latest stm32flash:

    cd ~
    git clone https://git.code.sf.net/p/stm32flash/code stm32flash
    cd stm32flash
    make
    sudo make install

## MMDVM firmware compilation and uploading for MMDVM-Pi

Download firmware sources:

    cd ~
    git clone https://github.com/g4klx/MMDVM
    cd MMDVM
    git clone https://github.com/juribeparada/STM32F4XX_Lib

Edit Config.h according your preferences:

    nano Config.h

You can select for example:

    // #define EXTERNAL_OSC 12000000 (disable any external TCXO)
    // #define ARDUINO_DUE_ZUM_V10 (this option doesn't matter for STM32 devices)
    #define ARDUINO_MODE_PINS
    #define SEND_RSSI_DATA
    #define SERIAL_REPEATER

Compile:

    make pi

If you are using Pi-Star, stop services:

    sudo pistar-watchdog.service stop
    sudo systemctl stop mmdvmhost.timer
    sudo systemctl stop mmdvmhost.service

Upload the firmware:

    sudo make deploy-pi

