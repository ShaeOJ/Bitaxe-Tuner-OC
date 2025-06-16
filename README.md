
![Bitaxe_roots_big](https://github.com/user-attachments/assets/8e00b5a7-0150-4e95-b5ca-80e0e34df731)

Application Summary
The Bitaxe Monitor & Tuner is a standalone desktop application designed to give you a comprehensive overview and control over your Bitaxe miners. It provides a user-friendly graphical interface to monitor real-time statistics, manage configurations, and automatically optimize your miners for performance and efficiency.

Key Features:

Real-time Monitoring: View live data for each miner, including hashrate, temperature, frequency, and voltage, presented in an intuitive dashboard with graphical dials.

Auto-Tuning: The application can automatically adjust your miner's frequency and voltage settings to maintain a target temperature. This helps to maximize performance while preventing overheating.

Centralized Configuration: Easily add, remove, and edit the configurations for all your miners from one place. You can set global tuning parameters or override them with specific settings for each individual miner.

No-Hassle Setup: As a standalone .exe file, there's no need for command-line setup or dependency installation. Just run the application to get started.

Activity Logging: An in-app activity log and access to detailed backend logs help you keep track of events and troubleshoot any issues.

User Guide
This guide will walk you through the main features of the application.

1. Getting Started: Adding Your First Miner
When you first launch the application, the dashboard will be empty. You need to add your miners to begin monitoring.

Click the large gear icon in the top-left corner to open the Global Controls menu.

Click the "Add New Miner" button.

A form will appear. Fill in the following details:

IP Address: The network IP address of your Bitaxe miner.

Nickname: A friendly name to help you identify the miner.

Review the other settings, but the defaults are fine to start with.

Click the "Add and Save Miner" button.

The miner will now appear as a card on the main dashboard, and the application will start pulling its data.

2. The Main Dashboard
The main screen is divided into two key areas:

Overall Stats: At the top, you'll see a summary of your entire mining operation, including total hashrate, total power consumption, and the average temperature of all online miners.

Miners Container: This is where you'll find a card for each miner you've added. Each card displays:

Dials: For a quick visual of Hashrate, Temperature, Frequency, and Voltage. The dials change color (green, yellow, red) based on the current value.

Text Stats: More detailed information like VRM Temperature, power usage, and accepted/rejected shares.

Action Buttons: Allow you to Edit, Restart, or Remove the miner.

You can also drag and drop the miner cards to rearrange them in any order you like.

3. Global Controls & Settings
The Global Controls menu (accessed via the gear icon) is your central hub for managing the application.

Refresh All Data: Manually forces an immediate update of the data for all miners.

View/Edit Global Config: Opens a modal where you can set universal parameters that apply to all miners, such as:

General Monitor Settings: API timeouts and temperature unit (°C/°F).

Global Auto-Tuning Settings: Default parameters for the auto-tuner, like target temperatures and adjustment intervals.

Add New Miner: Opens the form to add another miner.

Begin/Stop Tuning!: This is the master switch to start or stop the automatic tuning process for all miners that have auto-tuning enabled in their individual settings.

Mute Sounds: Toggles the UI sound effects on or off.

4. Configuring Individual Miners
To change settings for a specific miner, click the "Edit Miner" button on its card. This will open the Miner Settings modal, where you can configure:

General: Change the IP, Nickname, and enable/disable monitoring or auto-tuning for this miner.

Display Max Values: Adjust the maximum values for the dials on the dashboard for more accurate visual representation.

Auto-Tuning Min/Max Limits: Set the "safe" operating window for the auto-tuner by defining the minimum and maximum frequency and voltage it's allowed to use.

Per-Miner Auto-Tuning Overrides: If you want a specific miner to use different tuning settings from the global configuration, you can set them here. If you leave these fields blank, the miner will use the global defaults.

5. Using the Auto-Tuner
The auto-tuner intelligently adjusts your miner's settings to maintain a target temperature.

Enable Tuning for a Miner: Go to a miner's settings (Edit Miner button) and ensure the "Auto-Tuning Enabled" box is checked. Also, verify that the Min/Max Limits for frequency and voltage are set to reasonable values for your hardware.

Start the Global Loop: From the Global Controls menu, click the "Begin Tuning!" button. The button will turn red and read "Stop Tuning!", and the status indicator will show as "ACTIVE". A background animation will also appear to indicate the tuner is running.

The application will now periodically check the temperature of all eligible miners and make small adjustments to their frequency and voltage to keep them at the target temperature you defined in the global or per-miner configuration.

To stop the process, simply click the "Stop Tuning!" button in the Global Controls menu.

6. Activity Log and Troubleshooting
At the bottom of the dashboard is an Activity Log. This log shows important events, such as when tuning changes are applied, when a miner goes offline, or when configuration is saved.

If you encounter issues, you can use the buttons next to the log:

Clear Log: Clears the messages from the on-screen log.

View Log: Opens a pop-up showing the last 1000 lines from the detailed backend log file. This can be useful for diagnosing more complex problems.

Open Log Folder: Opens the folder on your computer where the application's configuration and log files are stored, giving you direct access for backups or advanced troubleshooting.

---

This Readme should help users understand and operate the Bitaxe Dynamic Temperature OC Controller script.
