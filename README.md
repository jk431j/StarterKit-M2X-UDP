# StarterKit-M2X-UDP

This is a binary for [AT&T IoT StarterKit](https://marketplace.att.com/products/att-iot-starter-kit-lte-m) that allows the kit to send sensor data to [M2X](https://m2x.att.com) and/or UDP endpoint in [Flow](https://flow.att.com).

You will need StarterKit and microSD card where you will put the configuration file.

Collected sensor data are temperature (in &deg;C), humidity and 3-axis accelerometer.

## How to use

1. format microSD card to FAT32 filesystem.
2. create `config.ini` file and put it in the root directory of microSD card
3. assemble the kit, insert microSD card into NXP FRDM-K64F board power it up and connnect to the computer
4. put `StarterKit_M2X_UDP_K64F.bin` into root directory of your `MBED` USB drive
5. after 10 seconds the green LED next to USB port on NXP board will stop blinking, press `RESET` button
6. if everything is properly configured the RGB led on NXP board should turn red first then blink yellow and after a moment turn green
7. the RGB led will blink green whenever data are being sent to M2X

## config.ini file

All configuration parameters are in config.ini file in format ```option=value``` one configuration option per line.
Configuration options are _case sensitive_. No quotes around text values. These are the supported configuration options:
- __Enable_M2X__ = enables sending data to M2X. 1=enabled, 0=disabled
- __Device_ID__ = M2X device ID
- __M2X_Key__ = M2X API key, could be either device's primary API key or master API key
- __M2X_Commands__ = periodicaly checks M2X for pending device commands, see [API reference](https://m2x.att.com/developer/documentation/v2/commands) for more information on commands. 1=enabled, 0=disabled
- __Enable_UDP__ = enables sending data to Flow over UDP. The endpoint can be any UDP listener, not just Flow. 1=enabled, 0=disabled
- __UDP_Host__ = UDP endpoint hostname 
- __UDP_Port__ = UDP endpoint port number
- __IMEI__ = StarterKit's IMEI. 16 digits. It is part of UDP packet.
- __Latitude__
- __Longitude__ = StarterKit's location. It is part of UDP packet. Latitude has to be in range -90 to 90 and Longitude in range -180 to 180.
- __Comand_Wait__ = This determines how many seconds should StarterKit wait between polling for comands
- __Command_Polls__ = This determines how many times should StarteKit poll M2X for pending commands between sending values

The wait time between sending values to M2X and/or UDP endpoint is a product of Command_Wait and Command_Polls: `wait time = Command_Polls * Command_Wait`. This formula is used even if M2X and commands are not enabled so make sure to always set Command_Wait and Command_Polls to reasonable values. 
For instance, if you want the values to be sent every minute you can set Command_Wait=10 and Command_Polls=6: 10 * 6 = 60 seconds.

Sample config.ini is in the repo.

## M2X streams
In order to send data to M2X the device and streams have to be already created. The code expects following stream names to be present on the device:

|Stream ID|Meaning|
|---|---|
|temp|temperature|
|humidity|humidity|
|accelX|X-axis accelerometer value|
|accelY|Y-axis accelerometer value|
|accelZ|Z-axis accelerometer value|

## UDP packet format
UDP packet payload is single line string and contains following comma separated values:

`IMEI,temperature,humidity,X-acelerometer,Y-accelerometer,Z-accelerometer,latitude,longitude`

IMEI, latitude and longitude are taken from the configuration file. IMEI is enclosed in single quotes.

## Source code
Source code is available in MBED repository: https://os.mbed.com/users/korycanjan/code/StarterKit_M2X_UDP/
