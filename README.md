### SOAR-EDR-PROJECT-HOME-LAB

## 🛡️ SOAR–EDR Automated Incident Response Playbook

This project demonstrates a real-world Security Operations Center (SOC) workflow that integrates an Endpoint Detection & Response (EDR) platform with a Security Orchestration, Automation, and Response (SOAR) solution to detect threats and automatically isolate compromised machines with analyst approval.

The lab simulates an attack, detects it at the endpoint level, triggers automated alerts, requests human approval, and performs controlled containment — just like a modern SOC environment.

## 🚀 Project Objective

The objective of this project is to design and implement an automated incident response workflow that:

- Detects malicious activity on an endpoint

- Sends detection alerts to a SOAR platform

- Notifies security analysts via Slack and Email

- Allows an analyst to decide whether the machine should be isolated

- Automatically isolates the endpoint if approved

- Verifies and reports the isolation status

This project reflects how security teams combine automation with human decision-making to reduce response time while avoiding accidental disruptions.

## 🧰 Technologies Used
 Tool	                       Role in the Project
- LimaCharlie (EDR)	           : Endpoint monitoring, detection, and response actions
- Tines (SOAR)                 : Workflow automation and playbook orchestration
- Slack             	         : Real-time SOC alert notifications
- Email	                       : Secondary alerting channel
- LaZagne	                     : Simulated credential dumping attack tool
- Windows 10 (Virtual Machine) : Target endpoint for attack simulation
- VirtualBox	                 : Virtual lab environment
- 
## 🖥️ Lab Architecture Overview

The lab consists of a Windows 10 virtual endpoint monitored by LimaCharlie. When suspicious activity is detected, LimaCharlie forwards the event data to Tines through a webhook. Tines processes the event, sends alerts, requests analyst input, and performs automated containment if approved. The final containment status is then reported back to the SOC team.

This architecture demonstrates the full lifecycle of Detection → Alerting → Human Decision → Automated Response → Verification.

## ⚙️ Step-by-Step Implementation
## 1️⃣ Endpoint & EDR Deployment

A Windows 10 virtual machine was deployed in VirtualBox to serve as the monitored endpoint. In LimaCharlie, an installation key was generated, and the sensor installation package was downloaded using the generated link. The sensor was installed on the Windows VM using this key.

After installation, the endpoint appeared in the LimaCharlie Sensors list, confirming that the EDR agent was successfully communicating with the platform and collecting telemetry.

## 2️⃣ Simulating Malicious Activity

To generate realistic security telemetry, the credential recovery tool LaZagne was executed on the Windows endpoint. LaZagne attempts to extract stored credentials, which makes it useful for simulating credential dumping behavior seen in real attacks.

Executing LaZagne produced process creation and command-line telemetry in LimaCharlie. This data was later used to build a detection rule.

## 3️⃣ Detection Engineering in LimaCharlie

A Detection & Response (D&R) rule was created in LimaCharlie to detect execution of lazagne.exe. Real telemetry from the endpoint timeline was used to test the detection logic to ensure accuracy and reduce false positives.

After successful testing, the rule was saved. When LaZagne was executed again, the detection triggered and appeared in the Detections section, confirming the detection rule was functioning correctly.

This step demonstrates practical detection engineering using real endpoint telemetry.

## 4️⃣ Sending Detections to SOAR (Tines)

To automate incident response, LimaCharlie detections were forwarded to Tines using a webhook integration. A webhook was created in Tines, and its URL was configured as an output destination in LimaCharlie.

When the detection rule triggered again, the event data was successfully received by Tines. This established a working pipeline between the EDR and SOAR platforms.

## 5️⃣ Alerting the SOC Team

Within Tines, automated alert actions were configured to notify analysts:

- A Slack message containing detection details

- An email notification with the same information

The alert messages included key forensic information such as:

- Computer name

- Username

- Process name

- Command line arguments

- Timestamp of detection

This ensures analysts have sufficient context to evaluate the threat before taking action.

## 6️⃣ Human-in-the-Loop Decision Page

A Tines Page was created to allow an analyst to make a containment decision. The page presented a simple question:

“Do you want to isolate this computer? (YES / NO)”

This step introduces a human approval checkpoint before performing disruptive response actions like network isolation. This mirrors real SOC procedures where containment must often be approved by an analyst.

## 7️⃣ Automated Endpoint Isolation (If YES)

If the analyst selected YES, Tines triggered an API request to LimaCharlie to isolate the endpoint. This placed the Windows machine into network isolation mode, preventing it from communicating with other systems.

The isolation status was verified in LimaCharlie, where the sensor showed that network isolation was active. A final Slack message was sent to inform the SOC team that the endpoint had been successfully isolated.

This step demonstrates automated containment with verification.

## 8️⃣ No Isolation Path (If NO)

If the analyst selected NO, the system did not isolate the endpoint. Instead, Tines sent a Slack message stating that the machine was not isolated and required further investigation. This maintains visibility while allowing analysts to continue manual investigation.

## 🔄 Final Playbook Workflow

- Suspicious process executes on the endpoint

- LimaCharlie detects the activity

- Detection event sent to Tines via webhook

- Slack and Email alerts sent to analysts

- Analyst decision requested through Tines page

- If YES → Endpoint isolated automatically

- Isolation status verified and reported

This workflow reflects a real-world SOC playbook combining automation and analyst oversight.

## 🎯 Skills Demonstrated

- Endpoint Detection & Response deployment

- Detection engineering using real telemetry

- SOAR playbook design and automation

- Webhook-based platform integration

- API-driven security response actions

- Human-in-the-loop incident response

- Real-time SOC alerting and communication

## 🧠 Key Learning Outcomes

This project highlights how detection, automation, and human decision-making work together in modern security operations. It demonstrates how automated containment must be carefully controlled and verified to ensure both security and operational stability.
