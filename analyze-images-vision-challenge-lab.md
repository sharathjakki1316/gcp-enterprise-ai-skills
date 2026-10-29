# 👁️ Analyze Images with the Cloud Vision API: Challenge Lab

A step-by-step guide to completing the **Analyze Images with the Cloud Vision API: Challenge Lab (ARC122)** using Google Cloud, Cloud Storage, API keys, JSON requests, and `curl`.

In this lab, you will use the Google Cloud Vision API to detect text in images using Optical Character Recognition (OCR) and identify landmarks in images stored in a Cloud Storage bucket.

---

## 🎯 Lab Objectives

By completing this lab, you will learn how to:

- Enable the Cloud Vision API.
- Create and use an API key.
- Access images stored in Cloud Storage.
- Create JSON request payloads.
- Perform text detection using the Vision API.
- Perform landmark detection using the Vision API.
- Send API requests using `curl`.
- Save API responses and upload them to Cloud Storage.

---

## ⚙️ Task 1: Verify Resources and Create an API Key

**Objective:** Enable the Vision API, create an API key, and locate an image stored in Cloud Storage.

### Step 1A: Enable the Vision API

1. Open the Google Cloud Console.
2. Click the **Activate Cloud Shell (`>_`)** icon in the top-right corner.
3. Run the following command:

```bash
gcloud services enable vision.googleapis.com
```

Wait for the command to finish successfully.

### Step 1B: Create an API Key

1. In the Google Cloud Console, open **Navigation Menu → APIs & Services → Credentials**.
2. Click **Create credentials**.
3. Select **API key**.
4. Copy the generated API key.

In Cloud Shell, replace `YOUR_API_KEY` with your actual API key:

```bash
export API_KEY="YOUR_API_KEY"
```

Verify that the variable is set without printing the key:

```bash
if [ -n "$API_KEY" ]; then
    echo "API key configured."
else
    echo "API key is missing."
fi
```

> **Security Note:** Do not publish your API key in a public GitHub repository or include it in screenshots. Use the lab-provided API key instructions and restrictions.

### Step 1C: Configure the Project and Bucket

Run the following commands in Cloud Shell:

```bash
# Get the active Google Cloud project ID
export PROJECT_ID="$(gcloud config get-value project)"

# Set the Cloud Storage bucket name
export BUCKET="gs://${PROJECT_ID}-bucket"

# Display the configured project and bucket
echo "Project ID: $PROJECT_ID"
echo "Bucket: $BUCKET"
```

> **Important:** Use the exact bucket name specified by your lab if it differs from `${PROJECT_ID}-bucket`.

### Step 1D: Find an Image in the Bucket

List the objects stored in your bucket:

```bash
gcloud storage ls "$BUCKET"
```

Select the first object returned by the command:

```bash
export IMAGE_URI="$(gcloud storage ls "$BUCKET" | head -n 1)"

echo "Target Image URI: $IMAGE_URI"
```

Confirm that `IMAGE_URI` contains a valid image object in Cloud Storage before continuing.

**Task Completion:** Verify the required resources in your lab and follow the lab's progress checker instructions.

---

## 📄 Tasks 2 & 3: Text Detection and Analysis

**Objective:** Create a JSON request for text detection, call the Cloud Vision API, save the response, and upload the response to Cloud Storage.

### Step 2A: Create the JSON Request

Run the following command in Cloud Shell:

```bash
cat <<EOF > request.json
{
  "requests": [
    {
      "image": {
        "source": {
          "gcsImageUri": "${IMAGE_URI}"
        }
      },
      "features": [
        {
          "type": "TEXT_DETECTION",
          "maxResults": 10
        }
      ]
    }
  ]
}
EOF
```

This request instructs the Vision API to detect text in the selected image.

### Step 2B: Review the Request

```bash
cat request.json
```

Confirm that the image URI and feature type are correct.

### Step 2C: Call the Cloud Vision API

Run the following command to submit the request and save the response:

```bash
curl -fSs -X POST \
    -H "Content-Type: application/json" \
    --data-binary @request.json \
    "https://vision.googleapis.com/v1/images:annotate?key=${API_KEY}" \
    -o text-response.json
```

The API response will be saved in `text-response.json`.

### Step 2D: Review the Text Detection Results

```bash
cat text-response.json
```

The response may include detected text, individual text annotations, bounding boxes, and other information.

For easier reading, you can use Python to format the JSON:

```bash
python3 -m json.tool text-response.json
```

### Step 2E: Upload the Response to Cloud Storage

```bash
gcloud storage cp text-response.json \
    "${BUCKET}/text-response.json"
```

Verify that the response file was uploaded:

```bash
gcloud storage ls "${BUCKET}/text-response.json"
```

### Task Completion

Return to the lab page and click **Check my progress** for the first **Analyze the image with the Cloud Vision API** task.

---

## 🏛️ Task 4: Landmark Detection and Analysis

**Objective:** Detect geographical landmarks in the image, save the API response, and upload it to Cloud Storage.

### Step 4A: Create the Landmark Detection Request

Run the following command in Cloud Shell:

