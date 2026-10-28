# ☁️ The Basics of Google Cloud Compute

Welcome to the ultimate step-by-step guide for **The Basics of Google Cloud Compute**! 

In this lab, you will learn the foundational skills needed to operate in Google Cloud. You will create scalable storage, spin up a Virtual Machine (VM), attach a persistent hard drive, and deploy a live web server. 

By the end of this guide, you will have a working website running on your very own cloud infrastructure!

---

## 🛠️ Task 1: Create a Cloud Storage Bucket
**Objective:** Create a secure place in the cloud to store files, code, and startup scripts.

**The Easiest Way (Cloud Shell):**
Instead of clicking through the menus, we will use the Cloud Shell terminal to do this instantly without mistakes.

1. Open the **Cloud Shell** by clicking the `>_` icon at the top right of your Google Cloud Console.
2. Copy and paste the following block of code into the terminal and press **Enter**:

```bash
# Set your Project ID as a variable
export PROJECT_ID=$(gcloud config get-value project)

# Create a multi-region bucket in the US using your Project ID as the name
gcloud storage buckets create gs://${PROJECT_ID}-bucket --location=US
