# Setup Guide - Temperature Monitoring using LoRa

Complete step-by-step guide to set up the wireless temperature monitoring system.

---

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Hardware Assembly](#hardware-assembly)
3. [Software Installation](#software-installation)
4. [Code Compilation](#code-compilation)
5. [Programming the Board](#programming-the-board)
6. [Testing the System](#testing-the-system)
7. [Troubleshooting](#troubleshooting)

---

## Prerequisites

### Hardware Checklist
- [ ] ARM Cortex-M4 LPC4088 Development Board
- [ ] 2x LoRa Modules (SX1278/SX1276)
- [ ] DHT11 Temperature & Humidity Sensor
- [ ] FT232 USB to Serial Module
- [ ] 10kΩ Resistor (pull-up for DHT11)
- [ ] Breadboard and jumper wires
- [ ] 2x LoRa Antennas
- [ ] USB Cables (Mini USB for LPC4088, appropriate for FT232)
- [ ] 5V Power Supply

### Software Checklist
- [ ] Keil µVision IDE (v5.36 or higher)
- [ ] Flash Magic v12.95 or higher
- [ ] HyperTerminal or PuTTY
- [ ] FT232 USB Drivers (if not auto-installed)

---

## Hardware Assembly

### Step 1: DHT11 Sensor Connection

1. **Identify DHT11 pins** (looking at front with grid):
   ```
   Pin 1 (Left)   = VCC
   Pin 2          = Data
   Pin 3          = Not Connected
   Pin 4 (Right)  = GND
   ```

2. **Connect to LPC4088:**
   ```
   DHT11 Pin 1 (VCC)  → LPC4088 5V pin
   DHT11 Pin 2 (Data) → LPC4088 P4.0
   DHT11 Pin 4 (GND)  → LPC4088 GND
   ```

3. **Add pull-up resistor:**
   - Connect 10kΩ resistor between DHT11 Data (Pin 2) and VCC (Pin 1)

### Step 2: LoRa Module Connection (Transmitter)

1. **Check LoRa module voltage:** Most modules are 3.3V - DO NOT use 5V!

2. **Connect to LPC4088:**
   ```
   LoRa VCC  → LPC4088 3.3V
   LoRa GND  → LPC4088 GND
   LoRa TX   → LPC4088 P0.3 (UART0_RX)
   LoRa RX   → LPC4088 P0.2 (UART0_TX)
   ```

3. **Attach antenna** to LoRa module's ANT pin

### Step 3: Receiver Setup

1. **Connect second LoRa module to FT232:**
   ```
   LoRa VCC  → FT232 3.3V Output
   LoRa GND  → FT232 GND
   LoRa TX   → FT232 RX
   LoRa RX   → FT232 TX
   ```

2. **Attach antenna** to second LoRa module

3. **Connect FT232 to laptop** via USB cable

---

## Software Installation

### Step 1: Install Keil µVision

1. Download from: https://www.keil.com/demo/eval/arm.htm
2. Run installer, select "ARM" option
3. Install C compiler and LPC4000 device pack
4. Register for evaluation license (if needed)

### Step 2: Install Flash Magic

1. Download from: https://www.flashmagictool.com/
2. Install with default settings
3. No license required for basic use

### Step 3: Install HyperTerminal Alternative

**Windows 10/11 (no built-in HyperTerminal):**
1. Download PuTTY: https://www.putty.org/
2. Install with defaults
3. Or use Windows Terminal with serial support

**Older Windows:**
- HyperTerminal should be pre-installed

### Step 4: Install FT232 Drivers

1. Connect FT232 to PC via USB
2. Windows should auto-install drivers
3. If not, download from: https://ftdichip.com/drivers/
4. **Check Device Manager** → "Ports (COM & LPT)" to find COM port number

---

## Code Compilation

### Step 1: Open Project in Keil

1. Launch Keil µVision
2. **File → Open Project**
3. Navigate to project folder
4. Select `DHT11.uvproj`
5. Click Open

### Step 2: Verify Project Settings

1. Click **Target Options** icon (magic wand)
2. **Device tab:**
   - Ensure "NXP LPC4088" is selected
3. **Target tab:**
   - Crystal: 12.0 MHz
   - Use IROM1 and IRAM1
4. **C/C++ tab:**
   - Optimization: Level 1 (-O1)
5. **Output tab:**
   - ☑ Create HEX File
6. Click **OK**

### Step 3: Build Project

1. **Project → Build Target** (or press **F7**)
2. Wait for compilation
3. Check **Build Output** window:
   ```
   compiling DHT11.c...
   linking...
   creating hex file from ".\Objects\DHT11.axf"...
   ".\Objects\DHT11.axf" - 0 Error(s), 0 Warning(s).
   ```
4. If errors, see [Troubleshooting](#troubleshooting)

### Step 4: Locate HEX File

1. Navigate to project folder
2. Open `Objects/` folder
3. Find `DHT11.hex` - this is what you'll flash

---

## Programming the Board

### Step 1: Prepare LPC4088 for Programming

1. **Disconnect power** from LPC4088
2. **Set boot mode jumper:**
   - Locate ISP jumper (usually labeled BOOT0 or ISP)
   - Set to ISP/BOOT mode (check board manual)
3. **Connect USB cable** to LPC4088
4. **Check COM port:**
   - Open Device Manager
   - Expand "Ports (COM & LPT)"
   - Note the COM port (e.g., COM5)

### Step 2: Configure Flash Magic

1. **Launch Flash Magic**
2. **Device:** Select "LPC4088"
3. **COM Port:** Select the port from Device Manager
4. **Baud Rate:** 115200
5. **Interface:** None (ISP)
6. **Oscillator (MHz):** 12.000

### Step 3: Flash the Program

1. **Step 1 - Erase:** ☑ Erase all Flash
2. **Step 2 - Select HEX:** Browse to `Objects/DHT11.hex`
3. **Step 3 - Options:** Leave defaults
4. **Step 4 - Start:** Click **Start** button
5. Wait for progress:
   ```
   Erasing flash... Done
   Programming flash... Done
   Verifying... Done
   Operation Complete
   ```

### Step 4: Run the Program

1. **Disconnect USB** from LPC4088
2. **Remove ISP jumper** (set back to normal mode)
3. **Reconnect power** to LPC4088
4. **Board should start** automatically

---

## Testing the System

### Step 1: Set Up Receiver Terminal

1. **Open PuTTY** (or HyperTerminal)
2. **Session Type:** Serial
3. **Serial line:** Select FT232 COM port (e.g., COM6)
4. **Speed:** 115200
5. **Connection → Serial:**
   - Data bits: 8
   - Stop bits: 1
   - Parity: None
   - Flow control: None
6. Click **Open**

### Step 2: Power On Transmitter

1. Ensure DHT11 and LoRa are connected to LPC4088
2. Power on the LPC4088 board
3. Wait 5-10 seconds for initialization

### Step 3: Verify Data Reception

You should see output like:
```
HUMIDITY & TEMPERATURE  --  0065    0028
HUMIDITY & TEMPERATURE  --  0066    0028
HUMIDITY & TEMPERATURE  --  0065    0029
```

**Data Format:**
- First number = Humidity (65%)
- Second number = Temperature (28°C)
- Updates every 11 seconds

### Step 4: Physical Test

1. **Breathe on DHT11** sensor - humidity should increase
2. **Hold sensor** in hand - temperature should rise
3. **Move transmitter away** - test LoRa range

---

## Troubleshooting

### Compilation Errors

**Error: "startup file not found"**
```
Solution:
1. Right-click project → Add Existing Files
2. Navigate to: Keil_v5/ARM/Startup/NXP/LPC407x_8x/
3. Add startup_LPC407x_8x_177x_8x.s
```

**Error: "LPC407x_8x_177x_8x.h not found"**
```
Solution:
1. Project → Manage → Pack Installer
2. Search "LPC4088"
3. Install "NXP LPC4000 Series Device Support"
```

### Flash Magic Errors

**"Failed to autobaud"**
```
Solutions:
- Check COM port is correct
- Verify ISP jumper is set
- Try lower baud rate (57600)
- Reset board while clicking "Start"
```

**"Unable to connect"**
```
Solutions:
- Verify LPC4088 is powered
- Check USB cable is data-capable (not charge-only)
- Try different USB port
- Update USB drivers
```

### No Data in Terminal

**Terminal shows nothing:**
```
Checklist:
□ Is FT232 COM port correct?
□ Is baud rate 115200?
□ Are LoRa modules on same frequency?
□ Is transmitter LoRa powered (3.3V)?
□ Are TX/RX crossed correctly?
□ Are antennas connected?
```

**Garbage characters:**
```
Solution: Baud rate mismatch
- Ensure both sides are 115200
- Check UART initialization in code
```

### DHT11 Sensor Issues

**Always reads 0:**
