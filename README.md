# rpi-neflaca-n41-cups-driver
Neflaca NL-N41 Thermal Printer - drivers for raspberry pi

## Intro

I got the Neflaca NL-N41 thermal printer used for very cheap. It works fine, but my workflow required wireless printing so I tried to configure it with CUPS on raspberry pi. 

While Linux drivers for this printer are available [here](https://drive.google.com/drive/folders/1Nj_xiN1Er8i-vx1U_JG8P1deI7xdV1nr), the included `raster-tspl` binary is not compiled for ARM. 

This driver fixes that and allows the rpi to successfully print to the N41 and share it on the network.

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

### Print a test page 

- Go to Printers, click on your printer
- on the first dropdown that says "Maintenance" select "Print test page"
- Ensure the test page came out ok. You should be good to go!

## But how?

Just in case you find yourself with a similar, but not-quite-the-same printer that needs to be ported, here's how I got this one working.

Most of these thermal printers are based on a similar chipset / hardware as the HPRT N41. There are some projects that have ported `raster-tspl` to the pi [here](https://github.com/thorrak/rpi-tspl-cups-driver). This might work fine for you, but they didn't work for me. I'd get all the way to the printing step and CUPS would "successfully" print, but nothing actually came out of the printer. So I had to keep digging.

I found a printer by Rollo that used a similar chipset, and they actually have some beta raspberry pi drivers already, [available on Rollo's website](https://www.rollo.com/driver-linux/). I downloaded those drivers and installed them. When I switched my drivers to those, they actually printed! But the stuff that came out was a garbled mess. 

The issue seemed to be that the included PPD had weird dimensions that were not supported by the N41, so I modified those to match the settings of my own printer, which I got from the official linux driver PPD file for it . The main sections to look out for are the things about page size and maximum dimensions. Specifically, these:

```

*OpenUI *PageSize/Media Size: PickOne
*OrderDependency: 10 AnySetup *PageSize
*DefaultPageSize: w101h152
*PageSize w101h152/4.00"x6.00"(101mm x 152mm): "<</PageSize[287 432]/ImagingBBox null>>setpagedevice"
*PageSize w57h32/2.25"x1.25"(57mm x 32mm): "<</PageSize[162 91]/ImagingBBox null>>setpagedevice"
*PageSize w59h101/2.30"x4.00"(59mm x 101mm): "<</PageSize[168 287]/ImagingBBox null>>setpagedevice"
*PageSize w76h25/3.00"x1.00"(76mm x 25mm): "<</PageSize[216 227]/ImagingBBox null>>setpagedevice"
*PageSize w76h50/3.00"x2.00"(76mm x 50mm): "<</PageSize[216 283]/ImagingBBox null>>setpagedevice"
*PageSize w50h101/2.00"x4.00"(50mm x 101mm): "<</PageSize[142 287]/ImagingBBox null>>setpagedevice"
*PageSize w104h159/4.00"x6.00"(104mm x 159mm): "<</PageSize[295 452]/ImagingBBox null>>setpagedevice"
*CloseUI: *PageSize
*OpenUI *PageRegion/Media Size: PickOne
*OrderDependency: 10 AnySetup *PageRegion
*DefaultPageRegion: w101h152 
*PageRegion w101h152/4.00"x6.00"(101mm x 152mm): "<</PageSize[287 432]/ImagingBBox null>>setpagedevice"
*PageRegion w57h32/2.25"x1.25"(57mm x 32mm): "<</PageSize[162 91]/ImagingBBox null>>setpagedevice"
*PageRegion w59h101/2.30"x4.00"(59mm x 101mm): "<</PageSize[168 287]/ImagingBBox null>>setpagedevice"
*PageRegion w76h25/3.00"x1.00"(76mm x 25mm): "<</PageSize[216 227]/ImagingBBox null>>setpagedevice"
*PageRegion w76h50/3.00"x2.00"(76mm x 50mm): "<</PageSize[216 283]/ImagingBBox null>>setpagedevice"
*PageRegion w50h101/2.00"x4.00"(50mm x 101mm): "<</PageSize[142 287]/ImagingBBox null>>setpagedevice"
*PageRegion w104h159/4.00"x6.00"(104mm x 159mm): "<</PageSize[295 452]/ImagingBBox null>>setpagedevice"
*CloseUI: *PageRegion
*DefaultImageableArea: w101h152 
*ImageableArea w101h152/4.00"x6.00"(101mm x 152mm): "0 0 287 432"
*ImageableArea w57h32/2.25"x1.25"(57mm x 32mm): "0 0 162 91"
*ImageableArea w59h101/2.30"x4.00"(59mm x 101mm): "0 0 168 287"
*ImageableArea w76h25/3.00"x1.00"(76mm x 25mm): "0 0 216 227"
*ImageableArea w76h50/3.00"x2.00"(76mm x 50mm): "0 0 216 283"
*ImageableArea w50h101/2.00"x4.00"(50mm x 101mm): "0 0 142 287"
*ImageableArea w104h159/4.00"x6.00"(104mm x 159mm): "0 0 295 452"
*DefaultPaperDimension: w101h152 
*PaperDimension w101h152/4.00"x6.00"(101mm x 152mm): "287 432"
*PaperDimension w57h32/2.25"x1.25"(57mm x 32mm): "162 91"
*PaperDimension w59h101/2.30"x4.00"(59mm x 101mm): "168 287"
*PaperDimension w76h25/3.00"x1.00"(76mm x 25mm): "216 227"
*PaperDimension w76h50/3.00"x2.00"(76mm x 50mm): "216 283"
*PaperDimension w50h101/2.00"x4.00"(50mm x 101mm): "142 287"
*PaperDimension w104h159/4.00"x6.00"(104mm x 159mm): "295 452"

*MaxMediaWidth: "306.14"
*MaxMediaHeight: "6480"
*HWMargins: 0.00 0.00 0.00 0.00
*CustomPageSize True: "pop pop pop <</PageSize[5 -2 roll]/ImagingBBox null>>setpagedevice"
*ParamCustomPageSize Width: 1 points 14.17 306.14
*ParamCustomPageSize Height: 2 points 14.17 6480
*ParamCustomPageSize WidthOffset: 3 points 0 0
*ParamCustomPageSize HeightOffset: 4 points 0 0
*ParamCustomPageSize Orientation: 5 int 0 0
*RequiresPageRegion All: True
```
