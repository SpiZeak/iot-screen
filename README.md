ESP32-C6 Home Assistant Smart Home Controller
Project Overview
This project aims to develop a sophisticated, locally controlled smart home interface using the ESP32-C6 Super Mini development board. It functions as an IoT gateway/controller for a Home Assistant instance (hosted separately), providing a direct touchscreen interface for controlling RGB lighting and receiving input from low-power PIR motion sensors. The entire system is built using the ESP-IDF framework, allowing for highly optimized performance and deep customization. Future-proofing is a core tenet, with native Matter connectivity planned for both the controller's functionalities and the low-power sensor network.
Features
 * ESP-IDF Core: Built entirely on the Espressif IoT Development Framework for maximum control, efficiency, and resource optimization.
 * Touchscreen Interface: Intuitive graphical user interface (GUI) on an ILI9341 TFT display with touch capabilities, powered by LVGL.
 * RGB LED Control: Direct control of WS2812B addressable LED strips for color, brightness, and effects, utilizing the ESP32-C6's RMT peripheral for precise timing.
 * Home Assistant Integration: Seamless communication with Home Assistant via MQTT for state synchronization and command execution.
 * Low-Power PIR Sensors: Integration of standalone, battery-friendly PIR motion sensors (based on ESP32-C6/C3/H2), designed for deep sleep operation and reporting motion events to Home Assistant.
 * Matter Connectivity (Planned): Future integration of Matter for direct, standardized control of the RGB strip and PIR sensors, leveraging the ESP32-C6's Wi-Fi 6, Bluetooth 5 (LE), and IEEE 802.15.4 capabilities.
Hardware Components
 * ESP32-C6 Super Mini: Main controller board.
 * ILI9341 TFT Display: A 2.8" or 3.2" (recommended) color display with SPI interface. A version including an XPT2046 touch controller is highly recommended for interactivity.
 * WS2812B RGB LED Strip: Addressable LED strip(s) for lighting control.
 * PIR Motion Sensors: Multiple units of low-power PIR sensors (e.g., HC-SR501, or more optimized modules).
 * ESP32-C6/C3/H2 Development Boards (for PIRs): Separate, small ESP boards for each PIR sensor, optimized for deep sleep.
 * Power Supplies: Appropriate 3.3V and/or 5V power supplies for the main controller, RGB strip, and individual PIR sensors.
 * Connecting Wires: Dupont cables, breadboard, or custom PCB for robust connections.
