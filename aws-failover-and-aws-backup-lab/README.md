# AWS High Availability & Automated Disaster Recovery Lab ☁️🛡️

## 📝 Project Overview
This project demonstrates the implementation of a highly available, fault-tolerant, and secure web infrastructure on AWS. The architecture is split into two primary operational phases:
1. **High Availability & DNS Failover:** Utilizing **Amazon Route 53** (Health Checks & Failover Routing) and **Amazon S3** (Static Website Hosting) to ensure zero downtime or graceful degradation. If the primary web server goes offline, user traffic is automatically redirected to a maintenance page.
2. **Automated Disaster Recovery (AWS Backup):** Implementing a centralized backup and data protection strategy for the primary **EC2** instance, utilizing automated scheduling, retention rules, and on-demand recovery points.

---

## 🏗️ Phase 1: High Availability & DNS Failover Architecture

To ensure continuous availability, an Elastic IP was associated with the primary web server, monitored by a Route 53 Health Check, and backed up by an S3 static failover page.

### Step 1: Associating Elastic IP with the Primary Server
An Elastic IP (`184.72.253.241`) was allocated and associated with the primary EC2 instance (`i-08fc33038a4b363c8`) to provide a static public entry point for DNS routing and health checks.

<img width="959" height="380" alt="connect-elastic-ip-with-server-for-health-check" src="https://github.com/user-attachments/assets/a2970473-1559-49fb-8d9d-57831fa1339a" />


### Step 2: Creating a Route 53 Health Check
A Route 53 Health Check was configured to monitor the server's public endpoint (`http://184.72.253.241:80/`). The health check actively probes the instance to determine its availability status.

<img width="954" height="419" alt="create a health cheak for -primary server" src="https://github.com/user-attachments/assets/5a63245c-f30b-44a1-9e68-abb9e799f850" />


### Step 3: Configuring Route 53 Failover Records
To route traffic appropriately based on server health, failover records were created in Route 53.
* **Primary Record:** An A record pointing to the EC2 instance, linked to the health check.
* **Secondary Record:** An Alias A record pointing to the S3 bucket endpoint hosting the static maintenance page.

<img width="945" height="446" alt="S3-as-failover" src="https://github.com/user-attachments/assets/57d427cb-3858-40d4-94dd-fea093fb38a9" />


<img width="956" height="466" alt="success-creat-failover-record" src="https://github.com/user-attachments/assets/4e1319f0-a649-47ec-82a1-96d86f984c4e" />

### Step 4: Validating Failover & Health Check Detection
To test the resilience of the architecture, the primary EC2 instance (`A4L-WEB`) was intentionally stopped from the AWS console.

<img width="959" height="380" alt="try-stop-instance-to-check-failover" src="https://github.com/user-attachments/assets/4ba3689b-1768-4317-a63e-55f5188de495" />


Route 53 rapidly detected the failure, and the health check status transitioned from `Healthy` to `Unhealthy`.

<img width="959" height="436" alt="health-checkers-detect-unhealthy" src="https://github.com/user-attachments/assets/f4f0bdf9-5d3d-47a4-9a79-cc0458c864b5" />


Once the health check failed, Route 53 automatically triggered the failover policy, redirecting all incoming domain traffic (`bittco.site`) to the secondary S3 bucket.

<img width="959" height="473" alt="failover-work-to-s3" src="https://github.com/user-attachments/assets/32dc205d-f9f9-4c62-8ce7-5a1e494b71a4" />


---

## 🛡️ Phase 2: Automated Instance Protection (AWS Backup)

To protect critical infrastructure data from accidental deletion, ransomware, or corruption, a centralized backup plan was established using AWS Backup.

### Step 1: Configuring the Backup Plan & Options
A new backup plan named `Lab-Backup-Plan` was created. Below is a detailed breakdown of the options evaluated during configuration:

* **Start Options (`Build a new plan`):** Selected to configure custom rules from scratch rather than using pre-built templates.
* **Backup Rule Name (`Daily-Rule`):** Defines the schedule and execution parameters.
* **Backup Vault (`Default`):** The secure, encrypted logical container where recovery points are stored. 
* **Backup Frequency (`Daily`):** Configures the system to take automated snapshots every 24 hours.
* **Total Retention Period (`7 Days`):** Configures automatic lifecycle deletion. Recovery points older than 7 days are automatically purged to optimize storage costs.

<img width="944" height="407" alt="lab-1" src="https://github.com/user-attachments/assets/cb923b24-563b-4f03-9d91-c50b9fed45a1" />


<img width="941" height="355" alt="lab-2" src="https://github.com/user-attachments/assets/2632bc4d-ede8-48c1-bf19-20f94ae743df" />


<img width="959" height="418" alt="plan-created-part3" src="https://github.com/user-attachments/assets/18a4a334-d4bf-4775-8eb6-a2b80a767011" />

### Step 2: Assigning Resources to the Backup Plan
The primary EC2 instance (`i-08fc33038a4b363c8`) was explicitly targeted using resource IDs. An automated IAM role was generated to allow AWS Backup to manage snapshots securely.

<img width="944" height="391" alt="assign-resource-part1" src="https://github.com/user-attachments/assets/a531bf8a-62d8-4869-a692-183160f1cf7a" />


<img width="959" height="336" alt="assign-resource-part-2" src="https://github.com/user-attachments/assets/bc8617d7-bb47-4d5a-846f-2edb8ff38fa4" />


<img width="955" height="263" alt="assign-resource-part3" src="https://github.com/user-attachments/assets/9b5b2f85-8a42-4fb2-acc3-1d8d38ad5584" />


### Step 3: Executing an On-Demand Backup
To immediately generate a recovery point without waiting for the scheduled daily window, an on-demand backup was triggered.

<img width="950" height="416" alt="create-on-demand-backup" src="https://github.com/user-attachments/assets/f688cb63-7ec5-4dac-9dd4-81c8b3645153" />


<img width="959" height="374" alt="create-on-demand-backup-2" src="https://github.com/user-attachments/assets/6adef27d-cbd0-45c4-8918-b46e1159642a" />


<img width="941" height="409" alt="on-demand-backet-creatted-complete" src="https://github.com/user-attachments/assets/c75b9e12-e4c0-4193-b93c-e8c0f59e65f2" />


---
## 🎉 Conclusion
The lab was successfully implemented, verified, and tested. The architecture guarantees high availability via Route 53 Failover to S3, alongside robust automated data protection using AWS Backup.
