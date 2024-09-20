# Build-A-SIEM-Lab

## Objectives
- To monitor and log system activities
- Detect suspicious activities and potential threats
- Report and analyse security incidents

## Tools Used
- Elastic Stack (Elasticsearch, Kibana, Logstash)
- Kali Linux VM (Using either VirtualBox)

## Methodology
### Step 1: Setting Up Elasticsearch
The first step is to create a free Elastic account. Once that is done, the next step is to log in to the Elastic Cloud console where you should create an Elasticsearch deployment based on your region and deployment size.

![Screenshot 2024-08-06 232245](https://github.com/user-attachments/assets/d85448c5-42e1-486e-9a66-facee7299fc5)
![Screenshot 2024-08-06 233627](https://github.com/user-attachments/assets/a59213fa-ee0f-446e-ab02-5a99ba6b437a)
![Screenshot 2024-08-06 233807](https://github.com/user-attachments/assets/5de61dba-c2a8-4f50-820f-6aaf7cd676a2)

The cloud provider chosen for the deployment is Amazon Web Service (AWS). The rest of the configurations shown above are at default which will remain that way since they are sufficient to provide optimised performance for SIEM. 

The next step is to go to the “Rules” section and then to “Detection rules (SIEM)”. From there, go to “Add Elastic rules” and install all available rules. Following this, enable all of the installed rules. 

### Step 2: Setting Up the Linux Virtual Machine
The virtual machine platform used for this is VirtualBox and a Kali Linux VM is used. To do this, you would need to download and install the Kali Linux VM from its official site and use VirtualBox to run the virtual machine. The default username and password for the Kali Linux VM is “kali”. 

The next step is setting up the Elastic Agent on the Kali VM to collect logs. 

### Step 3: Setting Up The Agent To Collect Logs
On the Elastic Cloud Console, click on “Add Integrations” to add and install the agent on the Kali VM using the Kali terminal.

![Screenshot 2024-08-07 121535](https://github.com/user-attachments/assets/3a2e0955-2e6d-4ef7-a144-988404963b90)

The agent that is being installed in the Kali VM is Elastic Defend. As for why this agent is chosen, Elastic Defend can prevent complex attacks, detect and rapidly prevent threats in high fidelity, secure cloud workloads, and view terminal sessions. 

![Screenshot 2024-08-07 122247](https://github.com/user-attachments/assets/26a2e229-1c65-4aaf-9f4e-e14fe4b28d9e)

Upon selecting Elastic Defend on the integrations page, you can follow the instructions to install the agent on the Kali VM using the Kali terminal. The first step is to “Add Elastic Defend” as shown below:

![Screenshot 2024-08-07 123246](https://github.com/user-attachments/assets/bf1eb109-85d4-49ff-a757-fce45bcb70b1)

The next step is to install the Elastic Agent on the Linux terminal on the Kali VM as shown below:

![Screenshot 2024-08-07 133642](https://github.com/user-attachments/assets/fee629b0-b220-4865-9a6f-1a93f596eb6e)

Once that is installed, add the integration. Once the integration is added, confirm the incoming data from enrolled agents. This may take a few minutes for the data to get to Elasticsearch.

![Screenshot 2024-09-18 204813](https://github.com/user-attachments/assets/6fe28b86-3d25-4be4-a10c-d50f072c647f)

To check if the Elastic agent is installed, run the following command:
sudo systemctl status elastic-agent.service

![Screenshot 2024-09-18 204926](https://github.com/user-attachments/assets/c12b60e4-b9ce-471f-840a-232902930c51)

As shown above, Elasticsearch is successfully running. 

The next step is to integrate Kibana into the Elastic Agent. To do this, go to “Add Integrations”, and search for Kibana. Once you are on the Kibana page, click “Add Kibana”. This will take you to the configuration page. Keep the default settings as it is, and in terms of where to add this integration, click on “Existing Hosts”, then select the same agent policy as the Elastic Defend. This will integrate Kibana into the Elastic Agent.

![Screenshot 2024-09-18 213433](https://github.com/user-attachments/assets/ec005771-7023-4825-94de-7b6091603235)

The next step is to generate security events using Nmap.

### Step 4: Generate Security Events using Nmap
To generate security events in Elastic, you must run Nmap scans on your Kali VM’s IP address. 

To run a Nmap scan on your Kali Linux maching run the following command:
sudo nmap localhost

![Screenshot 2024-09-18 225436](https://github.com/user-attachments/assets/eef4dbe7-8b73-437f-a784-161fee5cd920)

This scan shows which ports are open.

To run more scans, run the following commands:
sudo nmap -sS localhost 
sudo nmap -sT localhost
sudo nmap -p- localhost 
sudo nmap -A -p- localhost 

These are the following outputs:
![Screenshot 2024-09-18 225605](https://github.com/user-attachments/assets/b2bb8b88-a8ef-4944-a50d-d7302662fffe)

The scans above generate security events. To create more security events, run more Nmap scans.

The next step is to return to the Elastic Console and then to the Logs section to view the Nmap logs.

To filter out the logs to only show Nmap scans, go to Stream in the Logs section, on the search bar, type “process.args: “nmap””. This should give you the following output:
![Screenshot 2024-09-18 225705](https://github.com/user-attachments/assets/fc8d410b-a412-4532-81f6-ac049e94c64f)

The logs above show events concerning Nmap scans. To view more details about each event, click on the three dots next each event to view further information. 

![Screenshot 2024-09-15 222527](https://github.com/user-attachments/assets/e8c6e6f0-31d3-45ab-942b-4c8a868270d2)

The details in the latest event concerning Nmap scans show the commands used in that particular scan to trigger that event.

### Step 5: Creating a Dashboard to Visualise Events
The next step is to go to the dashboard in the Analytics section of the Elastic Console, click “Create Dashboard”, then click “Create visualization”. It should come up as this:

![Screenshot 2024-09-15 225005](https://github.com/user-attachments/assets/c8290941-8208-40bb-85e9-6cdc1e7fec8c)

On the filter section on the right, select the visualisation type that you prefer to view the events in whether it is “Area”, “Line”, “Bar vertical stacked” etc. 

![Screenshot 2024-09-16 130357](https://github.com/user-attachments/assets/96bcc913-c599-40c1-a694-4124b1f871e0)

After selecting your preferred visualisation type, for the horizontal axis, select “@timestamp”, and for the vertical axis, select “Count of Records”. 

The chart should look something like this:

![Screenshot 2024-09-18 230129](https://github.com/user-attachments/assets/593e0915-d541-4710-a2cf-2a628700c425)

The next step is to save the dashboard for future edits.

![Screenshot 2024-09-16 132857](https://github.com/user-attachments/assets/b632071a-b621-4708-ba64-c52f26c24a57)

The next step is to run more Nmap scans. 

![Screenshot 2024-09-18 230319](https://github.com/user-attachments/assets/0ef6931c-8e04-45a1-9a08-a06ad63e461a)

This generated more logs as shown in the bar chart:

![Screenshot 2024-09-18 230404](https://github.com/user-attachments/assets/df467588-572f-4ee0-b7ed-2baac5937aeb)

### Step 6: Generate an Alert

The next step is to create alerts on the Elastic instance to specifically detect Nmap scans. To do this, go to the menu, then to the “Security” section, click “Alerts”. This will take you to the “Alerts” page.

![Screenshot 2024-09-16 135517](https://github.com/user-attachments/assets/20f2014d-332d-40cc-8a61-23ab9b597462)

Once you have arrived on this page, go to “Manage rules”, then click “Create new rule”. Upon doing this, select “Custom query” as the rule type and then insert the following query:
process.args: "nmap" 

![Screenshot 2024-09-20 172618](https://github.com/user-attachments/assets/0e6ec39b-ddd1-4286-ac90-b773fc4e5c06)

Click continue and then name the rule as Nmap Scan and add a description. 

![Screenshot 2024-09-16 141314](https://github.com/user-attachments/assets/42b54bc9-b1c0-4904-b92f-d436d352cb00)

You can select a connector type in the “Rules actions” section. For this project, email is chosen as the connector type. Insert the email you want the alerts to be sent to and also you can customise the subject and  message for the email alert.

![Screenshot 2024-09-16 141752](https://github.com/user-attachments/assets/2ce4e63b-0a3e-4559-9e91-10b0785cc9e0)

The next step is to create and enable the rule. 

After creating and enabling the rule, the next step is to run more Nmap scans to trigger the rule.

Upon running several Nmap scans, this generated over 100 alerts as shown below:

![Screenshot 2024-09-20 173358](https://github.com/user-attachments/assets/9fde875d-8bd2-4890-9917-67919ae56699)
![Screenshot 2024-09-20 173716](https://github.com/user-attachments/assets/bcc04f31-e4ab-4171-a290-541bf18b6b9e)

The detection for the alerts concerning Nmap scans runs every five minutes. This means that when you run a Nmap scan, it will not generate an alert immediately but within the next 5 minutes, you will receive an email or any other connector type that you chose which summarises the number of alerts generated.

## Conclusion
### Challenges
There were several challenges that I have experienced when I was building a SIEM lab using the Elastic console for this project. The first challenge was installing and running the Elastic Agent on the Kali Linux VM. The attempts to follow this step repeatedly failed because this caused the computer, which the VM was running on, to crash. As a consequence of this, this caused the Elastic Agent to be offline and unable to start. As for why the computer crashed, that is because there was insufficient RAM allocated to the Kali VM. To rectify this issue, I allocated 4GB of RAM to the Kali VM using the VirtualBox platform. This is sustainable enough for the Kali VM to install and run the Elastic Agent without compromising the entire system.

Another challenge I faced was when I was running the Nmap scans to generate the events the Elastic console not receiving logs. Initially, the Elastic Agent did not have Kibana and Logstash integrated into the agent so to resolve the issue, I had to integrate both Kibana and Logstash to ensure that the logs are sent to the Elastic console when you run Nmap scans.  

### Evaluation
To conclude, the Elastic SIEM has been successfully implemented and it logs security events generated by the Kali VM. Additionally, the SIEM is able to generate alerts based on certain rules.

## References
- <a href="https://medium.com/@christoff.elce/setting-up-a-home-lab-for-elastic-siem-a-step-by-step-guide-e85f3750eb25">Setting Up a Home Lab for Elastic SIEM: A Step-by-Step Guide</a>
- <a href="https://www.elastic.co/guide/en/security/current/prebuilt-rules-management.html#load-prebuilt-rules">Install and manage Elastic prebuilt rules</a>
- <a href="https://medium.com/@aali23/a-simple-elastic-siem-lab-6765159ee2b2">A Simple Elastic SIEM Lab</a>
- <a href="https://youtu.be/2XLzMb9oZBI?si=42ag2QDgyXRGXwdR">Build a Powerful Home SIEM Lab Without Hassle! (Step by Step Guide)</a>  




























