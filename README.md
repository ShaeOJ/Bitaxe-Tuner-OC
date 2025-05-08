# Bitaxe Dynamic Temperature OC Controller

**Version:** (Based on script provided on May 7, 2025)

## **1. Purpose**

This Python script provides a graphical user interface (GUI) to monitor and dynamically control the overclocking (OC) settings (core frequency and voltage) of one or more Bitaxe (Ultra/Hex) ASIC miners. The primary goal is to maintain a target core temperature by automatically adjusting these settings. It also includes features for manual control, fan management, and configuration persistence.

The script communicates with Bitaxe miners via their HTTP API.

## **2. Features**

* **Dynamic Temperature Targeting:** Automatically adjusts miner frequency and voltage to stay at or below a user-defined target core temperature.
* **GUI Interface:** Provides a user-friendly Tkinter-based GUI to:
    * Display real-time stats for multiple miners (Nickname, ASIC Model, IP, Status, Frequency, Voltage, Core Temp, VRM Temp, Hashrate, Power, Fan Speed, Auto Fan status).
    * Add, remove, and edit miner configurations.
    * Configure global and per-miner tuning parameters.
    * Manually control fan settings (auto/manual, speed, polarity).
    * Start and stop the tuning process.
    * Restart miners.
    * Open miner web UI.
* **Configuration Persistence:** Saves all settings (global and per-miner) to a `tuner_config.json` file, so your setup is remembered between sessions.
* **Advanced Tuning Parameters:**
    * VRM temperature protection: Reduces frequency/voltage if VRM temperature exceeds a threshold.
    * Voltage bump at specific frequencies (e.g., increase voltage when reaching 600MHz).
    * Configurable step intervals and adjustment amounts.
* **Flatline Detection:** Can detect if a miner's hashrate flatlines and automatically restart it.
* **Daily Reset:** Option to schedule a daily restart for all configured miners.
* **Visual Feedback:** Progress bars for key metrics (total hashrate, total power, individual miner stats) and visual cues for selected miners.
* **Logging:** Displays timestamped logs of actions, status updates, and errors within the GUI.

## **3. Prerequisites & Installation**

* **Python 3.x:** Ensure you have Python 3 installed.
* **Required Python Libraries:**
    * `requests`: For making HTTP API calls to the miners.
    * `tkinter`: For the GUI (usually included with standard Python installations).
    * You can install `requests` using pip:
        ```bash
        pip install requests
        ```
* **Network Access:** The computer running this script must have network access to the Bitaxe miners you intend to control.
* **Bitaxe Firmware:** Assumes Bitaxe firmware with the standard HTTP API (e.g., `/api/system`, `/api/system/info`, `/api/system/restart`).

## **4. How to Run the Script**

1.  **Save the Script:** Save the Python code as a `.py` file (e.g., `BitaxeTempOC.py`).
2.  **Configuration File (`tuner_config.json`):**
    * When you run the script for the first time, it will create a `tuner_config.json` file in the same directory if one doesn't exist. This file will be populated with default settings.
    * You can manually edit this file, but it's generally recommended to configure settings through the GUI.
3.  **Execute the Script:**
    * Open a terminal or command prompt.
    * Navigate to the directory where you saved the script.
    * Run:
        ```bash
        python BitaxeTempOC.py
        ```
    * The GUI application should then start.

## **5. Understanding `tuner_config.json`**

This JSON file stores all your settings. Here's a brief overview of its structure:

* **`mode`**: Currently fixed to `"temp_target"`.
* **`temp_target_settings`**: Global defaults for temperature tuning. These can be overridden per miner.
    * `target_temp`: Desired core temperature (°C).
    * `temp_step_interval`: How often (in seconds) the script checks temperature and makes adjustments.
    * `temp_freq_step_down`, `temp_volt_step_down`: How much to decrease frequency/voltage if core temp is too high (Note: For the specific case where `temp >= target_temp`, voltage is always dropped by 10mV and frequency is unchanged, overriding these).
    * `temp_freq_step_up`, `temp_volt_step_up`: How much to increase frequency/voltage if core temp is below target (and outside tolerance).
    * `temp_tolerance`: A buffer (°C) below `target_temp`. No adjustments are made if the temperature is within `target_temp - temp_tolerance` and `target_temp`.
    * `vrm_temp_threshold`, `vrm_volt_step_down`, `vrm_freq_step_down`: Parameters for VRM temperature protection.
    * `safe_min_operational_voltage`: A fallback minimum voltage.
    * `voltage_bump_600mhz_target`, `frequency_for_voltage_bump`: Settings for the voltage bump rule (e.g., when frequency reaches 600MHz).
