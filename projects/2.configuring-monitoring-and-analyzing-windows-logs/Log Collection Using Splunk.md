# Log Collection Using Splunk 

The goal of this lab is to learn how to configure, collect, visualize, and analyze logs from different systems using Splunk Enterprise.
Splunk will allow us to: centralize logs from all machines on the network, analyze events, detect anomalies and cyberattacks.

## Environment — Virtual Machines Used

**Windows Server 2022 (SIEM1)** ➝ Will run Splunk Enterprise

**Windows Server 2019** ➝ Will run Splunk Universal Forwarder

**Ubuntu (SNORT machine)** ➝ Will run Splunk Universal Forwarder

**Kali Linux** ➝ Attacker machine to generate malicious activity

---

# 1️⃣ Configure Splunk Enterprise on **SIEM1**

---

**1. Install Splunk Enterprise on SIEM1**

- Install **Splunk Enterprise** on the Windows Server 2022 machine (SIEM1).  
- After installation, open a browser and go to:http://localhost:8000
- Log in using your Splunk **username** and **password**.

**2. Configure Splunk to Receive Logs from Forwarders**

Go to:

- Settings → Forwarding and Receiving
- Under **Receive Data**, click **Add New**
- Enter receiving port:9997

- Save.

 **3. Enable SplunkForwarder App**

- Go to **Apps → Manage Apps**
- In the list, find **SplunkForwarder**
- Under *Status*, click **Enable**
- Click **Edit Properties**
- Set:

→ **Visible : YES**

- Save.

**4. Restart Splunk Enterprise**

Go to:

- Settings → Server Controls
- Click **Restart Splunk**
After restart → log back in.

**5. Verify Incoming Logs**

Go to:

- Apps → Search & Reporting
- Click on **Data Summary**

You should now see logs coming from:

- **Windows Server 2019** (web server + forwarder)
- **ubuntu** (Ubuntu SNORT + forwarder)

✔️ This confirms all forwarders are sending logs to **SIEM1**.

---

# 2️⃣ Install Splunk Universal Forwarder on Windows Server 2019

-On Windows Server 2019, go to the SOC shared folder or download the Splunk Universal Forwarder

-Launch the installer

-Check the box: Accept the license agreement

-Click Customize Options

-Click Next, Next, Next

-Check all boxes under:

-Windows Event Logs

  Performance Monitor
  then click Next

-Leave the Deployment Server field empty.

-Enter the IP address of the Splunk Enterprise server (SIEM1):192.168.103.22:9997

# 3️⃣ Configure Splunk Universal Forwarder (Windows Server 2019)

Since the web server (IIS) is running on this machine, we must configure specific Splunk settings.

1. Copy default configuration files

Go to: C:\Program Files\SplunkUniversalForwarder\etc\system\default

Copy the following files:

**inputs.conf**
**props.conf**
**transforms.conf**

Paste them into: C:\Program Files\SplunkUniversalForwarder\etc\system\local

2. Modify the configuration files
   
 **Edit inputs.conf file**

Open the file located in system\local

Delete all existing content

Copy and paste this part content from
“Config File 1 — Module 3 script” (from the shared SOC folder) to
C:\Program Files\SplunkUniversalForwarder\etc\system\local\inputs.conf

<img width="675" height="244" alt="image" src="https://github.com/user-attachments/assets/b92bee49-7066-4415-b596-1ea0c9293e42" />


**Configure outputs.conf file**

In the same Config File 1 (Module 3),
copy the corresponding output configuration section.

Paste it into the outputs.conf file located at:C:\Program Files\SplunkUniversalForwarder\etc\system\local\outputs.conf

<img width="678" height="250" alt="image" src="https://github.com/user-attachments/assets/42495521-0fb8-4202-a347-569b607fdacf" />

**Configure props.conf file**

Open the file:

C:\Program Files\SplunkUniversalForwarder\etc\system\local\props.conf

Delete all existing content.

Copy the props.conf section from
Config File 1 (Module 3 script) located in the shared SOC folder.

Paste it into the local props.conf file

<img width="588" height="209" alt="image" src="https://github.com/user-attachments/assets/69160d4a-bc9d-4e9e-bcbb-ce7fddc28da7" />


**Configure transforms.conf file**

Open the file:

C:\Program Files\SplunkUniversalForwarder\etc\system\local\transforms.conf

Delete all existing content.

Copy the transforms.conf section from
Config File 1 (Module 3 script) in the shared SOC folder.

Paste it into the local file

<img width="816" height="502" alt="image" src="https://github.com/user-attachments/assets/77bf1f6c-7a58-4b0c-a6ed-60f0db899f4b" />

**Restart the Splunk Forwarder Service**

Press Windows + R

Type:services.msc

Press Enter to open the Services console.

Look for: SplunkForwarder Service

Right-click → Restart

<img width="945" height="257" alt="image" src="https://github.com/user-attachments/assets/7aa40f2a-799d-444b-853c-a67043a71a29" />

## 4️⃣ Installing Splunk Universal Forwarder on Ubuntu (SNORT VM)

This section explains how to install and configure Splunk Universal Forwarder on an Ubuntu machine running SNORT, allowing SNORT logs to be collected and forwarded to the Splunk Enterprise server.

**Download Splunk Universal Forwarder for Ubuntu**

-Create a Splunk account and download the Linux (.deb) Universal Forwarder package or use the direct download link:

sudo wget -O splunkforwarder-10.0.2-e2d18b4767e9-linux-amd64.deb \
https://download.splunk.com/products/universalforwarder/releases/10.0.2/linux/splunkforwarder-10.0.2-e2d18b4767e9-linux-amd64.deb

-Move the installer into /opt: sudo mv splunkforwarder-10.0.2-e2d18b4767e9-linux-amd64.deb /opt

**Install Splunk Forwarder**

Navigate to /opt and install:
cd /opt
sudo apt install ./splunkforwarder-10.0.2-e2d18b4767e9-linux-amd64.deb

**Accept License and Create Admin User**

sudo ./splunk --accept-license
Enter:
Admin username: admin
Password: P@ssw0rd

-Add the Splunk Enterprise Server as a Forward Destination
sudo ./splunk add forward-server 192.168.103.22:9997

Enter:
Username: admin
Password: P@ssw0rd

-Verify outputs.conf Configuration

-Navigate to the configuration folder:

cd /opt/splunkforwarder/etc/system/local
sudo nano outputs.conf
Do not modify anything here; just verify that the configuration exists Press CTRL + X to exit

**Enable Monitoring of SNORT Logs**

-Go back to the Splunk Forwarder bin directory:

cd /opt/splunkforwarder/bin
sudo ./splunk add monitor /var/log/snort

This enables Splunk to forward all SNORT logs.

**Modify inputs.conf in the Search App**

Navigate to the app directory to modify this file:

cd /opt/splunkforwarder/etc/apps/search/
sudo su
cd local
sudo nano inputs.conf

-before

<img width="490" height="125" alt="image" src="https://github.com/user-attachments/assets/8dfbb0c3-1061-490a-a852-90498287427b" />

-after

<img width="545" height="170" alt="image" src="https://github.com/user-attachments/assets/2c967b30-bd3f-4fdf-ba88-f6377edd1305" />

and save it

**Restart the Splunk Forwarder Service**

Return to the bin folder:

cd /opt/splunkforwarder/bin
sudo ./splunk restart

Your Ubuntu SNORT machine is now successfully forwarding logs to Splunk Enterprise


