---
title: Google Cloud Create Simple GCP GCE VM Instance
description: Learn to create Simple GCP GCE VM Instance on Google Cloud Platform GCP
---

## Step-01: Introduction
- We will learn the following concepts as part of this section
- How to create Linux VM Instnace in Google Cloud Platform ? 
- How to connect to Linux VM Instnace in GCP using SSH via Browser ? 
- What are various settings available on SSH via Browser ? 
- How to install a simple webserver on GCP VM Instance and access those sample pages on webserver via Browser using GCP External IP ? 
- How to Manage VM Instances (Stop / Start / Suspend / Resume / Reset / Delete etc)? 

---
## Step 02: Create a Virtual Machine Instance in GCP

In this step, we will create a Google Compute Engine (GCE) virtual machine instance using the Google Cloud Console.

### 1. Enable Compute Engine API

Before creating a VM instance, ensure that the **Compute Engine API** is enabled in your Google Cloud project.

1. Navigate to **APIs & Services → Library**.
2. Search for **Compute Engine API**.
3. Open the API and click **Enable**.

### 2. Create a New VM Instance

1. Navigate to **Compute Engine → VM Instances**.
2. Click **Create Instance**.
3. Configure the VM instance using the following specifications.

### 3. VM Instance Configuration

| Configuration | Value |
|---|---|
| VM Name | `demo1-vm` |
| Label | `environment: dev` |
| Region | `asia-south1` (Mumbai, India) |
| Zone | `asia-south1-a` |

### 4. Machine Configuration

Under the **Machine Configuration** section, select the following options:

| Configuration | Value |
|---|---|
| Machine Family | General Purpose |
| Series | E2 |
| Machine Type | `e2-micro` |

### 5. Availability Policies

Configure the VM availability settings:

| Configuration | Value |
|---|---|
| VM Provisioning Model | Standard |

### 6. Display Device and Confidential Computing

Configure the following options:

- **Display Device:** Enable (check the checkbox).
- **Confidential VM Service:** Leave unchecked (default configuration).
- **Container:** Leave unchecked (default configuration).

### 7. Boot Disk Configuration

Keep the default boot disk configuration for this demonstration.

The default operating system image, disk type, and disk size will be retained.

### 8. Identity and API Access

Configure the VM identity and API access settings:

| Configuration | Value |
|---|---|
| Service Account | Compute Engine default service account |
| Access Scopes | Allow default access |

### 9. Firewall Configuration

Under the Firewall section:

- Enable **Allow HTTP traffic**.

This allows inbound HTTP traffic on TCP port `80` through the applicable VPC firewall rules.

### 10. Advanced Options

Leave the Advanced Options at their default settings for now.

We will explore these configurations in detail in upcoming steps, including networking, security, storage, automation, and other advanced VM settings.

### 11. Create the VM Instance

Once all the configurations have been reviewed:

1. Verify the VM configuration.
2. Click **Create**.
3. Wait for Google Cloud to provision the virtual machine.
4. Navigate to **Compute Engine → VM Instances** and verify that `demo1-vm` is running.

### Expected Result

The VM instance should appear in the VM Instances dashboard with the following details:

- **Instance Name:** `demo1-vm`
- **Machine Type:** `e2-micro`
- **Region:** `asia-south1`
- **Zone:** `asia-south1-a`
- **Status:** Running
- **Environment:** dev

**Successfully created your first Google Compute Engine virtual machine instance in the Mumbai region, India!**

---
