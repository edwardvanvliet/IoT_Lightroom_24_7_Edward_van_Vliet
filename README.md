# IoT - Lightroom 24/7 - Arduino - Adafruit IO - Color Picker - Light Sensor - Manual - Edward van Vliet

# Picking a color via the Color Picker (Block) on Adafruit IO using a LED-strip connected to a NODEMCU 1.0, by using Arduino IDE (2.0) software. In my concept a light sensor is required to measure the brightness of the light coming from outside (to your window). We do have to connect our NodeMCU 1.0 to the internet. Because when using the PRE-SET Mode (see UX Documentation), my LED-lights will need to know the date and time. That's why we will use a NTP Server to determine the date and time. For example, in Winter times, it gets dark earlier at night and then the next morning the sun will also rise earlier. So the amount of light that is coming from outside also depends on what season it is.

By Edward van Vliet - 500761369<br>
Last updated 27 October 2022

## Introduction
Using this manual you'll be able to pick a color via the Color Picker (Block) on Adafruit IO using a LED-strip connected to a NODEMCU 1.0, by using Arduino IDE (2.0) software. In my concept a light sensor (sticked to your window) is required to measure the brightness/strength of the incoming light. So, by using a light sensor (sticked to or at your window), you'll be able to measure the brightness of the incoming light from outside, the natural light. We'll also need to connect our NodeMCU 1.0 to the internet. Because when using the PRE-SET Mode (see UX Documentation), my LED-lights will need to know the date and time. That's why we will use a NTP Server to determine the date and time. For example, in Winter times, it gets dark earlier at night and then the next morning the sun will also rise earlier. So the amount of light that is coming from outside also depends on what season it is. We will also connect our NodeMCU 1.0 to the internet, because eventually, you'll either have to use the NTP Client Library, or another Light Sensor API in this manual.

