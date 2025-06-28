# Environmental Guardian Station - Technical Requirements Document

## 1. Project Overview

### 1.1 Purpose
The Environmental Guardian Station is a portable, self-contained environmental monitoring system built on the ESP32-C3 platform. It provides real-time air quality, weather monitoring, and predictive analytics with mesh networking capabilities.

### 1.2 Scope
- Multi-sensor environmental data collection
- Real-time data processing and visualization
- Predictive analytics using on-device machine learning
- Mesh networking for area-wide monitoring
- Solar-powered operation with battery backup
- Web-based monitoring interface with mobile responsiveness

## 2. System Architecture

### 2.1 Hardware Architecture

#### 2.1.1 Core Processing Unit
- **Primary MCU**: ESP32-C3 (RISC-V, 160MHz, 400KB SRAM, 4MB Flash)
- **Secondary MCU**: STM32F103 (optional, for sensor preprocessing)
- **Real-Time Clock**: DS3231 with battery backup
- **External Storage**: 32GB microSD card for data logging

#### 2.1.2 Sensor Array
```
┌─────────────────────────────────────────────────────────────┐
│                    SENSOR CONFIGURATION                     │
├─────────────────────────────────────────────────────────────┤
│ Environmental Sensors:                                      │
│ • BME680 (I2C: 0x77) - Temp/Humidity/Pressure/VOC Gas     │
│ • SCD40 (I2C: 0x62) - CO2/Temperature/Humidity            │
│ • PMS5003 (UART) - PM1.0/PM2.5/PM10 Particulate Matter    │
│ • VEML6070 (I2C: 0x38) - UV Index                         │
│ • TSL2591 (I2C: 0x29) - Ambient Light/Lux                 │
│ • SGP30 (I2C: 0x58) - TVOC/eCO2                           │
│ • MiCS-6814 (Analog) - NO2/NH3/CO Gas Detection           │
│                                                             │
│ Power & Monitoring:                                         │
│ • INA219 (I2C: 0x40) - Power consumption monitoring        │
│ • MAX17048 (I2C: 0x36) - Battery fuel gauge               │
│ • BQ24295 - Solar charge controller                        │
└─────────────────────────────────────────────────────────────┘
```

#### 2.1.3 Communication Interfaces
- **Primary WiFi**: ESP32-C3 built-in 802.11b/g/n
- **Mesh Network**: ESP-NOW protocol for device-to-device communication
- **Long Range**: RFM95W LoRa module (868/915MHz)
- **Local Display**: 1.3" OLED SH1106 128x64 (I2C)
- **Status Indicators**: WS2812B RGB LED strip (8 LEDs)

#### 2.1.4 Power Management System
```
Solar Panel (6V, 2W) → BQ24295 Charge Controller → 18650 Li-ion (3400mAh)
                                ↓
                         ESP32-C3 + Sensors
                                ↓
                    Power Monitoring (INA219)
```

### 2.2 Software Architecture

#### 2.2.1 Firmware Stack
```
┌─────────────────────────────────────────────────────────────┐
│                    APPLICATION LAYER                        │
├─────────────────────────────────────────────────────────────┤
│ • Web Server & API          • Machine Learning Engine      │
│ • Data Analytics            • Mesh Network Manager         │
│ • Alert System              • Configuration Manager        │
├─────────────────────────────────────────────────────────────┤
│                    MIDDLEWARE LAYER                         │
├─────────────────────────────────────────────────────────────┤
│ • Sensor Abstraction Layer  • Data Storage Manager         │
│ • Communication Protocol    • Power Management             │
│ • Display Manager           • Time Synchronization         │
├─────────────────────────────────────────────────────────────┤
│                    HARDWARE LAYER                           │
├─────────────────────────────────────────────────────────────┤
│ • I2C Driver                • UART Driver                   │
│ • SPI Driver                • GPIO Driver                   │
│ • WiFi Driver               • LoRa Driver                   │
└─────────────────────────────────────────────────────────────┘
```

#### 2.2.2 Data Flow Architecture
```
Sensors → Preprocessing → ML Analysis → Storage → Visualization
   ↓            ↓             ↓          ↓          ↓
Raw Data → Calibration → Predictions → SD Card → OLED/Web
   ↓            ↓             ↓          ↓          ↓
Quality Check → Filtering → Alerts → Cloud Sync → Mobile App
```

## 3. Functional Requirements

### 3.1 Core Functionality

#### 3.1.1 Environmental Monitoring (FR-001)
- **Requirement**: System shall continuously monitor environmental parameters
- **Parameters**: Temperature, Humidity, Pressure, Air Quality Index, CO2, PM2.5, PM10, UV Index, Light Level
- **Sampling Rate**: 1 sample per minute (configurable: 10s - 10min)
- **Accuracy**: ±2% for temperature, ±3% for humidity, ±1hPa for pressure
- **Range**: -40°C to +85°C, 0-100% RH, 300-1100hPa

