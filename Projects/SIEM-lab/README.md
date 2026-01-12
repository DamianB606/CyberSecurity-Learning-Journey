# Wazuh SIEM Home Lab (VMware)
## Overview
I built a small SOC-style lab in **VMware** using **Wazuh SIEM** to centralise endpoint telemetry from **Windows and Linux agents**. The goal was to get hands-on experience with a basic SIEM setup and confirm that endpoint agents can send logs to a central manager, where alerts can be generated and reviewed.

## Lab architecture
**Environment:** VMware (isolated virtual network)

**Components:**
- **Wazuh Manager (Linux Ubuntu):** SIEM Server + dashboard
- **Windows Endpoint (Wazuh Agent):**
	- **Agent Name:** WindowsHost
- **Linux Endpoint (Wazuh Agent):**
	- **Agent Name:** UbuntuUser

**Evidence**

'01-agents-connected.png' - agents enrolled and reporting

**Note:** To onboard and initialise agents requires administrative privileges. However, once completed, this applies to the whole system. Then you can create user accounts without those privileges, continue receiving logs, and telemetry. The detections below were intentionally generated to validate alerting and practice SOC-style triage.

## What I wanted to prove
- Endpoints are successfully enrolled and **sending logs** to the Wazuh Manager.
- Wazuh can generate **alerts** when I create a simple test activity on the endpoints.

## What I tested

### Test 1: Failed logons (Windows)
**What I did:** I entered an incorrect password multiple times for a Windows account within a short period.

**What Wazuh showed:** An alert for repeated logon failures with the time, technique ID, impact level, and rule ID shown.

**Why it matters:** Failed logons can be a normal user error, but repeated attempts in a short time window can indicate password guessing.

**Screenshots**
- '02-alert-failed-logons.png'

### Test 2: Disabled Wazuh Agent (Linux)
**What I did:** I stopped the Wazuh Agent service on the Linux endpoint briefly, then restarted it.

**What Wazuh showed:** The alert showed that the agent stopped and two minutes later started (by looking at the timestamps).

**Why it matters:** Loss of telemetry reduces visibility. In a real environment, this would be investigated quickly to determine whether it was a benign issue or deliberate tampering.

**Screenshots**
- '03-alert-agent-stopped.png'

## Alert review
This section documents how I reviewed alerts in Wazuh using test activity. The goal is to show a basic SOC-style checklist to determine whether an alert looks low or high risk.

### Alert: Repeated failed logon attempts (Windows)
**What I captured from the alert**
- Endpoint: **Windows Agent** ('WindowsHost')
- Time windows : 17:57:36 - 17:57:45
- Count: 3 failed attempts

**What I checked (simple triage checklist)**
- **Frequency:** Were the attempts tightly grouped or just a few attempts?
- **Scope:** Was it one account, or multiple usernames being targeted?
- **Outcome:** Did a successful login occur after the failures?
- **Context:** Was this during normal usage time, or unusual timing for this account?
- **If origin data is present:** Was the source expected for the lab environment? 

**How I would judge severity**
- **Lower concern:** small number of failures, single user, no follow-up success, consistent with user error.
- **Higher concern:** high frequency, multiple users targeted, repeated activity, or success after failure.

## What I learned
- An SIEM alert needs **context** (user, timing, frequency, source) before deciding severity.
- "Agent offline" is important because loss of telemetry reduces visibility.
- Keeping short notes and screenshots makes it easier to explain what happened.
