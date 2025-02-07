
# Node-Red Flow for Energy Optimization with EOS and EVCC Integration

This repository contains a `flows.json` file that includes a Node-Red flow designed for energy optimization. The flow retrieves data from an InfluxDB database, processes it using the `/optimize` endpoint from [EOS](https://github.com/Akkudoktor-EOS/EOS). Results are used to control home storage and loadpoints via [EVCC](https://github.com/evcc-io/evcc).

## Disclaimer
Use at you own risk, the automation can create unexpected high costs.

## Features
- Fetch data from an InfluxDB instance.
- Perform energy optimization using the EOS `/optimize` API.
- Write the optimization results back to an InfluxDB bucket.

## Overview
### System
![System Overview](https://raw.githubusercontent.com/XHalamaX/EOS-EVCC-Node-Red-Integration/4d2e1b2a27ecc3b30d4871e73a99edf2927e9af5/overview.png)

### Process
![Process Overview](https://raw.githubusercontent.com/XHalamaX/EOS-EVCC-Node-Red-Integration/refs/heads/documentation/process.png)

## Requirements

 - [EOS](https://github.com/Akkudoktor-EOS/EOS)
 - [Node-RED](https://nodered.org/)
 - [InfluxDB](https://www.influxdata.com/)
 - [EVCC](https://evcc.io/) (optional, but recommended)
 - [Telegram](https://web.telegram.org) Account (optional, for status updates)
 - [SOLCAST](https://solcast.com/) Account (optional, until EOS does not provide PV Forecast)

### Data
The main data source is a influxdb. You need:

 - Current SOC from you car.
 - Current SOC from your home storage
 - ~~Power consumtion from the past 48h without loads which will be optimized by EOS~~ (currently ignored, so optional)
 - PV Forecast (will be provides by EOS in future)

The results will be written to influxdb as well. The flow expects a **eos** bucket and you need write access to it.

---

## Setup Instructions

### 1. Install Node-Red Dependencies
To ensure the flow functions correctly, you need to install the required Node-Red package:
1. Open Node-Red.
2. Click on the **burger menu** (top-right corner).
3. Select **Manage Palette**.
4. Search for `node-red-contrib-influxdb`.
5. Click **Install**.

### 2. Import the flow
Before you import the file you can search&replace everyhting which looks like <<<\<ip-replace>>>>
Otherwise you import the flow and do it manually in Node-Red directly (see below).

### 3. Configure InfluxDB Nodes
The flow includes InfluxDB nodes that need to be configured:
1. Locate an InfluxDB node in the flow.
2. Double-click the node.
3. Adjust the InfluxDB settings (e.g., host, port, database name, and authentication details)

### 4. Configure Environment Variables
This flow uses environment variables for flexibility in IP and port configurations:
1. Double-click the flow name (top-left of the Node-Red editor).
2. Click the **list icon** located below "Done" and next to the settings icon.
3. Adjust the IP and port variables to match your setup.

### 5. EOS Config
Your EOS config should contain at least the following entries:

```
"weather_provider":"BrightSky"
"loadakkudoktor_year_energy":7500
"load_provider":"LoadAkkudoktor"
"elecprice_provider":"ElecPriceAkkudoktor"
"latitude":52.2
"longitude":13.4
```
### 6. EVCC Dummy Loadpoint
We don't have the option to block your home storage directly. That's why you need a dummy load point which can be set to "now" so that the home storage disables discharge. Idea found [here](https://github.com/evcc-io/evcc/wiki/aaa-Lifehacks)
The flow expect that this loadpoint is loadpoint number 2 (which is 1 in an array). So if you already have more than one loadpoints, either you change the order or you adjust the flow.

```
chargers:
  - name: automatik
    type: template
    template: demo-charger
    status: C
    enabled: true

loadpoints:
  - title: Automatik
    charger: automatik
    mode: pv
```
### 7. Telegram bot
1. Write `/newbot` to @BotFather
2. Follow the instructions
3. Write a message to your bot
4. Open `https://api.telegram.org/bot<<<<YOUT_TOKEN>>>>/getUpdates` with your browser
5. Copy the **chatID** to the flow environment variables

---
## EVCC Integration - IMPORTTANT  
If everything is up and running you can control via EVCC if EOS should be used.  
1. Set **Automatik** to off - This will deactivate the automation completly. As soon as you did this the flow will not change any EVCC settings.
2. Set the smart costs for your loadpoint 0 to -0.05€ to **activate** the loadpoint automation. This makes sure you can use the logic

Both options make sure you can always switch off the automation if you want via EVCC.

---
## Writing to InfluxDB
The flow writes optimization results to an InfluxDB bucket. By default, it expects a bucket named `eos`. Ensure this bucket is created and properly configured before running the flow.

---
## Limitations, Issues & Bugs
- See https://github.com/XHalamaX/EOS-EVCC-Node-Red-Integration/issues
- PV Forecast: I use "Solcast" to get a PV Forecast. The EOS build in service is not working for me at the moment. See [here](https://github.com/Akkudoktor-EOS/EOS/discussions/248) and search for "solcast"
- Time(zone) issues + Summer and Winter time
- EOS Load prediction issue https://github.com/Akkudoktor-EOS/EOS/issues/428
- EOS is calculating a price prediction. In germany power prices can be different per region. The node **price adjusted from eos** contains two factors which can be adjusted. To get these two factors, download your e.g. tibber prices via [API](https://developer.tibber.com/docs/overview) and take the values from the same timeframe from EOS and ask ChatGPT for support.

## Additional Notes
- The flow assumes that the `/optimize` endpoint of EOS is accessible and properly configured.
- It's recommended to write EVCC data to InfluxDB. This way you get easily all needed information from InfluxDB. See [EVCC InfluxDB Integration](https://docs.evcc.io/docs/reference/configuration/influx)


See also [Node-Red](https://nodered.org/docs/) and [EOS](https://github.com/Akkudoktor-EOS/EOS).
