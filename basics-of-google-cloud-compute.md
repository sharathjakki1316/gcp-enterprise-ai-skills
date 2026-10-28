# ☁️ The Basics of Google Cloud Compute

A step-by-step guide to creating a Cloud Storage bucket, launching a Compute Engine virtual machine (VM), attaching a persistent disk, and deploying an NGINX web server on Google Cloud.

## 📌 Lab Objectives

By completing this lab, you will learn how to:

- Create a Google Cloud Storage bucket.
- Create a Compute Engine virtual machine.
- Create and attach a persistent disk.
- Install and verify an NGINX web server.
- Access a live website using the VM's external IP address.

---

## 🛠️ Task 1: Create a Cloud Storage Bucket

**Objective:** Create a Cloud Storage bucket to store files, code, and other resources.

### Step 1: Open Cloud Shell

1. Open the Google Cloud Console.
2. Click the **Activate Cloud Shell (`>_`)** icon in the top-right corner.
3. Authorize Cloud Shell if prompted.

### Step 2: Create the Bucket

Run the following commands in Cloud Shell:

```bash
# Get the active Google Cloud Project ID
export PROJECT_ID="$(gcloud config get-value project)"

# Create a bucket in the US multi-region
gcloud storage buckets create "gs://${PROJECT_ID}-bucket" --location=US
```

### Step 3: Verify the Bucket

```bash
gcloud storage buckets list
```

Check that your newly created bucket appears in the output.

**Task Completion:** Return to the lab instructions and click **Check my progress** for Task 1.

> **Note:** Bucket names must be globally unique. Follow your lab's required bucket name and location if they differ from these instructions.

---

## 🖥️ Task 2: Create and Attach a Persistent Disk to a VM

**Objective:** Create a virtual machine named `my-instance`, create a 200 GB persistent disk named `mydisk`, and attach the disk to the VM.

### Step 2A: Create the Virtual Machine

1. Open the Google Cloud Console.
2. Navigate to **Navigation Menu → Compute Engine → VM instances**.
3. Click **Create instance**.
4. Configure the VM using the following settings:

| Setting | Value |
|---|---|
| Name | `my-instance` |
| Region/Zone | Use the default provided by the lab |
| Series | `E2` |
| Machine type | `e2-medium` |
| Boot disk | Keep the lab's default configuration |
| Firewall | Enable **Allow HTTP traffic** |

5. Click **Create**.
6. Wait until the VM is running.

### Step 2B: Create the Persistent Disk

1. Navigate to **Compute Engine → Disks**.
2. Click **Create disk**.
3. Configure the disk:

| Setting | Value |
|---|---|
| Name | `mydisk` |
| Region/Zone | Same zone as `my-instance` |
| Size | `200 GB` |
| Disk type | Use the lab's required type or default |

4. Click **Create**.
5. Wait until the disk is created.

> **Important:** Ensure that the disk is created in the same zone as the VM so it can be attached successfully.

### Step 2C: Attach the Persistent Disk

1. Navigate to **Compute Engine → VM instances**.
2. Click the `my-instance` VM.
3. Click **Edit**.
4. Scroll down to the **Additional disks** section.
5. Click **Attach existing disk**.
6. Select `mydisk`.
7. Scroll to the bottom and click **Save**.
8. Verify that the disk appears in the VM's attached disks list.

**Task Completion:** Return to the lab instructions and click **Check my progress** for Task 2.

> **Note:** Attaching a disk does not automatically format or mount it inside the operating system. Perform those steps only if the lab requires them.

---

## 🌐 Task 3: Install an NGINX Web Server

**Objective:** Connect to the VM, install NGINX, and verify that the web server is running.

### Step 3A: Connect to the VM

1. Navigate to **Compute Engine → VM instances**.
2. Find `my-instance`.
3. Click **SSH** next to the VM.
4. Wait for the browser-based terminal to open.

### Step 3B: Update the Operating System

Run:

```bash
sudo apt-get update
```

Wait for the package list to finish updating.

### Step 3C: Install NGINX

Run:

```bash
sudo apt-get install nginx -y
```

The `-y` option automatically confirms installation prompts.

### Step 3D: Verify NGINX

Run:

```bash
sudo systemctl status nginx
```

Look for the following status:

```text
active (running)
```

Press `q` to exit the status screen.

Alternatively, run:

```bash
sudo systemctl is-active nginx
```

Expected output:

```text
active
```

**Task Completion:** Return to the lab instructions and click **Check my progress** for Task 3.

---

## 🎉 Bonus: Access the Live Website

After installing NGINX, you can access the default website hosted on your VM.

### Steps

1. Navigate to **Compute Engine → VM instances**.
2. Find `my-instance`.
3. Copy or click its **External IP** address.
4. Open a browser and enter the following URL, replacing `EXTERNAL_IP` with your VM's actual external IP address:

```text
http://EXTERNAL_IP
```

If everything is configured correctly, you should see the default **Welcome to nginx!** webpage.

### Troubleshooting

If the website does not open, check the following:

- The VM is running.
- The VM has an external IP address.
- NGINX is running:

  ```bash
  sudo systemctl is-active nginx
  ```

- **Allow HTTP traffic** was enabled when creating the VM, or an appropriate firewall rule allows inbound TCP traffic on port `80`.
- You are using `http://` in the browser address.

---

## ✅ Final Checklist

- [ ] Created a Cloud Storage bucket.
- [ ] Created the `my-instance` virtual machine.
- [ ] Created the `mydisk` persistent disk with the required size.
- [ ] Attached `mydisk` to `my-instance`.
- [ ] Installed NGINX.
- [ ] Verified that NGINX is running.
- [ ] Accessed the default NGINX webpage using the external IP.
- [ ] Clicked **Check my progress** for all required tasks.

---

## 📚 Key Concepts Learned

| Concept | Description |
|---|---|
| Cloud Storage | Object storage for files and data. |
| Compute Engine | A Google Cloud service for creating and managing virtual machines. |
| Persistent Disk | Durable block storage that can be attached to a VM. |
| SSH | A secure method of accessing a remote machine's terminal. |
| NGINX | Web server software used to serve web pages over HTTP. |
| Firewall Rules | Rules that control incoming and outgoing network traffic. |
| External IP | An IP address used to access a VM over the internet when network rules permit it. |

---

## 🧹 Optional Cleanup

If you are using your own Google Cloud project, delete resources when you no longer need them to avoid unnecessary charges. Before deleting resources, ensure that you no longer need their data.

If you are completing a managed lab, follow its cleanup instructions and do not delete resources before completing the assessment.

---

## 🎯 Result

Successfully created a Cloud Storage bucket, launched a Compute Engine VM, attached a persistent disk, installed NGINX, and hosted a webpage accessible through the VM's external IP address.
