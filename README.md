# fleet-manager

The Drogue IoT fleet manager is an add-on for Drogue IoT Cloud that allows you to build and manage firmware for a fleet of devices for a Drogue IoT Cloud application.

You can run the fleet manager locally or on Kubernetes (recommended for production).

## Architecture

The fleet manager uses the Drogue IoT Cloud event stream and command API to communicate with devices that have firmware update capability. The Drogue IoT device registry is used to store the 'desired' firmware and version for a given device.

An Open Container Initiative (OCI) registry is used to store images containing the device firmware. This allows reusing existing infrastructure commonly used in Kubernetes. 

Building and deploying firmware to the OCI registry is decoupled from the fleet manager, as long as the expected manifest is part of the container image. As a reference architecture, fleet manager uses [tekton](tekton.dev) pipelines to build firmware, and a local file database for storing an index of image versions that are built (expect this to move to some kind of SQL database eventually).

```
+--------+          +------------------+          +---------------+ 
|        | -------> |                  | -------> |               | 
| Device |          | Drogue IoT Cloud |          | Fleet Manager | 
|        | <------- |                  | <------- |               | 
+--------+          +------------------+          +---------------+ 
                                                          |
                                                          |
                                                          |
                                                          |
              +-------------------------+          +--------------+
              |                         |          |              |
              | Firmware Build Pipeline | -------> | OCI Registry |
              |                         |          |              |
              +-------------------------+          +--------------+
```
