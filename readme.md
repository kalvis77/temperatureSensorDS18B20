						# Temperature sensor project
				### Connecting the DS18B20 to the Raspberry Pi

The code used here is largely based on [DS18B20 Temperature Sensing by Simon Monk](https://cdn-learn.adafruit.com/downloads/pdf/adafruits-raspberry-pi-lesson-11-ds18b20-temperature-sensing.pdf).

	####	Hardware

	3V3 - Red wire
	Ground - Black wire
	GPIO 4 - White wire (with 4.7K resistor between this and 3V3).

The 3V3, Ground, GPIO wire can be connected to any 3.3V, Ground, GPIO pin accordingly.
Raspberry Pi 2 pinout diagram can be seen here:
[Raspberry-Pi-GPIO-Layout-Revision-1.png]

[picture with our sensor to be included] 


	####	Software

Edit /boot/config.txt and add the configuration line dtoverlay=w1-gpio, then reboot the Raspberry Pi.
`
	sudo nano /boot/config.txt
`
	\*add dtoverlay=w1-gpio and save\*
`
	sudo reboot
`
To see if it worked out, change directory (cd) /sys/bus/w1/devices. list directory contents (ls), and you should see a 28-xxxxxxx directory, one directory for each connected sensor. cd into that directory, then concatenate (cat) w1_slave.
```
	sudo modprobe w1-gpio
	sudo modprobe w1-therm
	cd /sys/bus/w1/devices
	ls
	cd 28-xxxxxxx
	cat w1_slave
```


			### Modifications 


What makes this programm different is it automatically writes temperature measurements in text file. 

You also might have noticed both temp.py and temp2.py in this repository. temp.py contains endless while True loop, thus making it useful for continuous measurements. However, temp2.py runs only once after each execution, therefore it can be used with cron. 


	####	Install dependencies

To see, which dependencies are required, check __init__ file.

Some packages can be found in the Raspbian archives. In that case use good old `apt-get`:
```
	sudo apt-get install python3-packagename
```
Some packages might need to be installed from the [Python Package Index (PiPI)](https://pypi.python.org/pypi). To install pip for python3:
```
	sudo apt-get install python3-pip
```
To install, for example, ntplib package:
```
	pip3 install ntplib
```

		###	Cron

In case you want to only execute the python programm at specific times. 

Installation
```
	sudo apt-get install cron
```
Edit cron jobs
```
	crontab -e
```
Basic format for a crontab is minute hour day_of_month month day_of_week command.
[https://crontab.guru/] -> use this for human readable form to test if it is set to exactly what you expect.

Here it is used as
	*/5 * * * * python3 /home/pi/temperature/temp2.py >/dev/null 2>&1
	every 5 minutes	| command 						| disable email notification

Use this to see, how cron works
```
	tail -f /var/sys/syslog
```

Example of the output received:
Jul 26 11:40:01 raspberrypi CRON[3000]: (pi) CMD (python3 /home/pi/temperature/temp2.py >/dev/null 2>&1)
Jul 26 11:45:01 raspberrypi CRON[3062]: (pi) CMD (python3 /home/pi/temperature/temp2.py >/dev/null 2>&1)
Jul 26 11:50:01 raspberrypi CRON[3159]: (pi) CMD (python3 /home/pi/temperature/temp2.py >/dev/null 2>&1)
Jul 26 11:55:01 raspberrypi CRON[3214]: (pi) CMD (python3 /home/pi/temperature/temp2.py >/dev/null 2>&1)