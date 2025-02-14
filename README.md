# SOC Homelab

## Objective

This SOC Homelab project aims to detect and notify SOC Analysts regarding brute-force attempts to SSH into an endpoint machine. Due to the nature of external network scanners, all telemetry generated and logged within the Security Information and Event Management (SIEM) system is -genuine-, and is meant to serve as a honeypot VM. This hands-on lab was made to deepen my experience and understanding of security, alerts, attack patterns, and overall flexibility of programs used for defense.

I have also generated Mimikatz telemetry to create custom alerts to detect malware activity.

### Skills Learned

- Deepened understanding of SIEM, SIEM programs, and SIEM functions via practical application.
- Proficiency in analyzing logs and alert information.
- Ability to utilize SOAR functions to enrich IOCs, via hashes and IP addresses.
- Enhanced knowledge of network protocols and security vulnerabilities.
- Self-learning and navigation of cloud technologies, API, and interconnection between VMs.

### Tools Used

- DigitalOcean for Linux server and endpoint set up.
- Wazuh for Security Information and Event Management (SIEM) capabilities like log ingestion and analysis.
- Cassandra for the Wazuh database.
- Elasticsearch as an indexer for Wazuh.
- TheHive for Security Incident Response, and testing case management.
- Shuffle as a Security Orchestration, Automation, and Response (SOAR) tool for IOC enrichment, data organization, and ingestion from Wazuh.

## Steps
*Homelab Network Diagram*

![sochomelab diagram](https://github.com/user-attachments/assets/17a3e9a4-2c50-43f5-ae38-349a3f3095e5)

- Initial setup involves mapping out the homelab and how the cloud components and services should be structured.
- I utilized DigitalOcean to create the Linux Ubuntu 22.04 machine, TheHive server, and Wazuh server.
