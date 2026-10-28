# 🌐 Build a Website on Google Cloud: Challenge Lab

A step-by-step guide to deploying the FancyStore e-commerce application on Google Cloud using Google Kubernetes Engine (GKE), Cloud Build, container images, and microservices.

In this lab, you will deploy the original monolithic application, separate the Orders and Products microservices, configure the frontend, and expose the application to the internet.

---

## 🎯 Lab Objectives

By completing this lab, you will learn how to:

- Clone an application source code repository.
- Build container images using Google Cloud Build.
- Push container images to Google Container Registry (GCR).
- Create a Google Kubernetes Engine cluster.
- Deploy and expose Kubernetes applications.
- Build and deploy Orders and Products microservices.
- Configure frontend environment variables.
- Deploy a frontend application and access it using an external IP address.

---

## ⚙️ Initial Setup: Define Your Lab Variables

Before running any commands, identify the resource names and locations provided by your challenge lab.

### Step 1: Open Cloud Shell

1. Open the Google Cloud Console.
2. Click the **Activate Cloud Shell (`>_`)** icon in the top-right corner.
3. Wait for Cloud Shell to initialize.

### Step 2: Configure the Variables

Replace every placeholder below with the exact value from your lab instructions.

```bash
# Google Cloud project
export PROJECT_ID="$(gcloud config get-value project)"

# Replace these placeholders with the exact values from your lab
export REGION="YOUR_REGION"
export ZONE="YOUR_ZONE"
export CLUSTER_NAME="YOUR_CLUSTER_NAME"
export MONOLITH_IDENTIFIER="YOUR_MONOLITH_IDENTIFIER"
export ORDERS_IDENTIFIER="YOUR_ORDERS_IDENTIFIER"
export PRODUCTS_IDENTIFIER="YOUR_PRODUCTS_IDENTIFIER"
export FRONTEND_IDENTIFIER="YOUR_FRONTEND_IDENTIFIER"

# Verify the configuration
echo "Project ID: $PROJECT_ID"
echo "Region: $REGION"
echo "Zone: $ZONE"
echo "Cluster: $CLUSTER_NAME"
```

> **Important:** Replace all `YOUR_...` placeholders with the exact resource names specified by your challenge lab. The commands below use the project ID returned by `gcloud config get-value project`.

---

## 🛠️ Task 1: Download the Monolith Code and Build the Container

**Objective:** Download the FancyStore source code, run the setup script, and build the monolithic application container image.

### Step 1: Clone the Repository

Run the following command in Cloud Shell:

```bash
git clone https://github.com/googlecodelabs/monolith-to-microservices.git
```

Navigate to the project directory:

```bash
cd ~/monolith-to-microservices
```

### Step 2: Run the Setup Script

```bash
./setup.sh
```

### Step 3: Install the Node.js LTS Version

```bash
nvm install --lts
```

### Step 4: Build and Push the Monolith Container

Navigate to the monolith directory:

```bash
cd ~/monolith-to-microservices/monolith
```

Build the container image and submit it to Google Cloud Build:

```bash
gcloud builds submit \
    --tag="gcr.io/${PROJECT_ID}/${MONOLITH_IDENTIFIER}:1.0.0" \
    .
```

Wait for the build to complete successfully.

### Task Completion

Return to the challenge lab page and click **Check my progress** for Task 1.

---

## 🖥️ Task 2: Create a Kubernetes Cluster and Deploy the Monolith

**Objective:** Create a three-node GKE cluster, deploy the monolithic application, and expose it through a LoadBalancer service.

### Step 1: Create the GKE Cluster

Run the following command in Cloud Shell:

```bash
gcloud container clusters create "$CLUSTER_NAME" \
    --num-nodes=3 \
    --zone="$ZONE"
```

> **Note:** This command creates a zonal cluster. Use the exact location and cluster configuration required by your challenge lab. Do not specify both `--zone` and `--region` in the same command.

Wait for the cluster to finish provisioning.

### Step 2: Configure kubectl

Retrieve the cluster credentials:

```bash
gcloud container clusters get-credentials "$CLUSTER_NAME" \
    --zone="$ZONE" \
    --project="$PROJECT_ID"
```

Verify the cluster connection:

```bash
kubectl get nodes
```

### Step 3: Deploy the Monolith

```bash
kubectl create deployment "$MONOLITH_IDENTIFIER" \
    --image="gcr.io/${PROJECT_ID}/${MONOLITH_IDENTIFIER}:1.0.0"
```

### Step 4: Expose the Monolith

```bash
kubectl expose deployment "$MONOLITH_IDENTIFIER" \
    --type=LoadBalancer \
    --port=80 \
    --target-port=8080
```

### Step 5: Verify the Deployment

```bash
kubectl get deployments
kubectl get pods
kubectl get services
```

Wait for the application pods to become ready and for the LoadBalancer to receive an external IP address.

### Task Completion

Return to the challenge lab page and click **Check my progress** for Task 2.

---

## 📦 Task 3: Build and Deploy the Orders Microservice

**Objective:** Build the Orders microservice container, push it to the container registry, and deploy it to GKE.

### Step 1: Navigate to the Orders Directory

