# 🔐 Configure Service Accounts and IAM Roles for Google Cloud

A step-by-step guide to configuring Identity and Access Management (IAM) and Service Accounts on Google Cloud. In this lab, you will create service accounts, assign permissions using the `gcloud` CLI, create a custom IAM role using YAML, attach service accounts to virtual machines, and use Python client libraries to query BigQuery.

---

## 🎯 Lab Objectives

By completing this lab, you will learn how to:

- Enable Gemini and explore its capabilities.
- Create service accounts using the Google Cloud CLI.
- Assign IAM roles and permissions.
- Create a Compute Engine VM with an attached service account.
- Define a custom IAM role using a YAML file.
- Access BigQuery public datasets using Python client libraries.
- Verify task completion using the lab progress checker.

---

## ⚙️ Initial Setup

Before starting, open **Cloud Shell** by clicking the `>_` icon in the top-right corner of the Google Cloud Console.

Run the following commands to configure your project ID and compute zone:

```bash
# Get the active Google Cloud Project ID
export PROJECT_ID="$(gcloud config get-value project)"

# Get the default compute zone configured for the lab
export ZONE="$(gcloud config get-value compute/zone)"

# Display the configured values
echo "Project ID: $PROJECT_ID"
echo "Zone: $ZONE"
```

> **Note:** If the zone variable is empty, use the zone specified in your lab instructions.

---

## 🤖 Task 1: Explore Gemini

**Objective:** Enable or access Gemini and ask it a question as described in the lab instructions.

1. Open the Gemini interface specified by your Google Cloud lab.
2. Enable Gemini if the lab requires it and your account has the necessary permissions.
3. Ask the question provided in the lab instructions.
4. Review the response.
5. Return to the lab page and click **Check my progress** for Task 1.

> **Note:** Follow the exact Gemini setup and question specified by your lab environment.

---

## 🛠️ Task 2 & 3: Create a Service Account and Assign IAM Permissions

**Objective:** Create a service account named `devops` and assign the IAM roles required to manage Compute Engine instances.

### Step 1: Create the Service Account

Run the following command in Cloud Shell:

```bash
gcloud iam service-accounts create devops \
    --display-name="devops"
```

### Step 2: Store the Service Account Email

```bash
export SA="devops@${PROJECT_ID}.iam.gserviceaccount.com"

echo "$SA"
```

### Step 3: Grant the Service Account User Role

```bash
gcloud projects add-iam-policy-binding "$PROJECT_ID" \
    --member="serviceAccount:${SA}" \
    --role="roles/iam.serviceAccountUser"
```

### Step 4: Grant the Compute Instance Admin Role

```bash
gcloud projects add-iam-policy-binding "$PROJECT_ID" \
    --member="serviceAccount:${SA}" \
    --role="roles/compute.instanceAdmin.v1"
```

### Step 5: Verify the IAM Bindings

```bash
gcloud projects get-iam-policy "$PROJECT_ID"
```

Check the output for the configured IAM roles and members.

**Task Completion:** Return to the lab page and click **Check my progress** for Tasks 2 and 3.

---

## 🖥️ Task 4: Create a Compute Instance with a Service Account

**Objective:** Create a Compute Engine virtual machine named `vm-2` and attach the `devops` service account.

Run the following command in Cloud Shell:

```bash
gcloud compute instances create vm-2 \
    --zone="$ZONE" \
    --service-account="$SA" \
    --scopes=https://www.googleapis.com/auth/cloud-platform
```

### Verify the VM

```bash
gcloud compute instances describe vm-2 \
    --zone="$ZONE" \
    --format="yaml(name,zone,status,serviceAccounts)"
```

Confirm that the VM exists and has the expected service account attached.

**Task Completion:** Return to the lab page and click **Check my progress** for Task 4.

> **Note:** The identity executing the VM creation command must have permission to attach the selected service account.

---

## 📜 Task 5: Create a Custom IAM Role Using YAML

**Objective:** Create a custom IAM role named `customRole` with permissions to connect to and retrieve information about Cloud SQL instances.

### Step 1: Create the YAML Definition

Run the following command in Cloud Shell:

```bash
cat <<'EOF' > role-definition.yaml
title: "Custom SQL Role"
description: "Custom role to connect and get Cloud SQL instances"
stage: "ALPHA"
includedPermissions:
  - cloudsql.instances.connect
  - cloudsql.instances.get
EOF
```

### Step 2: Review the YAML File

```bash
cat role-definition.yaml
```

The file defines the role title, description, release stage, and included permissions.

### Step 3: Create the Custom Role

```bash
gcloud iam roles create customRole \
    --project="$PROJECT_ID" \
    --file=role-definition.yaml
```

### Step 4: Verify the Custom Role

```bash
gcloud iam roles describe customRole \
    --project="$PROJECT_ID"
```

Confirm that the role contains the two configured Cloud SQL permissions.

**Task Completion:** Return to the lab page and click **Check my progress** for Task 5.

---

## 📊 Task 6: Use Python Client Libraries to Access BigQuery

**Objective:** Create a service account for BigQuery, grant the required IAM roles, attach the account to a VM, and run a Python script that queries a public BigQuery dataset.

### Step 6A: Create the BigQuery Service Account

Run the following command in Cloud Shell:

```bash
gcloud iam service-accounts create bigquery-qwiklab \
    --display-name="BigQuery Qwiklab Service Account"
```

Store the service account email in a variable:

```bash
export BQ_SA="bigquery-qwiklab@${PROJECT_ID}.iam.gserviceaccount.com"

echo "$BQ_SA"
```

### Step 6B: Assign BigQuery IAM Roles