* **`daily_reset_enabled`**, **`daily_reset_time`**: For the optional daily miner restart.
* **`flatline_detection_enabled`**, **`flatline_hashrate_repeat_count`**: For hashrate flatline detection and restart.
* **`miners`**: An array of miner objects. Each miner object has:
    * `ip`: The IP address of the miner ( **required** ).
    * `nickname`: A user-friendly name for the miner.
    * `enabled`: `true` if this miner should be actively managed by the script, `false` otherwise.
    * `min_freq`, `max_freq`, `min_volt`, `max_volt`: **Required** operational boundaries for the miner.
    * Other settings like `target_temp`, `autoFanSpeed`, `invertFanPolarity`, `flipScreen`, etc., which can override the global defaults for that specific miner.

## **6. Using the GUI**

### **6.1. Main Window**

* **Miner List:** The central table displays information for each configured miner.
    * You can right-click a miner for a context menu with more options.
    * Left-clicking a miner selects it.
* **Control Buttons (Top Row):**
    * **Scan Network:** (Simulated) Opens a dialog to input an IP range. Actual scanning is not implemented; this is a placeholder.
    * **Add Miner:** Opens a dialog to add a new miner to the configuration.
    * **Remove Miner:** Deletes the selected miner(s) from the configuration.
    * **Global Settings:** Opens a window to configure global application and tuning parameters.
    * **Set Param.:** Opens a window to configure per-miner temperature target settings in a table format.
    * **Save Config:** Saves the current miner list (IPs, nicknames from the tree) to `tuner_config.json`. This is useful if you've manually added/removed miners or changed nicknames in the main list.
* **Tuning Controls (Second Row):**
    * **Tuning Mode Display:** Shows the current mode (fixed to "Temperature Target").
    * **Start Tuning / Tuning Running:** Starts the temperature targeting process for all *enabled* miners. Button text and state change when running.
    * **Stop Tuning:** Stops the tuning process for all miners.
    * **Shut Down:** Gracefully stops all processes and closes the application.
* **Log Output (Bottom Left):** Displays timestamped messages about script operations, API calls, and errors.
* **Monitor Bars (Bottom Right):**
    * **Total Hashrate & Power:** Progress bars and value labels for the sum of hashrate and power from all *enabled* miners.
    * **Individual Miner Bars:** For each *enabled* miner, displays progress bars for Core Temperature, Frequency, Voltage, and Fan RPM.

### **6.2. Adding a Miner**

1.  Click the **"Add Miner"** button.
2.  Enter a **Nickname** (optional) and the **IP Address** (required) of the Bitaxe.
3.  Click **"Add Miner"**.
4.  The miner will be added to `tuner_config.json` with default settings and appear in the main list. You should then edit its specific configuration.

### **6.3. Removing a Miner**

1.  Select one or more miners in the main list.
2.  Click the **"Remove Miner"** button.
3.  Confirm the deletion.

### **6.4. Global Settings**

1.  Click the **"Global Settings"** button.
2.  Navigate through the tabs:
    * **Global Temp Tuning Tab:** Adjust default parameters that apply to all miners unless overridden at the miner level.
    * **Other Global Tab:** Configure daily reset and flatline detection.
3.  Click **"Save Global Settings"** to apply and save.

### **6.5. Per-Miner Temperature Target Settings ("Set Param.")**

1.  Click the **"Set Param."** button.
2.  This window provides a table view to quickly edit key temperature tuning parameters for all miners.
3.  **Enable:** Check this box to include the miner in the automatic tuning process.
4.  Modify values as needed. Empty fields for numeric settings usually mean the miner will inherit the global default.
5.  Click **"Save All Miner Temp Settings"** to apply and save.

### **6.6. Editing Individual Miner Configuration**

1.  Right-click on a miner in the main list.
2.  Select **"Edit Miner Config"**.
3.  A detailed window appears allowing you to configure:
    * **General:** Nickname, IP (read-only here), Enabled status, Flip Screen.
    * **Fan Control:** Auto Fan Speed, Invert Fan Polarity.
    * **Core Temp Tuning:** Min/Max Freq/Volt, Target Temp, Step Intervals, Tolerance.
    * **Advanced Temp Tuning:** VRM settings, voltage bump rules.
4.  Click **"Save Config"** to apply and save changes for that specific miner.

### **6.7. Fan Control**

1.  Right-click on a miner in the main list.
2.  Select **"Control Fan"**.
3.  Adjust settings:
    * **Mode:** Choose "Auto Fan" (miner controls fan speed) or "Manual Fan".
    * **Manual Speed (%):** If "Manual Fan" is selected, use the slider to set the desired fan speed (0-100%). The current RPM is displayed.
    * **Invert Fan Polarity:** Check if your fan requires inverted polarity.
