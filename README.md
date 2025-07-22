Cypher-Lynx: ESP32-C3 Multi-Protocol Reverse Engineering Tool
Project Overview
Cypher-Lynx is an open-source project that provides a powerful, self-contained, and web-enabled reverse engineering tool built on the ESP32-C3 microcontroller. It's specifically designed to help analyze and understand communication protocols of embedded devices, with a focus on inverters that utilize Modbus RTU (over RS485) and Bluetooth Low Energy (BLE) for control and data exchange.
Unlike generic sniffers or basic serial bridges, Cypher-Lynx offers an integrated web interface for live logging, intelligent decoding, filtering, highlighting, and dynamic aliasing of unknown registers, making the process of reverse engineering proprietary protocols significantly more intuitive and accessible.
Features
Dual Protocol Monitoring:
RS485 Modbus RTU: Passive sniffing of Modbus RTU traffic, live raw hex display, and decoded register values.
Bluetooth Low Energy (BLE): Active scanning for advertised devices, connection establishment, GATT service/characteristic discovery, and monitoring of notification data.
Web-Based User Interface:
Remote Access: Control and monitor Cypher-Lynx from any device with a web browser (PC, tablet, smartphone) over Wi-Fi.
Mode Switching: Easily toggle between RS485 Monitor, BLE Monitor, and Serial Bridge modes.
Comprehensive Logging: Dedicated web pages for:
RS485 Log: Displays raw hex packets and decoded Modbus frames.
BLE Log: Shows BLE scan results, connection events, GATT discovery, and received notification data.
Interactive Reverse Engineering Aids (for RS485 Log):
Filtering: Filter log entries by specific text or hex patterns.
Highlighting: Visually highlight specific hex patterns in raw logs.
Register Aliasing/Renaming: Define custom, human-readable names for Modbus registers as you discover their meaning, which are then applied to the decoded log.
Web-Based Command Sending: Send custom Modbus RTU commands directly from the web interface (in RS485 Monitor mode).
Configurable RS485 Settings: Adjust baud rate and parity via the web UI.
Authentication: Basic HTTP authentication for secure access.
Local Display Support: Integrates with OLED (SSD1306) and TFT (TFT_eSPI) displays for on-device status and live previews.
Serial Bridge Mode: Transforms the ESP32 into a transparent USB-to-RS485 bridge, allowing direct interaction from a PC serial terminal.
Cost-Effective: Built on readily available and inexpensive ESP32-C3 hardware.
Open Source: Fully customizable and extensible Arduino firmware.
Why Cypher-Lynx? (Unique Value Proposition)
Many open-source tools exist for individual protocol sniffing or basic serial communication. However, Cypher-Lynx aims to fill a gap by providing:
Integrated Workflow: Combines multiple RE functionalities into one device with a unified web interface, reducing the need for multiple tools and complex setups.
User-Friendly RE: The interactive web-based logging with dynamic filtering, highlighting, and especially user-defined register aliasing, significantly simplifies the often tedious process of identifying and understanding unknown data points in proprietary protocols.
Accessibility: Low hardware cost and Arduino framework make it accessible to hobbyists, students, and professionals alike.
Portability: A self-contained unit that can be deployed directly at the device location.
Hardware Requirements
ESP32-C3 Development Board: (e.g., ESP32-C3-DevKitM-1).
USB-C Cable: For flashing.
Jumper Wires: For connections.
5V Power Supply: (e.g., USB charger/power bank) for standalone operation.
Optional: OLED Display (SSD1306 compatible), TFT Display (SPI compatible).
CRITICAL WARNING: RS485 Voltage Compatibility
Your inverter's RS485 communication lines (A and B) may operate at 5V logic levels. ESP32-C3 GPIO pins are typically 3.3V tolerant. Connecting 5V signals directly to 3.3V tolerant pins can damage your ESP32.
Before connecting your ESP32 to the inverter's RS485 A/B lines, you MUST:
Identify the voltage levels on the inverter's RS485 A and B lines using a multimeter or oscilloscope.
If the voltage levels exceed 3.3V (e.g., they are 5V), you MUST use a logic level shifter (e.g., a simple bidirectional 3.3V/5V level shifter module) between the inverter's RS485 lines and the ESP32's GPIO pins. Failure to do so may permanently damage your ESP32.
Pinout and Wiring
This section describes the connections from your ESP32-C3 to the inverter's RS485 bus and optional displays.
ESP32-C3 to Inverter RS485 Bus:
The ESP32-C3's internal UART1 is used for RS485 communication. You will be connecting directly to the inverter's RS485 A and B lines.
ESP32 GPIO20 (UART1 RX) to Inverter RS485 A line (via logic level shifter if 5V)
ESP32 GPIO21 (UART1 TX) to Inverter RS485 B line (via logic level shifter if 5V)
ESP32 GND to Inverter RS485 GND (common ground is essential)
Connection Type: These connections should be made in parallel to the existing RS485 communication lines between your inverter and its WiFi module. You are simply listening in on the conversation.
Optional: OLED Display (SSD1306 I2C) to ESP32-C3:
OLED VCC to ESP32 3.3V
OLED GND to ESP32 GND
OLED SCL to ESP32 GPIO6
OLED SDA to ESP32 GPIO5
Optional: TFT Display (SPI) to ESP32-C3:
Specific pins may vary slightly depending on your TFT display module and TFT_eSPI configuration. Common SPI pins for ESP32-C3 are:
TFT VCC to ESP32 3.3V
TFT GND to ESP32 GND
TFT MOSI (Data In) to ESP32 GPIO7
TFT SCLK (Clock) to ESP32 GPIO8
TFT CS (Chip Select) to ESP32 GPIO9
TFT DC (Data/Command) to ESP32 GPIO10
TFT RST (Reset) to ESP32 GPIO11
TFT BL (Backlight) to ESP32 3.3V (or PWM pin if dimming is desired)
Software Requirements & Installation
Arduino IDE: Download and install the latest version from arduino.cc.
ESP32 Board Support Package:
In Arduino IDE, go to File > Preferences.
In "Additional Boards Manager URLs", add: https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json
Go to Tools > Board > Boards Manager.... Search for "esp32" and install the "esp32 by Espressif Systems" package.
Required Arduino Libraries: Install these via the Arduino IDE's Library Manager (Sketch > Include Library > Manage Libraries...):
U8g2 by oliver
AsyncTCP by me-no-dev
ESPAsyncWebServer by me-no-dev
BLE (comes with ESP32 board package)
PacketSerial by Rob F.
ArduinoJson by Benoit Blanchon
arduinoFFT by Enrique Garcia
mbedtls (comes with ESP32 board package)
TFT_eSPI by Bodmer (if using TFT display)
Flashing the Firmware
Download the Sketch: Get the latest .ino file from the project's GitHub repository.
Open in Arduino IDE: Open the downloaded sketch in the Arduino IDE.
Customize (Optional but Recommended):
WiFi Credentials: Modify const char* ssid = "RE_Tool_AP"; and const char* password = "reverse_engineer"; to something unique and secure.
Web Server Credentials: Change const char* http_username = "admin"; and const char* http_password = "securepass"; for security.
Inverter BLE Name: In MyAdvertisedDeviceCallbacks::onResult, update advertisedDevice.getName() == "Ecoworthy Inverter" to the actual name your inverter advertises (you'll discover this during initial BLE scanning).
BLE UUIDs: Once discovered, update serviceUUID, charWriteUUID, and charNotifyUUID with your inverter's specific BLE UUIDs.
Select Board & Port:
Tools > Board > ESP32 Arduino > ESP32C3 Dev Module
Tools > Port > (Your ESP32's COM Port)
Upload: Click the "Upload" button (right arrow icon) in the Arduino IDE.
Usage Guide
1. Initial Access
Power On Cypher-Lynx: Connect your ESP32 to a 5V power source.
Connect to Wi-Fi: On your computer or phone, connect to the Wi-Fi access point created by Cypher-Lynx (default: RE_Tool_AP).
Open Web Interface: Navigate to http://192.168.1.1/ (or your configured static IP) in your web browser.
Login: Use the configured username and password.
2. Operating Modes
Cypher-Lynx defaults to MODE_BLE. You can switch modes from the main web page.
BLE Monitor Mode (Default):
Purpose: Actively scans for and connects to BLE devices (your inverter). Logs all advertising data, GATT service/characteristic discovery, and received notification data.
Usage:
Ensure MODE_BLE is active on the main page.
Go to the /blelog page (link on main page).
Open your inverter's official phone app and connect it to the inverter.
Observe the BLE Log: Watch for device advertisements, connection events, discovered UUIDs (Services, Characteristics), and especially BLE Notify messages.
Interact with the Phone App: Change settings, check status, turn outputs on/off. Correlate these actions with the hex data appearing in the BLE Notify messages to identify commands and data structures.
RS485 Monitor Mode:
Purpose: Passively listens to all traffic on the RS485 bus between your inverter and its WiFi module. Decodes Modbus RTU frames.
Usage:
Ensure MODE_RS485 is active on the main page.
Go to the /rs485log page.
Observe Raw & Decoded Data: Look for Modbus requests (e.g., 01 03 ...) and their corresponding responses.
Correlate with Inverter Behavior: Change settings or observe values on your inverter's physical display. Identify which decoded register values (Reg X: VALUE) change in the log.
Use Register Aliases: On the /rs485log page, use the "Register Aliases" section to give meaningful names to discovered Modbus register addresses.
Send Commands: From the main page, use the "Send Modbus Command" section to send custom hex payloads (e.g., 01 03 00 00 00 01 - without CRC, the tool adds it) to test your understanding.
Serial Bridge Mode:
Purpose: Turns Cypher-Lynx into a transparent bridge, forwarding data directly between its USB serial port (connected to your computer) and the RS485 bus.
Usage:
Ensure MODE_BRIDGE is active on the main page.
Open a serial terminal program on your computer (e.g., Arduino IDE Serial Monitor, PuTTY) and connect to the ESP32's COM port at 115200 baud.
Type raw bytes/commands into the terminal to send them over RS485, and see raw RS485 responses printed back.
Note: In this mode, web-based live data streams for RS485 are paused.
Troubleshooting
No data in logs / Gibberish:
Wiring: Double-check all physical connections, especially RS485 A/B polarity and common ground.
Voltage Compatibility: Re-verify RS485 voltage levels. If >3.3V, a level shifter is required.
RS485 Parameters: In MODE_RS485, try cycling through common Baud Rates (9600, 19200, 38400, 115200) and Parity settings (8N1, 8E1, 8O1) via the web UI.
Active Communication: Ensure the inverter's original communication module (WiFi app or separate module) is active and communicating.
BLE Connection Issues:
Inverter Name/UUIDs: Ensure the advertisedDevice.getName() check and serviceUUID/charWriteUUID/charNotifyUUID in the sketch match your inverter's actual BLE parameters (found by initial broad scanning).
Proximity: Ensure Cypher-Lynx is close enough to the inverter for a stable BLE connection.
Web UI not accessible:
WiFi Connection: Confirm your device is connected to Cypher-Lynx's Wi-Fi AP.
IP Address: Verify you're using the correct IP (http://192.168.1.1/ or your static IP).
Authentication: Check username/password.
ESP32 not flashing:
Ensure correct board and COM port selected in Arduino IDE.
Install all required ESP32 board support packages and libraries.
Contributing
Cypher-Lynx is open source and thrives on community contributions!
Share Your Discoveries: As you decode protocols for your inverter, consider sharing the specific UUIDs, Modbus register maps, and data interpretations. This is invaluable for others.
Report Issues: Found a bug? Please open an issue on GitHub.
Suggest Enhancements: Have an idea for a new feature or improvement? We'd love to hear it.
Code Contributions: Fork the repository, implement fixes or features, and submit pull requests.
