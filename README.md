# ClownCar
Clown Car is an Arduino Nano ESP32 + OTG adapter that changes profiles for the RT4K based on gameID. <br />

<img width="500" src=https://github.com/user-attachments/assets/4a560cfe-95f1-4e88-9f77-3f6dc569a9a4><br />



See it in action: https://youtu.be/ldbfFbKzjh8
<br /><br />

## Parts used:
  - **OTG Adapter:** https://www.amazon.com/dp/B0CQKXWRNF
     - 18w version, not 60w
     - not all OTG adapters work, this one works the best
  - **Arduino Nano ESP32:** https://www.amazon.com/dp/B0CXHZXJXP
 <br /><br />

## gameID devices currently supported
| **Device**    | Supported | Notes |
| ------------- | ------------- |------------- |
|PS1Digital | yes, confirmed first hand | |
|N64Digital | yes, confirmed first hand | |
| MemCardPro 2 | yes, for GameCube, betting the same for PS |
| Fenrir ?| | |
| more on the way... |  

### LED activity:
| **Color**    | Blinking | On | Notes |
| ------------- | ------------- |------------- |------------- |
|<code style="color : blue">BLUE</code> | WiFi active, querying gameID addresses| long slow blinks is unsuccessful query of gameID address. A powered off console in the list.| |
|<code style="color : green">GREEN</code> | 1 second blink is gameID match found and SVS profile being sent to RT4K | |  | 
|<code style="color : red">RED</code> | | Power| No way to control as it's hardwired in. May just need to cover with tape. |

You can disable the <code style="color : blue">BLUE</code> / <code style="color : green">GREEN</code> LEDs by commenting out the following lines in the .ino:
```
 //pinMode(LED_GREEN, OUTPUT);
 //pinMode(LED_BLUE, OUTPUT);
```
## Programming the Arduino Nano ESP32
I recommend the [Official Arduino IDE and guide](https://www.arduino.cc/en/Guide) if you're unfamiliar with Arduinos. All .ino files used for programming are listed above. The following Libraries will also need to be added in order to Compile successfully.<br />
- **Libraries:**
  - If a Library is missing, it's usually available through the built-in Library Manager under "Tools" -> "Manage Libraries..."
  - <EspUsbHostSerial_FTDI.h>  Follow these steps to add EspUsbHostSerial_FTDI.h
    - Goto https://github.com/wakwak-koba/EspUsbHost
    - Click the <code style="color : green">GREEN</code> "<> Code" box and "Download ZIP"
    - In Arudino IDE; goto "Sketch" -> "Include Library" -> "Add .ZIP Library"
   

**To put the Nano ESP32 into programming mode** 
 - Double-click the button on top right after connecting the usb-c cable.
 - You will see the <code style="color : green">GREEN</code> led strobe if successful.
 - TLDR is: Because ClownCar takes over the usb port, this resets that.

## General Setup

*** At the moment it takes about 25 seconds to "boot". Just be aware when you don't see any activity at first. ***

If you have multiple gameID consoles on when the ClownCar is booting, the console furthest up the "consoles" list wins. After that it keeps track of the order.

There are a multiple moving parts with this setup, and if you have issues, please use the "ClownCar_usb-only-test.ino". More info in the troublehshooting section at the end.

#### I believe the only library you need to add is a fork of "EspUsbHost"
Go to: https://github.com/wakwak-koba/EspUsbHost 
 - Click the GREEN "<> Code" box and "Download ZIP"
 - In the Arudino IDE; goto "Sketch" -> "Include Library" -> "Add .ZIP Library"

<br />

## Adding gameIDs

Edit the .ino file to include your gameID addresses and gameID to SVS profile matches. Chances are if this isn't done correctly, it won't compile... which is a good way to know. :)
```
Console consoles[] = {{"http://ps1digital.local/gameid",0,0,0}, // you can add more, but stay in this format
                      {"http://n64digital.local/gameid",0,0,0}
                      };

String gameDB[][2] = {{"00000000-00000000---00","7"}, // 7 is for S7_profilename.rt4
                      {"XSTATION","8"},               // XSTATION is the gameID from http://ps1digital.local/gameid
                      {"SCUS-94300","9"}};
```
## WiFi setup
WiFi is listed just below. **ONLY** compatible with **2.4GHz** WiFi APs. Replace SSID and password with your network's. Make sure to not leave out the "" "" quotes.
```
wifiMulti.addAP("SSID","password");
```
<br />

## Thank you!
 - Thanks to https://github.com/wakwak-koba for his fork of the EspUsbHost library. Without it, it would have taken much longer to figure out the usb communication bits.
  - Huge thanks to @CielFricker249 / "Aru" on the RetroTink discord for the idea and testing of the Donut Dongle project as well!

## TroubleShooting ##

The <code style="color : green">GREEN</code> and <code style="color : blue">BLUE</code> leds indicate WiFi and usb serial/gameID lookup respectively. This should help diagnose as a first step.

After that, confirm the following:
 - Configured Wifi settings in .ino.
    - Make sure it's to a **2.4GHz** Wifi AP. **5GHz is NOT supported** by the Arduino Nano ESP32.
 - Have at least 1 gameID to profile in the gameDB
 - Have at least 1 address in the consoles db that you can access with a web browser

 If you are sure of these settings, and it still does not work, try the following to test the usb serial connection:
  - Configure your Arduino Nano ESP32 with the provided "ClownCar_usb-only-test.ino". This is configured to only load "remote profile 8".
    - You can change the 8 to 1 - 9 if needed.
  - Verify that everything is connected with your OTG adapter and has power.
  - Press the reset button on top of the Arduino and within a couple of seconds it should load the remote profile.

    If this works, then there must be a wifi connectivity issue somewhere. 

    Here is a video of the "usb only test" being performed: https://www.youtube.com/watch?v=XP7OSW7X0DQ