```bash
cd ~/monolith-to-microservices/microservices/src/orders
```

### Step 2: Build and Push the Orders Container

```bash
gcloud builds submit \
    --tag="gcr.io/${PROJECT_ID}/${ORDERS_IDENTIFIER}:1.0.0" \
    .
```

### Step 3: Deploy the Orders Microservice

```bash
kubectl create deployment "$ORDERS_IDENTIFIER" \
    --image="gcr.io/${PROJECT_ID}/${ORDERS_IDENTIFIER}:1.0.0"
```

### Step 4: Expose the Orders Microservice

```bash
kubectl expose deployment "$ORDERS_IDENTIFIER" \
    --type=LoadBalancer \
    --port=80 \
    --target-port=8081
```

### Step 5: Verify the Orders Service

```bash
kubectl get deployments
kubectl get pods
kubectl get services
```

Wait until the Orders deployment is ready.

### Task Completion

Return to the challenge lab page and click **Check my progress** for Task 3.

---

## 📦 Task 4: Build and Deploy the Products Microservice

**Objective:** Build the Products microservice container, push it to the container registry, and deploy it to GKE.

### Step 1: Navigate to the Products Directory

```bash
cd ~/monolith-to-microservices/microservices/src/products
```

### Step 2: Build and Push the Products Container

```bash
gcloud builds submit \
    --tag="gcr.io/${PROJECT_ID}/${PRODUCTS_IDENTIFIER}:1.0.0" \
    .
```

### Step 3: Deploy the Products Microservice

```bash
kubectl create deployment "$PRODUCTS_IDENTIFIER" \
    --image="gcr.io/${PROJECT_ID}/${PRODUCTS_IDENTIFIER}:1.0.0"
```

### Step 4: Expose the Products Microservice

```bash
kubectl expose deployment "$PRODUCTS_IDENTIFIER" \
    --type=LoadBalancer \
    --port=80 \
    --target-port=8082
```

### Step 5: Verify the Products Service

```bash
kubectl get deployments
kubectl get pods
kubectl get services
```

Wait until the Products deployment is ready.

### Task Completion

Return to the challenge lab page and click **Check my progress** for Task 4.

---

## 🌐 Task 5: Configure the Frontend Microservice

**Objective:** Obtain the external IP addresses of the Orders and Products microservices and configure the frontend to communicate with them.

### Step 1: Wait for the Orders External IP

Run the following commands in Cloud Shell:

```bash
echo "Waiting for the Orders service external IP..."

ORDERS_IP=""

while [ -z "$ORDERS_IP" ]; do
    sleep 5
    ORDERS_IP="$(kubectl get service "$ORDERS_IDENTIFIER" \
        -o jsonpath='{.status.loadBalancer.ingress[0].ip}' 2>/dev/null)"
done

echo "Orders IP: $ORDERS_IP"
```

### Step 2: Wait for the Products External IP

```bash
echo "Waiting for the Products service external IP..."

PRODUCTS_IP=""

while [ -z "$PRODUCTS_IP" ]; do
    sleep 5
    PRODUCTS_IP="$(kubectl get service "$PRODUCTS_IDENTIFIER" \
        -o jsonpath='{.status.loadBalancer.ingress[0].ip}' 2>/dev/null)"
done

echo "Products IP: $PRODUCTS_IP"
```

### Step 3: Verify the External IP Addresses

```bash
echo "Orders URL: http://${ORDERS_IP}/api/orders"
echo "Products URL: http://${PRODUCTS_IP}/api/products"
```

### Step 4: Navigate to the React Frontend Directory

```bash
cd ~/monolith-to-microservices/react-app
```

### Step 5: Create the Frontend Environment File

```bash
cat > .env <<EOF
REACT_APP_ORDERS_URL=http://${ORDERS_IP}/api/orders
REACT_APP_PRODUCTS_URL=http://${PRODUCTS_IP}/api/products
EOF
```

Verify the file:

```bash
cat .env
```

The environment file should contain the actual external IP addresses of the Orders and Products services.

### Task Completion

Return to the challenge lab page and click **Check my progress** for Task 5.

---

## 🏗️ Task 6: Build the Frontend Container

**Objective:** Install the frontend dependencies, generate the React application build, and submit the frontend container image to Google Cloud Build.

### Step 1: Install Dependencies

Make sure you are in the React frontend directory:

```bash
cd ~/monolith-to-microservices/react-app
```

Run:

```bash
npm install
```

### Step 2: Build the React Application

```bash
npm run build
```

Wait for the production build to complete successfully.

### Step 3: Build and Push the Frontend Container

```bash
gcloud builds submit \
    --tag="gcr.io/${PROJECT_ID}/${FRONTEND_IDENTIFIER}:1.0.0" \
    .
```

Wait for Google Cloud Build to finish.

### Step 4: Verify the Container Image

```bash
gcloud container images list
```

> **Note:** The exact repository-listing command may vary depending on the registry used by your lab. Follow the lab instructions if it uses Artifact Registry instead of Google Container Registry.

### Task Completion

Return to the challenge lab page and click **Check my progress** for Task 6.

---

