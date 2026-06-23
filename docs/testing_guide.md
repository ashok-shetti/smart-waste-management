# Testing & Verification Guide - Smart Waste Management System

This guide outlines the procedures for testing each component of the **Smart Waste Management System** to ensure it is functioning correctly.

---

## 1. Verify the Python Simulator & MQTT Connection

1. Open PowerShell or Command Prompt.
2. Navigate to the project directory:
   ```powershell
   cd d:\projects\smart-waste-management-system
   ```
3. Activate your virtual environment if not already active:
   * **On PowerShell**:
     ```powershell
     .\venv\Scripts\Activate.ps1
     ```
   * **On Command Prompt (CMD)**:
     ```cmd
     .\venv\Scripts\activate.bat
     ```

4. Run the simulator script inside the virtual environment:
   ```powershell
   python simulator/bin_simulator.py
   ```
4. **Expected Output:**
   - The simulator will connect to the local broker.
   - It will print a listing of all 5 bins with their cities and GPS coordinates.
   - Every 5 seconds, it will display a new cycle.
   - It will show each bin's fill level increasing and the published JSON data:
     ```text
     [SYSTEM] Connected successfully to MQTT Broker at localhost:1883
     --------------------------------------------------------------------------------
     --- [Cycle #1] Generating & Publishing Data ---
      * BIN001 (Pune): Fill Level increased to 22% (+12%)
        Published to waste/bin/data: {"bin_id": "BIN001", "fill_level": 22, "weight": 11, ...}
     ```

---

## 2. Verify the SQLite Database Logs

The Node-RED flow automatically writes incoming MQTT messages to `waste_management.db`. You can verify this using a simple Python script (no external database viewer required).

1. Let the simulator run for at least 15-20 seconds (3-4 cycles).
2. Open a separate Command Prompt or PowerShell window in the project folder.
3. Run the following command to query the last 5 records:
   ```powershell
   python -c "import sqlite3; conn = sqlite3.connect('waste_management.db'); cur = conn.cursor(); cur.execute('SELECT * FROM waste_data ORDER BY id DESC LIMIT 5'); [print(row) for row in cur.fetchall()]; conn.close()"
   ```
4. **Expected Output:**
   You should see 5 printed tuples corresponding to the latest records logged by Node-RED, for example:
   ```text
   (25, 'BIN005', 42, 22, 13.0827, 80.2707, '2026-06-23 10:15:32', 'Normal')
   (24, 'BIN004', 58, 31, 28.7041, 77.1025, '2026-06-23 10:15:32', 'Warning')
   (23, 'BIN003', 89, 47, 12.9716, 77.5946, '2026-06-23 10:15:32', 'Critical')
   (22, 'BIN002', 31, 16, 19.0760, 72.8777, '2026-06-23 10:15:32', 'Normal')
   (21, 'BIN001', 19, 10, 18.5204, 73.8567, '2026-06-23 10:15:32', 'Normal')
   ```

---

## 3. Verify the Dashboard Interface

1. Start Node-RED (`node-red` command) and import the flow if not already done.
2. Open your browser and navigate to `http://localhost:1880/ui`.
3. Test each page using the navigation menu (top-left button with three lines):

### Page 1: Overview
- Check the **Bin Counts Summary** card. It should show 5 monitored bins.
- The numbers of Normal, Warning, and Critical bins should change as the simulator runs.
- **System Statistics** should show the "Total Records Logged" counting up dynamically, along with a "Last Updated Time" that refreshes every 5 seconds.

### Page 2: Bin Monitoring
- Verify the **Live Telemetry Table**. It should list exactly 5 rows (one for each bin ID) showing current Fill Levels, Weights, and Status labels.
- The values must update automatically in place every 5 seconds.

### Page 3: Bin Locations Map
- You should see an interactive map centered over India.
- Five pins should be visible at Pune, Mumbai, Bengaluru, Delhi, and Chennai.
- The pins will dynamically change color: **Green** (Normal, 0-50%), **Orange** (Warning, 51-80%), or **Red** (Critical, 81-100%).
- Click on any marker. A popup should open displaying the Bin ID, current fill level, weight, and status.

### Page 4: Analytics & Trends
- You should see two line charts: "Fill Level Trend (%)" and "Garbage Weight Trend (kg)".
- Each chart should draw 5 distinct colored lines, one for each bin.
- The lines should gradually climb as bins accumulate waste.

---

## 4. Test the Alert system & Waste Collection Resets

To verify the alert toasts and simulated collections:

1. Let the simulator run continuously.
2. Monitor the simulator console. Eventually, one or more bins will accumulate enough waste to exceed `85%` (e.g., rising from `65%` -> `80%` -> `92%`).
3. **Check the Dashboard:**
   - As soon as a bin exceeds `80%` fill level, a red toast alert should pop up in the top-right corner of the dashboard screen:
     `ALERT: BIN003 (Bengaluru) is Full at 92%!`.
   - The marker for that bin on the map page should turn **Red**.
   - The status in the table should change to **Critical**.
4. **Simulate Collection:**
   - Keep watching the simulator console. The bin will remain at its high fill level for 1 or 2 cycles (e.g., displaying `WAITING COLLECTION (Level: 92%)`).
   - On the next cycle, you should see the collection log in the console:
     `[TRUCK COLLECTED] Emptying BIN003 in Bengaluru. Level reset to 4%`
   - On the dashboard, the toast alert will disappear, the map marker will return to **Green**, and the charts will show a sharp drop down to the low value, confirming that the simulated collection was successfully logged.