## Required hardware components
  - 1x Node MCU 1.0
  - 1x LED-strip
  - 3x Jumper Wires glued to the end of the LED-strip
  - 1x USB-C to USB-B microcable (https://www.allekabels.nl/usb-c-kabel/11518/4080378/usb-c-naar-usb-b-micro-kabel.html?gclid=Cj0KCQjw1vSZBhDuARIsAKZlijQllk1tmEmzDAyOpEEB6p9cz1rm71ymovd92VNrhihNyiu-eR0gt8saAvW9EALw_wcB)
  
  
## Step 1: Connecting the Node MCU 1.0 to your laptop, and connect your LED-strip to the Node MCU 1.0

The first thing we want to do is connect our Node MCU to your laptop, using an USB-C to USB-B microcable. Then, we want to connect our LED-strip to the Node MCU, using Jumber Wires glued at the other end of the LED-strip. The image below shows which wires need to be connected to which pins on the board.

Leftmost pin to `3v3` (red wire)<br>
Second pin to `GND` (black wire)<br>
Right pin to `D5` (yellow wire)<br>

![Image of requirements](https://github.com/edwardvanvliet/IoT_Lightroom_24_7_Edward_van_Vliet/blob/main/images/00_Required_Hardware_Components_20221010_125042_HDR.jpg)


## Step 2: Installing the required libraries from Adafruit IO in Arduino IDE

For the LED-strip to properly function, we will first need to install the required libraries in the [Arduino IDE](https://www.arduino.cc/en/main/software). We can do this by going to the 'Sketch' dropdown menu, selecting 'Include Library' and then clicking on 'Manage Libraries'.<br>

1. Go to the libraries tab that is the third button on the left. If your IDE verion is lower than 2.0, you can find this in Sketch > Include library > Manage libraries.
2. Here you search for "Adafruit IO Arduino". Watch out because sometimes the right one is not the one that pops up first.
3. Find the right one and click "Install" and then "Install All".


## Step 3: Installing the required libraries for the NTP Server

For the NTP Server you'll need to install the following library, called ["NTP Client"](https://randomnerdtutorials.com/esp8266-nodemcu-date-time-ntp-client-server-arduino/):

1. Go to your Library Manager once again, on the left.
2. Here you search for "NTP Client". Watch out because sometimes the right one is not the one that pops up first.
3. Find the right one (by Fabrice Weinberg), choose the latest version "NTP Client by Fabrice Weinberg (V.3.2.1.)" and click "Install".

![Image of NTP Client Installed](https://github.com/edwardvanvliet/IoT_Lightroom_24_7_Edward_van_Vliet/blob/main/images/02_NTP_Client_Library_Installed.png)


## Step 4: Copy the code and use your own WiFi (using an own Mobile Hotspot Wi-Fi is recommended, avoid 5GHz connection)

~~~
#include <NTPClient.h>
// change next line to use with another board/shield
#include <ESP8266WiFi.h>
//#include <WiFi.h> // for WiFi shield
//#include <WiFi101.h> // for WiFi 101 shield or MKR1000
#include <WiFiUdp.h>

// Replace with your network credentials
const char *SSID     = "REPLACE_WITH_YOUR_SSID";
const char *PASSWORD = "REPLACE_WITH_YOUR_PASSWORD";

// Define NTP Client to get time
WiFiUDP ntpUDP;
NTPClient timeClient(ntpUDP, "pool.ntp.org");

//Week Days
String weekDays[7]={"Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"};

//Month names
String months[12]={"January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"};

void setup() {
  // Initialize Serial Monitor
  Serial.begin(115200);
  
  // Connect to Wi-Fi
  Serial.print("Connecting to ");
  Serial.println(ssid);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

// Initialize a NTPClient to get time
  timeClient.begin();
  // Set offset time in seconds to adjust for your timezone, for example:
  // GMT +1 = 3600
  // GMT +8 = 28800
  // GMT -1 = -3600
  // GMT 0 = 0
    timeClient.setTimeOffset(7200);
}

void loop() {
  timeClient.update();

  time_t epochTime = timeClient.getEpochTime();
  Serial.print("Epoch Time: ");
  Serial.println(epochTime);
  
  String formattedTime = timeClient.getFormattedTime();
  Serial.print("Formatted Time: ");
  Serial.println(formattedTime);  

  int currentHour = timeClient.getHours();
  Serial.print("Hour: ");
  Serial.println(currentHour);  

  int currentMinute = timeClient.getMinutes();
  Serial.print("Minutes: ");
  Serial.println(currentMinute); 
   
  int currentSecond = timeClient.getSeconds();
  Serial.print("Seconds: ");
  Serial.println(currentSecond);  

  String weekDay = weekDays[timeClient.getDay()];
  Serial.print("Week Day: ");
  Serial.println(weekDay);    

  //Get a time structure
  struct tm *ptm = gmtime ((time_t *)&epochTime); 

  int monthDay = ptm->tm_mday;
  Serial.print("Month day: ");
  Serial.println(monthDay);

  int currentMonth = ptm->tm_mon+1;
  Serial.print("Month: ");
  Serial.println(currentMonth);

  String currentMonthName = months[currentMonth-1];
  Serial.print("Month name: ");
  Serial.println(currentMonthName);

  int currentYear = ptm->tm_year+1900;
  Serial.print("Year: ");
  Serial.println(currentYear);

  //Print complete date:
  String currentDate = String(currentYear) + "-" + String(currentMonth) + "-" + String(monthDay);
  Serial.print("Current date: ");
  Serial.println(currentDate);

  Serial.println("");

  delay(2000);
}
~~~


## Step 5: If-Else Statement
Add an IF-ELSE Statement, as stated below. 
Make sure that the following code is added at the end, but (important) inside the void loop!

~~~
if (currentHour == 8) {
    Serial.println("Hello, it is 8");
    }
// do stuff if the condition is true
else{
    Serial.println("Hello it's not 8");
    }
~~~

If the clock is at 8 (AM) you will get the following line on your serial monitor “Hello, it is 8”. If that's not the case, you will get the message “Hello, it's not 8”. The two (==), is also essential. It [means](https://www.arduino.cc/reference/en/language/structure/comparison-operators/equalto/) that it is true if currentHour is equal to 8 and it is false if currentHour is not equal to 8 (in this case).


## Step 6: Setting up Adafruit IO

1. Go to https://io.adafruit.com/, click on "Get started for free" and make your account.
2. When your account is ready, click on "IO" in the navigation menu at the top of the page.
3. Click on the button with the yellow key icon.
![Image of your key and username in Adafruit IO](https://github.com/edwardvanvliet/IoT_Lightroom_24_7_Edward_van_Vliet/blob/main/images/01_ADAFRUIT_IO_KEY_USERNAME.png)
4. Copy your key and remember your username for later use.


## Step 7: Implementing elements of Adafruit Neopixels' Library code with your NTP Client code, adjust your code

Step 7 is implementing/adding the right elements of Adafruit Neopixels' Library code to your current NTP Client Code.
Copy the following code between the library’s and the Wi-Fi settings, in your NTP Client code.

~~~
#include <Adafruit_NeoPixel.h>
#ifdef __AVR__
 #include <avr/power.h> // Required for 16 MHz Adafruit Trinket
#endif

// Which pin on the Arduino is connected to the NeoPixels?
#define PIN        6 // On Trinket or Gemma, suggest changing this to 1

// How many NeoPixels are attached to the Arduino?
#define NUMPIXELS 18 // Popular NeoPixel ring size

// When setting up the NeoPixel library, we tell it how many pixels,
// and which pin to use to send signals. Note that for older NeoPixel
// strips you might need to change the third parameter -- see the
// strandtest example for more information on possible values.
Adafruit_NeoPixel pixels(NUMPIXELS, PIN, NEO_GRB + NEO_KHZ800);

#define DELAYVAL 500 // Time (in milliseconds) to pause between pixels
~~~
Don't forget to change the the pin into D5 and change the number om NUMPIXELS into the amount of your ledstrip.
<br>
Put the next type of code on the lower side of the void loop.
~~~
void loop() {
  pixels.clear(); // Set all pixel colors to 'off'

  // The first NeoPixel in a strand is #0, second is 1, all the way up
  // to the count of pixels minus one.
  for(int i=0; i<NUMPIXELS; i++) { // For every pixel

    // pixels.Color() takes RGB values, from 0,0,0 up to 255,255,255
    // Here we're using a moderately bright green color:
    pixels.setPixelColor(i, pixels.Color(0, 150, 0));

    pixels.show();   // Send the updated pixel colors to the hardware

    delay(DELAYVAL); // Pause before next pass through loop
  }
}
~~~


## Step 8: Adjust your IF-ELSE Statement in the NTP Client code

One of the last steps is making the IF-ELSE Statement complete (as below):

~~~
if (currentHour == 8) {
    Serial.println("Hello, it is 8");
  for(int i=0; i<NUMPIXELS; i++) { // For every pixel

    // pixels.Color() takes RGB values, from 0,0,0 up to 255,255,255
    // Dark blue night color:
        pixels.setPixelColor(i, pixels.Color((123, 132, 255));

    pixels.show();   // Send the updated pixel colors to the hardware
    delay(DELAYVAL); // Pause before next pass through loop
  }
}
else {
    Serial.println("Hello it's not 8");
  for(int i=0; i<NUMPIXELS; i++) {

    // pixels.Color() takes RGB values, from 0,0,0 up to 255,255,255
    // Bright daylight blue color:
    pixels.setPixelColor(i, pixels.Color((0, 229, 255));

    pixels.show();   // Send the updated pixel colors to the hardware

    delay(DELAYVAL); // Pause before next pass through loop
  }
}
~~~

If you upload the code, it will give your LED-strip a light blue (day)light color.
And if the clock turns 8 (AM), your LED-strip will turn into dark blue (night). 


## Step 9: Uploading your code will give you the following image in your Serial Monitor

![Image of Serial Monitor NTP Client](https://github.com/edwardvanvliet/IoT_Lightroom_24_7_Edward_van_Vliet/blob/main/images/03_Serial_Monitor_NTP_Gelukt.png)


## Step 10: Storyboard Case of Herman in reality
Imagine being my persona Herman, a hardworking office worker, but he often works at home, working graveyard-shifts for most of the time. 
So for example, Herman's graveyard-shift starts at 11 PM (= 23) at night, so the LED's will have to turn into a bright daylight color.
Herman sleeps after his shift around 9 AM (= 9), so he'll need to sleep by then. So then, from 9 AM the lights need to dim a bit, into a darker blue (night) color.
This was my solution in the following code:

~~~
if (currentHour > 23){
    Serial.println("It’s time to work Herman!");
  for(int i=0; i<NUMPIXELS; i++) { // For each pixel

    // pixels.Color() takes RGB values, from 0,0,0 up to 255,255,255
    // Bright daylight blue color to keep Herman sharp for work:
        pixels.setPixelColor(i, pixels.Color(0, 229, 225));

    pixels.show();   // Send the updated pixel colors to the hardware

    delay(DELAYVAL); // Pause before next pass through loop
  }
}
else {
    Serial.println("It’s bedtime for you Herman.");
  for(int i=0; i<NUMPIXELS; i++) {

    // pixels.Color() takes RGB values, from 0,0,0 up to 255,255,255
    // Dark blue night color, so Herman can sleep like a baby:
        pixels.setPixelColor(i, pixels.Color(123, 132, 255));
    pixels.show();   // Send the updated pixel colors to the hardware

    delay(DELAYVAL); // Pause before next pass through loop
    }
  ~~~
<br>


## Possible Errors:

## Error 1: Connection Error
If you keep seeing dots coming up on your Serial Monitor, it means something is wrong with the connection of your NodeMCU 1.0.
In my case the problem was the WiFi connection.
Because at first, I tried using my 5GHz (Home) WiFi throughout this whole process.
Unfortunately the Serial Monitor kept presenting me dots...

![Image of the connection error dots in the Serial Monitor](https://github.com/edwardvanvliet/IoT_Lightroom_24_7_Edward_van_Vliet/blob/main/images/17_Connection_Error_dots.png)

The dots that are appearing every 1/2 a second on your Serial Monitor means that your hardware, in this case the NodeMCU 1.0, can't properly connect to a WiFi network.
That's why it is highly recommended to use an own mobile hotspot, PREVENT using a 5GHz WiFi or hotspot! (According to [this source](https://arduino.stackexchange.com/questions/49370/esp8266-not-connecting-to-wifi) on Arduino Stack Exchange.)

## Solution to Error 1: Connection Error
Seeing the dots keep coming up on my Serial Monitor, I directly knew there was something wrong with the connection.
And then I searched about this connection error, and I found out I was not the only one with this problem.
Apparently according to [this article post about ESP WiFi problems](https://arduino.stackexchange.com/questions/49370/esp8266-not-connecting-to-wifi), the ESP(-296609) is not able to connect with 5Ghz WiFi.

![Image of mobile hotspot WiFi connected](https://github.com/edwardvanvliet/IoT_Lightroom_24_7_Edward_van_Vliet/blob/main/images/14_Device_is_connected_to_my_hotspot.png)

So then I tried connecting the ESP-296609 to my mobile hotspot WiFi, on my smartphone. And fortunately, it worked!


## Error 2: Missing Bracket
Sometimes when you work too fast, you'll get the following error your Serial Monitor, it means that there's a bracket missing on a certain line.

![Image of bracket error](https://github.com/edwardvanvliet/IoT_Lightroom_24_7_Edward_van_Vliet/blob/main/images/05_Bracket_Error.png)

But luckily you can read exactly where you are missing a bracket, so you don't have to search through your code.
(What can be quite frustrating when you have a huge file of code.)

## Solution to Error 2: Missing Bracket

Just simply add the bracket where it belongs. You should find it quite fast, because Arduino highlights the line red for you, that's where the error occurred.

