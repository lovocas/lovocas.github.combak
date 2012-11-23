--- 
date: 2010-11-07 16:25:59 +08:00
layout: post
title: !binary |
    UnVubmluZyBQaG90b3Nob3AgQ1M0IG9uIExpbnV4IHVzaW5nIFdpbmU=

wordpress_id: 498
wordpress_url: !binary |
    aHR0cDovL3d3dy50b2J5Y24ub3JnL2IvP3A9NDk4

---
I love open source. Up until this point, I’ve been doing all my image editing in GIMP, for better or worse. But as an old Photoshop user, certain things in GIMP’s UI just pisses me off rubs me the wrong way. One of these occasions was yesterday, when I was trying to create a gradient for a Django project I’m working on. So I set off trying to get a working installation of Photoshop CS4 on Ubuntu. Here’s a rundown of how it’s done:

   1.  Install wine 1.1.17 (Ubuntu/Debian packages here). Do not use a newer version as this bug makes the installation fail. I haven’t tried with versions older than 1.1.17, but that’s the newest version that works, so I recommend sticking with it for the time being. (Update: due to the lack of 1.1.17 packages for Ubuntu 9.04 and newer, I will be putting up a PPA for 1.1.17 starting with packages for Ubuntu 10.04. There is also a patch attached to the aforementioned bug report, which provides a workaround for newer versions of wine. Instructions for building a patched version are provided below).
   2.  I recommend doing this with a clean Wine install.
[code lang="bash"]
       mv -f ~/.wine ~/.wine_backup
       winecfg
[/code]
      This will move all of your Wine applications and libraries and set up a new environment, respectively.
   3. Download winetricks, a helper script for installing Wine software and install the required software.
[code lang="bash"]
      wget http://www.kegel.com/wine/winetricks
      chmod +x winetricks
      ./winetricks msxml6 gdiplus gecko vcrun2005 ie6 fontsmooth-rgb
[/code]
   4. Also, install some Microsoft TTF fonts
[code lang="bash"]
      sudo aptitude install msttcorefonts
[/code]
   5. Next, download <a>atmlib.dll</a> and place it in ~/.wine/drive_c/windows/system32. Run winecfg, go to the Libraries tab and add atmlib to the list. Make sure it is registered as native (edit the entry if it isn’t).
   6. Grab a copy of Photoshop CS4 Extended. I’m using the 30-day trial version which can be downloaded for free from Adobe’s website, since the full version costs truckloads of cash. It will be enough for my project. Run the installer using wine, like this:
[code lang="bash"]
      LANG=C wine Setup.exe
[/code]
      From here it’s a matter of just following the installer wizard. I experienced some minor graphical glitches in the installer on my system, like check-boxes not rendering properly. Nothing serious, just annoying.

Congrats! If everything went well you should now be able to run Photoshop CS4 from the Wine application menu, or running
[code lang="bash"]
wine "C:Program FilesAdobeAdobe Photoshop CS4Photoshop.exe"
[/code]
The graphics are a bit wonky sometimes (I’m also running compiz), but otherwise it works great. Additional test reports can be found here.
Patching a newer version of wine

Note: these steps are experimental, and involve compilation. This may or may not work for you, but feel free to report your results in the comments section should you decide to try it.
[code lang="bash"]
sudo aptitude update
sudo aptitude purge wine
sudo aptitude install build-essential checkinstall
sudo apt-get build-dep wine
apt-get source wine
cd wine*
wget "http://bugs2.winehq.org/attachment.cgi?id=25093" -O wine_18070.diff
patch -p1 < wine_18070.diff
./configure
make
sudo checkinstall
[/code]
checkinstall will ask a few questions - just accept the provided defaults. wine will now be installed, and you can continue setting it up from step 2 in the tutorial above.