```bash
cat <<EOF > request.json
{
  "requests": [
    {
      "image": {
        "source": {
          "gcsImageUri": "${IMAGE_URI}"
        }
      },
      "features": [
        {
          "type": "LANDMARK_DETECTION",
          "maxResults": 10
        }
      ]
    }
  ]
}
EOF
```

This request instructs the Vision API to identify recognizable landmarks in the selected image.

### Step 4B: Review the Request

```bash
cat request.json
```

Confirm that the image URI is correct and that the feature type is set to `LANDMARK_DETECTION`.

### Step 4C: Call the Cloud Vision API

Run the following command:

```bash
curl -fSs -X POST \
    -H "Content-Type: application/json" \
    --data-binary @request.json \
    "https://vision.googleapis.com/v1/images:annotate?key=${API_KEY}" \
    -o landmark-response.json
```

The API response will be saved in `landmark-response.json`.

### Step 4D: Review the Landmark Detection Results

```bash
cat landmark-response.json
```

For formatted output, run:

```bash
python3 -m json.tool landmark-response.json
```

If landmarks are detected, the response can contain landmark descriptions, confidence scores, and location information.

> **Note:** The API may return no landmark annotations if the image does not contain a recognizable landmark. This does not necessarily indicate an API error.

### Step 4E: Upload the Response to Cloud Storage

```bash
gcloud storage cp landmark-response.json \
    "${BUCKET}/landmark-response.json"
```

Verify the uploaded file:

```bash
gcloud storage ls "${BUCKET}/landmark-response.json"
```

### Task Completion

Return to the lab page and click **Check my progress** for the final **Analyze the image with the Cloud Vision API** task.

---

## 🔍 Verification and Troubleshooting

### Verify the API Is Enabled

```bash
gcloud services list --enabled \
    --filter="name:vision.googleapis.com"
```

### Verify the Bucket Contents

```bash
gcloud storage ls "$BUCKET"
```

### Verify the Text Detection Response

```bash
python3 -m json.tool text-response.json
```

### Verify the Landmark Detection Response

```bash
python3 -m json.tool landmark-response.json
```

### Verify Both Uploaded Files

```bash
gcloud storage ls "$BUCKET"
```

Look for the following objects:

- `text-response.json`
- `landmark-response.json`

### Common Problems

**1. API key error**

Ensure that the API key is correct and that the Vision API is enabled for the relevant project. Check any API key restrictions configured in your project.

**2. Image not found**

Verify the image URI:

```bash
echo "$IMAGE_URI"
```

Check that the image exists in Cloud Storage:

```bash
gcloud storage ls "$IMAGE_URI"
```

**3. Permission denied**

Ensure that the identity calling the API has the required permissions and that the image can be accessed by the Vision API. Follow the access configuration provided by the lab.

**4. Empty image URI**

If `IMAGE_URI` is empty, list the bucket contents:

```bash
gcloud storage ls "$BUCKET"
```

Then assign an actual image object URI:

```bash
export IMAGE_URI="gs://YOUR_BUCKET_NAME/YOUR_IMAGE_FILE"
```

Replace the placeholders with the actual bucket name and image filename.

**5. API request failed**

Check the error response and confirm that the request uses valid JSON, the correct image URI, and a valid feature type.

---

## ✅ Final Checklist

- [ ] Enabled the Cloud Vision API.
- [ ] Created an API key.
- [ ] Configured the project ID and Cloud Storage bucket.
- [ ] Located an image stored in Cloud Storage.
- [ ] Created the text detection JSON request.
- [ ] Called the Vision API for text detection.
- [ ] Saved the response as `text-response.json`.
- [ ] Uploaded the text detection response to Cloud Storage.
- [ ] Created the landmark detection JSON request.
- [ ] Called the Vision API for landmark detection.
- [ ] Saved the response as `landmark-response.json`.
- [ ] Uploaded the landmark detection response to Cloud Storage.
- [ ] Verified both uploaded response files.
- [ ] Clicked **Check my progress** for all required tasks.

---

## 📚 Key Concepts Learned

| Concept | Description |
|---|---|
| Cloud Vision API | A Google Cloud service that analyses images and detects visual information. |
| API Key | A credential used to identify API requests and apply configured restrictions. |
| Cloud Storage | Object storage for images, files, and API response data. |
| OCR | Optical Character Recognition, used to identify text within images. |
| Text Detection | Detects and extracts text present in an image. |
| Landmark Detection | Identifies recognizable geographical or built landmarks in images. |
| JSON | A structured data format commonly used to send API requests and receive responses. |
| `curl` | A command-line tool for sending HTTP requests to APIs. |
| REST API | An interface that allows applications to communicate through HTTP requests. |

---

## 🧹 Optional Cleanup

If you are using your own Google Cloud project, remove resources that are no longer required, following your project's retention and security policies.

Do not delete resources or response files before completing the assessment in a managed training lab. Follow the lab's cleanup instructions.

---

## 🎯 Result

Successfully configured the Cloud Vision API, identified an image stored in Cloud Storage, performed text detection and landmark detection using JSON requests and `curl`, and uploaded both API responses to the Cloud Storage bucket.
