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
## Step 03: Connect to a Linux VM Instance Using SSH

In this step, we will connect to the Linux virtual machine instance using **SSH (Secure Shell)** through the Google Cloud Console browser-based SSH feature.

We will also understand how Google Cloud handles SSH authentication in the background and where the SSH public key is stored on the VM.

### 1. Connect to the VM Using Browser-Based SSH

Follow these steps to establish an SSH connection:

1. Navigate to **Compute Engine → VM Instances**.
2. Locate the `demo1-vm` instance.
3. Click the **SSH** button next to the VM instance.
4. Select **Open in browser window**.
5. Wait for Google Cloud to establish the SSH connection.

Once connected, you will get a terminal session inside your Linux virtual machine.

### 2. Understand What Happens in the Background

When you establish an SSH connection through the Google Cloud Console, Google Cloud handles SSH authentication automatically.

The following operations take place in the background:

1. Google Cloud generates or uses an existing SSH key pair for authentication.
2. The SSH public key is made available to the VM through the configured SSH key management mechanism.
3. The SSH client uses the corresponding private key to authenticate the connection.
4. The SSH server on the VM validates the authentication request.
5. Once authentication succeeds, an SSH session is established.

**Note:** Depending on the VM's configuration, SSH keys may be managed through Compute Engine project/instance metadata or OS Login. They are not always added directly to the user's `authorized_keys` file.

### 3. Verify the Logged-In User

After successfully connecting to the VM, execute the following command:

```bash
id
```

**Observation:**

- The command displays the UID, GID, and group memberships of the currently authenticated Linux user.
- Verify that the username matches the identity associated with your Google Cloud SSH login.

Example:

```bash
uid=1001(stackcouture) gid=1001(stackcouture) groups=1001(stackcouture)
```

*Note: The username and UID shown above are illustrative. Your actual output may differ.*

### 4. Verify the SSH Authorized Keys

SSH public keys are generally stored in the following file when standard OpenSSH user-based key management is used:

```bash
~/.ssh/authorized_keys
```

To inspect the authorized public keys for the current user, execute:

```bash
cat ~/.ssh/authorized_keys
```

Alternatively, use the absolute path:

```bash
cat /home/stackcouture/.ssh/authorized_keys
```

**Observation:**

- The `authorized_keys` file contains public keys authorized to authenticate as that Linux user.
- Each entry represents an authorized SSH public key.
- You may find a key associated with your Google Cloud SSH session if metadata-based SSH key management is being used.

**Important:** If OS Login is enabled, SSH access is managed through OS Login, and you may not find the expected public key in the user's `authorized_keys` file.

### 5. Key Takeaways

By completing this step, you should understand:

- How to connect to a Linux VM using browser-based SSH.
- How Google Cloud manages SSH authentication.
- How to identify the currently authenticated Linux user using the `id` command.
- Where SSH public keys are stored in a standard Linux SSH configuration.
- The difference between metadata-based SSH key management and OS Login.

**Note:** We will explore SSH key generation, public and private keys, SSH authentication, OS Login, metadata-based SSH access, and other SSH configuration options in a dedicated hands-on demonstration.

---
## Step 04: Create and Execute the Web Server Installation Script

In this step, we will create a Bash script named `webserver-install.sh` to install and configure the Nginx web server on our Ubuntu-based Google Compute Engine VM.

The script will perform the following operations:

1. Update Ubuntu package repositories.
2. Install Telnet.
3. Install Nginx web server.
4. Enable and start the Nginx service.
5. Configure appropriate permissions for the web root directory.
6. Create a custom HTML landing page displaying VM details.

### 1. Create the Installation Script

Connect to the `demo1-vm` instance using SSH and create the script:

```bash
vi webserver-install.sh
```

### 2. Add the Following Script Content

**File: `webserver-install.sh`**

