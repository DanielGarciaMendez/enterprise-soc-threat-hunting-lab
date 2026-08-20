# What is WAZUH?
Wazuh is an open-source security monitoring/SIEM platform that collects and analyzes security telemetry from endpoints. In this lab, Wazuh will act as the central location where I can view Windows and Sysmon events, investiagate alerts, correlate activity between systems, and building custom detections. 

Endpoints use a Wazuh agent to send their security data to the Wazuh server, where the data will be analyzed and viewed!


There is a WAZUH agent that has three central compoinets: 
1. Wazuh Server
2. Wazuh Indexer 
3. Wazuh Dashboard 

# What is telemetry?
It is just data that a computer/system generated about what it is doing...

# Why are we using Sysmon if a Wazuh agent can also collect and send data?
The Wazuh agent can collect security information by itself, but Sysmon provides richer Windows endpoint telemetry. Sysmon records detailed process, network, file, and other system activity, while the Wazuh agent collects those Sysmon events and sends them to the Wazuh server for centralized analysis.