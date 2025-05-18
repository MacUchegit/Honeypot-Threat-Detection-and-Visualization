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
  
  ![create RG1](https://github.com/user-attachments/assets/4c0716e6-8d82-47ce-b3a7-97ec41288c7f)
  
* I named it `HONEYPOT-LAB`.
  
![create RG2](https://github.com/user-attachments/assets/0f52f33d-2e48-423a-a1f4-222e4dcbb5f3)

* Click **Review + Create**.

![create RG3](https://github.com/user-attachments/assets/db235307-f3e7-4c79-a41c-fb9a934bb4be)
![create RG4](https://github.com/user-attachments/assets/05771aaa-d181-4559-957f-6ecee587629b)

> 🔎 *Resource groups* in Azure are containers that hold related resources like virtual networks and machines, making management and billing easier.

---

### **Step 2: Create a Virtual Network (VNet)**

* Search for **“Virtual Networks”**, click **Create**, and I name it `ADMIN NETWORK`.

![create VN1](https://github.com/user-attachments/assets/a0959570-bce8-4475-b5f7-9e9ad0113a56)

* Assign it to the `HONEYPOT-LAB` resource group and ensure the **Region** is **East US 2**.

![create VN2](https://github.com/user-attachments/assets/46a7fd27-be45-40cc-96f4-df84b3c6fa72)

* Click **Review + Create**.
  
![create VN3](https://github.com/user-attachments/assets/1d070b40-5356-4035-8f52-c35921f2f7e9)
![create VN4](https://github.com/user-attachments/assets/d48142d6-5340-40f7-a75b-a8917c05ff46)

Notice the Virtual Network `ADMIN-NETWORK`, has been created under the Resource Group `HONEYPOT-LAB`
![create VN5](https://github.com/user-attachments/assets/aa7a8db4-4008-46c8-8fb4-33746288a4f0)

> 🛠️ The virtual network allows communication between Azure resources. It also simulates a corporate network layout to make the honeypot believable.

---

### **Step 3: Deploy a Virtual Machine**

* Search **“Virtual Machines”**, click **Create**, and name it `FINANCE-DB` (an attractive name for attackers).
  
![create VM1](https://github.com/user-attachments/assets/3029aaad-8cf4-416a-a01c-a2fe23b2fd0c)
![create VM2](https://github.com/user-attachments/assets/b0d43d23-0c7a-4c25-a1db-b11242ab6477)

* Select **Windows 10**, choose a manageable size (for cost), and input your credentials.
  
![create VN5 correct](https://github.com/user-attachments/assets/182f8e7e-13a8-466a-873a-626aa113cb6b)

* Create your login credentials(Username and password). Try NOT to forget these credentials because they will be needed to log into your Virtual Machine. Next, Acknowledge the license checkbox.

![create VM4](https://github.com/user-attachments/assets/e94d4296-0c04-46f0-8382-b454878738f7)

* Under **Management**, check the option to delete NIC and IP with the VM.
  
![create VM5](https://github.com/user-attachments/assets/e5436841-bffe-400e-90cc-f947c1dbf089)

* In **Advanced**, disable boot diagnostics.
* Click **Review + Create** → **Create**.

Once deployed:

* Navigate to the `HONEYPOT-LAB` resource group, locate the `FINANCE-DB-nsg`, and delete the default RDP rule.**

![Create VMsecurity Group](https://github.com/user-attachments/assets/269944da-abe6-48c6-a611-79222680358f)
![create VM secutity G2](https://github.com/user-attachments/assets/f4a8a020-405f-488f-9e06-32ba56349c7e)

* Create a new rule to **allow all inbound traffic**.
  
![create VM Security G3](https://github.com/user-attachments/assets/5c566a14-a760-4009-bd55-6cd451054235)

> 🚨 This increases exposure, making the system more likely to be targeted.

---

### **Log into the VM and Turn Off Firewall**

* For Windows users, you can search for **Remote Desktop** in the search bar to connect to the Virtual Machine using your public IP (you can get your public IP by opening your VM `FINANCE-DB` in Azure Portal), username, and password.

![remote desktop](https://github.com/user-attachments/assets/38009eab-0851-4359-8d34-63c44fe168fb)
![logging into VM2](https://github.com/user-attachments/assets/befc64ac-85b6-490a-9028-d4b5118b9abf)

* Disable the firewall via `wf.msc > Properties > Turn off all profiles`.
  
![logging into VM3](https://github.com/user-attachments/assets/be985c8d-6d1f-4f80-a4d1-f0f375d4baa5)

> 🔒 Disabling the firewall ensures no traffic is blocked, mimicking a misconfigured system — perfect bait for attackers.

### **Simulate and Observe Logins**

* I logged out of my VM and intentionally attempted to log in using incorrect credentials (e.g., username “employee”) to mimic failed login attempts. This was done to verify if such activity would be captured in the Security logs. Most of the time, these failed attempts are recorded with Event ID 4625.
* Confirm via **Event Viewer > Security logs**.
* Look for **Event ID 4625** (failed login attempts).

![eventviewer1](https://github.com/user-attachments/assets/ae3572b9-7633-4f82-a352-5baa6a8873a8)

---

## **Step 4: Configure Centralized Logging with Azure**

### **Create a Log Analytics Workspace (LAW)**

* Search **“Log Analytics Workspaces”**, click **Create**, and link it to `HONEYPOT-LAB`.
  
![Log Analytics 1](https://github.com/user-attachments/assets/44b4c7cb-eeae-4e21-9886-64fae129e2c0)
![Log Analytics 2](https://github.com/user-attachments/assets/6741cd7d-c38c-4bfa-a36d-8c3a3e5139c8)

### **Connect Azure Sentinel**

* Create a **Sentinel Instance** and connect it to your LAW (Log Analytics Workspace).
  
![sentinel1](https://github.com/user-attachments/assets/462cb3b3-ed8d-40c1-ad81-6e84baba5d97)
![sentinel2](https://github.com/user-attachments/assets/1e818744-3417-4b6e-8c99-21bec634047d)

#### **Enable the “Windows Security Events via AMA” Connector**

* Go to **Content Management > Content Hub**, search “Security Event”.
* Click **Install**, then **Manage**, select the **windows security event via AMA** then open the **connector page**.
  
![windows Security Events](https://github.com/user-attachments/assets/7bf44148-aa95-47a4-ac37-92b1e68af9f0)
![windows Security Events2](https://github.com/user-attachments/assets/2a871688-6811-439a-a472-42db0a7a2a14)
![windows Security Events4](https://github.com/user-attachments/assets/bdc8c48f-8414-479b-8172-964409c5e679)

* Create a **Data Collection Rule**.

![windows Security Events5](https://github.com/user-attachments/assets/b3753e76-6a64-418f-97dc-8601838d86e1)
![windows Security Events6](https://github.com/user-attachments/assets/d331a0e3-5ec2-45ea-bdf2-b2cf263edddb)

> 📊 The **AMA (Azure Monitor Agent)** streams Windows Security Events (like login failures) to your workspace for monitoring and threat detection.

To confirm:

* Go to **VM > Settings > Extensions + Applications** and verify Azure Monitor is installed.
  
![windows Security Events7](https://github.com/user-attachments/assets/4c2d07b8-df22-434f-a47d-e8b73baccf8d)

---

## **Step 5: Query and Visualize Logs**

* Open your **Log Analytics Workspace > Logs**.
* Run the query: `SecurityEvent` to view VM activity.

![windows Security Events8](https://github.com/user-attachments/assets/127e86f0-133c-416f-943d-53975572cbbe)

  You’ll see logs such as:

  * Login attempts with IP addresses.
  * No geographic data yet.

## **Step 6: Import IP Geolocation Watchlist**

To map attacker IP addresses to their geographic locations, we leverage **Azure Sentinel's Watchlist** feature by importing a spreadsheet that contains IP address blocks and their corresponding geolocation data (e.g., city, country, latitude, and longitude). This process enables a visual correlation between malicious IP addresses and their origin on a global map.

#### **Steps to Import and Use the Watchlist:**

**Create a Watchlist in Sentinel**

   * Navigate to your **Microsoft Sentinel** instance.
   * Go to **Configuration > Watchlist**, then click **Create new watchlist**.
     
![watchlist1](https://github.com/user-attachments/assets/2c4a4435-8597-43ce-ae7b-8ec145b5d070)

   * Set both the **Name** and **Alias** to `geoip`.
     
![watchlist2](https://github.com/user-attachments/assets/21923700-1558-43d1-b9d2-60e440e66d7c)

  * Use an Azure Storage and then enter this URL: https://sacyberrange00.blob.core.windows.net/vm-applications/geoip-summarized.csv?sp=r&st=2025-05-16T00:31:16Z&se=2030-01-01T08:31:16Z&spr=https&sv=2024-11-04&sr=b&sig=taUGULhhgRiY0BRwEEbhusoh%2BxaIQJWAFwxN2%2FOWWhc%3D
   * Set the **SearchKey** field to `network` (this ensures that the IP ranges in the logs are matched correctly).
     
![windows Security Events9](https://github.com/user-attachments/assets/bbc5bb11-b48c-4b77-87ee-37fc9c9dd439)

NOTE: Wait till it fully downloads the file data

![windows Security Events10](https://github.com/user-attachments/assets/0beb6d7d-44f1-4d99-9d9b-6ddb1d256e82)

**Matching IP Logs to Geolocation Data**
   Azure Sentinel uses the IP address from the `SecurityEvent` logs (specifically those tied to failed login attempts, Event ID 4625) and looks up the corresponding IP block in the `geoip` watchlist using the `ipv4_lookup()` function in KQL (Kusto Query Language).

   * The **SearchKey** `network` is compared against the attacker IPs from the logs.
   * When a match is found, it returns the geolocation metadata (city, country, coordinates) from the spreadsheet.

**Visualizing Attacker Locations**
   The mapped data is later rendered in an **Attack Map** (via a Sentinel Workbook) where each attacker IP is plotted on a world map based on its latitude and longitude. This visual helps analysts identify high-risk regions and patterns in attacker behavior.

---

## **Step 7: Create a Global Attack Map**

* In **Sentinel > Workbooks**, click **Create new workbook**.
  
![create map1](https://github.com/user-attachments/assets/89c1568b-c9f3-4a7d-b0eb-6d8c7c722819)

* Add a **query**, switch to **Advanced Editor**, and paste the following JSON:
  
![create map2](https://github.com/user-attachments/assets/2795bd41-ab48-4122-98a6-c58911c772e0)
![create map3](https://github.com/user-attachments/assets/ed4e7578-1a0e-48e8-8b0a-24eb89a195cc)

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

![map created1](https://github.com/user-attachments/assets/1f32e4d0-683f-4705-acab-4f83b9976195)

**After 8 Hours:**

* Numbers significantly increased, proving the honeypot's constant lure to malicious actors globally.

![8hrs Map](https://github.com/user-attachments/assets/44ba0cc8-c523-4b90-9ff1-a6dab1c41755)

---

## **Conclusion and Project Overview**

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
