# Automatic-Screenshot-Take-Scroll-Stitch
### Introduction

There are a lot of websites that use server-side rendering to prevent users from getting hi-res photo directly, this is a script that automatically moves the cursor, does click, scroll actions, and then takes screenshots, and stitches them together to obtain a hi-res photo.

**Claim: Neither this script or its author(myself) has the intention to get the copyright photo from anyone or any organization, the code is neutral and used for only academic purpose, the one who used the code will take full responsibility of any consequence it may cause.**



### Example

Below is a website that uses server-side rendering to hide the hi-res photo. It shows a compressed, low-res photo and only when you clicked, the right preview window gives a part of a relatively high-res photo:

<img src="./README.assets/01.png" style="zoom: 33%;" />

<img src="./README.assets/02.png" alt="02" style="zoom:33%;" />

This script will automatically move the cursor, and take the screenshots on the frame area multiple times, then merge the results to a single image:

<img src="./README.assets/result.png" alt="result" style="zoom:50%;" />

The example seems perfect but due to the compression (jpeg probably) returned by the server, there are still visible merging grids on the image, but it is acceptable for using as online post (such as Instagram).



### Prepare the Environment

##### Install Required Modules

The script is tested on Python 3 environment, and before you proceed, please install required modules using terminal and PIP:

```
For Windows:
No need to install any modules.

For macOS (OS X):
sudo pip3 install pyobjc-framework-Quartz
sudo pip3 install pyobjc-core
sudo pip3 install pyobjc

For Linux:
sudo pip3 install python3-xlib
sudo apt-get install scrot
sudo apt-get install python3-tk
sudo apt-get install python3-dev

Then for all platforms:
sudo pip install pyautogui
sudo pip install pynput
sudo pip install pyscreenshot
```

##### Define Frame Area

There are some parameters and files you need to change for the script to work, first thing is to define the frame area. For the website above, you can see what I defined, are two frames, the external one and internal one, the reason is that, before you actually click anything on the low-res preview, the entire external frame is

<img src="./README.assets/frame.png" alt="frame" style="zoom:25%;" />

Which is convenient for the script to locate, but, after clicking on the low-res preview, the area turns into

<img src="./README.assets/frame2.png" alt="frame2" style="zoom:25%;" />

So there is an actual frame, which is inside the border. What we really want is the area inside, so we need to define an internal frame that has the same size of the internal area without the border, for the script to know, it is the area to take the screenshots.



So, before you begin using the script, you need to do similarly, to define an external frame image for the script to detect the frame on the webpage, and then an internal frame for the script to know where it should take the screenshots, so **the external frame is very important**, and must be the **same as the default frame area**, but the internal frame could simply be any image that has the size equal to the internal area for the script to calculate, and **you're expected to modify this part according to the website as you need**.



The external frame file is `frame.png`, the internal frame file is `internal_frame.png`, I also attached a PSD file for you as the reference to understand:

<img src="./README.assets/frame3.png" alt="frame3" style="zoom:25%;" />

**Adjust Parameters:**

Open `main.py`, scroll down to the `main() ` class, you will see several parameters for you to change, I'll briefly explain here:

`dpi_scale`

Commonly a lot operating systems now use DPI scaling for hi-res screen, such as retina for Mac. This parameter is very important to correctly locate the frame and the perform mouse clicking. Usually if you're using Desktop Windows with regular screen or a laptop without hi-res screen, or any DPI scaling if you know, set this to `1`, if you're using recent iMac/MacBook Pro or any retina display Macs, set to 2, or if you know the DPI scaling you set, enter the value here (1 means 100%).

`scan_window_size`

This is to decide how far the cursor click the low-res area, usually small or equal to the internal frame size, but, **large value may lead to visible grid on the image**, so try use a smaller number. However, if you change it to a value that is too small, it will take a significant time to finish the screenshots taking, also the result may not be even improved because the website I'm trying to use on, has a random function to randomly change the position of the hi-res preview, so no matter what you do, there will be a little offs, so I used `50` here, but **you're free to change and try**, I did a comparison here:

`scan_window_size = 20`

<img src="./README.assets/result20.png" alt="result20" style="zoom: 50%;" />

`scan_window_size = 50`

<img src="./README.assets/result50.png" alt="result50" style="zoom:50%;" />

`scan_window_size = 100`

<img src="./README.assets/result100.png" alt="result100" style="zoom:50%;" />

But for the first example photo, `50` looks perfect enough, so please wisely change this parameter.

`delay`

This is to decide how long the cursor will wait until move to the next point and click, the problem I have is that, the hi-res image is stored on the server, and it only return the preview image when you click to send out the request, so it means, **if the server is too busy or, the internet connection is not good, the cursor may be automatically moved and clicked, but the image is not changed, which will lead to a messed-up result**, so change this based on your environment (In the **unit of second**).

` confidence_factor`

This is a factor that is used for the script to decide how confident it can decide the frame is detected on your screen. The problem is that, sometime there is color differences on different screens (or a single computer with different color profiles from screenshot image and the actual display), to find a pixel-pixel match is usually difficult, so by introducing a confidence factor as a threshold, the script can better decide. When it is set to be a really small value, the script will detect irrelevant area and caused wrong position of the frame when take screenshots, so I used `0.98` here, which is perfect for my environment, **please do not change this unless you have problem for the script to find the frame location**.



### Run the Script

1. Run the `main.py`

2. Open the web page for the script to detect the frame, if succeeded, console will print

	`Frame detected! (578.0, 105.0, 114.0, 82.0)`

	The values are the **top left x and y coordinates of the internal frame**, the **width of the internal frame**, and the offset, which is the **x and y distance between the top left points of the external and internal frames**.

3. It will then prompt you to `Press Ctrl to start.`, when you press control key, it will ask you to **click the top left corner of the low-res preview area**, then the **bottom right corner**, in order to **let the script knows, where to move and click the cursor**.

4. After you set up the two points, **Don't touch the cursor**, the script will begin moving the cursor, and click, take the screenshots, you will see the cursor is moving automatically.

5. When finished, it will show you a preview of the result, and **the result has been saved onto the disk in `result.png`** , to the same folder of the script.

### Other Problems and Solutions:

- If same parameters produce correct result on non-retina screen, but leaves grid (border) on retina, please change the `internal_frame.png` by shrinking it for several pixels, this problem is caused by DPI up-scaling, which will fuzzy the pixels of the borders.

### Other Information

`frame_detection_assets` 

Contains pre-defined files for frame detection, both DPI = 1 for regular screen, and DPI = 2 for retina.

`stitch.py`

This file does the regular screenshots taking and stitching, it is a practice for me when writing`main.py`.

`test.py`

Also when I write some functions and I would like to test it before I put it into the `main.py`, I did it here.

##### Console Print Out

If the script runs successfully, it will print similar content in the console like this:

```bash
Press Ctrl + C to quit.
 Please open the webpage for frame detection...
Frame detected! (569.0, 105.0, 114.0, 82.0)
Press Ctrl to start.
Click the top left corner to get coordinates.
Click the bottom right corner to get coordinates.
Top left and bottom right:  [46, 71] [517, 692]
Box coordinates:  [46, 71] [517, 71] [46, 692] [517, 692]
Total  130  screenshots to be take.
Horizontally:  10 , vertically:  13 .
Begin taking screenshots...
Begin merging all screenshots...
Merged result has been saved to result.png .
```

