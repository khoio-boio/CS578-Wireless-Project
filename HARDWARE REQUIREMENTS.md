## 🛠 Hardware Requirements & Setup

This project uses a multi-device hardware architecture consisting of:

- An ESP32 development board (Controller)
- An Arduino UNO R4 WiFi (Device `dev1`)
- A servo motor (door actuator)
- An IR receiver module (optional local input)
- A momentary push button (for ESP32 manual trigger)
- A shared WiFi network

Below are the full requirements and setup instructions for each device.

---

## Hardware Components

### Required
- ESP32 Dev Board (any standard ESP32-WROOM board)
- Arduino UNO R4 WiFi
- SG90 or MG90S servo motor
- Standard IR receiver (VS1838B or compatible)
- Momentary push button
- Jumper wires (female–male and male–male)
- 5V power via USB for both boards
- 2.4 GHz WiFi network

### Optional
- External 5V power supply for servo (recommended for heavy loads)
- Breadboard
- Rubber bands / mounts for door mechanism
- Inline resistor for button (if not using internal pull-ups)

---

## ESP32 Setup (Controller)

### Purpose
The ESP32 acts as a simple physical trigger device. Pressing the button sends a UDP broadcast command to the network.

### Wiring

| Component | ESP32 Pin |
|----------|-----------|
| Button (one side) | GPIO 2 (D2) |
| Button (other side) | GND |
| USB power | USB port |

**Note:**  
The pin is configured using `INPUT_PULLUP`, so no external resistor is required.

### Steps

1. Connect ESP32 to your PC via USB.
2. Install ESP32 board support in Arduino IDE.
3. Open the `controller` sketch.
4. Update your WiFi SSID/password.
5. Upload to ESP32.
6. Open Serial Monitor (115200 baud) to confirm WiFi connection.

---

## Arduino UNO R4 WiFi Setup (Device `dev1`)

### Purpose
This device receives commands and physically actuates the door via a servo.  
It also supports IR remote inputs.

### Wiring

| Component | Arduino R4 Pin |
|-----------|----------------|
| Servo Signal | D9 |
| Servo VCC | 5V |
| Servo GND | GND |
| IR Receiver Signal | D2 |
| IR Receiver VCC | 5V |
| IR Receiver GND | GND |
| USB Power | USB-C |

**Servo note:**  
The R4 can power a small servo (SG90) from USB.  
For larger servos, use an external 5V supply with shared ground.

### Steps

1. Connect the R4 via USB-C.
2. Install the `WiFiS3`, `IRremote`, and `Servo` libraries if needed.
3. Open the `dev1` sketch.
4. Update your WiFi SSID/password to match the ESP32.
5. Upload the sketch.
6. Open Serial Monitor (9600 baud) to confirm connection.

---

## WiFi Network Setup

All devices **must be on the same 2.4GHz WiFi LAN**.  
This network facilitates:

- UDP broadcasts (CMD)
- UDP unicast/broadcast ACKs
- Peer-to-peer device communication

### Recommended Settings

| Setting | Value |
|---------|--------|
| Band | 2.4 GHz only |
| Isolation | Disabled |
| AP Mode | Normal |
| UDP Broadcast Allowed | Yes |

If devices fail to receive commands, check:

- Router AP isolation  
- Firewall rules blocking local broadcast packets  
- IP subnet (must share the same `192.168.x.x` network)

---

## Verification Checklist

Before running the PC CLI:

- [ ] ESP32 boots and prints WiFi connection + UDP started  
- [ ] Arduino R4 prints WiFi connection + "Listening on UDP port 4210"  
- [ ] Pressing ESP32 button shows CMD printed on PC CLI  
- [ ] Arduino executes CLOSE_DOOR and prints corresponding ACK  
- [ ] PC CLI receives ACK, parses metadata, and logs it  

Once all checks pass, the system is fully operational.

