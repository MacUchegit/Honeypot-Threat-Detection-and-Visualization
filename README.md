![honeypot architecture](https://github.com/user-attachments/assets/9213d368-ea23-4fae-ae54-e14b011acc31)

# **🛡️ Honeypot Project: Threat Detection & Visualization using Azure Sentinel**


## **1. Introduction**

A **honeypot** is a cybersecurity mechanism designed to attract attackers by simulating vulnerable systems. It acts as a decoy, luring malicious actors to interact with it, so security professionals can monitor and analyze attack patterns, techniques, and origins in a controlled environment.

This project showcases the design and deployment of a cloud-based honeypot using **Microsoft Azure**, which simulates a high-value asset to attract real-world attackers. It captures security event logs and visualizes attack origins, helping security analysts gain practical insights into modern threats.

## **2. Importance of Honeypots to Security Analysts**

For security analysts, honeypots are critical tools for:

* Studying attack behaviors in real time.
* Gathering threat intelligence without compromising production systems.
* Enhancing detection and response mechanisms by understanding attacker tactics.
* Testing and evaluating incident response procedures.

Honeypots allow analysts to observe threats from inception, helping to strengthen defense mechanisms based on actual attacker behavior.

---

## **3. Project Objective**

The goal of this project is to:

* Deploy a virtualized honeypot environment on Azure.
* Capture, analyze, and visualize attacker data.
* Leverage **Azure Sentinel** and **Log Analytics Workspace** for centralized logging and threat intelligence.
* Provide a real-time **attack map** showing global attacker locations based on IP geolocation.

---

## **4. Project Implementation**

### **Step 1: Create a Resource Group**

Using the Azure portal:

* Search for **"Resource groups"** and click **Create**.
  ![create RG1](https://github.com/user-attachments/assets/bf0c28d9-2264-4bff-a7e7-c10b670ceabd)
* Name it `HONEYPOT-LAB`.
![create RG2](https://github.com/user-attachments/assets/2f034be9-6fad-4a10-9ba7-a0edc3d7430b)
* Click **Review + Create**.
![create RG3](https://github.com/user-attachments/assets/5e07ccb7-87ee-4744-ae7e-bd3511f53d1d)
![create RG4](https://github.com/user-attachments/assets/024a2114-8752-4bcb-8040-308f82d259a0)


> 🔎 *Resource groups* in Azure are containers that hold related resources like virtual networks and machines, making management and billing easier.

---

### **Step 2: Create a Virtual Network (VNet)**

* Search for **“Virtual Networks”**, click **Create**, and name it `ADMIN NETWORK`.
![create VN1](https://github.com/user-attachments/assets/b1033bf5-9840-41c5-9b56-7a4fef14a496)

* Assign it to the `HONEYPOT-LAB` resource group and ensure the **Region** is **East US 2**.
![create VN2](https://github.com/user-attachments/assets/995c90a0-154e-49bc-90d6-893b92cef583)
![create VN3](https://github.com/user-attachments/assets/0db26f77-b7da-4823-90d1-96083987d4a1)

* Click **Review + Create**.
![create VN3](https://github.com/user-attachments/assets/9ee28646-fba5-44b7-a488-6d282b8caa5a)
![create VN4](https://github.com/user-attachments/assets/d992bdbb-d205-40ff-aa32-4f3ee32927ce)
![create VN5](https://github.com/user-attachments/assets/cebd5436-6763-48c8-8c24-db2dab94664f)

> 🛠️ The virtual network allows communication between Azure resources. It also simulates a corporate network layout to make the honeypot believable.

---

### **Step 3: Deploy a Virtual Machine**

* Search **“Virtual Machines”**, click **Create**, and name it `FINANCE-DB` (an attractive name for attackers).
![create VM1](https://github.com/user-attachments/assets/6fbbbf92-0c07-4830-95f3-fb1738d36025)
![create VM2](https://github.com/user-attachments/assets/6e9dd369-b078-492c-b459-2a25a180c35a)

* Select **Windows 10**, choose a manageable size (for cost), and input your credentials.
![create VN5 correct](https://github.com/user-attachments/assets/268d25d2-1910-4f38-af10-1e72b03f8a61)

* Acknowledge the license checkbox.
![create VM3](https://github.com/user-attachments/assets/9aa3926e-4729-497b-ba54-5b70354cd09e)

* Under **Management**, check the option to delete NIC and IP with the VM.
![create VM5](https://github.com/user-attachments/assets/cd87ae67-894a-4013-957f-2811da2c5d0c)

* In **Advanced**, disable boot diagnostics.
* Click **Review + Create** → **Create**.

Once deployed:

* Navigate to the `HONEYPOT-LAB` resource group, locate the `FINANCE-DB-nsg`, and delete the default RDP rule.**
![Create VMsecurity Group](https://github.com/user-attachments/assets/7ab051d8-2f5f-481b-8db9-aeb555a5f9fb)
![create VM secutity G2](https://github.com/user-attachments/assets/726360f7-aa20-4eda-8264-4f665dbe7a79)

* Create a new rule to **allow all inbound traffic**.
![create VM Security G3](https://github.com/user-attachments/assets/db898c5d-52d2-4c1a-b30b-0c5c01dd4a44)

> 🚨 This increases exposure, making the system more likely to be targeted.

---

### **Log into the VM and Turn Off Firewall**

* Use **Remote Desktop** to connect using the public IP, username, and password.
![logging into VM2](https://github.com/user-attachments/assets/ce17d4f7-2559-4531-9386-b6d270e238b2)

* Disable the firewall via `wf.msc > Properties > Turn off all profiles`.
![logging into VM3](https://github.com/user-attachments/assets/19d084ab-80cf-4a62-90c0-4caf78746638)

> 🔒 Disabling the firewall ensures no traffic is blocked, mimicking a misconfigured system — perfect bait for attackers.

### **Simulate and Observe Logins**

* Attempt failed logins manually (e.g., username “employee”) and confirm via **Event Viewer > Security logs**.
* Look for **Event ID 4625** (failed login attempts).
![eventviewer1](https://github.com/user-attachments/assets/9ff3bb41-4224-4c29-80f9-811d08cc7233)

---

## **Step 4: Configure Centralized Logging with Azure**

### **Create a Log Analytics Workspace (LAW)**

* Search **“Log Analytics Workspaces”**, click **Create**, and link it to `HONEYPOT-LAB`.
![Log Analytics 1](https://github.com/user-attachments/assets/bb8fd118-7234-446f-817f-a6e6d1706233)
![Log Analytics 2](https://github.com/user-attachments/assets/09cee22a-e369-488b-b1d7-472b8e59e502)

### **Connect Azure Sentinel**

* Create a **Sentinel Instance** and connect it to your LAW (Log Analytics Workspace).
![sentinel1](https://github.com/user-attachments/assets/724c098d-3752-4f82-ae02-e578347f2dc0)
![sentinel2](https://github.com/user-attachments/assets/ed9ec9a7-4681-46ea-b200-b24e0a92564f)

#### **Enable the “Windows Security Events via AMA” Connector**

* Go to **Content Management > Content Hub**, search “Security Event”.
* Click **Install**, then **Manage**, select the **windows security event via AMA** then open the **connector page**.
![windows Security Events](https://github.com/user-attachments/assets/7a94fb20-63b4-415d-8fa1-6a1a6b59a659)
![windows Security Events2](https://github.com/user-attachments/assets/3b130639-a878-4402-a23b-8fa8b921ef58)

* Create a **Data Collection Rule**.
![windows Security Events5](https://github.com/user-attachments/assets/0a40c940-c1f0-416b-a9c9-b37b2a352d00)

> 📊 The **AMA (Azure Monitor Agent)** streams Windows Security Events (like login failures) to your workspace for monitoring and threat detection.

To confirm:

* Go to **VM > Settings > Extensions + Applications** and verify Azure Monitor is installed.
![windows Security Events7](https://github.com/user-attachments/assets/e94163a2-b436-4bb6-b9aa-8b1142204de9)

---

## **Step 5: Query and Visualize Logs**

* Open your **Log Analytics Workspace > Logs**.
* Run the query: `SecurityEvent` to view VM activity.
![windows Security Events8](https://github.com/user-attachments/assets/4659aaab-840f-4155-8d04-0922dbac5240)

  You’ll see logs such as:

  * Login attempts with IP addresses.
  * No geographic data yet.

## **Step 6: Import IP Geolocation Watchlist**

To map attacker IP addresses to their geographic locations, we leverage **Azure Sentinel's Watchlist** feature by importing a spreadsheet that contains IP address blocks and their corresponding geolocation data (e.g., city, country, latitude, and longitude). This process enables a visual correlation between malicious IP addresses and their origin on a global map.

#### **Steps to Import and Use the Watchlist:**

**Create a Watchlist in Sentinel**

   * Navigate to your **Microsoft Sentinel** instance.
   * Go to **Configuration > Watchlist**, then click **Create new watchlist**.
![watchlist1](https://github.com/user-attachments/assets/77281f61-cffd-4b5d-88b9-97aa08dc7692)

   * Set both the **Name** and **Alias** to `geoip`.
![watchlist2](https://github.com/user-attachments/assets/c3714fc5-02db-46ba-a560-c2cac8ec54ab)

  * use a Azure Storage and then enter this URL: https://sacyberrange00.blob.core.windows.net/vm-applications/geoip-summarized.csv?sp=r&st=2025-05-16T00:31:16Z&se=2030-01-01T08:31:16Z&spr=https&sv=2024-11-04&sr=b&sig=taUGULhhgRiY0BRwEEbhusoh%2BxaIQJWAFwxN2%2FOWWhc%3D
   * Set the **SearchKey** field to `network` (this ensures that the IP ranges in the logs are matched correctly).
![windows Security Events9](https://github.com/user-attachments/assets/ef808903-d2b8-4820-96f1-e6a04e5806c1)

NOTE: Wait till it fully downloads the file data
![windows Security Events10](https://github.com/user-attachments/assets/66d56fe6-e2a4-4382-8b6a-c3e1de887401)

**Matching IP Logs to Geolocation Data**
   Azure Sentinel uses the IP address from the `SecurityEvent` logs (specifically those tied to failed login attempts, Event ID 4625) and looks up the corresponding IP block in the `geoip` watchlist using the `ipv4_lookup()` function in KQL (Kusto Query Language).

   * The **SearchKey** `network` is compared against the attacker IPs from the logs.
   * When a match is found, it returns the geolocation metadata (city, country, coordinates) from the spreadsheet.

**Visualizing Attacker Locations**
   The mapped data is later rendered in an **Attack Map** (via a Sentinel Workbook) where each attacker IP is plotted on a world map based on its latitude and longitude. This visual helps analysts identify high-risk regions and patterns in attacker behavior.

---

## **Step 7: Create a Global Attack Map**

* In **Sentinel > Workbooks**, click **Create new workbook**.
![create map1](https://github.com/user-attachments/assets/ac30ccd4-f182-4564-8d8e-de913993ae5a)

* Add a **query**, switch to **Advanced Editor**, and paste the following JSON:
![create map2](https://github.com/user-attachments/assets/cc16262a-a97e-408c-86e0-ee60f9ea711d)
![create map3](https://github.com/user-attachments/assets/b65406c6-f6da-4e9b-825e-947ee9e0e80b)

```json
{
  "type": 3,
  "content": {
    "version": "KqlItem/1.0",
    "query": "let GeoIPDB_FULL = _GetWatchlist(\"geoip\");\nlet WindowsEvents = SecurityEvent;\nWindowsEvents | where EventID == 4625\n| order by TimeGenerated desc\n| evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network)\n| summarize FailureCount = count() by IpAddress, latitude, longitude, cityname, countryname\n| project FailureCount, AttackerIp = IpAddress, latitude, longitude, city = cityname, country = countryname,\nfriendly_location = strcat(cityname, \" (\", countryname, \")\");",
    "size": 3,
    "timeContext": {
      "durationMs": 2592000000
    },
    "queryType": 0,
    "resourceType": "microsoft.operationalinsights/workspaces",
    "visualization": "map",
    "mapSettings": {
      "locInfo": "LatLong",
      "locInfoColumn": "countryname",
      "latitude": "latitude",
      "longitude": "longitude",
      "sizeSettings": "FailureCount",
      "sizeAggregation": "Sum",
      "opacity": 0.8,
      "labelSettings": "friendly_location",
      "legendMetric": "FailureCount",
      "legendAggregation": "Sum",
      "itemColorSettings": {
        "nodeColorField": "FailureCount",
        "colorAggregation": "Sum",
        "type": "heatmap",
        "heatmapPalette": "greenRed"
      }
    }
  },
  "name": "query - 0"
}
```

* Click **Done Editing** → Save.

### 📌 **Live Attack Statistics**

**After 1 Hour:**

* 26.6k failed logins from Ranchos, Argentina
* 13.3k from Jordanów, Poland
* 13.3k from Bruges, Belgium
* 13.3k from Tilburg, Netherlands
* 2 attacks from Cape Town, South Africa

![map created1](https://github.com/user-attachments/assets/8eb7ef1d-68cb-407c-880a-46691f619658)

**After 8 Hours:**

* Numbers significantly increased, proving the honeypot's constant lure to malicious actors globally.

---

### **Conclusion and Project Overview**

This Honeypot project demonstrates how a deceptively simple Windows virtual machine, when exposed to the public internet, can attract thousands of malicious login attempts within minutes. The real power of this setup lies in its integration with **Azure Sentinel**, **Log Analytics**, and **Watchlists**, allowing for comprehensive monitoring, threat detection, and actionable insights.

By walking through each stage of the deployment and configuration, this project shows how security teams and individuals can simulate a vulnerable environment and analyze real-world attack behavior using Microsoft Azure’s security tools.

---

### **Project Stages at a Glance**

* **Honeypot Deployment**

  * Deployed a Windows 10 VM in Azure with minimal security.
  * Modified RDP rules to allow all inbound traffic.

* **Log Analytics Integration**

  * Connected the VM to Log Analytics to collect Windows Security Events (e.g., Event ID 4625).

* **Threat Detection with Azure Sentinel**

  * Enabled Azure Sentinel on the workspace.
  * Created a custom analytic rule to extract attacker IPs from failed login attempts.

* **IP Mapping with Watchlists**

  * Imported a watchlist (CSV) containing IP ranges and geolocation data.
  * Used `ipv4_lookup()` to match attacker IPs to locations.

* **Attack Visualization**

  * Built a Sentinel workbook to visualize attack data.
  * Displayed attacker geolocations on a dynamic map.

---

### **Key Outcomes**

* Captured and analyzed real-world attack data.
* Mapped malicious IPs to geolocations using a custom Watchlist.
* Visualized attacker behavior and locations using Sentinel workbooks.

This project provides a powerful and practical demonstration of cloud-native security monitoring and threat intelligence capabilities using Azure. It's an excellent foundation for further exploration into advanced detection, automation, and response strategies.
