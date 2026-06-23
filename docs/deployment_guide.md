# Deployment & Presentation Guide - Smart Waste Management System

This document explains how to set up the **Smart Waste Management System** for long-term local execution and provides recommendations for presenting this project to evaluators, professors, or on GitHub.

---

## Part 1: Local Deployment as Background Services

To make this system run continuously in the background on your local machine without keeping multiple command windows open, follow these configurations:

### 1. Running Mosquitto Broker as a Service (Default)
On Windows, the Mosquitto installer automatically registers itself as a Windows Service.
- To manage it, press `Win + R`, type `services.msc`, and press Enter.
- Locate **Mosquitto Broker** in the list.
- Ensure the **Startup type** is set to **Automatic** so that it starts whenever your computer boots up.

### 2. Running Node-RED in the Background using PM2
`PM2` is a production process manager for Node.js applications that allows you to run Node-RED in the background.

1. Open PowerShell as Administrator and install PM2 globally:
   ```powershell
   npm install -g pm2
   ```
2. Start Node-RED under PM2 control:
   ```powershell
   pm2 start C:\Users\<Your-Username>\AppData\Roaming\npm\node-red.cmd --name "node-red"
   ```
3. To configure PM2 to start automatically on Windows boot, use `pm2-windows-service` (optional) or simply start it when testing.
4. **PM2 Commands:**
   - View running services: `pm2 list`
   - View Node-RED logs: `pm2 logs node-red`
   - Stop Node-RED: `pm2 stop node-red`

### 3. Running the Python Simulator in the Background
To run the simulator script in the background on Windows without a terminal window:
- Rename your script from `simulator/bin_simulator.py` to `simulator/bin_simulator.pyw`.
- double-click the file, or run it via command line:
  ```powershell
  pythonw simulator/bin_simulator.pyw
  ```
- This runs the Python interpreter without opening a console window.
- To stop the simulator, open the Windows **Task Manager**, look for the **python** process, and click **End Task**.

---

## Part 2: Academic Presentation & Viva Guide

If you are presenting this project for an academic evaluation, final-year viva, or seminar, follow this structured demo script to impress your evaluators:

### 1. Presentation Slides Structure (Suggested)
- **Slide 1: Title** - Smart Waste Management System for Metropolitan Cities.
- **Slide 2: Problem Statement** - Challenges of manual collection (overflowing bins, hygiene issues, high fuel costs).
- **Slide 3: Architecture Diagram** - Explain the 5 layers (Simulator -> MQTT Broker -> Node-RED -> SQLite -> Dashboard).
- **Slide 4: Key Features** - Real-time monitoring, threshold classification, automated alerts, and mapping.
- **Slide 5: Implementation Details** - Discuss technologies used (Python, Paho-MQTT, SQLite, Node-RED Dashboard).
- **Slide 6: Future Scope** - Mention Route Optimization, Predictive Maintenance, and Waste Analytics.

### 2. Live Demonstration Script (Step-by-Step)
1. **Show the Code & Architecture First:**
   - Open your project folder in **VS Code** to show the project structure.
   - Explain `sql/database_schema.sql` and the database structure.
2. **Start the Simulator Console:**
   - Run `python simulator/bin_simulator.py` in a visible terminal. Let the evaluators see the formatted text logging and point out that it represents actual IoT telemetry packets being sent via MQTT.
3. **Show the Node-RED Backend (The "Engine"):**
   - Open `http://localhost:1880`.
   - Explain how the MQTT node subscribes to the broker and how the data is split into multiple paths: status calculation, SQLite logging, mapping, chart telemetry, and threshold checks.
4. **Open the Database:**
   - Run the Python query command from the [Testing Guide](./testing_guide.md#2-verify-the-sqlite-database-logs) to show that rows are indeed being saved in SQLite in real time.
5. **Open the Dashboard UI:**
   - Navigate to `http://localhost:1880/ui`.
   - Show the **Overview** page: highlight the auto-updating counters for Normal, Warning, and Critical bins.
   - Show the **Bin Monitoring** page: show the live telemetry table values updating in real time.
   - Show the **Bin Locations Map**: show pins at Mumbai, Delhi, Chennai, Bengaluru, and Pune. Click a pin to show its live statistics in the popup.
   - Show the **Analytics & Trends** page: point out the multi-line chart and explain how the lines represent the gradual accumulation of waste.
6. **Trigger an Alert Live:**
   - Wait for a bin to exceed `80%`. Point out the red toast notification popup in the top-right corner. Highlight that the pin on the map has changed to red.
   - Explain that after a few cycles, the system simulates a truck emptying the bin, and show the level resetting back to empty on the charts and map.
