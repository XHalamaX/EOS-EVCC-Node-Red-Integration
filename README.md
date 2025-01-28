# Node-Red Flow for Energy Optimization with EOS and EVCC Integration

This repository contains a `flows.json` file that includes a Node-Red flow designed for energy optimization. The flow retrieves data from an InfluxDB database, processes it using the `/optimize` endpoint from [EOS](https://github.com/Akkudoktor-EOS/EOS). Later it should prepares the results for integration with [EVCC](https://github.com/evcc-io/evcc).

## Features
- Fetch data from an InfluxDB instance.
- Perform energy optimization using the EOS `/optimize` API.
- Write the optimization results back to an InfluxDB bucket.

## PLANNED Features
- Integration with EVCC

---

## Setup Instructions

### 1. Install Node-Red Dependencies
To ensure the flow functions correctly, you need to install the required Node-Red package:
1. Open Node-Red.
2. Click on the **burger menu** (top-right corner).
3. Select **Manage Palette**.
4. Search for `node-red-contrib-influxdb`.
5. Click **Install**.

### 2. Configure InfluxDB Nodes
The flow includes InfluxDB nodes that need to be configured:
1. Locate an InfluxDB node in the flow.
2. Double-click the node.
3. Adjust the InfluxDB settings (e.g., host, port, database name, and authentication details) to match your environment.

### 3. Configure Environment Variables
This flow uses environment variables for flexibility in IP and port configurations:
1. Double-click the flow name (top-left of the Node-Red editor).
2. Click the **list icon** located below "Done" and next to the settings icon.
3. Adjust the IP and port variables to match your setup.

---

## Writing to InfluxDB
The flow writes optimization results to an InfluxDB bucket. By default, it expects a bucket named `eos`. Ensure this bucket is created and properly configured before running the flow.

---

## Additional Notes
- The flow assumes that the `/optimize` endpoint of EOS is accessible and properly configured.
- Integration with EVCC is planned for future updates to streamline energy management for EV charging.
- It's recommended to write EVCC data to InfluxDB. This way you get easily all needed information from InfluxDB. See [EVCC InfluxDB Integration](https://docs.evcc.io/docs/reference/configuration/influx)

See also [Node-Red](https://nodered.org/docs/) and [EOS](https://github.com/Akkudoktor-EOS/EOS).