Software Stack
 * Development Framework: ESP-IDF (Espressif IoT Development Framework)
 * Real-Time Operating System (RTOS): FreeRTOS (integrated within ESP-IDF)
 * Graphics Library: LVGL (Light and Versatile Graphics Library)
 * Display Driver: ESP-IDF's esp_lcd component, with a custom driver for ILI9341.
 * RGB LED Driver: ESP-IDF's led_strip component utilizing the RMT peripheral for WS2812B.
 * Network Protocol: MQTT (using ESP-IDF's esp-mqtt component) for communication with Home Assistant.
 * Smart Home Platform: Home Assistant (running on a separate host).
 * Matter Integration: Espressif's esp-matter SDK (for future Matter functionality).
Getting Started
1. ESP-IDF Setup
Follow the official ESP-IDF Getting Started Guide for your operating system (Windows, macOS, Linux/WSL). This will involve installing the toolchain, setting up environment variables, and cloning the ESP-IDF repository. Ensure you select the correct ESP32-C6 target.
2. Hardware Wiring
Carefully connect your components:
 * ILI9341 to ESP32-C6:
   * Connect VCC to 3.3V, GND to GND.
   * Connect SPI pins: MOSI, SCK, CS, DC (or A0), RST.
   * Connect BL (Backlight) if you want software control.
   * If using touch (XPT2046): Connect T_CS, T_IRQ, T_DIN, T_DO, T_CLK.
   * Refer to your specific ESP32-C6 Super Mini board's pinout and the ILI9341 documentation for exact GPIO assignments.
 * WS2812B to ESP32-C6:
   * Connect Data In to a suitable GPIO pin on the ESP32-C6.
   * Connect VCC to an appropriate power supply (may require external 5V depending on strip length).
   * Connect GND to common ground.
   * Crucial: Ensure a voltage level shifter if your WS2812B data input requires 5V and your ESP32-C6 output is 3.3V.
 * PIR Sensor to ESP32-C6/C3/H2 (for each sensor):
   * Connect VCC and GND.
   * Connect OUT (data signal) to a GPIO input pin on the low-power ESP board.
3. Project Structure (Conceptual)
Your ESP-IDF project will likely be structured with main components and custom components:
```
esp32-c6-smart-home-controller/
├── main/
│   ├── Kconfig.projbuild
│   ├── main.c
│   ├── CMakeLists.txt
│   └── sdkconfig.defaults
├── components/
│   ├── lvgl_port/           # ESP-IDF LVGL port component
│   │   └── ...
│   ├── ili9341_driver/      # Custom ILI9341 driver component
│   │   └── ...
│   ├── xpt2046_driver/      # XPT2046 touch driver component
│   │   └── ...
│   ├── ws2812b_controller/  # WS2812B RMT LED strip control logic
│   │   └── ...
│   ├── mqtt_client_app/     # MQTT communication logic
│   │   └── ...
│   ├── ui_manager/          # LVGL UI screens and event handlers
│   │   └── ...
│   └── Kconfig
└── CMakeLists.txt
```

4. Software Implementation Steps
a. ESP32-C6 Main Controller Firmware
 * Initialize SPI Bus: Configure the SPI peripheral for the ILI9341 and XPT2046.
 * ILI9341 Driver: Implement or adapt an ESP-IDF compatible driver using esp_lcd for the ILI9341. This handles low-level pixel manipulation.
 * LVGL Integration: Integrate LVGL into your project. Configure the LVGL display driver to use your ILI9341 driver for flushing pixels. Set up an LVGL input device driver for the XPT2046 touch controller.
 * WS2812B Controller: Use the led_strip component with the RMT peripheral to control the WS2812B strip. Implement functions to set colors and effects.
 * Wi-Fi Connectivity: Configure the ESP32-C6 to connect to your Wi-Fi network.
 * MQTT Client: Initialize and connect the esp-mqtt client to your Home Assistant's MQTT broker. Implement callbacks for receiving messages and functions for publishing state.
 * LVGL UI Design: Create your GUI using LVGL widgets (buttons, sliders, color pickers, labels). Implement event handlers for UI interactions (e.g., button presses to change LED colors, slider changes for brightness). These events will trigger MQTT messages to Home Assistant or direct WS2812B updates.
 * Home Assistant API Interaction (Optional): Use esp_http_client to make API calls to Home Assistant for more complex data retrieval (e.g., sensor readings to display on screen).
 * FreeRTOS Tasks: Organize your application into FreeRTOS tasks (e.g., a dedicated task for LVGL handling, one for MQTT communication, one for sensor polling).
b. Low-Power PIR Sensor Firmware (Each Sensor)
 * GPIO Input: Configure a GPIO pin to read the PIR sensor's output signal.
 * Wi-Fi Connectivity (brief): On wake-up, connect to Wi-Fi.
 * MQTT Client: Initialize and connect the esp-mqtt client.
 * Deep Sleep Logic:
   * Set the PIR sensor's GPIO as a wake-up source (esp_deep_sleep_enable_gpio_wakeup).
   * When motion is detected (GPIO HIGH), wake up.
   * Publish an MQTT message indicating motion (e.g., home/motion/front_door/state: ON).
   * After a configurable delay or when the PIR signal clears, enter deep sleep again (esp_deep_sleep_start).
 * Power Optimization: Aggressively manage power. Disable unused peripherals, optimize wake-up times, and minimize active Wi-Fi time.
c. Home Assistant Configuration
 * MQTT Broker: Ensure you have the MQTT integration set up in Home Assistant.
 * MQTT Light: Configure an mqtt light entity in your configuration.yaml to control the WS2812B strip via MQTT messages from the ESP32-C6.
   # Example for configuration.yaml
light:
  - platform: mqtt
    name: "Living Room RGB Strip"
    state_topic: "esp32/rgb_strip/state"
    command_topic: "esp32/rgb_strip/command"
    rgb_state_topic: "esp32/rgb_strip/rgb_state"
    rgb_command_topic: "esp32/rgb_strip/rgb_command"
    brightness_state_topic: "esp32/rgb_strip/brightness_state"
    brightness_command_topic: "esp32/rgb_strip/brightness_command"
    # ... other options like `effect`

 * MQTT Binary Sensor: Configure mqtt binary sensor entities for each PIR sensor.
   # Example for configuration.yaml
binary_sensor:
  - platform: mqtt
    name: "Front Door Motion"
    state_topic: "home/motion/front_door/state"
    payload_on: "ON"
    payload_off: "OFF"
    device_class: motion
    # ... other options like availability_topic

 * Automations: Create Home Assistant automations that trigger actions based on the PIR motion sensors' states or control the RGB strip.
5. Matter Connectivity (Future Work)
This will involve leveraging Espressif's esp-matter SDK.
 * Integrate esp-matter: Add the esp-matter component to your main ESP32-C6 project.
 * Define Endpoints: Register Matter endpoints for the RGB strip (e.g., as a Color Control light device) and potentially for the PIR sensors (as Occupancy Sensor devices).
 * Link to Drivers: Implement the Matter cluster callbacks to interact with your existing WS2812B RMT driver and PIR input logic.
 * Commissioning: Test commissioning the Matter devices with a Matter controller (e.g., Home Assistant's Matter integration, Google Home, Apple Home).
Development Workflow
 * Clone this Repository: git clone <repository_url>
 * Navigate to Project: cd esp32-c6-smart-home-controller
 * Set IDF Target: idf.py set-target esp32c6
 * Configure Project: idf.py menuconfig (configure Wi-Fi credentials, MQTT broker details, GPIO pins, etc.)
 * Build: idf.py build
 * Flash: idf.py -p /dev/ttyUSB0 flash (replace /dev/ttyUSB0 with your ESP's serial port)
 * Monitor: idf.py -p /dev/ttyUSB0 monitor
Repeat steps 5-7 as you develop and debug. For PIR sensors, you'll have separate projects/builds.
Contributing
Contributions are welcome! Please feel free to open issues or pull requests to improve this project.
License
This project is open-source and released under the MIT License.