#### 3.1.2 Data Logging (FR-002)
- **Requirement**: System shall store historical data locally and remotely
- **Local Storage**: 30 days of minute-resolution data on SD card
- **Data Format**: JSON with timestamp, sensor ID, and values
- **Compression**: GZIP compression for storage efficiency
- **Backup**: Automatic cloud synchronization when WiFi available

#### 3.1.3 Predictive Analytics (FR-003)
- **Requirement**: System shall provide weather and air quality predictions
- **Algorithm**: TensorFlow Lite micro models
- **Prediction Horizon**: 6-hour weather forecast, 2-hour air quality trend
- **Accuracy Target**: 85% for weather, 80% for air quality trends
- **Update Frequency**: Model inference every 15 minutes

#### 3.1.4 Alert System (FR-004)
- **Requirement**: System shall generate alerts for threshold violations
- **Alert Types**: Visual (LED), Audio (Buzzer), Web notification, Email
- **Thresholds**: User-configurable for all parameters
- **Response Time**: <30 seconds from threshold violation to alert
- **Escalation**: Multiple alert levels (Warning, Critical, Emergency)

### 3.2 User Interface Requirements

#### 3.2.1 OLED Display (FR-005)
- **Requirement**: Local display shall show current status and trends
- **Display Modes**: 
  - Overview: All parameters summary
  - Detail: Individual parameter with 24h graph
  - Network: Mesh network status and connected devices
  - Settings: Configuration menu
- **Update Rate**: 5-second refresh for current values
- **Navigation**: Rotary encoder with push button

#### 3.2.2 Web Interface (FR-006)
- **Requirement**: Web-based dashboard for remote monitoring
- **Features**: 
  - Real-time data visualization
  - Historical data charts (1h, 24h, 7d, 30d views)
  - Configuration interface
  - Alert management
  - Firmware update interface
- **Compatibility**: Modern browsers (Chrome 90+, Firefox 88+, Safari 14+)
- **Responsive Design**: Mobile-first approach

#### 3.2.3 Mobile Application (FR-007)
- **Requirement**: Native mobile app for iOS/Android
- **Features**:
  - Push notifications for alerts
  - GPS-based device location
  - Multi-device management
  - Offline data viewing
- **Platforms**: iOS 14+, Android 10+

### 3.3 Communication Requirements

#### 3.3.1 Mesh Networking (FR-008)
- **Requirement**: Multiple devices shall form self-healing mesh network
- **Protocol**: ESP-NOW with custom routing algorithm
- **Range**: 100m line-of-sight, 30m through walls
- **Topology**: Self-organizing mesh with automatic parent selection
- **Data Sharing**: Sensor data aggregation across network

#### 3.3.2 Internet Connectivity (FR-009)
- **Requirement**: System shall connect to internet when available
- **Primary**: WiFi 802.11n (2.4GHz)
- **Backup**: LoRaWAN for remote locations
- **Data Sync**: Automatic upload when connected
- **Security**: WPA3 encryption, certificate-based authentication

## 4. Non-Functional Requirements

### 4.1 Performance Requirements

#### 4.1.1 Response Time (NFR-001)
- **Web Interface**: Page load <2 seconds
- **API Response**: <500ms for data queries
- **Sensor Reading**: <100ms per sensor
- **Alert Generation**: <30 seconds from trigger

#### 4.1.2 Throughput (NFR-002)
- **Data Processing**: 1000 sensor readings/minute
- **Web Requests**: 50 concurrent users
- **Mesh Network**: 10 devices per network segment
- **Data Storage**: 1GB/month per device

#### 4.1.3 Memory Usage (NFR-003)
- **RAM Usage**: <300KB peak usage
- **Flash Usage**: <3MB for firmware
- **SD Card**: Minimum 8GB, recommended 32GB
- **Data Retention**: 2 years local, unlimited cloud

### 4.2 Reliability Requirements

#### 4.2.1 Availability (NFR-004)
- **System Uptime**: 99.5% (excluding maintenance)
- **Sensor Accuracy**: Maintain calibration for 12 months
- **Data Integrity**: <0.1% data loss rate
- **Recovery Time**: <5 minutes from power failure

#### 4.2.2 Fault Tolerance (NFR-005)
- **Sensor Failure**: Continue operation with remaining sensors
- **Communication Loss**: Store data locally, sync when restored
- **Power Failure**: Graceful shutdown, data preservation
- **Watchdog Timer**: Automatic recovery from software hangs

### 4.3 Security Requirements

#### 4.3.1 Authentication (NFR-006)
- **Web Interface**: Multi-factor authentication support
- **API Access**: JWT token-based authentication
- **Device Access**: Certificate-based device authentication
- **User Management**: Role-based access control

#### 4.3.2 Data Protection (NFR-007)
- **Encryption**: AES-256 for stored data
- **Transmission**: TLS 1.3 for all communications
- **Privacy**: GDPR compliance for personal data
- **Audit Trail**: Log all configuration changes

