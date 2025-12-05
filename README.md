# 📘 AUTO DOOR CONTROLLER PROJECT  
### *By Khoi Tran, Cody Tran, and Benjamin Vanhuang*

---

## 🚪 Overview

The **Auto Door Controller Project** is a multi-device network automation system designed to remotely trigger mechanical actions — such as closing a door — using:

- An **ESP32 Controller** with a physical button  
- An **Arduino R4 WiFi Device** that controls a servo  
- A **PC Command Line Interface (CLI)** for manual control, scheduling, logging, and system management

All devices communicate over **UDP broadcast** on the same local network, using a small custom text protocol.

The PC functions as the “brain” of the system, providing:

- Manual command execution  
- Scheduled and recurring commands  
- Persistent schedule storage  
- Command logging  
- ACK processing  
- Real-time summaries of all command traffic  

This system is fully extensible, allowing additional devices or actions to be added in the future.

---

## 📝 Features

### ✔ PC CLI
- Manual command execution  
- Schedule “one-shot” commands  
- Schedule recurring commands  
- Persistent schedule storage (`schedules.enc`)  
- Clean console summaries of CMDs and ACKs  
- Log files stored per session  
- Startup and shutdown banners  
- Graceful shutdown with schedule saving  

### ✔ ESP32 Controller
- Button-triggered `CLOSE_DOOR` commands  
- Debouncing logic  
- UDP broadcasting  
- WiFi reconnect handling  
- ACK parsing for debugging  

### ✔ Arduino R4 Device ("dev1")
- Listens for network CMD packets  
- Parses device target field  
- Executes actions (servo movement)  
- Sends ACK packets reporting:
  - origin  
  - cmd ID  
  - scheduled / recurring flags  
  - next scheduled time  
  - status: `OK`, `UNKNOWN_ACTION`, etc.  

### ✔ UDP Text Protocol
Packets follow a simple semicolon-separated key-value format:

#### CMD Packet
  ```CMD;origin=PC;id=14;target=dev1;action=CLOSE_DOOR;scheduled=1;recurring=0;next=2025-12-04T21:30:00```

#### ACK Packet
  ```ACK;origin=dev1;id=14;device=dev1;status=OK;scheduled=1;recurring=0;next=2025-12-04T21:30:00```

---

## 🔧 Installation

### Requirements
- Python 3.10+  
- Arduino IDE / PlatformIO  
- ESP32 Dev Board  
- Arduino UNO R4 WiFi  
- Servo motor  
- Local WiFi network  

### PC Setup Steps
``` git clone https://github.com/<your-repo-here>.git
cd auto-door-controller
pip install -r requirements.txt
python pc_cli.py
```

---

## ▶️ Using the PC CLI

### Startup Banner
```──────────────────────────────────────────────────────────────
🚪 Auto Door Controller – v1.0
An automation project created by:
  • Khoi Tran
  • Cody Tran
  • Benjamin Vanhuang

For documentation & project updates:
  https://github.com/<your-repo>

Type 'help' to see available commands.
──────────────────────────────────────────────────────────────
```
### 💻 Commands
| Command | Description |
|---------|-------------|
| `send <target> <action>` | Manually send a CMD |
| `schedule-once <target> <action> <seconds>` | Run action after N seconds |
| `schedule-every <target> <action> <seconds>` | Run action repeatedly |
| `list` | List all scheduled commands |
| `cancel <id>` | Cancel a scheduled command |
| `help` | Display help |
| `quit` | Exit, saving schedules |


### 📁 File Storage
**Logs**

Stored in: ```logs/session-YYYYMMDD-HHMMSS.log```

Contains full raw CMD/ACK traffic + system events.

**Persistent Schedule Storage**

Stored in: ```schedules.json```

Features:
- Automatically loaded on startup
- Recurring schedules roll forward if past due
- One-shot past schedules are dropped

---

## 🛰 Protocol Details

### CMD Fields

| Field     | Meaning                                      |
|-----------|-----------------------------------------------|
| origin    | Who sent the command (`PC`, `controller`, etc.) |
| id        | Unique command counter per origin             |
| target    | Device (`dev1`, `ALL`)                        |
| action    | Action to perform                             |
| scheduled | `1` if scheduled, `0` otherwise               |
| recurring | `1` if recurring                              |
| next      | Next recurrence time or `NONE`                |

### ACK Fields

| Field     | Meaning                                      |
|-----------|-----------------------------------------------|
| origin    | Device sending ACK                            |
| id        | Command ID being acknowledged                 |
| device    | Name of device                                |
| status    | OK / ERROR / UNKNOWN_ACTION                   |
| scheduled | Echo from CMD                                 |
| recurring | Echo from CMD                                 |
| next      | Echo from CMD                                 |

---

## 🔍 Example Traffic
### PC sends a command:
  `[SYS] SENT CMD: PC#14 -> target=dev1, action=CLOSE_DOOR, scheduled, one-shot, next=2025-12-04T21:30:00`

### dev1 ACKs:
  `[SYS] ACK: device=dev1, for PC#14, status=OK, scheduled, one-shot, next=2025-12-04T21:30:00`

### ESP32 button press:
  `[SYS] CMD heard: from controller#7 -> target=dev1, action=CLOSE_DOOR, manual, one-shot, next=NONE`


---

## 🧭 Future Features

- Web UI dashboard  
- Device discovery  
- Encryption of UDP traffic  
- Command retry system  
- Multi-device support  
- OTA firmware updates  
- Cloud integration  

---

## 🛠 Development Philosophy

The project prioritizes:

- Simplicity  
- Human-readable packets  
- No reliance on external servers  
- Fast iteration  
- Robustness to PC restarts  
- Flexible, device-level autonomy  

---

## 👤 Authors

- **Khoi Tran**  
- **Cody Tran**  
- **Benjamin Vanhuang**
