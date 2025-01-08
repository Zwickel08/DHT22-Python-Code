# DHT22-Python-Code
# Mondl Marcel

import time
import pigpio
import sys
sys.path.insert(0, '/home/pi400/DEVEL/pigpio')  # Add the path where DHT22_module.py is located

# Import the DHT22 module
import DHT22_module as DHT22

# Intervals of about 2 seconds or less will eventually hang the DHT22.
INTERVAL = 3

# Initialize the pigpio library and the DHT22 sensor
pi = pigpio.pi()
s = DHT22.sensor(pi, 2, LED=16, power=8)  # GPIO 2 for the data pin

# Initialize a counter for readings
r = 0

# Get the current time for the next reading
next_reading = time.time()

# Open the file in write mode
with open("/home/pi400/DEVEL/pigpio/my_dht22_results.csv", "w") as file:
    while True:
        r += 1
        s.trigger()
        time.sleep(0.2)

        # Read humidity and temperature
        humidity = round(s.humidity(), 1)
        temperature = round(s.temperature(), 1)

        # Format the output for the console
        console_output = "{};{};{};".format(r, humidity, temperature)
        print(console_output)

        # Write the output to the file
        file.write(console_output + "\n")

        # Calculate the next reading time
        next_reading += INTERVAL
        time.sleep(next_reading - time.time())  # Overall INTERVAL second polling

# Cancel the sensor and stop pigpio
s.cancel()
pi.stop()

# to download your new csv file just put the following code in the cmd from your PC and change personal info

# Windows:
# scp pi400@192.168.0.15:/home/pi400/DEVEL/pigpio/my_dht22_results.csv "C:/User/" 
