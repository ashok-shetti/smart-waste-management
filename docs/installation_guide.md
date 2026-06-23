# Installation Guide - Smart Waste Management System

This guide provides step-by-step instructions for installing all required components for the **Smart Waste Management System** on a local Windows machine.

---

## Prerequisites Overview

The project requires the following software to be installed:
1. **Python 3.x** (for running the garbage bin simulator)
2. **Node.js** & **npm** (prerequisites for running Node-RED)
3. **Mosquitto MQTT Broker** (for handling message distribution)
4. **Node-RED** & associated dashboard nodes (for the UI, maps, and data routing)

---

## Step 1: Install Python & Project Dependencies

1. Download and install Python 3.x from the official website: [python.org](https://www.python.org/downloads/).
   > [!IMPORTANT]
   > Make sure to check the box **"Add Python to PATH"** during installation.

2. Open **PowerShell** or **Command Prompt** and navigate to your project directory:
   ```powershell
   cd d:\projects\smart-waste-management
   ```

3. Create a Python Virtual Environment (`venv`) to isolate project dependencies:
   ```powershell
   python -m venv venv
   ```

4. Activate the virtual environment:
   * **On PowerShell**:
     ```powershell
     .\venv\Scripts\Activate.ps1
     ```
     *(Note: If PowerShell reports an execution policy error, run `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process` to temporarily allow scripts in the current session, then run the activation command again).*
   * **On Command Prompt (CMD)**:
     ```cmd
     .\venv\Scripts\activate.bat
     ```

5. Install the required Python MQTT library inside the active virtual environment:
   ```powershell
   pip install -r requirements.txt
   ```

---

## Step 2: Install Mosquitto MQTT Broker

1. Download the Windows installer from [mosquitto.org/download/](https://mosquitto.org/download/) (select the `64-bit` installer).
2. Run the installer and complete the wizard. By default, it installs to `C:\Program Files\mosquitto`.
3. The installer automatically registers Mosquitto as a Windows Service and starts it.
4. **Verify the Broker is running:**
   - Open Command Prompt and type:
     ```cmd
     net stat -an | findstr 1883
     ```
   - If you see a line showing port `1883` in the `LISTENING` state, the broker is running successfully!

---

## Step 3: Install Node.js & Node-RED

1. Download and install the LTS version of Node.js from [nodejs.org](https://nodejs.org/).
2. Open a new Command Prompt or PowerShell window and verify the installation:
   ```powershell
   node --version
   npm --version
   ```
3. Install **Node-RED** globally using npm:
   ```powershell
   npm install -g --unsafe-perm node-red
   ```
4. Start Node-RED:
   ```powershell
   node-red
   ```
5. Leave this command window open. Node-RED is now running locally on `http://127.0.0.1:1880/`.

---

## Step 4: Install Required Node-RED Nodes

Before importing the flow, you must install the dashboard, SQLite, and worldmap components.

1. Open your browser and navigate to `http://localhost:1880`.
2. Click the **Menu button** (top-right corner, three horizontal lines) and select **Manage palette**.
3. Go to the **Install** tab.
4. Search for and install the following three packages (click **Install** for each):
   - **`node-red-dashboard`**: Standard user interface widgets (gauges, charts, text, groups).
   - **`node-red-contrib-web-worldmap`**: Interactive map visualization engine.
   - **`node-red-node-sqlite`**: Integration node to interact with SQLite databases.
5. Close the Palette Manager when installations are complete.

---

## Step 5: Import the Node-RED Flow

1. Open the file [node_red_flow.json](../node_red/node_red_flow.json) in a text editor and copy the entire JSON content.
2. Go back to Node-RED in your browser (`http://localhost:1880`).
3. Click the **Menu button** (top-right) -> **Import**.
4. Paste the copied JSON into the text area.
5. Select **Import to New Flow** and click **Import**.
6. Click the red **Deploy** button in the top-right corner to make the flow active!
