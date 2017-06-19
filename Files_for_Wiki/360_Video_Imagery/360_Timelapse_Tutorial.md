# How to capture, stitch and publish a 360° timelapse 

Consumer ready 360° cameras are becoming ever more accessible and many people are experimenting with a variety of 360° content. 
Out of the many cameras on the market the Ricoh Theta S is one of the most user-friendly, turn-key solutions with lots of built-in features. 
However, the camera's videos are limited 1920x960 resolution and the Theta+ app only lets you create a timelapse with up to 300 or 400 images. 
The workaround is to use interval shooting to capture as many images as you'd like at the 5376x2688 to  full resolution and then stitch them
together manually into an HD video. There are few GUI solutions (especially open-source/free) which let you do this with ease. Here's how you do it:


## Set up interval shooting on your Ricoh Theta S
This step is specific to the Theta s, but the rest of the tutorial applies to any series of 360° images


* Once you choose a location, set up the camera on a tripod and connect to it with your smartphone, go to the shooting settings and choose interval shooting
* This lets you set the number of images to capture and the interval between them. Unfortunately, the maximum number of images is currently 200
so in my case when I was capturing an image every 10 seconds, I would have to reset the interval shooting roughly every 30 minutes. Based on the length of your interval,
make sure to calculate how long the camera will shoot so you know when to come back and reset it. 
* Another drawback with the Theta is that tripod mounts will cover the ports, making it impossible to charge the camera while shooting, so you only 
have juice for about 1.5 hours of shooting. Which in my case was about 600 images, or 24 seconds of timelapse @ 25fps
    * Luckily there are [solutions](https://havecamerawilltravel.com/photographer/ricoh-theta-te1-extension-adapter/) in the form of [tripod](https://www.amazon.com/dp/B01HZHM526?tag=0506-tps-20) mount 
    [extenders](https://www.amazon.com/dp/B01HH432W4?tag=0506-tps-20)

## Transfer and organize files


* Once you are done capturing images and transferring them to your computer, you will need to make sure they are all sequentially named so FFmpeg can handle the entire series
* In my case, I had to delete a couple images and then rename all of them starting with Image_0001.JPG to Image_0585.JPG Make sure you have an extra '0' digit 
in the file names so the images are ordered correctly
* If you are on a Windows operating system, using the [Bulk Rename Utility](http://www.bulkrenameutility.co.uk/Download.php) makes this process a breeze
* If you are on Linux (or OSX) you can use an awesome tool called mmv - [man page](http://manpages.ubuntu.com/manpages/precise/man1/mmv.1.html) or a variety of
other command line options

## Stitching images into a video with FFmpeg


* Now comes the fun part, actually spinning up FFmpeg and combining the images into a video 
* First, if you don't have it, you need to install ffmpeg and if you are on Windows, add it to your PATH so you can call it in the command line
    * [Install FFmpeg on Windows 10](http://www.wikihow.com/Install-FFmpeg-on-Windows)
    * [Install FFmpeg on Windows/OSX/Ubuntu](https://github.com/adaptlearning/adapt_authoring/wiki/Installing-FFmpeg)
* Open your Terminal/PowerShell/cmd
* Navigate into the folder with your sequentially numbered images

The entire command I used looks like this:

`ffmpeg -r 25 -start_number 0001 -i Image_%04d.JPG -s 3840x1920 -vcodec libx264 -b:v 10M pix_fmt yuv420p video_out.mp4`


Now, let's unpack what all those things mean:

- `ffmpeg` is the command that calls the actual application
- `-r` is the parameter for setting the frame rate of your video, in my case above, 25 frames per second
- `-start_number` is the number of the first image in your sequencem. Since my images start with Image_0001.JPG, 0001 is my start number
- `-i` is the path to your images, but since we already navigated to the image folder, we only specify the image name
- `Image_%04d.JPG` tells ffmpeg that the filename is `Image_` followed by four digits. If you had more than 1000 images for example with
numbers from Image_00001 to Image_01200, you would specify `Image_%05d.JPG`
- `-s` lets us adjust the resolution of the output video. In my case, I scaled it down from 5376x2688 to 3840x1920 while maintaining
the equirectangular 2:1 ratio
- `-vcodec` lets you set the video codec you want to use. In this case `libx264` for H.264
- `-b:v` sets the video bitrate. The number is followed by either K or M Kbit/s and Mbit/s so here the bitrate is set to 10 Mbps
- `-pix_fmt` sets the pixel formate of the video, set to yuv420p for playback
- Finally, `video_out.mp4` is the name and format of the output video. Now just hit `enter` and watch the magic happen

#### For a more complete guide and cheatsheet to creating 360° videos with FFmpeg check out [nickkraakman's](https://gist.github.com/nickkraakman) [FFmpeg Cheat Sheet for Creating 360° Video](https://gist.github.com/nickkraakman/e351f3c917ab1991b7c9339e10578049)

## Last step - inject 360 metadata


* When the video comes out of FFmpeg, it has no 360 spatial metadata, so YouTube or any other video player won't recognize it as a 360°
equirectangular video
* To fix this, we will use an awesome tool developed by Google - [The Spatial Media Metadata Injector](https://github.com/google/spatial-media/releases)
* Download it from google's github repo above and [look at how to use it](https://support.google.com/jump/answer/7044297?hl=en)
* It is extremely simple to use GUI interface. Launch the app, `open` your video, check `My video is spherical (360)` and press `Inject metadata`
* **Done**

#### [Here is our finished video](https://www.youtube.com/watch?v=QFVkUI-o-Cc)

**Now your video is ready to be uploaded to YouTube or played in 360° mode with the Windows `Movies & TV` app.**

Note that the Ricoh Theta desktop app will not recognize or play this as a 360° video. 





