# Smart Textile Temperature & Humidity Monitor

A comprehensive IoT system for monitoring temperature and humidity in smart textiles using ESP32 microcontroller with DS18B20 and DHT22 sensors, featuring automatic Peltier cooling control and a modern smartphone-optimized web interface.

## 🌟 Features

- **Real-time Monitoring**: Live temperature and humidity readings from DS18B20 and DHT22 sensors
- **Automatic Control**: Intelligent Peltier relay control based on configurable thresholds
- **Data Visualization**: Interactive charts showing historical sensor data
- **Smartphone Interface**: Mobile-optimized web dashboard accessible from any device
- **Data Storage**: Local storage of up to 200 sensor readings with CSV export
- **WiFi Connectivity**: Wireless communication and remote monitoring
- **Mock Data Fallback**: Works offline with simulated data for testing

## 🔧 Hardware Requirements

### Components
- **ESP32 Development Board** (ESP32-WROOM-32 or similar)
- **DS18B20 Temperature Sensor** (waterproof version recommended)
- **DHT22 Temperature & Humidity Sensor**
- **Relay Module** (5V, capable of handling Peltier current)
- **Peltier Cooling Element** (TEC1-12706 or similar)
- **Breadboard and Jumper Wires**
- **Power Supply** (12V for Peltier, 5V for ESP32)

### Wiring Diagram

![Hardware Wiring Diagram](hardware_setup.png)

*Complete wiring diagram showing ESP32 connections to DHT22, DS18B20, relay module, and TEC1-12706 Peltier cooling element*

