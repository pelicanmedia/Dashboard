#Dashboard
#####Because thrift store monitors still need things to do.

This project turns your monitor and Raspberry Pi into a simple, skinnable time and weather dashboard.

![alt text](https://lh5.googleusercontent.com/OvyLwyLtXF69AJ-8U68OPnLXhZNwOPG7JYv5i-fa_44=w1167-h875-no "Pi Kitchen Dashboard")

* * *

+ [Items Needed](#itemsNeeded)
+ [Instructions](#instructions)
    - [Cloning](#cloning)
    - [Fulfilling requirements](#fulfillingRequirements)
    - [Setting your location](#settingYourLocation)
    - [Configuring your Pi](#configuringYourPi)
        * [Disallowing screen sleep](#disallowingScreenSleep)
        * [Installing Unclutter](#hidingCursor)
        * [Installing Midori](#installingMidori)
        * [Auto-starting Unclutter and Midori](#autoStartingMidori)
    - [Scheduling screen sleep](#scheduling)
+ [Changing the skin](#changingTheSkin)
+ [Creating skins](#creatingSkins)
+ [Credit](#credit)

* * *

## <a name="itemsNeeded"></a>Items needed

+ Raspberry Pi
+ Monitor
+ Adapter to hook said Raspberry Pi to said monitor
+ Internet connection

## <a name="instructions"></a>Instructions

### <a name="cloning"></a>Cloning

Clone this repository with: 

  ```
  git clone https://github.com/pelicanmedia/Dashboard.git
  ```
  
If your Pi does not currently have git, you will need to install it first with: 
  
  ```
  sudo apt-get install git
  ```  
  
### <a name="fulfillingRequirements"></a>Fulfilling requirements

This project is not distributed with its dependencies; however, [Bower](http://bower.io/) will automatically pull them in.

1. Update your system:
  ```
  sudo apt-get update && sudo apt-get upgrade -y
  ```
  
2. Install Node Package Manager (required for Bower):
  ```
  curl -sL https://deb.nodesource.com/setup_7.x | sudo -E bash -
  sudo apt-get install -y nodejs build-essential
  ```
  Taken from <a href="https://nodejs.org/en/download/package-manager/#debian-and-ubuntu-based-linux-distributions">Installing Node.js via package manager</a>

3. Install Bower:
  ```
  sudo npm install -g bower
  ```
  
4. cd into the directory of the cloned project:
  ```
  cd ~/Dashboard
  ```
  
5. Install the project's dependencies:
  ```
  bower install
  ```
  
### <a name="settingYourLocation"></a>Setting your location

Open `js/weather.js` and find the following section at the top:

  ```javascript
  // Your Yahoo WOEID code
  // Find your WOEID code at http://zourbuth.com/tools/woeid/
  var woeid = 23416998;

  // Your temperature unit measurement
  // This bit is simple, 'c' for Celcius, and 'f' for Fahrenheit
  var unit = 'c';

  // Yahoo! query interval (milliseconds)
  // Default is every 15 minutes. Be reasonable. Don't query Yahoo every 500ms.
  var waitBetweenWeatherQueriesMS = 900000;
  ```
  
Change these variables to match your location, unit measurement, and desired update interval, and your part of the coding is done!

### <a name="configuringYourPi"></a>Configuring your Pi

You will need a Raspberry Pi (although you could use anything else) with Raspbian (again, or anything else) and an internet connection. To complete the dashboard, your Pi will need disallow screen sleep and automatically start kiosk mode.

#### <a name="disallowingScreenSleep"></a>Disallowing screen sleep

Unless screen sleep is prevented, the dashboard screen will go black after a few minutes and require a mouse movement or keypress to wake up. Scheduled times for the display to turn off are covered in a [later section](#scheduling).
  ```
  sudo nano /etc/lightdm/lightdm.conf
  ```
  
Add the following lines to the [SeatDefaults] section:
  ```
  xserver-command=X -s 0 dpms
  ```
  
#### <a name="hideCursor"></a>Installing Unclutter

Unclutter causes the mouse cursor to disappear when the mouse isn't being moved. This prevents the dash from having a cursor over the middle unless you plug in a mouse and move it elsewhere.
  ```
  sudo apt-get install unclutter
  ```
  
#### <a name="installingMidori"></a>Installing Midori

Midori is used for its compatibility with multiple RPi generations and reasonably solid rendering. Other browsers may be used if preferred using much the same strategy.
  ```
  sudo apt-get install midori
  ```
  
#### <a name="autoStartingMidori"></a>Auto-starting Unclutter and Midori

1. Create a new directory at `~/.config/autostart` if it does not exist
2. cd into this directory - `cd ~/.config/autostart`
3. Create a new .desktop file - `nano unclutterAuto.desktop`
4. Add the following lines and save.
  
  ```
  [Desktop Entry]
  Type=Application
  Exec=unclutter -idle 0.1
  ```
  
5. Create a new .desktop file - `nano midoriAuto.desktop`
6. Add the following lines, edit the file path to where this project's index.html lives on your Pi and save.
  
  ```
  [Desktop Entry]
  Type=Application
  Exec=midori -e Fullscreen -a file:///home/pi/Dashboard/index.html
  ```
  
Your Pi should now atomatically start kiosk mode and show the dashboard, full screen, once your desktop loads.

If your time or date are incorrect, use `sudo raspi-config` to set your locale and timezone.

### <a name="scheduling"></a>Scheduling screen sleep

If you don't want your display to run 24/7, you can use cron jobs to fire a pair of included bash scripts: screenOff.sh and screenOn.sh. Please ensure you've completed the [Disallowing screen sleep](#disallowingScreenSleep) step above in order to keep the display always on during the times it's scheduled to be on.

1. cd into your Dashboard directory and set both scripts to executable
  ```
  chmod +x screenOff.sh
  chmod +x screenOn.sh
  ```
  
2. Run `crontab -e` and add cronjobs to the end using the provided scripts. If you're not comfortable writing cronjobs manually, you can use a <a href="http://cron.nmonitoring.com/cron-generator.html">crontab generator</a>. The following lines, for example, shut off the display at 11:00PM each night and turn it back on at 6:00AM. Be sure to edit the file paths if necessary.
  ```
  0 23 * * * /home/pi/Dashboard/screenOff.sh
  0 6 * * * /home/pi/Dashboard/screenOn.sh
  ```
  
## <a name="changingTheSkin"></a>Changing the skin

Skins are kept, conveniently, in the skins folder. To switch skins, edit `Dashboard/index.html` and insert the folder name of the skin you wish to use where the comments direct.

## <a name="creatingSkins"></a>Creating skins

Creating your own skin or a new skin for distribution is easy and only requires knowledge of HTML and CSS. Just copy the `default` folder under skins, rename it, and begin editing. Comments in the default skin will guide you through the process, but it basically boils down to 99% using your imagination and 1% placing a few IDs and classes so that time and weather data can be auto-populated.

## <a name="credit"></a>Credit

Weather icons by Lukas Bischoff and Erik Flowers https://github.com/erikflowers/weather-icons. Icons licensed under [SIL OFL 1.1](http://scripts.sil.org/OFL).  

Time formatting by [Moment.js](http://momentjs.com/)  

Weather data retrieved using Yahoo! Weather API.  

Default skin responsiveness by [RYJASM](https://github.com/ryjasm).

Project is under [MIT license](http://choosealicense.com/licenses/mit/).  
