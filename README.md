# Autonomous Bot with LoRa Communication

An autonomous 2-wheeled bot system with LoRa wireless communication, GPS navigation, and obstacle avoidance capabilities.

##  Project Overview

This project implements a distributed autonomous bot system consisting of:
- **Transmitter**: Remote control unit with button interface and LoRa communication
- **Receiver**: Autonomous 2-wheeled vehicle with GPS navigation, obstacle detection, and wireless communication

The system uses **LoRa (SX1278 433MHz)** for long-range wireless communication between the transmitter and receiver bot.

##  Features

### Transmitter Features
- Single button press: Send GPS coordinates (latitude/longitude) to receiver
- Double button press: Trigger buzzer on receiver bot
- LoRa 433MHz wireless transmission
- Compact remote control interface
- Real-time status feedback

### Receiver Features
- **Navigation**: Autonomous movement to transmitted GPS coordinates
- **Obstacle Avoidance**: Ultrasonic sensor with servo-mounted head for 360° detection
- **Motor Control**: Dual 2-wheel motor drive system
- **Communication**: LoRa 433MHz receiver module
- **Feedback**: "Reached The Location" confirmation message sent to transmitter
- **Audio Alert**: Buzzer activation triggered by transmitter double-press
- **Power Distribution**: Optimized power management for all components

## Hardware Components

### Transmitter Side
- Microcontroller (Arduino/ESP32/STM32)
- Push Button Switch
- LoRa SX1278 433MHz Transmitter Module
- Power supply (Battery/USB)

### Receiver Side
- Microcontroller (Arduino/ESP32/STM32)
- LoRa SX1278 433MHz Receiver Module
- **Motors**: Dual DC motors for 2-wheel drive
- **Motor Driver**: L298N or similar H-bridge motor driver
- **Ultrasonic Sensor**: HC-SR04 (or equivalent)
- **Servo Motor**: MG90S or SG90 for sensor head control
- **Buzzer**: Active buzzer (5V)
- **GPS Module** (optional): For actual location tracking
- **Power Distribution System**:
  - Battery pack (capacity to be determined)
  - Voltage regulators/Buck converters
  - Power management PCB

##  LoRa Communication Protocol

### Message Format

#### Transmitter → Receiver (Coordinate Data)
```
[HEADER: 0xAA][CMD: 0x01][LAT_HIGH][LAT_LOW][LON_HIGH][LON_LOW][CHECKSUM]
```
- **Header**: 0xAA (start marker)
- **Command**: 0x01 (coordinate transmission)
- **Latitude**: 32-bit float
- **Longitude**: 32-bit float
- **Checksum**: XOR verification

#### Transmitter → Receiver (Buzzer Command)
```
[HEADER: 0xAA][CMD: 0x02][DURATION][CHECKSUM]
```
- **Header**: 0xAA (start marker)
- **Command**: 0x02 (buzzer trigger)
- **Duration**: Buzzer duration in milliseconds
- **Checksum**: XOR verification

#### Receiver → Transmitter (Confirmation)
```
[HEADER: 0xAA][STATUS: 0x03][LOCATION_REACHED][CHECKSUM]
```
- **Header**: 0xAA (start marker)
- **Status**: 0x03 (status message)
- **Location_Reached**: 0x01 (reached) or 0x00 (not reached)
- **Checksum**: XOR verification

##  Getting Started

### Prerequisites
- Arduino IDE or PlatformIO
- Required Libraries:
  - LoRa SX1278 library (e.g., `RadioHead` or `arduino-LoRa`)
  - Servo library
  - Motor control libraries (if needed)
  - GPS library (if using GPS module)

### Installation

1. **Clone this repository**
   ```bash
   git clone https://github.com/skj198405-cmd/autonomous-bot.git
   cd autonomous-bot
   ```

2. **Install required libraries**
   - In Arduino IDE: Sketch → Include Library → Manage Libraries
   - Search and install:
     - `RadioHead` by Mike McCauley
     - `Servo` (built-in)
     - `TinyGPS++` (if using GPS)

3. **Upload code to microcontrollers**
   - Flash transmitter code to transmitter MCU
   - Flash receiver code to receiver MCU

### Pin Configuration

#### Transmitter Pins
```
LoRa SX1278:
- NSS (CS):    Pin 10
- MOSI:        Pin 11
- MISO:        Pin 12
- SCK:         Pin 13
- DIO0 (IRQ):  Pin 2

Button:
- Signal:      Pin 3
- GND:         GND
```

