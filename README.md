## Raspberry Pi Instagram Frame

Creating a digital slideshow frame with a Raspberry Pi that streams photos from your favorite Instagram accounts.  

## Materials 

- [Raspberry Pi 3](https://www.amazon.com/Raspberry-Pi-RASPBERRYPI3-MODB-1GB-Model-Motherboard/dp/B01CD5VC92/ref=sr_1_3?s=pc&ie=UTF8&qid=1517631343&sr=1-3&keywords=raspberry+pi+3)

- [Raspberry Pi 7" Touchscreen](https://www.amazon.com/Raspberry-Pi-7-Touchscreen-Display/dp/B0153R2A9I/ref=sr_1_3?s=electronics&ie=UTF8&qid=1517631532&sr=1-3&keywords=raspberry+pi+7+touchscreen+display)

- [SanDisk Ultra Class 10 16GB MicroSD](https://www.amazon.com/SanDisk-Ultra-Micro-Adapter-SDSQUNC-016G-GN6MA/dp/B010Q57SEE/ref=sr_1_3?s=electronics&ie=UTF8&qid=1517631803&sr=1-3&keywords=16gb+microsd)

- [5V Power Adapter](https://www.amazon.com/Official-Raspberry-Foundation-Power-Supply/dp/B01LCNF8FU/ref=sr_1_1?s=electronics&ie=UTF8&qid=1517632103&sr=1-1&keywords=raspberry+pi+power+supply+white) 

- [Bamboo Frame](https://www.amazon.com/Eleduino-Raspberry-Official-Touchscreen-Display/dp/B01FZ2RJN8/ref=sr_1_1?s=electronics&ie=UTF8&qid=1517632187&sr=1-1&keywords=raspberry+pi+bamboo) 

## Raspberry Pi Setup

I won't go into too much detail of getting Raspbian set up since there's a ton of [guides](https://projects.raspberrypi.org/en/projects/noobs-install) available online. 

Once I got Raspbian up and running, I ran these commands to make sure everything is up to date:
 
```
sudo apt-get update

sudo apt-get upgrade 

sudo apt-get dist-upgrade 

sudo apt-get install raspberrypi-ui-mods 

sudo apt-get install raspberrypi-net-mods 
```
### Setting up the Scripts
I'll be grabbing photos from Instagram using the [Instagram-Scraper](https://github.com/rarcega/instagram-scraper), which can be installed using pip:

```
pip install instagram-scraper
```

Instagram-Scraper allows you to download photos in a variety of ways, including usernames and hashtags. When executed, the shell script `download_igPhotos.sh` will grab photos from a couple of my girlfriend's favorite instagram accounts.

In the `slideshow.sh` shell script, I went with [feh](https://feh.finalrewind.org/) to run my slideshow. 
 
### Disable Sleep
In order to have the screen running the slideshow without going to sleep, I installed XScreensaver using:

```
sudo apt-get install xscreensaver
```

After XScreensaver is installed, click on the Menu button (top left corner) of the desktop GUI and go to Preference > Screensaver and set the mode to "Disable Screen Saver" from the drop menu.
 
### Boot to slideshow
X must be running for XScreensaver to work so I've added `startx` into my `.bashrc` and `launch xscreensaver` into `.xsessionrc`.

To boot straight into the slideshow, the `slideshow.sh` script must be added into `.xsessionrc`. Here's an example of how the `.xsesionrc` file should look like:

```
#! /bin/bash
xscreensaver &
/home/pi/frame_pi/slideshow.sh
```

At this point, the Raspberry Pi successfully turns on and boots straight into a slideshow of images collected from the desired Instagram accounts!

## Using CRON to Update the Image Folder
To keep things new and interesting instead of the same images all the time, I used CRON to automatically delete past images and download new images from Instagram every 3 days. 

Edit your crontab with `crontab -e` and add these lines:

```
0 0 */3 * * sudo /home/pi/frame_pi/download_igPhotos.sh 
0 0 */3 * * find /home/pi/frame_pi/photos/ -type f -name "*.jpg" -mtime 3 -exec rm{} \;
```
