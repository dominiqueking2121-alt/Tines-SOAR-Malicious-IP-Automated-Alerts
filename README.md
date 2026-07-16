# SOAR Automation & Incident Response Pipeline 
### Ingesting, Analyzing, and Alerting on Malicious IPs with Tines & VirusTotal API

---

## 💻 Project Overview
This hands-on security engineering project demonstrates how to build and deploy a fully automated **Security Orchestration, Automation, and Response (SOAR)** pipeline. Using **Tines** and the **VirusTotal v3 API**, this pipeline automates a core Security Operations Center (SOC) workflow: ingesting an Indicator of Compromise (IoC) from a triggering alert, enriching that threat intelligence, and delivering an actionable, dynamic alert to security analysts.

Ingestion (Webhook) ──> Enrichment (VirusTotal API) ──> Notification (Formatted Email Alert)

By transitioning from manual investigative lookups to sub-second automated enrichment, this workflow models how modern enterprise SOCs combat analyst alert fatigue and dramatically lower their Mean Time to Resolution (MTTR).

---

## 🛠️ Technologies & Tools Used
* **SOAR Platform:** Tines Cloud
* **Threat Intelligence Engine:** VirusTotal API v3
* **Data Format:** JSON Payloads
* **Protocols:** HTTP REST (GET / POST)
* **Variable Templating:** Liquid Markup / Dynamic Pills

---

## 📋 Comprehensive Lab PDF
A fully compiled, formal engineering report of this project is available in this repository:
👉 **[Download the Full SOAR Automation Lab Report (PDF)](./SOAR_Automation_Lab_Report.pdf)**

---

## 🚀 Step-by-Step Implementation

### Step 1: Webhook Ingress Configuration
To simulate a trigger from an external security tool (like a SIEM or firewall), we deployed an inbound HTTP Webhook listener in Tines. This generated a unique, cloud-hosted webhook URL configured to parse incoming JSON payloads containing the targeted IP address.

* **Test Payload:**
  ```json
  {
    "ip_address": "8.8.8.8"
  }
Webhook Setup & Ingress Payload
​<img src="./1_Webhook_Setup.png" alt="Webhook Setup" width="600px">
<img src="./2_Test_Payload_Received.png" alt="Parsed Webhook Payload" width="600px">
Step 2: VirusTotal API Connection & Secure Handshake
​We integrated an HTTP Request block to dynamically fetch third-party threat intelligence. We isolated our VirusTotal API key inside Tines credentials and established a static REST GET connection using 8.8.8.8 to test authentication and confirm a successful handshake.
​API Request Structure & Status Check
​<img src="./3_VirusTotal_API_Setup.png" alt="VirusTotal API Configuration" width="600px">
<img src="./4_VirusTotal_Header_Key.png" alt="API Authentication and Headers" width="600px">
<img src="./5_VirusTotal_Test_Success.png" alt="Sandbox Request Test Success" width="600px">
​The API successfully returned an HTTP 200 OK status code along with a rich JSON threat schema.
​Parsing the API JSON Response
​By expanding the parsed payload, we isolated the nesting location of the IP attributes, the scan metadata, and the associated console links.
<img src="./6_VirusTotal_Data_Expanded.png" alt="VirusTotal Response Schema" width="600px">
​Step 3: Transitioning to Dynamic Variables & Orchestration
​With static testing validated, we transformed the hardcoded API destination into a dynamic URL endpoint. Using Tines’ data mapping, the flow now programmatically fetches whichever IP address was ingested by the webhook trigger, rendering a modular, reusable pipeline:
[https://www.virustotal.com/api/v3/ip_addresses/](https://www.virustotal.com/api/v3/ip_addresses/){{webhook.body.ip_address}}
The Logical SOAR Flow Diagram
​<img src="./7_Tines_SOAR_Workflow.png" alt="Full Tines Flowchart" width="600px">
​Step 4: Dynamic Email Alert Mapping
​We constructed a Send Email notification block to instantly alert analysts. Using Tines’ templating engine, we replaced the plain-text placeholders with interactive, dynamic data "pills" that pull real-time data straight from the upstream blocks:
Hi Team,

The automated SOC pipeline has detected suspicious activity.

Details of the investigated IP:
- IP Address: {{webhook.body.ip_address}}
- Scan Status: {{http_request.body.data.type}}
- VirusTotal Link: {{http_request.body.data.links.self}}

Please review the details in the VirusTotal console.

Regards,
Automated SOC Orchestrator
In-Platform Successful Delivery Logs
​Tines processed the formatting variables, parsed the values, and confirmed a successful SMTP handoff:
<img src="./8_Tines_Email_Log_Success.png" alt="Dynamic Email Block Success" width="600px">
​🏆 Final Verification: The Live Analyst Alert
​Below is the actual final alert delivered to the security inbox. Note that the template has successfully resolved the dynamic pills into concrete threat indicators, complete with a direct clickable investigation URL:
​<img src="./9_SOC_Alert_Email.png" alt="Received SOC Inbox Alert" width="700px">
​🧠 Key Takeaways & Security Insights
​API Ingestion & Security: Gained hands-on experience securely handling and utilizing API credentials to query live threat databases.
​Logical Parsing: Learned how to navigate nested JSON payloads and map target variables into user-friendly layouts.
​Optimized Incident Response: Demonstrated how automation reduces analyst overhead, mitigates alert fatigue, and reduces MTTR from minutes to milliseconds.
