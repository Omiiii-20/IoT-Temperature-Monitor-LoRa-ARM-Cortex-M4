# Temperature Monitoring System using LoRa Module

A wireless temperature and humidity monitoring system using ARM Cortex-M4 LPC4088, DHT11 sensor, and LoRa module for long-range data transmission.

##  Project Overview

This project implements a wireless sensor network where temperature and humidity data from a DHT11 sensor is transmitted via LoRa (Long Range) communication to a remote receiver connected to a laptop. The system demonstrates IoT capabilities with low-power, long-range wireless communication.

##  Hardware Requirements

### Transmitter Side (Field Unit):
- **ARM Cortex-M4 LPC4088 Development Board**
- **LoRa Module** (SX1278/SX1276 based)
- **DHT11 Temperature & Humidity Sensor**
- **Power Supply** (5V/3.3V)
- **Connecting Wires**

### Receiver Side (Base Station):
- **LoRa Module** (matching frequency with transmitter)
- **FT232 USB to Serial Module**
- **Laptop/PC** with HyperTerminal
- **USB Cable**

## Software Requirements

- **Keil µVision IDE** (v5 or higher) - For compiling ARM code
- **Flash Magic** - For programming LPC4088 via UART
- **HyperTerminal** or **PuTTY** - For serial data visualization
- **LoRa Module Drivers** (if applicable)

## Features

✅ Real-time temperature and humidity monitoring  
✅ Long-range wireless communication (up to 2-5 km line of sight)  
✅ Low power consumption with LoRa technology  
✅ UART serial output for debugging  
✅ Automatic sensor calibration  
✅ Configurable data transmission intervals  
✅ ASCII data formatting for easy readability  

##  Pin Connections

### DHT11 Sensor → LPC4088
```
DHT11 Data Pin  → P4.0 (GPIO)
DHT11 VCC       → 5V
DHT11 GND       → GND
```

### LoRa Module → LPC4088
```
LoRa TX   → P0.3 (UART0 RX)
LoRa RX   → P0.2 (UART0 TX)
LoRa VCC  → 3.3V
LoRa GND  → GND
```

### FT232 Module → Laptop (Receiver)
```
FT232 TX  → LoRa RX
FT232 RX  → LoRa TX
FT232 VCC → 3.3V
FT232 GND → GND
USB       → Laptop USB Port
```

##  Setup Instructions

### 1. Hardware Setup

1. **Connect DHT11 to LPC4088** as per pin diagram
2. **Connect LoRa module to LPC4088** UART0 pins
3. **Power the transmitter unit** with 5V supply
4. **Connect second LoRa module to FT232**
5. **Connect FT232 to laptop** via USB

### 2. Software Setup

#### Compile and Flash Code:
```bash
1. Open DHT11.uvproj in Keil µVision
2. Build Project (F7)
3. Check for errors in Build Output
4. Locate DHT11.hex in Objects/ folder
5. Open Flash Magic
6. Select Device: LPC4088
7. Select COM Port (check Device Manager)
8. Browse and select DHT11.hex
9. Click "Start" to program
10. Reset the board
```

#### Configure HyperTerminal:
```
Baud Rate: 115200
Data Bits: 8
Parity: None
Stop Bits: 1
Flow Control: None
```

### 3. Testing

1. Open HyperTerminal and select FT232 COM port
2. Configure serial settings (115200, 8N1)
3. Power on the transmitter (LPC4088 + LoRa + DHT11)
4. You should see data like:
   ```
   HUMIDITY & TEMPERATURE  --  0065    0028
   ```

##  Data Format

The system sends data in the following format:
```
HUMIDITY & TEMPERATURE  --  [HUMIDITY]    [TEMPERATURE]
```

Example:
```
HUMIDITY & TEMPERATURE  --  0065    0028
```
- Humidity: 65%
- Temperature: 28°C

##  How It Works

### Transmitter Flow:
1. **Initialize** GPIO, UART, Timers
2. **Configure DHT11** sensor on P4.0
3. **Read sensor data** (humidity and temperature)
4. **Apply calibration** (humidity/1.60, temperature/2.05)
5. **Convert to ASCII** format
6. **Transmit via UART** to LoRa module
7. **LoRa broadcasts** data wirelessly
8. **Wait 11 seconds**, repeat

### Receiver Flow:
1. **LoRa receives** wireless data
2. **Forwards to FT232** via UART
3. **FT232 sends to PC** via USB
4. **HyperTerminal displays** data

##  Troubleshooting

| Problem | Solution |
|---------|----------|
| No data received | Check LoRa module frequency match, verify UART connections |
| Incorrect readings | Verify DHT11 pull-up resistor (10kΩ), check calibration factors |
| Flash Magic error | Ensure LPC4088 in ISP mode (BOOT0 jumper), correct COM port |
| Gibberish in terminal | Check baud rate (must be 115200), verify FT232 drivers |

##  LoRa Configuration

**Frequency:** 433MHz / 868MHz / 915MHz (region dependent)  
**Bandwidth:** 125kHz  
**Spreading Factor:** 7-12 (configurable)  
**Coding Rate:** 4/5  
**Range:** 2-5 km (line of sight)  

##  Learning Outcomes

- ARM Cortex-M4 programming
- LoRa wireless communication
- DHT11 sensor protocol implementation
- UART serial communication
- Timer-based delays
- GPIO configuration
- Real-time embedded systems

##  Code Structure

```
DHT11.c
├── Init_GPIO()          - Initialize GPIO peripheral
├── Init_Timer_1()       - Setup microsecond timer
├── Init_Timer_2()       - Setup millisecond timer
├── Init_Uart0()         - Configure UART for LoRa
├── Config_DHT11()       - Setup DHT11 sensor pin
├── Init_DHT11()         - Read DHT11 data
├── receive_bytes()      - DHT11 protocol decoder
├── Delay_ms()           - Millisecond delay
├── Delay_us()           - Microsecond delay
├── integer_to_ascii()   - Number to string converter
└── main()               - Main program loop
```

##  Security Note

LoRa communication in this basic implementation is **not encrypted**. For production systems, implement:
- AES encryption
- Authentication tokens
- Secure key exchange

##  Future Enhancements

- [ ] Add multiple sensor nodes
- [ ] Implement bi-directional communication
- [ ] Add LCD display on transmitter
- [ ] Battery power with sleep modes
- [ ] Data logging to SD card
- [ ] Web dashboard for visualization
- [ ] GPS coordinates for location tracking

##  References

- [LPC4088 User Manual](https://www.nxp.com/docs/en/user-guide/UM10562.pdf)
- [DHT11 Datasheet](https://www.mouser.com/datasheet/2/758/DHT11-Technical-Data-Sheet-Translated-Version-1143054.pdf)
- [LoRa SX1278 Datasheet](https://www.semtech.com/products/wireless-rf/lora-core/sx1278)
- [Keil µVision Documentation](https://www.keil.com/support/man/docs/uv4/)

##  Author

**Om Bidikar**
- GitHub: [@Omiiii-20](https://github.com/Omiiii-20)
- LinkedIn: [Om Bidikar](https://linkedin.com/in/ombidikar)
- Email: ombidikar72@gmail.com

##  License

This project is open source and available under the MIT License.

##  Contributing

Contributions, issues, and feature requests are welcome!

---

**⭐ If you find this project useful, please consider giving it a star!**