\`\`\`
ESP32 Pin Connections:
├── GPIO 4  → DS18B20 Data
├── GPIO 5  → DHT22 Data
├── GPIO 2  → Relay Control Signal
├── 3.3V    → DS18B20 VCC, DHT22 VCC
├── GND     → DS18B20 GND, DHT22 GND, Relay GND
└── 5V      → Relay VCC

DS18B20 (3 wires):
├── Red    → 3.3V
├── Black  → GND
└── Yellow → GPIO 4

DHT22 (4 pins):
├── Pin 1 → 3.3V
├── Pin 2 → GPIO 5
├── Pin 3 → Not connected
└── Pin 4 → GND

Relay Module:
├── VCC → 5V
├── GND → GND
├── IN  → GPIO 2
├── COM → Peltier Positive
├── NO  → 12V Power Supply Positive
└── NC  → Not used
\`\`\`

## 📦 Software Setup

### Arduino IDE Setup

1. **Install ESP32 Board Package**:
   - Open Arduino IDE
   - Go to File → Preferences
   - Add to Additional Board Manager URLs: 
     \`\`\`
     https://dl.espressif.com/dl/package_esp32_index.json
     \`\`\`
   - Go to Tools → Board → Boards Manager
   - Search for "ESP32" and install "ESP32 by Espressif Systems"

2. **Install Required Libraries**:
   \`\`\`
   Tools → Manage Libraries → Install:
   - OneWire by Jim Studt
   - DallasTemperature by Miles Burton
   - DHT sensor library by Adafruit
   - ArduinoJson by Benoit Blanchon
   \`\`\`

3. **Board Configuration**:
   - Board: "ESP32 Dev Module"
   - Upload Speed: "921600"
   - CPU Frequency: "240MHz (WiFi/BT)"
   - Flash Frequency: "80MHz"
   - Flash Mode: "QIO"
   - Flash Size: "4MB (32Mb)"
   - Partition Scheme: "Default 4MB with spiffs"

### ESP32 Code Setup

1. **Configure WiFi Credentials**:
   \`\`\`cpp
   const char* ssid = "YOUR_WIFI_SSID";
   const char* password = "YOUR_WIFI_PASSWORD";
   \`\`\`

2. **Upload the Code**:
   - Connect ESP32 to computer via USB
   - Select correct COM port in Tools → Port
   - Click Upload button
   - Monitor Serial output at 115200 baud

3. **Find ESP32 IP Address**:
   - Open Serial Monitor
   - Look for "IP Address: xxx.xxx.xxx.xxx"
   - Note this IP for web interface access

## 🌐 Smartphone Web Interface

### Features Overview

The smartphone-optimized web interface provides full control and monitoring capabilities:

#### Home Dashboard
- **Current Readings**: Live temperature and humidity display cards
- **Relay Status**: Real-time Peltier cooling status indicator
- **Quick Overview**: At-a-glance system status with large, touch-friendly displays

#### Monitoring Page
- **Live Data**: Real-time sensor readings from both DS18B20 and DHT22
- **Historical Charts**: Interactive Chart.js graphs showing temperature and humidity trends
- **Multi-sensor Display**: Comparative data visualization

#### Temperature Control
- **Manual Control**: Touch-friendly buttons to turn Peltier cooling on/off
- **Smart Auto Control**: Prevents manual override when automatic control is actively managing the relay
- **Threshold Settings**: Configure temperature (°C) and humidity (%) limits
- **Auto Mode Toggle**: Enable/disable automatic temperature regulation with visual feedback

#### Data Center
- **Historical Data**: View all stored sensor readings in formatted display
- **CSV Export**: Download complete data history for analysis
- **Data Refresh**: Manual refresh of stored readings

### Configuration

1. **Set ESP32 IP Address**:
   \`\`\`javascript
   // In smartphone.tsx, update the API_URL
   const API_URL = 'http://192.168.1.100'; // Replace with your ESP32 IP
   \`\`\`

2. **Mock Data Mode**:
   - Leave `API_URL` empty for offline testing with simulated sensor data
   - Perfect for development and demonstration purposes

3. **Access the Interface**:
   - Open any web browser on smartphone, tablet, or computer
   - Navigate to your deployed web application
   - Interface automatically detects and connects to ESP32

### Smart Auto Control Logic

The smartphone interface includes intelligent auto control management:
- **Manual buttons disabled** when auto control is actively managing the relay
- **Visual indicators** show when auto control is in charge
- **Threshold monitoring** displays current vs. target values
- **Automatic override prevention** ensures consistent operation

## 📊 API Endpoints

The ESP32 provides REST API endpoints that the smartphone interface uses:

| Endpoint | Method | Description | Smartphone Usage |
|----------|--------|-------------|------------------|
| `/api/data` | GET | Get current sensor readings | Real-time dashboard updates |
| `/api/relay/on` | GET | Turn relay ON | Manual control buttons |
| `/api/relay/off` | GET | Turn relay OFF | Manual control buttons |
| `/api/thresholds` | GET/POST | Get/set control thresholds | Settings management |
| `/api/stored` | GET | Get historical data | Data center display |
| `/api/export` | GET | Export data as CSV | Download functionality |

### Smartphone API Integration

The smartphone interface handles API communication with automatic fallback:
\`\`\`javascript
// Automatic fallback to mock data if ESP32 unavailable
const safeFetch = async (path, options) => {
  if (!API_URL) return mockData();
  try {
    return await fetch(`${API_URL}${path}`, options);
  } catch (err) {
    return mockData(); // Graceful degradation
  }
}
\`\`\`

## 🔧 Configuration

### Threshold Settings
- **Temperature Threshold**: Set maximum temperature (°C) before cooling activates
- **Humidity Threshold**: Set maximum humidity (%) before cooling activates
- **Auto Control**: Enable automatic relay control based on thresholds

### Default Values
\`\`\`cpp
Temperature Threshold: 30.0°C
Humidity Threshold: 70.0%
Auto Control: Disabled
Data Storage: 200 readings maximum
Polling Interval: 5 seconds
\`\`\`

## 🚀 Advanced Features

### Data Persistence
- Settings automatically saved to EEPROM
- Survives power cycles and resets
- 200 readings stored in RAM (lost on restart)

### Automatic Control Logic
\`\`\`cpp
if (temperature >= tempThreshold || humidity >= humThreshold) {
    // Activate cooling
    relay = ON;
} else {
    // Deactivate cooling
    relay = OFF;
}
\`\`\`

### WiFi Watchdog
- Automatic restart if WiFi disconnected for >30 seconds
- Ensures continuous operation
- Prevents hanging in disconnected state

## 📈 Performance Specifications

- **Sensor Accuracy**: ±0.5°C (DS18B20), ±2% RH (DHT22)
- **Update Rate**: 5-second intervals
- **Data Storage**: 200 readings (≈17 minutes at 5s intervals)
- **Response Time**: <1 second for manual controls
- **Power Consumption**: ~200mA (ESP32 + sensors)
- **Operating Range**: -10°C to +85°C, 0-100% RH

## 🛡️ Safety Considerations

- **Electrical Safety**: Ensure proper insulation of high-voltage connections
- **Heat Management**: Monitor Peltier temperature to prevent overheating
- **Power Supply**: Use appropriate current rating for Peltier element
- **Moisture Protection**: Protect electronics from condensation

## 📝 License

This project is open-source and available under the MIT License.

## 🤝 Contributing

Contributions are welcome! Please feel free to submit issues, feature requests, or pull requests.

## 📞 Support

For technical support or questions:
- Check the troubleshooting section
- Review serial monitor output for debug information
- Verify hardware connections and power supply

## 🔍 Troubleshooting

### Common Issues

1. **WiFi Connection Failed**:
   - Check SSID and password
   - Ensure 2.4GHz network (ESP32 doesn't support 5GHz)
   - Check signal strength

2. **Sensor Reading Errors**:
   - Verify wiring connections
   - Ensure proper power supply (3.3V for sensors)

3. **Web Interface Not Loading**:
   - Verify ESP32 IP address
   - Check if ESP32 is connected to same network
   - Ensure firewall isn't blocking connections

4. **Relay Not Working**:
   - Check relay module power (5V)
   - Verify GPIO 2 connection
   - Test relay with multimeter
   - Ensure adequate power supply for Peltier

### Smartphone Interface Issues

1. **Interface Not Loading**:
   - Check if web app is properly deployed
   - Verify smartphone is on same network as ESP32
   - Try accessing with different browser

2. **No Real Data (Mock Mode)**:
   - Verify `API_URL` is set to ESP32 IP address
   - Check ESP32 is powered on and connected to WiFi
   - Ensure ESP32 and smartphone are on same network

3. **Auto Control Not Working**:
   - Check threshold values are properly set
   - Verify ESP32 is receiving threshold updates
   - Monitor serial output for auto control logic

4. **Charts Not Updating**:
   - Refresh the page to reinitialize Chart.js
   - Check browser console for JavaScript errors
   - Verify sensor data is being received
