## Cybersecurity Final Project Report: CSN150
## Project Name: ESP-Eye-Witness: IoT-Integrated Motion Surveillance & Forensic Logging

## Mohamed Sylla

## May 22, 2026

## Purpose
The purpose of this project is to research, build, and deploy an embedded Internet of Things (IoT) physical security monitoring tool. The device uses an ESP32-CAM to establish a localized surveillance endpoint that automatically captures visual forensic evidence and securely transmits it to a remote, authenticated email destination over an encrypted network layer.

## Equipment & Hardware Overview
ESP32-CAM (AI-Thinker Module): Powered by an ESP32-S dual-core 32-bit LX6 microprocessor operating at 240 MHz. Contains 520 KB of internal SRAM and 4MB of external PSRAM necessary to handle RAM-intensive JPEG image processing.

OV2640 Camera Sensor: A 2-megapixel CMOS image sensor configured to capture and compress photos into JPEG payloads.

FTDI USB-to-TTL Serial Adapter: Utilized to flash the compiled binary firmware onto the board via the UART0 interface (GPIO 1/TX and GPIO 3/RX), utilizing a temporary connection between GPIO 0 and GND to trigger programming flash mode.

USB Micro Data Cable & Jumper Wires

## Links to Documentation & Tools
Core Library Documentation: Mobizt ESP Mail Client GitHub Repository

Hardware & Setup Reference: Random Nerd Tutorials: ESP32-CAM E-Book Reference https://randomnerdtutorials.com/esp32-cam-send-photos-email/

Google Security Portal: Google App Passwords Configuration Hub

## AI GPTs Used
Gemini Pro (Assisted in re-architecting synchronous setups down into continuous asynchronous runtime execution loops and debugging library header maps).

## Steps Followed
1. Identity & Access Management (IAM) Hardening
Because modern mail servers block insecure legacy SMTP authentication, Google Account parameters had to be securely bypassed. 2-Step Verification (2FA) was activated on the sender account (mohamed225mhd223@gmail.com). An isolated, 16-character application cryptographic token (App Password) was generated exclusively for the ESP32-CAM hardware interface, neutralizing the need to expose primary account credentials inside the plain-text firmware sketch.

2. Software Environment Configuration
The Arduino IDE 2.x development environment was prepared by installing the core ESP32 board manager packages. Using the integrated Library Manager, the complete <ESP_Mail_Client.h> package suite authored by Mobizt was compiled and linked along with dependencies for the virtual hardware flash filesystem (<FS.h> and <LittleFS.h>).

3. Code Architecture & Network Mapping
The network firmware configurations were explicitly mapped out:

Network Handshake: Bound the system to local Wi-Fi nodes via the WiFi.begin(ssid, password) script over a Verizon private network gateway.

Encryption Layer: Bound the automated script to negotiate outbound handshakes over Port 465 (SSL/TLS) pointing directly to the secure destination smtp.gmail.com.

Program Loop Realignment: Refactored the core execution functions out of the one-and-done setup() block. The camera frame-buffer query (capturePhotoSaveLittleFS()) and payload transmission configurations (sendPhoto()) were placed down inside the continuous runtime loop() block, governed by a delay(20000) threshold to prevent inbox floods or server-side denial-of-service flags.

4. Deployment & Execution
GPIO 0 was jumped directly to a Ground (GND) pin to force the ESP32-S system-on-chip into bootloader programming mode. The code was uploaded across the serial bridge at a communication speed of 115200 baud. Once successfully flashed, the jumper wire grounding GPIO 0 was removed, the hardware reset switch was clicked, and the device initialized its network configuration.

## Diagram and screenshoot 
[ Physical Environment ]
             │
             ▼ (Motion / Runtime Loop Trigger)
     ┌───────────────┐
     │   ESP32-CAM   │ ──► [ Captures JPEG Image Data ]
     └───────┬───────┘
             │
             ▼ (Encrypted TLS/SSL Handshake)
       [ Wi-Fi Router ] (Verizon_QKZQ4P)
             │
             ▼ (Outbound Port 465 via Internet)
     ┌───────────────┐
     │  smtp.gmail.com│ ──► [ Google App Password Authentication ]
     └───────┬───────┘
             │
             ▼ (Secure E-mail Delivery)
   ┌───────────────────┐
   │ mohamed225mhd223  │ ──► [ Forensic Visual Evidence Received ]
   │    @gmail.com     │
   └───────────────────┘
   
<img width="1636" height="1977" alt="image" src="https://github.com/user-attachments/assets/e7e06bd3-2436-4b5f-ac0b-81889735c19c" /> 
<img width="3619" height="1982" alt="image" src="https://github.com/user-attachments/assets/0269987a-54d7-482a-9339-3823b7eed6ea" />


## Problems and Solutions
Problem 1: Missing Compilation Dependencies * Symptom: Compilation terminated immediately on line 10 throwing a fatal error: ESP_Mail_Client.h: No such file or directory.

## Solution: Accessed the internal Arduino IDE Library Manager, pulled down the official ESP Mail Client library by Mobizt, and committed an "Install All" dependency command to pull in the filesystem hooks natively.

## Problem 2: SMTP Transport Layer Authentication Failures * Symptom: Outbound connection attempts dropped out or timed out when trying to talk to the mail routing server.

## Solution: Identified that Google blocks basic password strings on embedded devices. Resolved the issue by enforcing 2FA on the host account, generating a unique application app-password string ("dovsupzeiakopxpx"), stripping out the display spaces, and pasting the continuous block directly into the #define emailSenderPassword line.

## Final Report
The final deployment successfully resulted in a functional, automated IoT-Integrated Forensic Capture & Monitoring Device. During testing, the system initialized properly over the local area network, printed out its internal IP address on the Serial Monitor, executed its frame capture sequence, and securely uploaded the raw data.
The validation metric was met at 1:04 AM when a secure alert email titled "ESP32-CAM Photo Captured" containing an attached JPEG image of the monitoring workspace was successfully received by the recipient inbox, confirming operational integrity. 