```bash
#!/bin/bash

set -e

sudo apt-get update -y
sudo apt-get install -y telnet nginx
sudo systemctl enable nginx
sudo systemctl start nginx

sudo chmod -R 755 /var/www/html

HOSTNAME=$(hostname)
VM_IP=$(hostname -I | awk '{print $1}')

sudo tee /var/www/html/index.html > /dev/null <<EOF
<!DOCTYPE html>
<html>
<head>
    <title>StackCouture - WebVM App1</title>
</head>
<body style="background-color:rgb(250, 210, 210);">
    <h1>Welcome to StackCouture - WebVM App1</h1>
    <p><strong>VM Hostname:</strong> ${HOSTNAME}</p>
    <p><strong>VM IP Address:</strong> ${VM_IP}</p>
    <p><strong>Application Version:</strong> V1</p>
    <p>Google Cloud Platform - Demos</p>
</body>
</html>
EOF

echo "Nginx web server installation and configuration completed successfully!"
```

### 3. Provide Execute Permissions

Make the script executable:

```bash
chmod +x webserver-install.sh
```

### 4. Execute the Installation Script

Run the script:

```bash
./webserver-install.sh
```

The script will automatically install and configure Nginx on your Ubuntu VM.

### 5. Verify Nginx Service Status

Check whether the Nginx service is running:

```bash
sudo systemctl status nginx
```

Expected status:

```text
Active: active (running)
```

### 6. Verify the Web Application Locally

Execute the following command from the VM:

```bash
curl http://localhost
```

You should see the HTML content of your custom landing page.

### 7. Access the Web Application from a Browser

1. Navigate to **Google Cloud Console → Compute Engine → VM Instances**.
2. Locate the `demo1-vm` instance.
3. Copy its **External IP address**.
4. Open a browser and access:

```text
http://<EXTERNAL_IP>
```

You should see the custom Nginx landing page displaying the VM hostname, IP address, and application version.

**Note:** Ensure that the VM has an applicable VPC firewall rule allowing inbound TCP traffic on port `80`. The **Allow HTTP traffic** option enabled during VM creation should configure the required HTTP firewall rule when applicable.

### Expected Result

The Nginx web server should be successfully installed and running on the Ubuntu VM, and the custom landing page should be accessible through the VM's external IP address.

---
## Step 05: Troubleshooting GCP VM Sudo Permission Denied

### 1. Overview

This guide documents how to troubleshoot and restore `sudo` access on an Ubuntu Virtual Machine (VM) running on Google Cloud Platform (GCP).

The issue occurred when the SSH user was unable to execute administrative commands despite being a member of the `google-sudoers` Linux group.

The recovery was performed using a Google Cloud startup script configured through instance metadata. The script executed with root privileges during VM startup and restored passwordless sudo access.

### 2. Environment Details

| Component | Configuration |
|---|---|
| Cloud Provider | Google Cloud Platform |
| Compute Service | Compute Engine |
| Operating System | Ubuntu Minimal |
| VM Name | `demo1-vm` |
| Machine Type | `e2-micro` |
| Zone | `asia-south1-a` |
| SSH Username | `ismartarshu` |
| Linux Group | `google-sudoers` |
| Recovery Method | GCE Startup Script |
| Shell | Bash |

### 3. Problem Description

While accessing the VM through SSH-in-browser, administrative commands failed with the following error:

```bash
sudo -l
```

**Error:**

```text
sudo: Sorry, user ismartarshu may not run sudo on demo1-vm.
```

Other commands also failed:

```bash
sudo whoami
sudo su -
```

**Expected behavior:**

The SSH user should be able to execute administrative commands using sudo.

**Actual behavior:**

The user was unable to obtain root privileges.

### 4. Initial Troubleshooting

#### Step 1: Verify the current Linux user

```bash
whoami
```

Output:

```text
ismartarshu
```

#### Step 2: Check user group membership

```bash
id
```

Relevant output:

```text
uid=1001(ismartarshu)
gid=1002(ismartarshu)
groups=1002(ismartarshu),...,1001(google-sudoers)
```

The user was already a member of the `google-sudoers` group.

#### Step 3: Verify sudo permissions

```bash
sudo -l
```

Output:

```text
sudo: Sorry, user ismartarshu may not run sudo on demo1-vm.
```

