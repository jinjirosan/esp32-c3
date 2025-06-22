# ESP32-C3 Access Point Monitor with OLED + OTA

This project turns your ESP32-C3 into a self-hosted Wi-Fi accessible IoT board with:

- A **local web server** protected by basic authentication
- A **0.42" OLED display (72x40 visible pixels)** showing system status
- **OTA firmware updates** via web interface
- **Status stripes** on-screen for extendable feedback
- A **presence-detection LED** (turns off when a client connects)

---

## Features

| Feature             | Description                                                  |
|---------------------|--------------------------------------------------------------|
| 📡 Wi-Fi Access Point | The ESP32-C3 runs in AP mode with a custom SSID             |
| 🔐 Web Interface     | Basic HTTP Auth (username/password) for `/`, `/config`, `/update` |
| 🖥️ OLED Display      | Shows device name, software version, uptime, IP or "offline", and 5 status stripes |
| 💡 LED Feedback      | LED is ON when no client is connected, OFF when clients are present |
| 🔁 OTA Updates       | Drag-and-drop firmware update via `/update`                  |
| ⚙️ Config Stub       | `/config` route in place for future extensions               |

---

## Hardware Requirements

- **ESP32-C3** microcontroller
- **0.42" SSD1306-compatible OLED** display (I2C, 72x40 visible resolution)
- **LED** connected to pin `GPIO8` (can be changed)
- Optional: reset button for OTA recovery or factory reset (not yet implemented)

---

## Wiring

| OLED Pin | ESP32-C3 Pin |
|----------|--------------|
| SDA      | GPIO5        |
| SCL      | GPIO6        |
| GND      | GND          |
| VCC      | 3.3V         |

---

## Web Access

After powering on:
1. Connect to the ESP32-C3 Wi-Fi AP (SSID: `ESP32-C3-[DeviceName]`, default: `Ray`)
2. Navigate to [http://192.168.4.1/](http://192.168.4.1/)
3. Login with:
   - **Username:** `admin`
   - **Password:** `12345678`

### Available Routes

- `/` – Main device info and status page
- `/config` – Placeholder config page
- `/update` – OTA firmware upload

---

## Display Layout

Line 1: Ray v1.4.3

Line 2: Uptime: 3m

Line 3: █ ██ ░░ █ ← status stripes (filled or outlined boxes)

Line 4: 192.168.4.1 OR "offline"



---

## Customization

Modify in `base_ESP32-C3.ino`:

| Variable           | Purpose                             |
|--------------------|-------------------------------------|
| `deviceName`       | Sets device name and AP SSID        |
| `softwareVersion`  | Displayed on OLED and web UI        |
| `statusStripes[]`  | Controls on-screen status boxes     |
| `http_username`    | Web login username                  |
| `http_password`    | Web login password                  |

---

## License

MIT License — free to use, modify, and share. Contributions welcome.

---

## Roadmap Ideas

- ✅ OLED display + status stripes
- ✅ OTA updates via `/update`
- 🔒 Optional HTTPS support (future)
- 🛠️ Config persistence (EEPROM or Preferences)
- 🌐 mDNS / hostname access
- 🧠 Smart watchdog reboot logic

---

## Screenshots

_OLED preview coming soon_

---

## Credits

Created by JinjiroSan  
Built using: [ESP32 Arduino Core](https://github.com/espressif/arduino-esp32), [U8g2 OLED library](https://github.com/olikraus/u8g2)