Grant the BigQuery Data Viewer role:

```bash
gcloud projects add-iam-policy-binding "$PROJECT_ID" \
    --member="serviceAccount:${BQ_SA}" \
    --role="roles/bigquery.dataViewer"
```

Grant the BigQuery User role:

```bash
gcloud projects add-iam-policy-binding "$PROJECT_ID" \
    --member="serviceAccount:${BQ_SA}" \
    --role="roles/bigquery.user"
```

These roles provide BigQuery data access and the ability to run queries, subject to the permissions granted by the lab environment.

### Step 6C: Create the Compute Engine VM

Create a VM named `bigquery-instance` and attach the BigQuery service account:

```bash
gcloud compute instances create bigquery-instance \
    --zone="$ZONE" \
    --service-account="$BQ_SA" \
    --scopes=https://www.googleapis.com/auth/cloud-platform
```

Wait for the VM to finish provisioning.

### Step 6D: Connect to the VM Using SSH

1. Open the Google Cloud Console.
2. Navigate to **Compute Engine → VM instances**.
3. Find `bigquery-instance`.
4. Click **SSH**.
5. Wait for the browser-based terminal to open.

Run the remaining commands inside the VM's SSH terminal.

### Step 6E: Install Python Dependencies

Update the package list:

```bash
sudo apt-get update
```

Install Python and pip:

```bash
sudo apt-get install python3-pip -y
```

Install the BigQuery client libraries and supporting packages:

```bash
pip3 install google-cloud-bigquery pandas db-dtypes --break-system-packages
```

> **Note:** The `--break-system-packages` option is used in some Debian-based lab environments. If the lab specifies a virtual environment, use that environment instead.

### Step 6F: Configure the Project ID Inside the VM

```bash
export PROJECT_ID="$(gcloud config get-value project)"

echo "Project ID: $PROJECT_ID"
```

The VM must be able to determine the project ID. In managed lab environments, use the project ID assigned to your lab.

### Step 6G: Create the Python Query Script

Run the following command to create `query.py`:

```bash
cat <<'EOF' > query.py
from google.cloud import bigquery

# Use the credentials of the service account attached to this VM.
client = bigquery.Client()

query = """
SELECT
    name,
    SUM(number) AS total_people
FROM
    `bigquery-public-data.usa_names.usa_1910_2013`
WHERE
    state = 'TX'
GROUP BY
    name
ORDER BY
    total_people DESC
LIMIT 20
"""

# Execute the query and display the results.
query_job = client.query(query)
results = query_job.to_dataframe()

print(results)
EOF
```

The script uses Application Default Credentials from the VM's attached service account. It queries the public USA Names dataset, filters results for Texas (`TX`), calculates totals by name, and returns the top 20 results.

### Step 6H: Run the Python Script

Execute the script:

```bash
python3 query.py
```

If successful, the terminal will display a table containing names and their corresponding totals.

### Step 6I: Troubleshooting

If the script fails, check the following:

- The BigQuery service account is attached to `bigquery-instance`.
- The service account has the required BigQuery IAM roles.
- The VM has the required OAuth access scope.
- The Python packages are installed correctly.
- The query references the correct public dataset.
- The lab project and service account are configured correctly.

You can inspect the VM's attached service account from Cloud Shell:

```bash
gcloud compute instances describe bigquery-instance \
    --zone="$ZONE" \
    --format="yaml(name,serviceAccounts)"
```

**Task Completion:** Return to the lab page and click **Check my progress** for Task 6.

---

## ✅ Final Checklist

- [ ] Completed Task 1 according to the lab instructions.
- [ ] Created the `devops` service account.
- [ ] Assigned the required IAM roles.
- [ ] Created `vm-2` with the `devops` service account attached.
- [ ] Created `role-definition.yaml`.
- [ ] Created and verified the `customRole` IAM role.
- [ ] Created the `bigquery-qwiklab` service account.
- [ ] Assigned the required BigQuery IAM roles.
- [ ] Created `bigquery-instance` with the BigQuery service account attached.
- [ ] Connected to the VM using SSH.
- [ ] Installed the Python BigQuery client libraries.
- [ ] Created and executed `query.py`.
- [ ] Verified that the BigQuery query returned results.
- [ ] Clicked **Check my progress** for all required tasks.

---

## 📚 Key Concepts Learned

| Concept | Description |
|---|---|
| IAM | Controls who can access Google Cloud resources and what actions they can perform. |
| Service Account | An identity used by applications and virtual machines to access Google Cloud services. |
| IAM Role | A collection of permissions assigned to a user, group, or service account. |
| Custom Role | A role created with a selected set of permissions for a specific purpose. |
| YAML | A human-readable format used to define configuration data. |
| Compute Engine | A Google Cloud service for creating and managing virtual machines. |
| BigQuery | A serverless data warehouse used to analyse large datasets with SQL. |
| Python Client Library | A library that allows Python applications to interact with Google Cloud services. |
| Application Default Credentials | A mechanism for applications to find credentials for accessing Google Cloud APIs. |
| Cloud Shell | A browser-accessible command-line environment for managing Google Cloud resources. |

---

## 🧹 Optional Cleanup

If you are working in your own Google Cloud project, delete resources when they are no longer needed to avoid unnecessary charges. Before deleting resources, confirm that you no longer need their data or configuration.

For a managed Google Cloud training lab, follow its cleanup instructions and do not delete resources before completing the assessment.

---

## 🎯 Result

Successfully configured service accounts and IAM roles, created a Compute Engine VM with an attached service account, defined a custom IAM role using YAML, and used Python client libraries to query a public BigQuery dataset.