This confirmed that group membership alone was not sufficient to grant sudo access.

#### Step 4: Inspect GCE instance metadata

Open Google Cloud Shell and select the appropriate project:

```bash
gcloud config set project PROJECT_ID
```

Inspect the VM metadata:

```bash
gcloud compute instances describe demo1-vm \
  --zone=asia-south1-a \
  --format="yaml(metadata.items)"
```

The metadata contained the `ssh-keys` entry but no existing `startup-script` entry.

This allowed the recovery startup script to be added without replacing an existing startup script.

### 5. Recovery Solution: GCE Startup Script

#### Step 1: Create the recovery script

Open Google Cloud Shell and create the following file:

```bash
cat > recover-sudo.sh <<'EOF'
#!/bin/bash
set -euo pipefail

exec > >(tee -a /var/log/sudo-recovery.log) 2>&1

echo "Starting sudo recovery..."

getent group google-sudoers >/dev/null

FILE=/etc/sudoers.d/99-google-sudoers-recovery
TEMP=$(mktemp)

trap 'rm -f "$TEMP"' EXIT

printf '%s\n' '%google-sudoers ALL=(ALL:ALL) NOPASSWD:ALL' > "$TEMP"

chown root:root "$TEMP"
chmod 0440 "$TEMP"

visudo -cf "$TEMP"

mv "$TEMP" "$FILE"

visudo -c

echo "SUDO_RECOVERY_SUCCESS"
EOF
```

#### Step 2: Understand the recovery script

| Command | Purpose |
|---|---|
| `set -euo pipefail` | Enables strict Bash error handling |
| `exec > >(tee ...)` | Logs startup script execution |
| `getent group` | Verifies that the Linux group exists |
| `mktemp` | Creates a temporary sudoers file |
| `printf` | Defines the sudo permission rule |
| `chown` | Sets ownership to root |
| `chmod 0440` | Applies secure sudoers file permissions |
| `visudo -cf` | Validates the temporary sudoers configuration |
| `mv` | Installs the validated configuration |
| `visudo -c` | Validates the complete sudoers configuration |

The temporary recovery rule:

```bash
%google-sudoers ALL=(ALL:ALL) NOPASSWD:ALL
```

This grants passwordless sudo privileges to all users belonging to the `google-sudoers` group.

**Security consideration:** This is a broad administrative permission. Use it only as a controlled recovery measure and remove it after restoring the intended sudo configuration.

#### Step 3: Add the startup script to the VM

Execute:

```bash
gcloud compute instances add-metadata demo1-vm \
  --zone=asia-south1-a \
  --metadata-from-file=startup-script=recover-sudo.sh
```

Verify the metadata:

```bash
gcloud compute instances describe demo1-vm \
  --zone=asia-south1-a \
  --format="yaml(metadata.items)"
```

Confirm that the VM metadata now contains:

- `ssh-keys`
- `startup-script`

#### Step 4: Restart the VM

Stop the VM:

```bash
gcloud compute instances stop demo1-vm \
  --zone=asia-south1-a
```

Start the VM:

```bash
gcloud compute instances start demo1-vm \
  --zone=asia-south1-a
```

Wait until the VM reaches the `RUNNING` state.

The GCE startup script executes during boot with root privileges.

### 6. Verify Sudo Access

Reconnect to the VM through SSH-in-browser.

#### Step 1: Verify root access

```bash
sudo whoami
```

**Actual output:**

```text
root
```

This confirmed that sudo access had been restored.

#### Step 2: Verify sudo permissions

```bash
sudo -l
```

**Actual output:**

```text
User ismartarshu may run the following commands on demo1-vm:
    (ALL : ALL) NOPASSWD: ALL
    (ALL : ALL) NOPASSWD: ALL
```

Sudo access was successfully restored.

However, the duplicate entries indicated that two matching sudo rules were being applied. Further investigation was required to identify and remove the temporary duplicate rule safely.

### 7. Post-Recovery Cleanup

**Important:** The following cleanup is a required final step. Do not leave temporary recovery permissions or startup scripts configured unnecessarily.

