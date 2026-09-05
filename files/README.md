# Wazuh SIEM Home Lab — File Integrity Monitoring

A self-built home SIEM lab using Wazuh to monitor a Windows endpoint from a
Linux manager, with real-time file integrity monitoring (FIM) configured and
verified end-to-end.

## Overview

Wazuh is a free, open-source security platform providing log analysis, file
integrity monitoring, intrusion detection, vulnerability detection, and
real-time alerting. This lab covers standing up the core manager/agent
architecture and validating FIM detections from a Windows endpoint.

## Lab Architecture

| Component     | Host                       | Role                                             |
|---------------|----------------------------|---------------------------------------------------|
| Wazuh Manager | Ubuntu Server (VirtualBox) | Collects, analyzes, and stores data from agents   |
| Wazuh Agent   | Windows (host machine)     | Sends logs and system events to the manager       |

Both machines run on a bridged network so the Ubuntu VM sits on the same
subnet as the Windows host.

![Lab topology](screenshots/01-lab-topology.png)

## Build Steps

**1. Install the Wazuh Manager on Ubuntu**
Imported the Wazuh GPG key to verify package authenticity, then installed
the manager and indexer components via the official Wazuh install script.

![GPG key import](screenshots/02-gpg-key-setup.png)

**2. Access the Wazuh Dashboard**
Logged into the web dashboard over HTTPS using the credentials generated
at install time.

![Dashboard login](screenshots/03-dashboard-login.png)

**3. Register the Windows agent**
Generated an agent key on the Ubuntu manager with `manage_agents`, then
imported that key into the Wazuh Agent Manager GUI on the Windows host
along with the manager's IP address.

![Agent registration on the manager](screenshots/04-agent-registration.png)
![Importing the key into the Windows agent](screenshots/05-agent-key-import.png)

Once connected, the agent appeared as active on the manager, confirming
two-way communication.

![Agent active in the dashboard](screenshots/06-agent-active-dashboard.png)

**4. Configure File Integrity Monitoring**
Edited the agent's `ossec.conf` to add a real-time monitored directory
under the `<directories>` block, then restarted the agent service to
apply the change.

![FIM directory configuration](screenshots/07-fim-config.png)

**5. Verify detections**
Created, modified, and deleted files inside the monitored directory and
confirmed matching `syscheck` alerts appeared in the dashboard under File
Integrity Monitoring, tied to the correct agent ID.

![FIM alert in the dashboard](screenshots/08-fim-alert-verification.png)

## Key Takeaways

- How Wazuh's manager/agent model works, and how enrollment (key generation
  → key import → service restart) ties an endpoint into the manager.
- How `ossec.conf` controls agent behavior, and how to scope real-time FIM
  to a specific directory instead of the full default ruleset.
- How to validate a detection end-to-end: trigger an event on the endpoint,
  confirm the alert in the SIEM, and trace it back to the originating agent
  and rule group.

## Notes

IP addresses shown are private/internal lab addresses (RFC1918) and are not
reachable outside the lab network.
