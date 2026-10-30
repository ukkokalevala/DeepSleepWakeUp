How DS1302 RTC Works with ESP32-C3 in Deep Sleep
ESP32-C3 wakes up based on an external alarm from the DS1302 RTC module. Let's break it down step by step.
________________________________________
Understanding the DS1302 RTC Module
The DS1302 Real-Time Clock (RTC) keeps track of time even when the ESP32 is powered off, thanks to its battery backup. It communicates using a 3-wire interface (CLK, DAT, RST).
Connections in the case:
• CLK (Clock) → GPIO 2
• DAT (Data I/O) → GPIO 3
• RST (Reset/Enable) → GPIO 4
• VCC → 3.3V
• GND → GND
What It Does
• Stores the current date & time
• Can be programmed to set an alarm
• Runs on a small battery, so it keeps time even when ESP32 is off
________________________________________
ESP32-C3 Deep Sleep & Wake-Up
The ESP32-C3 can enter deep sleep mode to save power. While sleeping, only some GPIOs (RTC GPIOs) can wake it up.
How Wake-Up Works
• The DS1302 sends a signal to a GPIO when the alarm triggers
• The ESP32 wakes up and executes the code
• The onboard LED blinks, and the LCD 1602 displays the boot count
• The ESP32 goes back to deep sleep and waits for the next alarm
ESP32-C3 Wake-Up Pin (Example)
If using GPIO 4 as the wake-up pin:
cpp
esp_sleep_enable_ext0_wakeup(GPIO_NUM_4, LOW); // Wake up on LOW signal
________________________________________
LCD 1602 I2C for Display
The LCD 1602 (I2C version) uses two wires for communication:
Connections:
• SDA (Data Line) → GPIO 7
• SCL (Clock Line) → GPIO 6
What It Does
• Displays boot count (how many times ESP32 woke up)
• Shows at current time from RTC
________________________________________
Buzzer for Alarm Sound if available 
You can use a buzzer to sound an alert when ESP32 wakes up.
 Active vs Passive Buzzer
• Active Buzzer → Always makes a sound when powered
• Passive Buzzer → Needs a frequency signal to generate sound
To beep for a few seconds:
cpp
CopyEdit
digitalWrite(buzzerPin, HIGH);  
delay(2000);  // Beep for 2 seconds  
digitalWrite(buzzerPin, LOW);
________________________________________
Code Flow (Step by Step)
1. ESP32 reads the current time from DS1302 RTC
2. Sets an alarm for the next wake-up time
3. Goes into deep sleep
4. When the alarm triggers, the RTC sends a wake-up signal
5. ESP32 wakes up
6. Blinks LED + Updates LCD with boot count + Sounds buzzer
7. Going back to sleepSummary
• DS1302 keeps time & triggers alarm
• ESP32 wakes up on alarm signal
• Boot count & time displayed on LCD
• Buzzer sounds for a few seconds when available 
• ESP32 sleeps again until the next alarm
When to Use Deep Sleep Wake-Up on ESP32-C3
Deep sleep is used when you need to save power while still allowing the ESP32 to wake up under certain conditions.
When to Use Deep Sleep?
1. Battery-Powered Devices – To extend battery life (e.g., IoT sensors, trackers).
2. Low-Power Projects – Where the ESP32 doesn’t need to be always on.
3. Scheduled Tasks – Wake up at specific times to check conditions (e.g., weather updates).
4. Event-Based Wake-Up – Only wake when a button is pressed or an alarm triggers.
5. Data Logging – Wake up, read sensors, save data, then go back to sleep.
When to Wake Up?
ESP32-C3 can wake up from deep sleep using:
1. RTC Timer Wake-Up → After a set time (e.g., every 10 minutes).
2. External Wake-Up → Triggered by an external signal (e.g., DS1302 RTC alarm).
3. Touch Wake-Up → If using touch-sensitive pins.
4. GPIO Wake-Up → When a button or sensor changes state.
5. ULP Coprocessor Wake-Up → Runs a low-power processor to check conditions.
Your project uses RTC Alarm Wake-Up with the DS1302, meaning the ESP32 sleeps until the RTC sends a signal to wake it up.
