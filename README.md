# lowloris-detection-suricata-wazuh
Real-time SlowLoris DoS detection &amp; auto-block using Suricata, Wazuh &amp; n8n.

# Automated SlowLoris Attack Detection & Mitigation (SOAR Workflow)

A fully automated Active Response security workflow that detects, blocks, and reports SlowLoris HTTP-layer DoS attacks in real time — built entirely on an open-source stack (Suricata + Wazuh + n8n).

This is not just a lab demo — it's a production-style SOAR pipeline that any organization facing HTTP-layer DoS attacks can adapt and deploy.

## Architecture

```
Suricata (IDS) → Wazuh (SIEM) → n8n (poll every 15s) → SSH Active Response (block IP) → Email Notification
```

## How It Works

### 1. Threat Detection — Suricata + Wazuh
Suricata inspects inbound HTTP traffic. When SlowLoris behavior is detected (many slow, incomplete connections from the same source), an alert is generated and forwarded to Wazuh, including:
- Malicious IP
- Signature ID
- Threat category
- Timestamp & event details

This ensures accurate detection with zero false positives.

### 2. Automated Analysis & Trigger — n8n
n8n polls Wazuh every 15 seconds for new security events. Once a SlowLoris signature appears, the workflow:
- Fetches the alert via API
- Extracts and filters the attacker IP
- Applies cooldown logic (to avoid duplicate/repeat alerts)
- Passes the validated IP to the mitigation step

### 3. Automatic Mitigation — SSH Active Response
Once validated, n8n connects to the target server over SSH and:
- Blocks the attacker's IP
- Applies firewall/ACL rules
- Confirms the attack has stopped

The entire response — detection to block — takes seconds, not minutes.

### 4. Instant Email Notification
Once the IP is blocked, an automated email is sent to the security admin with:
- Blocked source IP
- Attack type (SlowLoris)
- Affected device/server
- Timestamp & alert age
- A 30-second cooldown to prevent notification spam

This creates a complete audit trail for SOC review.

## Tools Used

| Component | Role |
|---|---|
| Suricata | IDS/IPS — traffic inspection & signature-based detection |
| Wazuh | SIEM — alert aggregation |
| n8n | Workflow automation / SOAR orchestration & SSH active response |
| Kali (Pentmenu) | Attack simulation for testing (lab only) |

## Why This Matters

Most organizations still rely on manual incident response, which is slow and error-prone. This workflow delivers:

- Faster threat response (seconds vs. minutes)
- Zero-touch automated mitigation
- Seamless integration of IDS + SIEM + SOAR
- Fully open-source — no licensing cost, easy to replicate

## Setup Notes

> Workflow JSON export and Suricata rule file available in this repo — import the workflow directly into n8n and connect your own Wazuh, SSH, and email credentials.

## Disclaimer

This project was built and tested entirely in a controlled, isolated lab environment (Kali, Rocky Linux, Wazuh, Suricata) for learning and demonstration purposes. All IP addresses shown in screenshots are internal lab addresses and have been redacted where necessary.
