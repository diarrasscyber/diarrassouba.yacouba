## Creating a Brute-Force Detection Alert in Splunk
This lab demonstrates how to configure a Splunk alert to detect brute-force login attacks using Windows Security Event Logs.

**1. Run the Detection Query in Splunk**

In Search & Reporting, paste the following query and press Enter:

index=* (EventCode=4624 OR EventCode=4625)
| bin _time span=5m as minute
| stats count(Keywords) as Attempts, count(eval(match(Keywords,"Échec de l’audit"))) as Failed, count(eval(match(Keywords,"Succès de l’audit"))) as Success by minute Nom_du_compte
| where Attempts>=5 AND Success>0 AND Failed>=2
| eval minute=strftime(minute,"%H:%M")

This search will detect suspicious authentication behavior within a 5-minute window

**2. Save Search as an Alert**

Click Save As

Select Alert

Configure:

Alert Type: Scheduled or Real-time

Condition: When number of results > 0

Click Save

<img width="945" height="416" alt="image" src="https://github.com/user-attachments/assets/b225ffb4-6fee-4b1d-aeb1-fbc212d7e5a5" />

<img width="945" height="405" alt="image" src="https://github.com/user-attachments/assets/b2ff6f1a-3ce4-4d8c-be47-ec584d5eb715" />

**3. Simulate a Brute-Force Attack (Kali Linux)**

On the attacking Kali machine, run a brute-force attack using Hydra:

hydra -L /home/kali/Documents/Wordlist/userlist.txt -P /home/kali/Documents/Wordlist/pass.txt ftp://192.168.103.19


This will trigger multiple failed login attempts against the FTP server.

to see the alert go to splunk of the SIEM1 virtual machine then in ACTIVITY and click on TRIGGERED ALERT







