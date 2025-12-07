#  Windows Local Logging and IIS Lab  
## Configure, Monitor, and Analyze Windows Security Logs

The objective of this lab is to learn how to:

- Configure Windows local logging  
- View security events  
- Analyze attack attempts (bruteforce)

This lab uses two virtual machines:

-  **Windows Server 2019** (target)
-  **Kali Linux** (attacking machine)

---

# Enable Logon Auditing on Windows

On **Windows Server 2019**:

1. Open:  
   **Windows Administrative Tools → Local Security Policy**
3. Navigate to:  
   **Local Policies → Audit Policy**
4. In the right panel, double-click:  
   **Audit logon events**
5. Check both options:  
   - ✔️ Success  
   - ✔️ Failure  
6. Click **Apply**, then **OK**
<img width="945" height="394" alt="image" src="https://github.com/user-attachments/assets/f25f9236-3865-4f09-8249-130bd37f5041" />

---

# 2️⃣ Perform a Bruteforce Attack from Kali

On the **Kali Linux** or **Parrot OS** machine, launch a FTP bruteforce attack using Hydra:

hydra -L /home/kali/Documents/Wordlist/userlist.txt \
     -P /home/kali/Documents/Wordlist/pass.txt \
     ftp://192.168.103.19

On the **Windows Server 2019** machine:

1. Right-click the **Start Menu**
2. Open **Event Viewer**
3. In the left panel, navigate to:  
   **Windows Logs → Security**
<img width="945" height="257" alt="image" src="https://github.com/user-attachments/assets/7e1889ed-1943-4ab6-a132-a9ed283b5391" />

---

## Filter Security Events

1. In the right panel, click **Filter Current Log**
2. In the Event ID field, enter: 4625
<img width="945" height="543" alt="image" src="https://github.com/user-attachments/assets/61489d90-cc19-4932-b71f-aea05f0ee070" />

3.You will now see all failed logon attempts, including the bruteforce activity
<img width="945" height="543" alt="image" src="https://github.com/user-attachments/assets/af95dc07-d97a-49e6-809d-8cf60063f48d" />

4.Windows log files are stored at:
C:\Windows\System32\winevt\Logs
<img width="1171" height="705" alt="image" src="https://github.com/user-attachments/assets/65248de4-baa4-4057-88bc-ff58183c2544" />

---

# 🌐 IIS Logging Lab  
## Configure, Monitor, and Analyze IIS Web Server Logs

The objective of this lab is to learn how to:

- Configure IIS logging  
- Visualize IIS logs  
- Detect malicious web activity (SQL Injection)

This lab uses the following environment:

-  **Windows Server 2019** (hosting IIS)
-  **Kali Linux / Parrot OS** (attacking machine)
-  **Web application:** `www.luxurytreats.com`

---

# 1️⃣ Configure IIS Logging

On **Windows Server 2019**:

1. Open **Administrative Tools**
2. Launch **Internet Information Services (IIS) Manager**
3. In the left panel, expand:
   - `windows19`
   - `Sites`
   - <img width="945" height="389" alt="image" src="https://github.com/user-attachments/assets/3b9ab754-6cb2-4542-9024-2d19af56c79f" />

4. Click on **LuxuryTreats**
5. Double-click **Logging**
6. Click **Select Fields**
7. Check **all fields** to enable full logging
8. Click **Apply**
<img width="945" height="410" alt="image" src="https://github.com/user-attachments/assets/67b15b72-e5cd-4831-a8c6-37d10dfc1662" />

---

# 2️⃣ Perform a Web Attack from Kali / Parrot

On the **Kali / Parrot** machine:

1. Open a web browser  
2. Enter the URL:www.luxurytreats.com
3. Log in using:

- **Username:** bob  
- **Password:** Passw0rd

4. After logging in:
- Click **My Order**
- Select **ORD-001**
<img width="945" height="313" alt="image" src="https://github.com/user-attachments/assets/514dd78b-788a-4543-9b77-40b36fe9eef2" />

---

# 3️⃣ Attempt an SQL Injection

In the search or input field, enter: ' or 1=1;
<img width="945" height="46" alt="image" src="https://github.com/user-attachments/assets/694df2c9-f7f9-4030-a8b9-8b7c79e567af" />

If the site is vulnerable, you will gain access to **all of Bob's orders**, demonstrating an SQL Injection flaw.
<img width="945" height="352" alt="image" src="https://github.com/user-attachments/assets/1b35cfbe-f844-4fa1-99c8-d8a3a94419fe" />

---

# 4️⃣ Analyze the IIS Logs

Back on **Windows Server 2019**:

1. Navigate to the IIS log directory (usually inside `C:\inetpub\logs\LogFiles`)
2. Open the latest log file using **Notepad++**
3. Search for the SQL injection payload:1=1;
<img width="945" height="591" alt="image" src="https://github.com/user-attachments/assets/f118ae19-b338-4e48-a132-e7ae64b0b9dd" />


