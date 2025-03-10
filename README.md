import Adafruit_DHT
import time
import requests

# Sensor configuration
DHT_SENSOR = Adafruit_DHT.DHT11
DHT_PIN = 4  # GPIO pin where the sensor is connected

# IoT dashboard API configuration
THINGSPEAK_URL = "https://api.thingspeak.com/update"
THINGSPEAK_API_KEY = "YOUR_API_KEY_HERE"  # Replace with your API key

# Function to read temperature and humidity
def read_sensor():
    humidity, temperature = Adafruit_DHT.read(DHT_SENSOR, DHT_PIN)
    if humidity is not None and temperature is not None:
        return temperature, humidity
    else:
        print("Failed to retrieve data from sensor")
        return None, None

# Function to send data to ThingSpeak
def send_to_thingspeak(temperature, humidity):
    payload = {
        'api_key': THINGSPEAK_API_KEY,
        'field1': temperature,
        'field2': humidity
    }
    response = requests.get(THINGSPEAK_URL, params=payload)
    if response.status_code == 200:
        print("Data sent successfully")
    else:
        print("Failed to send data")

# Main loop
if __name__ == "__main__":
    while True:
        temp, hum = read_sensor()
        if temp is not None and hum is not None:
            print(f"Temperature: {temp}°C, Humidity: {hum}%")
            send_to_thingspeak(temp, hum)
        time.sleep(15)  # Send data every 15 seconds

