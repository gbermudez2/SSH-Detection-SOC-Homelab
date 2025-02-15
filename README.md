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
### *Homelab Network Diagram*

![sochomelab diagram](https://github.com/user-attachments/assets/17a3e9a4-2c50-43f5-ae38-349a3f3095e5)

- Initial setup involves mapping out the homelab and how the cloud components and services should be structured.
- I utilized DigitalOcean to create the Linux Ubuntu 22.04 machine, TheHive server, and Wazuh server.

### *Wazuh Server installation*

![image](https://github.com/user-attachments/assets/97a4e9e5-5fab-4ebc-9074-160b1920788e)
![image](https://github.com/user-attachments/assets/f8b216ee-a575-4103-b673-be56375b76b7)

- I installed the Wazuh dashboard first on my cloud server.
  - curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh
  - bash ./wazuh-install.sh -a
- Following installation, I could access the dashboard using the server's public IP.

### *Wazuh Endpoint Installed on Ubuntu 22.04 Endpoint*

![image](https://github.com/user-attachments/assets/33d0e817-ff2c-4b30-a9ae-948c98c7d7d1)
![image](https://github.com/user-attachments/assets/0e2a4450-ef1c-4e35-9559-56a7144511ee)
![image](https://github.com/user-attachments/assets/853e6a9b-538a-4b46-b406-35c7d17dc6b7)

- The Wazuh Agent was installed onto the Ubuntu 22.04 endpoint.
- The Wazuh dashboard has a pretty streamlined process regarding installing an agent, even providing the command that would link the server to the endpoint.
- Unfortunately, due to some unknown circumstances, the command did not work, and I had to manually enter in the server IP into the ossec.conf file. The ossec.conf file is the main client-side Wazuh configuration file. This is to forward any incoming traffic from the endpoint to the server.



*Under Construction*
