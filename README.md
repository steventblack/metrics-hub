# metrics-hub
Docker compose file for setting basic components for collecting and storing metrics for a home network. 

This is intended to handle the setup of Prometheus for storing the metrics and Grafana for dashboards and display. It also includes working examples for several additional services that may be relevant for a home monitoring hub, including monitoring a Synology NAS, Linux (Raspberry Pi) nodes, PiHole DNS servces, Unifi access points, and a Tesla PowerWall. These additional services can be modified or removed as necessary.

## Prometheus

### prometheus
### node_exporter
### synology
### pihole
### unifi
### powerwall

## Grafana

Grafana requires a admin username and password to be specified. This is provided using the docker secrets facility. The compose file looks for two files for the necessary information: `grafana/graf-admin.txt` and `grafana/graf-password.txt`. These files are **not** contained within the repository for security reasons and **must** be created separately. The `.gitignore` file has already been configured to ignore these files in order to prevent accidental inclusion into a public repository.

Required configuration:
- create `grafana/graf-admin.txt` with the desired administrative username
- create `grafana/graf-password.txt` with the desire admistrative password

Optional configuration:
- additional provisioned dashboards can be saved in the `grafana/dashboards` directory in JSON format
- provisioned dashboards may be removed from the `grafana/dashboards` directory if not needed
- updates to a provisioned dashboard must be re-exported and saved in the `grafana/dashboards` directory

## SNMP

The `snmp_exporter` is an optional service and should be removed if SNMP is not used for collecting device metrics. This service utilizes the open source `prom/snmp-exporter` utility to collect the SNMP data and store it in Prometheus. The `snmp_exporter` service references the `snmp/snmp.yml` file for decoding the SNMP data. This file is the output of the `snmp-generator` tool. For more information on the configuration and use of `snmp_exporter`, please see the Github documentation for [snmp_exporter](https://github.com/prometheus/snmp_exporter) and [snmp_generator](https://github.com/prometheus/snmp_exporter/tree/main/generator).

The `snmp/snmp.example` file is a prebuilt `snmp.yml` with many common MIBs already included. If the devices requiring SNMP service are already included, it can be renamed to `snmp/snmp.yml` and used as is.

Required configuration:
- identify and download any MIBs that may be necessary for processing SNMP information from the device(s)
- generate the `snmp.yml` file using the generator tool of `snmp_exporter` specifying the MIBs necessary
- modify the `snmp.yml` with any authentication data that may be needed (community strings, auth usernames/passwords, etc.)
- copy the `snmp.yml` file into the `snmp` directory 

If SNMP is not used:
- remove the `snmp_exporter` block from the `compose.yml` file
- remove the `synology` job block from the `prometheus/prometheus.yml` file