4.  Click **"Apply Fan Settings"**. This sends the command to the miner and updates the miner's `autoFanSpeed` and `invertFanPolarity` settings in `tuner_config.json`.

### **6.8. Starting/Stopping Tuning**

* Click **"Start Tuning"** to begin the automatic temperature management for all enabled miners. The button will change to "Tuning Running" and become disabled.
* Click **"Stop Tuning"** to halt the automatic adjustments. The "Start Tuning" button will become active again.

### **6.9. Other Context Menu Options (Right-click on miner)**

* **Refresh Miner Data:** Manually fetches and updates the display for the selected miner.
* **Restart Miner:** Sends a restart command to the selected miner.
* **Open Miner Web UI:** Opens the selected miner's IP address in your default web browser.

## **7. Core Tuning Logic (Temperature Target Mode)**

For each enabled miner, a separate monitoring thread performs the following:

1.  **Initial Setup:**
    * Loads all relevant tuning parameters (target temperature, min/max freq/volt, step values, etc.) from the configuration, respecting miner-specific overrides.
    * Ensures initial frequency and voltage are within the configured `min_freq`/`max_freq` and `min_volt`/`max_volt` bounds.
    * Applies these initial settings to the miner.

2.  **Monitoring Loop (Repeats every `temp_step_interval` seconds):**
    * Fetches current stats from the miner (core temp, VRM temp, frequency, voltage, etc.).
    * Logs the current status.
    * **Flatline Check:** If enabled, and the hashrate has been identical for `flatline_hashrate_repeat_count` consecutive checks, the miner is restarted.
    * **Tuning Adjustments (only if `autoFanSpeed` is true for the miner):**
        * **Priority 1: VRM Temperature High:**
            * If `vr_temp > vrm_temp_threshold`, significantly reduces frequency by `vrm_freq_step_down` and voltage by `vrm_volt_step_down` (respecting min limits).
        * **Priority 2: Frequency-Based Voltage Bump:**
            * If `current_frequency >= frequency_for_voltage_bump` (e.g., 600MHz) AND `current_voltage < voltage_bump_600mhz_target` (e.g., 1200mV), the voltage is increased by `temp_volt_step_up` towards the `voltage_bump_600mhz_target`, capped by `max_volt`.
            * Frequency is not changed in this step.
        * **Priority 3: Core Temperature At or Above Target (`temp >= target_temp`):**
            * **Action:** Decrease core voltage by a fixed **10mV**.
            * **Frequency:** Remains **unchanged**.
            * The voltage will not go below `min_volt`.
            * This logic overrides the configured `temp_freq_step_down` and `temp_volt_step_down` for this specific condition.
        * **Priority 4: Core Temperature Too Low (`temp < target_temp - temp_tolerance`):**
            * Attempts to increase frequency by `temp_freq_step_up` (if not at `max_freq`).
            * If frequency cannot be increased, attempts to increase voltage by `temp_volt_step_up` (if not at `max_volt`).
        * **Core Temperature Within Tolerance:** No adjustments are made.
    * **Apply Settings:** If any settings were changed by the logic above, and the new calculated values are different from the current ones, they are applied to the miner via an API call. The frequency and voltage are always clamped within the `min_freq`/`max_freq` and `min_volt`/`max_volt` configured for the miner.

## **8. Troubleshooting & Notes**

* **API Errors:** If the script reports errors connecting to miners or decoding API responses:
    * Verify the miner's IP address is correct in the configuration.
    * Ensure the miner is powered on and connected to the network.
    * Check if you can access the miner's web UI from the computer running the script.
    * Ensure the Bitaxe firmware API is responding as expected.
* **Configuration Not Saving:** Ensure the script has write permissions in the directory where it's located, as it needs to create and update `tuner_config.json`.
* **"Missing required miner settings":** If you see this error for a miner, it means essential fields like `min_freq`, `max_freq`, `min_volt`, or `max_volt` are missing or empty in its configuration. Edit the miner (via right-click -> "Edit Miner Config" or "Set Param.") to provide these values.
* **Tuning Behavior:** The effectiveness of the tuning depends heavily on the thermal solution of your Bitaxe, ambient temperature, and the specific characteristics of your ASIC. You may need to experiment with `target_temp`, step intervals, and min/max values to find optimal settings.
* **Console Output:** The script also prints log messages to the console, which can be helpful for debugging if the GUI is unresponsive or closed.

---

This Readme should help users understand and operate the Bitaxe Dynamic Temperature OC Controller script.
