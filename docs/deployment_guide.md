# Deployment Guide - Smart Waste Management System

This document explains how to configure and run the **Smart Waste Management System** components for long-term background execution on a local machine.

---

## 1. Running Mosquitto Broker automatically as a Service
On Windows, the Mosquitto installer automatically registers itself as a Windows Service that runs in the background.
* To verify or manage the service, press `Win + R`, type `services.msc`, and press Enter.
* Find **Mosquitto Broker** in the list.
* Right-click it and select **Properties**.
* Ensure the **Startup type** is set to **Automatic** so that the broker launches automatically whenever your machine boots up.

---

## 2. Running Node-RED automatically in the Background using PM2
`PM2` is a lightweight, production-grade process manager for Node.js applications that allows Node-RED to run silently in the background and restart on failure.

1. Open PowerShell or Command Prompt as Administrator and install PM2 globally:
   ```powershell
   npm install -g pm2
   ```
2. Start Node-RED under PM2 control:
   ```powershell
   pm2 start C:\Users\<Your-Username>\AppData\Roaming\npm\node-red.cmd --name "node-red"
   ```
   *(Replace `<Your-Username>` with your actual Windows username. On some setups, you can simply run `pm2 start node-red`).*
3. Configure PM2 to start automatically on Windows boot (optional, using `pm2-windows-service` or setting up a startup task).
4. Useful PM2 commands:
   * View running background processes: `pm2 list`
   * Monitor live logs: `pm2 logs node-red`
   * Stop the process: `pm2 stop node-red`
   * Restart the process: `pm2 restart node-red`

---

## 3. Running the Simulator automatically in the Background
To run the Python garbage bin simulator script in the background on Windows without keeping a command prompt open:

1. Rename the script from `simulator/bin_simulator.py` to `simulator/bin_simulator.pyw` (the `.pyw` extension tells Windows to run the script using `pythonw.exe`, which suppresses the console window).
2. To run the simulator in the background:
   ```powershell
   pythonw simulator/bin_simulator.py
   ```
3. To configure the simulator to run automatically on system startup, you can create a basic script or use the Windows Task Scheduler:
   * Open **Task Scheduler** and select **Create Basic Task...**
   * Set the trigger to **When the computer starts** or **When I log on**.
   * Set the action to **Start a program**.
   * Set the Program/Script to your virtual environment's Python executable (e.g., `d:\projects\smart-waste-management\venv\Scripts\pythonw.exe`) and arguments to the simulator path (e.g., `simulator/bin_simulator.py`).
4. **Stopping the background simulator**:
   * Open the Windows **Task Manager** (`Ctrl + Shift + Esc`).
   * Locate the background **pythonw** process.
   * Right-click the process and select **End Task**.

---

## 4. Local Deployment Considerations
When deploying the system on local development environments, keep the following considerations in mind:
* **Ports Availability**: Ensure ports `1883` (MQTT) and `1880` (Node-RED) are free and not blocked by local firewalls or other web servers.
* **Database Pathing**: The SQLite database (`waste_management.db`) is written relative to the execution context of Node-RED. Ensure Node-RED has write permissions to the repository directory.
* **Virtual Environment Execution**: When launching the simulator via scripts or task schedulers, always specify the absolute path to the Python executable within the virtual environment (`venv/Scripts/python.exe` or `venv/Scripts/pythonw.exe`) to ensure all dependencies are resolved.