#### Step 1: Identify existing sudoers rules

Run inside the VM:

```bash
sudo grep -RnsE 'google-sudoers|ismartarshu' \
  /etc/sudoers /etc/sudoers.d 2>/dev/null
```

Inspect the sudoers directory:

```bash
sudo ls -l /etc/sudoers.d/
```

Identify the original Google-managed sudo configuration before removing anything.

#### Step 2: Remove the temporary recovery rule

Only after confirming the original sudo configuration is valid:

```bash
sudo rm -f /etc/sudoers.d/99-google-sudoers-recovery
```

Validate the configuration:

```bash
sudo visudo -c
```

#### Step 3: Remove the startup script from VM metadata

Run in Google Cloud Shell:

```bash
gcloud compute instances remove-metadata demo1-vm \
  --zone=asia-south1-a \
  --keys=startup-script
```

Verify that the startup script has been removed:

```bash
gcloud compute instances describe demo1-vm \
  --zone=asia-south1-a \
  --format="yaml(metadata.items)"
```

#### Step 4: Verify normal sudo access

Reconnect to the VM:

```bash
sudo whoami
```

Expected output:

```text
root
```

Check the permissions:

```bash
sudo -l
```

Confirm that the remaining permissions match the intended original configuration.

### 8. Troubleshooting Commands Reference

| Purpose | Command |
|---|---|
| Identify current user | `whoami` |
| Check user groups | `id` |
| Check sudo privileges | `sudo -l` |
| Inspect sudoers rules | `sudo grep -Rns 'google-sudoers' /etc/sudoers /etc/sudoers.d/` |
| List sudoers files | `sudo ls -l /etc/sudoers.d/` |
| Validate sudoers configuration | `sudo visudo -c` |
| Check VM metadata | `gcloud compute instances describe demo1-vm --zone=asia-south1-a --format="yaml(metadata.items)"` |
| Stop VM | `gcloud compute instances stop demo1-vm --zone=asia-south1-a` |
| Start VM | `gcloud compute instances start demo1-vm --zone=asia-south1-a` |
| Remove startup script | `gcloud compute instances remove-metadata demo1-vm --zone=asia-south1-a --keys=startup-script` |

### 9. Root Cause and Lessons Learned

#### Key findings

1. The SSH user was already a member of `google-sudoers`.
2. Despite group membership, sudo access was denied.
3. The VM did not initially contain a configured startup script.
4. A GCE startup script provided a recovery mechanism because it executed with root privileges during boot.
5. The temporary sudoers configuration successfully restored administrative access.
6. Duplicate sudo permission entries appeared after recovery and required cleanup.

#### Important lessons

- Linux group membership does not automatically guarantee effective sudo permissions.
- Always inspect `/etc/sudoers` and `/etc/sudoers.d/` when troubleshooting sudo access.
- Use `visudo` to validate sudoers configurations.
- GCE startup scripts can provide a recovery mechanism when SSH access works but administrative privileges are unavailable.
- Remove temporary recovery permissions after the intended configuration is restored.
- Keep VM startup metadata and privileged access rules under control.

### 10. Final Recovery Status

| Check | Status |
|---|---|
| SSH connectivity | Successful |
| Linux user identification | Successful |
| `google-sudoers` membership | Confirmed |
| Initial sudo access | Failed |
| Startup script configuration | Successful |
| VM restart | Successful |
| Root access restoration | Successful |
| Duplicate sudo rule investigation | Pending |
| Temporary recovery rule cleanup | Pending |
| Startup script metadata cleanup | Pending |

**Current result:** Sudo access has been successfully restored on the GCP Ubuntu VM. Post-recovery cleanup remains necessary before considering the troubleshooting process fully complete.

---
### Author Notes

**Platform:** Google Cloud Platform  
**Service:** Compute Engine  
**Topic:** Linux Sudo Permission Troubleshooting  
**Recovery Technique:** GCE Startup Script and Sudoers Configuration  
**Documentation Type:** Hands-on troubleshooting guide

---