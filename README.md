# 🌍 IoT-Based Urban Air Quality Monitoring System

An intelligent, real-time air pollution monitoring system using low-cost IoT components like ESP32, MQ135, PMS5003, and DHT22. It collects data on harmful gases, particulate matter (PM2.5/PM10), temperature, and humidity, and transmits this data to a mobile dashboard via the Blynk IoT platform.

---

## 📌 Project Overview

This project addresses growing concerns about air pollution in urban areas. It enables remote monitoring of air quality and provides real-time alerts when pollution levels rise. The system is designed to be affordable, scalable, and suitable for deployment in schools, streets, offices, and smart cities.

---

## 🧪 Features

- 🟢 Real-time monitoring of gas levels and PM2.5/PM10
- 🌡️ Temperature and Humidity measurement
- 📱 Live dashboard using Blynk IoT platform
- 🔔 Alert system using LEDs and buzzers
- 💾 Simulated testing using Wokwi (for safe prototyping)
- ☁️ Cloud-connected (Wi-Fi enabled)
- ⚡ Low power, suitable for solar or battery operation

---

## 🛠️ Technologies Used

- **ESP32** – Wi-Fi enabled microcontroller
- **MQ135** – Air quality sensor (NH₃, CO₂, Benzene)
- **PMS5003** – Particulate matter sensor (PM1.0, PM2.5, PM10)
- **DHT22** – Temperature and humidity sensor
- **Arduino IDE** – Firmware development
- **Wokwi** – Circuit simulation

---

## 📷 System Architecture

```
[Sensors] → [ESP32] → [Wi-Fi] → [Blynk Cloud] → [User Dashboard]
    |
 [LEDs/Buzzer for Alerts]
```

---

## 📲 Live Dashboard

Users can monitor data on the Blynk app with:
- Gauges (Temp, Humidity, PM2.5)
- Graphs (Historical data)
- Real-time Notifications

---

## 🚀 Getting Started

### 🧱 Hardware Requirements
- ESP32 board
- MQ135 sensor
- PMS5003 PM sensor
- DHT22 sensor
- Buzzer + LEDs
- Breadboard, jumpers

### 💾 Software Requirements
- Arduino IDE
- Blynk Library
- ESP32 Board support
- Wokwi (optional for simulation)

### 🔧 Setup

1. Clone this repository
2. Open `main.ino` in Arduino IDE
3. Replace Blynk credentials in the code
4. Upload the code to ESP32
5. Connect to the Blynk app and monitor data

---

## 📈 Results

- Successfully measured PM levels during vehicle traffic
- Air quality trends detected in daily cycles
- LED & buzzer alerts triggered when levels crossed thresholds
- Data visualized remotely through Blynk dashboard

---

## 📚 References

- [MQ135 Datasheet](https://components101.com/sensors/mq135-gas-sensor)
- [PMS5003 Sensor Guide](https://www.adafruit.com/product/3686)
- [Blynk Platform](https://blynk.io/)
- [Wokwi Simulator](https://wokwi.com/)
- [ESP32 Arduino Core](https://github.com/espressif/arduino-esp32)

---

## 🤝 Contributing

Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 👨‍💻 Maintained by

[mce cse students]  
[advijithkumar <._.>]
