# rpi-neflaca-n41-cups-driver
Neflaca N41 Thermal Printer - drivers for raspberry pi

## Intro

I got the Neflaca N41 thermal printer used for very cheap. It works fine, but my workflow required wireless printing so I tried to configure it with CUPS on raspberry pi. 

While linux drivers for this printer are available, the `raster-tspl` binary is not compiled for ARM. This driver fixes that and allows the rpi to successfully print to the N41 and share it on the network.

## Installation

This assumes you already have cups installed.

- Attach your printer via USB and power it on

### Copy the raster file to CUPS filters

- Copy the file `raster-tspl` to the raspberry pi's CUPS filters directory: `sudo cp ./raster-tspl /usr/lib/cups/filter/`
- Ensure that file is executable with: `sudo chmod +x /usr/lib/cups/filter/raster-tspl`

### Install the PPD driver

- Open the CUPS web interface
- Navigate to `Administration` > `Add Printer`
- Select your printer from the "Local Printers" section: "NefLaca NL-N41 (NefLaca NL-N41)"
- Continue until the driver selection screen. Click "Or Provide a PPD File" > "Choose File" and upload the `Neflaca-N41.ppd` file from this repo

## But how?

Just in case you find yourself with a similar, but not-quite-the-same printer that needs to be ported. Here's how I got this one working.

Most of these thermal printers are based on a similar chipset / hardware as the HPRT N41. There are some projects that have ported `raster-tspl` these might work fine for you, but they didn't work for me. I'd get all the way to the printing step and CUPS would "successfully" print, but nothing actually came out of the printer. So I had to keep digging.

I got the rasterization binary from Rollo's website [https://www.rollo.com/driver-linux/] 