## 🚀 Task 7: Deploy and Expose the Frontend

**Objective:** Deploy the frontend container to GKE and expose it through an external LoadBalancer IP address.

### Step 1: Create the Frontend Deployment

Run the following command in Cloud Shell:

```bash
kubectl create deployment "$FRONTEND_IDENTIFIER" \
    --image="gcr.io/${PROJECT_ID}/${FRONTEND_IDENTIFIER}:1.0.0"
```

### Step 2: Expose the Frontend

```bash
kubectl expose deployment "$FRONTEND_IDENTIFIER" \
    --type=LoadBalancer \
    --port=80 \
    --target-port=8080
```

### Step 3: Verify the Frontend Deployment

```bash
kubectl get deployments
kubectl get pods
kubectl get services
```

Wait for the frontend pod to become ready and for the LoadBalancer to receive an external IP.

### Step 4: Retrieve the Frontend External IP

```bash
kubectl get service "$FRONTEND_IDENTIFIER"
```

To display only the external IP address:

```bash
kubectl get service "$FRONTEND_IDENTIFIER" \
    -o jsonpath='{.status.loadBalancer.ingress[0].ip}'

echo
```

### Step 5: Open the Website

Copy the external IP address from the output and open the following URL in your browser, replacing `EXTERNAL_IP` with the actual address:

```text
http://EXTERNAL_IP
```

If the configuration is correct, the FancyStore website should load in your browser.

### Task Completion

Return to the challenge lab page and click **Check my progress** for Task 7.

---

## 🔍 Verification and Troubleshooting

### Check All Deployments

```bash
kubectl get deployments
```

### Check All Pods

```bash
kubectl get pods
```

### Check All Services and External IP Addresses

```bash
kubectl get services
```

### Check Pod Logs

Replace `POD_NAME` with the actual pod name:

```bash
kubectl logs POD_NAME
```

### Describe a Deployment

Replace `DEPLOYMENT_NAME` with the relevant deployment name:

```bash
kubectl describe deployment DEPLOYMENT_NAME
```

### Common Problems

**1. The external IP remains pending**

LoadBalancer provisioning may take several minutes. Check the service status:

```bash
kubectl get services
```

**2. The container image cannot be pulled**

Verify that the image name and tag are correct:

```bash
gcloud container images list
```

Also verify that the cluster has permission to pull the image from the registry.

**3. The deployment is not ready**

Check the pod status and logs:

```bash
kubectl get pods
kubectl describe pods
kubectl logs POD_NAME
```

**4. The frontend cannot reach the microservices**

Verify the environment variables in the React frontend's `.env` file and confirm that the Orders and Products services have external IP addresses.

If the frontend build has already completed, rebuild the React application after changing its environment variables.

**5. A resource already exists**

Check the existing resource before creating it again:

```bash
kubectl get deployments
kubectl get services
```

If the lab has already created a resource, verify its configuration instead of blindly recreating it.

---

## ✅ Final Checklist

- [ ] Configured all required lab variables.
- [ ] Cloned the monolith-to-microservices repository.
- [ ] Ran the setup script.
- [ ] Built and pushed the monolith container.
- [ ] Created the GKE cluster.
- [ ] Configured kubectl credentials.
- [ ] Deployed and exposed the monolith.
- [ ] Built and deployed the Orders microservice.
- [ ] Built and deployed the Products microservice.
- [ ] Retrieved the Orders external IP address.
- [ ] Retrieved the Products external IP address.
- [ ] Configured the frontend `.env` file.
- [ ] Built the React frontend.
- [ ] Built and pushed the frontend container image.
- [ ] Deployed and exposed the frontend.
- [ ] Opened the FancyStore website using its external IP address.
- [ ] Clicked **Check my progress** for every required task.

---

## 📚 Key Concepts Learned

| Concept | Description |
|---|---|
| Google Kubernetes Engine (GKE) | A managed Kubernetes service for deploying and operating containerized applications. |
| Kubernetes Cluster | A group of nodes that run containerized workloads. |
| Kubernetes Deployment | Manages application pods and supports scaling and updates. |
| Kubernetes Service | Exposes applications and enables network communication. |
| LoadBalancer | Provides an external IP address for accessing a service from outside the cluster. |
| Container Image | A packaged application and its dependencies. |
| Google Cloud Build | Builds container images and runs build processes in Google Cloud. |
| Google Container Registry (GCR) | A container image registry used by some Google Cloud labs. |
| Microservices | An architectural approach that separates an application into independently deployable services. |
| React | A JavaScript library used to build user interfaces. |
| Environment Variables | Configuration values used by applications to connect to external services. |

---

## 🧹 Optional Cleanup

If you are working in your own Google Cloud project, delete unused resources when you no longer need them to help avoid unnecessary charges.

Before deleting anything, ensure that you have completed the challenge lab and no longer need the resources.

For a managed Google Cloud training lab, follow its cleanup instructions.

---

## 🎯 Result

Successfully built and deployed the FancyStore monolithic application and its Orders, Products, and frontend microservices on Google Kubernetes Engine. Configured the frontend to communicate with the backend services and exposed the website using an external LoadBalancer IP address.