### 4.4 Environmental Requirements

#### 4.4.1 Operating Conditions (NFR-008)
- **Temperature**: -20°C to +60°C operating
- **Humidity**: 0-95% RH non-condensing
- **Altitude**: 0-3000m above sea level
- **Vibration**: IEC 60068-2-6 compliant

#### 4.4.2 Power Consumption (NFR-009)
- **Normal Operation**: <200mA average current
- **Sleep Mode**: <10mA current consumption
- **Battery Life**: 7 days without solar charging
- **Solar Efficiency**: 80% charging efficiency

## 5. Technical Specifications

### 5.1 Hardware Specifications

#### 5.1.1 Physical Dimensions
- **Enclosure**: 150mm x 100mm x 50mm (IP65 rated)
- **Weight**: <500g including battery
- **Mounting**: Wall mount, pole mount, desktop stand
- **Material**: UV-resistant ABS plastic

#### 5.1.2 Electrical Specifications
- **Input Voltage**: 5-12V DC (solar panel input)
- **Battery**: 18650 Li-ion 3.7V 3400mAh
- **Power Consumption**: 
  - Active: 1.2W average
  - Sleep: 0.05W
  - Peak: 2.5W (during WiFi transmission)

### 5.2 Software Specifications

#### 5.2.1 Firmware Requirements
- **Development Environment**: ESP-IDF v5.0+
- **Programming Language**: C++ with Arduino framework
- **Real-Time OS**: FreeRTOS
- **Memory Management**: Dynamic allocation with leak detection

#### 5.2.2 Machine Learning Specifications
- **Framework**: TensorFlow Lite for Microcontrollers
- **Model Size**: <100KB per model
- **Inference Time**: <1 second per prediction
- **Training Data**: Minimum 10,000 samples per parameter

## 6. Integration Requirements

### 6.1 Third-Party Services

#### 6.1.1 Weather Services (INT-001)
- **Primary**: OpenWeatherMap API
- **Backup**: WeatherAPI.com
- **Update Frequency**: Every 30 minutes
- **Data Points**: Current conditions, 5-day forecast

#### 6.1.2 Cloud Storage (INT-002)
- **Primary**: AWS IoT Core
- **Backup**: Google Cloud IoT
- **Data Format**: MQTT JSON payloads
- **Retention**: 2 years historical data

### 6.2 External Hardware

#### 6.2.1 Sensor Calibration (INT-003)
- **Calibration Kit**: Reference sensors for field calibration
- **Frequency**: Annual calibration recommended
- **Standards**: NIST traceable references
- **Documentation**: Calibration certificates required

## 7. Testing Requirements

### 7.1 Unit Testing
- **Coverage**: Minimum 80% code coverage
- **Framework**: Unity testing framework
- **Automation**: Continuous integration testing
- **Mock Objects**: Hardware abstraction layer mocking

### 7.2 Integration Testing
- **Sensor Integration**: All sensors functional simultaneously
- **Communication Testing**: Mesh network with 10 devices
- **Endurance Testing**: 30-day continuous operation
- **Environmental Testing**: Full temperature/humidity range

### 7.3 Performance Testing
- **Load Testing**: 1000 concurrent web requests
- **Stress Testing**: Maximum sensor sampling rate
- **Memory Testing**: 24-hour memory leak detection
- **Power Testing**: Battery life validation

## 8. Deployment Requirements

### 8.1 Manufacturing
- **Production Testing**: Automated test fixture
- **Quality Control**: Statistical process control
- **Calibration**: Factory calibration with certificates
- **Packaging**: Anti-static packaging with documentation

### 8.2 Field Deployment
- **Installation Guide**: Step-by-step setup instructions
- **Configuration Tool**: Mobile app for initial setup
- **Site Survey**: RF environment assessment
- **Commissioning**: Functional verification checklist

## 9. Maintenance Requirements

### 9.1 Preventive Maintenance
- **Sensor Cleaning**: Monthly cleaning schedule
- **Calibration Check**: Quarterly verification
- **Firmware Updates**: Automatic OTA updates
- **Battery Replacement**: 3-year replacement cycle

### 9.2 Diagnostic Features
- **Self-Test**: Daily automated system checks
- **Health Monitoring**: Continuous system health metrics
- **Remote Diagnostics**: Cloud-based diagnostic tools
- **Error Reporting**: Automatic error log transmission

## 10. Documentation Requirements

### 10.1 Technical Documentation
- **Hardware Manual**: Schematic, PCB layout, BOM
- **Software Documentation**: API reference, code comments
- **User Manual**: Installation and operation guide
- **Troubleshooting Guide**: Common issues and solutions

### 10.2 Compliance Documentation
- **FCC Certification**: Radio frequency compliance
- **CE Marking**: European conformity
- **RoHS Compliance**: Hazardous substance restrictions
- **Safety Certifications**: UL/CSA electrical safety 