#### Receiver Pins
```
LoRa SX1278:
- NSS (CS):    Pin 10
- MOSI:        Pin 11
- MISO:        Pin 12
- SCK:         Pin 13
- DIO0 (IRQ):  Pin 2

Motor Driver (L298N):
- IN1:         Pin 5
- IN2:         Pin 6
- IN3:         Pin 9
- IN4:         Pin 8
- ENA:         Pin 3
- ENB:         Pin 11

Ultrasonic Sensor (HC-SR04):
- TRIG:        Pin 4
- ECHO:        Pin 5

Servo Motor:
- Signal:      Pin 7
- VCC:         5V (through power dist)
- GND:         GND

Buzzer:
- Signal:      Pin A0
- GND:         GND
```

##  Power Distribution

### Power Budget (To be calculated)
- LoRa SX1278: ~120mA (transmit), ~10mA (standby)
- DC Motors: ~500mA - 1A each (dependent on load)
- Servo Motor: ~100-200mA
- Ultrasonic Sensor: ~15mA
- Buzzer: ~50-100mA
- Microcontroller: ~50-100mA

**Estimated Total Draw**: 1.2A - 2.5A (peak)

### Recommended Power Supply
- **Battery**: 3S LiPo (11.1V) or 4S LiPo (14.8V) with 2000-5000mAh capacity
- **Voltage Regulators**:
  - 5V buck converter for logic circuits and sensors (2A+)
  - 5V buck converter for servo motor (500mA+)
  - Motor power directly from battery (through motor driver)

### Power Distribution Schematic (To be added)
```
[Battery Pack] 
    ├─→ Motor Driver → Motors
    ├─→ Buck Converter (5V, 2A) → Logic/Sensors
    ├─→ Buck Converter (5V, 500mA) → Servo
    └─→ Protection Circuit (fuse/BMS)
```

##  Repository Structure

```
autonomous-bot/
├── README.md
├── transmitter/
│   ├── transmitter.ino          # Main transmitter code
│   └── config.h                 # Configuration parameters
├── receiver/
│   ├── receiver.ino             # Main receiver code
│   ├── motor_control.ino        # Motor functions
│   ├── ultrasonic.ino           # Ultrasonic sensor functions
│   ├── lora_communication.ino   # LoRa protocol functions
│   └── config.h                 # Configuration parameters
├── docs/
│   ├── HARDWARE_SETUP.md        # Detailed hardware assembly
│   ├── LORA_PROTOCOL.md         # LoRa communication protocol
│   ├── POWER_DISTRIBUTION.md    # Power system design
│   └── TROUBLESHOOTING.md       # Common issues and solutions
├── schematics/
│   ├── transmitter_schematic.pdf
│   ├── receiver_schematic.pdf
│   └── power_distribution.pdf
└── .gitignore
```

##  Development Progress

### Completed 
- [x] Transmitter hardware and code
- [x] Receiver LoRa communication setup

### In Progress 
- [ ] Receiver motor control code
- [ ] Receiver ultrasonic obstacle avoidance
- [ ] Servo control for sensor head
- [ ] Coordinate to motor command conversion
- [ ] Power distribution design and implementation
- [ ] Integration testing

### To Do 
- [ ] GPS module integration (if applicable)
- [ ] Advanced obstacle avoidance algorithms
- [ ] Improved battery management
- [ ] PCB design and manufacturing
- [ ] Enclosure design
- [ ] Comprehensive testing and documentation

##  Troubleshooting

See [TROUBLESHOOTING.md](docs/TROUBLESHOOTING.md) for common issues and solutions.

Common issues:
- **LoRa not connecting**: Check SPI pins and power supply
- **Motors not responding**: Verify motor driver connections and power
- **Ultrasonic reading errors**: Check sensor power and connections
- **Power issues**: Verify buck converter outputs and battery capacity

## 📚 Resources & References

- [LoRa SX1278 Datasheet](https://www.semtech.com/uploads/documents/DS_SX1272.pdf)
- [RadioHead Library Documentation](http://www.airspayce.com/mikem/arduino/RadioHead/)
- [HC-SR04 Ultrasonic Sensor Guide](https://www.electronicshub.org/ultrasonic-sensor-arduino/)
- [L298N Motor Driver Tutorial](https://www.electronicstutorials.ws/blog/l298n-motor-driver.html)
- [Arduino Servo Library](https://www.arduino.cc/reference/en/libraries/servo/)

##  Contributing

Feel free to contribute by:
- Submitting issues for bugs or improvements
- Creating pull requests with enhancements
- Improving documentation
- Sharing optimization tips

##  License

This project is licensed under the MIT License - see the LICENSE file for details.

##  Author

**skj198405-cmd**

##  Support

If you encounter any issues or have questions:
1. Check the troubleshooting guide
2. Review existing GitHub issues
3. Create a new issue with detailed description

---

