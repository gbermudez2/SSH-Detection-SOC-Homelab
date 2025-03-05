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
- Cassandra for the TheHive database.
- Elasticsearch as an indexer for TheHive.
- TheHive for Security Incident Response, and testing case management.
- Shuffle as a Security Orchestration, Automation, and Response (SOAR) tool for IOC enrichment, data organization, and ingestion from Wazuh.

## Steps
### *Homelab Network Diagram*

![sochomelab diagram](https://github.com/user-attachments/assets/17a3e9a4-2c50-43f5-ae38-349a3f3095e5)

- Initial setup involves mapping out the homelab and how the cloud components and services should be structured.
- I utilized DigitalOcean to create the Linux Ubuntu 22.04 machine, TheHive server, and Wazuh server.
  - *It's important to note that the servers have Firewalls that block all incoming traffic EXCEPT from my own public IP to prevent unwanted connections.*

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

### *TheHive Server installation*

![image](https://github.com/user-attachments/assets/c71b31d1-9a9b-4879-92f7-01c48de56eb4)
![image](https://github.com/user-attachments/assets/caa7e337-e59f-4f81-9775-09cbf2da29f5)

- It's important to note that TheHive requires a stack of dependencies to function properly.
- The stack I used was **Cassandra** for the database, **Elasticsearch** for indexing, and **Wazuh** for log collection.
- Java is also required.
- Logging in requires using [server-IP:9000].
- These tools culminate into TheHive, which serves as a case management system for SOC Analysts to manage cases.
- Admittedly, I did not get it to work much, which I will talk about later in the Shuffle section, but I was able to get it to display dedicated cases and create users/organizations.

### Firewall Configuration

![image](https://github.com/user-attachments/assets/4ac20178-da02-4c97-a5fd-1c5089b7ef0b)

- The nature of bots and scanners is that they will always try to find a possible password combination to get into your machine.
- However, I want this happen. So I have _intentionally_ created a "honeypot firewall" on DigitalOcean to allow all inbound TCP connections on all ports.

## Wazuh Alert Configuration

![image](https://github.com/user-attachments/assets/cd87b64d-f719-4b05-adf9-4cbc03fd7588)

- It's vital for a custom rule to be appended to the rule file, or else Wazuh won't display the relevant alerts!
- Opening the local.rules file in Wazuh's dashboard, we can add the above rule to the file to display all level 5 alerts.
  - All SSH bruteforce attempts are classed as level 5 alerts, so all of them will be classed as such and displayed by Wazuh.
  - By default, Wazuh also classifies them as T1110.001 and T1021.004; Password Guessing and Remote Services, respectively.

![image](https://github.com/user-attachments/assets/c923881d-a8a9-4262-b259-579c482f0c2c)

![image](https://github.com/user-attachments/assets/d6db3210-8e9d-4ed4-bd05-c2dd301fdb18)

- At the time of writing (February 17), there have been over 10 million alerts.
  - The memory of my server has been used fully, so no more alerts can be sent.
  - This has caused some issues on launching the Wazuh dashboard. It can be mitigated by stopping the wazuh-dashboard service, clearing the wazuh-registry.json file, and restarting the wazuh-dashboard service (with a cleared browser cache).

## Shuffle SOAR Automation

![image](https://github.com/user-attachments/assets/ba30bb73-3258-48f2-9b02-675ea6261ed1)

- Using SOAR Automation, I can automate the process of IOC enrichment and send these alerts to TheHive.
- The webhook contains an API that I can paste into the configuration files of Wazuh.

![image](https://github.com/user-attachments/assets/381936ec-a12d-44e3-82f8-6a19b1a4d6e3)

- An important aspect of IOC enrichment is making sure the information you wish to enrich is in a readable format.
  - This is to prevent noise, as well as focus on relevant aspects of threat detection.
  - In my automation, I chose to extract external IPs, and remove any extra characters NOT included in the IP using regex.

![image](https://github.com/user-attachments/assets/c022bfba-11dc-47b2-98d3-1a0dd59cb7dc)

- With this VirusTotal node, I take the extracted IP and input it into VirusTotal using another API (account creation required).
- This is to see if the IP is attached to any malicious botnets, entities, or anything reported as malicious.
  - As far as I could tell, none of the IPs had any information on them, but it's possible there could have been.

![image](https://github.com/user-attachments/assets/a215fb54-e2ba-4884-bce2-ec0a5a8d09d3)

- These IPs are then reported to TheHive and made into a case.
- This is where an issue arose; the **original implementation** was to prompt the Analyst to either allow or block the IP brought up. However, I had some issues curling the Wazuh API key and was unable to continue.
- I've put a pause on this project in the meantime to work on other home-labs, but eventually I'll figure out a method for it.

![image](https://github.com/user-attachments/assets/066f7cd4-23c6-44c5-a3c7-2cdbe51acff4)

- On another test implementation, I was able to forward the VirusTotal data and craft an example email that Analysts can use.

