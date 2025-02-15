https://tinyurl.com/GasDetectingSystemReport

# Intelligas Sentinel: Autonomous IoT-Integrated Gas Leakage Detection and Mitigation System

## Overview  
An autonomous system that detects hazardous gas leaks, triggers mitigation actions (door opening, ventilation), and alerts users via SMS/calls. Built with Arduino Uno R3 and IoT integration.

## Key Features  
- **Gas Detection**: Real-time monitoring via MQ-series sensor.  
- **Automatic Mitigation**: Servo-controlled door activation + exhaust fan engagement.  
- **User Alerts**: SMS/call notifications via SIM800L GSM module.  
- **Audible Alarm**: 85dB buzzer for immediate local alerts.  
- **IoT Integration**: Cellular communication for remote warnings.  

## Technical Specifications  
### Hardware  
- **Controller**: Arduino Uno R3  
- **GSM Module**: SIM800L (Quad-band 850/900/1800/1900MHz)  
- **Actuators**: SG90 servo (door), 5V DC exhaust fan  
- **Sensors**: MQ-2/MQ-5 gas detector (LPG/CNG/propane)  
- **Alert System**: Active buzzer (5V, 30mA)  
- **Power**: 9V DC supply + LM2596 voltage regulator  

### Software  
- **IDE**: Arduino 2.0+  
- **Libraries**: `Servo.h`, `SoftwareSerial.h`, `TinyGSM`  
- **Logic**: Threshold-based trigger system  

## Installation & Setup  
1. **Hardware Assembly**  
   | Component      | Arduino Connection |  
   |----------------|--------------------|  
   | Gas Sensor     | A0 (Analog)        |  
   | SIM800L TX/RX  | D2/D3 (SoftwareSerial)|  
   | Servo          | D9                 |  
   | Fan            | D10 (Transistor)|  
   | Buzzer         | D11                |  

   ![Wiring Diagram](path/to/your_diagram.png) *Replace with actual image path*

2. **Code Upload**  
   ```arduino
   #include <SoftwareSerial.h>
   #include <Servo.h>
   
   #define GAS_SENSOR A0
   #define BUZZER 11
   #define FAN 10
   Servo doorServo;
   
   SoftwareSerial sim800(2, 3); // RX, TX
   
   void setup() {
     pinMode(FAN, OUTPUT);
     pinMode(BUZZER, OUTPUT);
     doorServo.attach(9);
     sim800.begin(9600);
   }
   
   void loop() {
     if (analogRead(GAS_SENSOR) > 300) {
       trigger_emergency();
     }
   }
   
   void trigger_emergency() {
     doorServo.write(90); // Open door
     digitalWrite(FAN, HIGH);
     digitalWrite(BUZZER, HIGH);
     sim800.println("ATD+1234567890;"); // Replace with user number
     delay(20000); // 20-second call
     sim800.println("ATH");
     sim800.println("AT+CMGF=1");
     sim800.println("AT+CMGS=\"+1234567890\"\r"); // SMS alert
     sim800.print("GAS LEAK DETECTED! System activated.");
     sim800.write(26); // Ctrl+Z to send
   }

   ---------------------------------------------------------------------
   SIM800L Configuration

Insert active SIM card

Set APN in code (sim800.println("AT+CSTT=\"your_APN\""))

Threshold Calibration
Adjust gas sensor baseline in code via serial monitor readings.

Testing Scenarios
Gas Detection Test
Introduce calibrated gas sample to verify sensor response.

Full System Activation
Confirm sequential actions:

Servo rotates 90° (door open)

Fan activates

Buzzer sounds

SMS sent + voice call initiated

Network Validation
Ensure SIM800L connects to cellular network (LED blinking 3/sec).
