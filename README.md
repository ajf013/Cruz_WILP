# 🧹 Orphaned Resource Clean-Up Through Automation on Azure

![PowerShell Badge](https://img.shields.io/badge/PowerShell-Automation-blue?logo=powershell&logoColor=white)
![Azure Badge](https://img.shields.io/badge/Azure-Cloud%20Resources-0078D4?logo=microsoftazure&logoColor=white)
![GitHub Badge](https://img.shields.io/badge/GitHub-Repository-black?logo=github)
![Gmail Badge](https://img.shields.io/badge/Gmail-Email%20Notifications-D14836?logo=gmail&logoColor=white)

## Overview

This project automates the identification, notification, and deletion of orphaned Azure resources — including **Public IPs**, **Network Interfaces (NICs)**, **Managed Disks**, and **Snapshots** — across all subscriptions. It helps reduce unnecessary cloud costs while respecting protection tags and sending personalized email alerts.

Built with PowerShell and Azure Automation, this solution is designed for DevOps teams who value clarity, cost-efficiency, and cultural personalization.

---

## ✨ Features

- 🔍 Detects unattached (orphaned) resources across all subscriptions
- 🏷️ Tags resources with `OrphanedSince` for tracking
- 📧 Sends reminder emails on days 7, 5, 3, and 1 before deletion
- 🛡️ Honors `DO NOT DELETE = yes` protection tag
- 🗑️ Deletes orphaned resources on day 0
- 💰 Calculates and reports actual cost savings in ₹ INR using `Az.CostManagement`
- 🌐 Uses SendGrid for email delivery with HTML templates

---

## 📁 Supported Resources

| Resource Type     | Detection Logic                     | Deletion Trigger | Cost Calculation |
|-------------------|-------------------------------------|------------------|------------------|
| Public IP         | Not linked to any NIC               | After 7 days     | Actual via `Get-ConsumptionUsageDetail` |
| Network Interface | Not attached to any VM              | After 7 days     | Actual via `Get-ConsumptionUsageDetail` |
| Managed Disk      | Not attached to any VM              | After 7 days     | Actual via `Get-ConsumptionUsageDetail` |
| Snapshot          | Not linked to any disk              | After 7 days     | Actual via `Get-ConsumptionUsageDetail` |

---

## 🚀 Setup Instructions

1. **Import Required Modules** into your Azure Automation Account:
   - `Az.Accounts`
   - `Az.Network`
   - `Az.Compute`
   - `Az.Resources`
   - `Az.CostManagement`

2. **Set Runtime Version** to **PowerShell 7.2** for compatibility.

3. **Create Automation Variables**:
   - `SendGridApiKey` (Encrypted string)

4. **Assign Permissions** to the Automation Account’s Managed Identity:
   - Reader or Contributor on target subscriptions
   - Cost Management Reader
   - Resource deletion permissions

5. **Customize Email Templates** if needed (e.g., add Tamil greetings, emojis, or branding).

---

## 📬 Email Notifications

Each email includes:
- Resource details (name, group, location, subscription)
- Current or saved cost in ₹ INR
- Reminder or deletion notice
- Protection status if applicable

Emails are sent using SendGrid with HTML formatting for clarity and professionalism.

---

## 🧠 Logic Flow

```text
For each subscription:
    For each orphaned resource:
        If DO NOT DELETE = yes:
            Send protection email
        Else:
            If OrphanedSince not tagged:
                Tag with today's date
            If days left in [7,5,3,1]:
                Send reminder email
            If days left = 0:
                Delete resource
                Send deletion confirmation with cost saved

Regards,
Azure Automation team
