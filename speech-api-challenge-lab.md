# 🎙️ Using the Google Cloud Speech API: Challenge Lab

A step-by-step guide to completing the **Using the Google Cloud Speech API: Challenge Lab (ARC131)** using Google Cloud, Speech-to-Text API, Cloud Storage, API keys, JSON requests, and `curl`.

In this lab, you will process English WAV and Spanish FLAC audio files using the Google Cloud Speech-to-Text API, save the transcription responses, and upload the request and response files to Cloud Storage.

---

## 🎯 Lab Objectives

By completing this lab, you will learn how to:

- Enable the Google Cloud Speech-to-Text API.
- Create and configure an API key.
- Create a Cloud Storage bucket.
- Prepare JSON request files for audio transcription.
- Transcribe English audio using the Speech-to-Text API.
- Transcribe Spanish audio using the Speech-to-Text API.
- Send API requests using `curl`.
- Upload request and response files to Cloud Storage.
- Verify task completion using the lab progress checker.

---

## ⚙️ Task 1: Create an API Key and Enable Services

**Objective:** Enable the Speech-to-Text API and create an API key for making API requests.

### Step 1A: Open Cloud Shell

1. Open the Google Cloud Console.
2. Click the **Activate Cloud Shell (`>_`)** icon in the top-right corner.
3. Wait for Cloud Shell to initialize.

### Step 1B: Enable the Speech-to-Text API

Run the following command:

```bash
gcloud services enable speech.googleapis.com
```

Wait for the command to complete successfully.

### Step 1C: Create an API Key

1. In the Google Cloud Console, navigate to **Navigation Menu → APIs & Services → Credentials**.
2. Click **Create credentials**.
3. Select **API key**.
4. Copy the generated API key.

### Step 1D: Export the API Key

In Cloud Shell, replace `YOUR_API_KEY` with your actual API key:

```bash
export API_KEY="YOUR_API_KEY"
```

Verify that the key is configured without displaying its value:

```bash
if [ -n "$API_KEY" ]; then
    echo "API key configured."
else
    echo "API key is missing."
fi
```

> **Security Note:** Do not publish your API key in a public GitHub repository. Use the restrictions and security settings required by your lab.

**Task Completion:** Verify that the API is enabled and the API key is configured before proceeding.

---

## 🇺🇸 Task 2: Transcribe Speech to English Text

**Objective:** Create a JSON request for English audio transcription, call the Speech-to-Text API, save the response, and upload both files to Cloud Storage.

### Step 2A: Configure the Project and Create a Storage Bucket

Run the following commands in Cloud Shell:

```bash
# Get the active Google Cloud project ID
export GOOGLE_CLOUD_PROJECT="$(gcloud config get-value project)"

# Define the Cloud Storage bucket name
export BUCKET_NAME="${GOOGLE_CLOUD_PROJECT}-speech"

# Create a Cloud Storage bucket in the US
gcloud storage buckets create "gs://${BUCKET_NAME}" \
    --location=US
```

Verify that the bucket exists:

```bash
gcloud storage ls "gs://${BUCKET_NAME}"
```

> **Note:** Bucket names must be globally unique. If the bucket already exists or the name is unavailable, follow the exact resource naming requirements of your lab.

### Step 2B: Create the English Transcription Request

Create the JSON request file:

```bash
cat <<'EOF' > speech_request_en.json
{
  "config": {
    "languageCode": "en-US",
    "audioChannelCount": 2
  },
  "audio": {
    "uri": "gs://spls/arc131/question_en.wav"
  }
}
EOF
```

The request specifies English (United States) as the transcription language and uses the provided WAV audio file.

### Step 2C: Review the Request

```bash
cat speech_request_en.json
```

Confirm that the audio URI and language settings are correct.

### Step 2D: Call the Speech-to-Text API

Run the following command to submit the request and save the response:

```bash
curl -fSs -X POST \
    -H "Content-Type: application/json" \
    --data-binary @speech_request_en.json \
    "https://speech.googleapis.com/v1/speech:recognize?key=${API_KEY}" \
    -o speech_response_en.json
```

The API response will be saved in `speech_response_en.json`.

### Step 2E: Review the English Transcription Response

```bash
cat speech_response_en.json
```

For formatted JSON output, run:

```bash
python3 -m json.tool speech_response_en.json
```

A successful response may contain a `results` array with recognized text and confidence information.

### Step 2F: Upload the Request and Response Files

Upload the request file:

```bash
gcloud storage cp speech_request_en.json \
    "gs://${BUCKET_NAME}/speech_request_en.json"
```

Upload the response file:

```bash
gcloud storage cp speech_response_en.json \
    "gs://${BUCKET_NAME}/speech_response_en.json"
```

Verify both files:

```bash
gcloud storage ls "gs://${BUCKET_NAME}"
```

Check that the following objects exist:

- `speech_request_en.json`
- `speech_response_en.json`

### Task Completion

Return to the lab page and click **Check my progress** for the English transcription task.

---

## 🇪🇸 Task 3: Transcribe Speech to Spanish Text

**Objective:** Create a JSON request for Spanish FLAC audio transcription, call the Speech-to-Text API, save the response, and upload both files to Cloud Storage.

### Step 3A: Create the Spanish Transcription Request

Run the following command in Cloud Shell:

```bash
cat <<'EOF' > speech_request_sp.json
{
  "config": {
    "encoding": "FLAC",
    "languageCode": "es-ES",
    "audioChannelCount": 2
  },
  "audio": {
    "uri": "gs://spls/arc131/multi_es.flac"
  }
}
EOF
```

This request specifies Spanish (Spain) as the transcription language and uses the provided FLAC audio file.

### Step 3B: Review the Request

```bash
cat speech_request_sp.json
```

Confirm that the audio URI, encoding, and language code match the lab instructions.

### Step 3C: Call the Speech-to-Text API

Run the following command:

```bash
curl -fSs -X POST \
    -H "Content-Type: application/json" \
    --data-binary @speech_request_sp.json \
    "https://speech.googleapis.com/v1/speech:recognize?key=${API_KEY}" \
    -o response_speech_sp.json
```

The API response will be saved in `response_speech_sp.json`.

### Step 3D: Review the Spanish Transcription Response

```bash
cat response_speech_sp.json
```

For formatted JSON output, run:

```bash
python3 -m json.tool response_speech_sp.json
```

A successful response may contain the recognized Spanish speech in the `transcript` field.

### Step 3E: Upload the Request and Response Files

Upload the request file:

```bash
gcloud storage cp speech_request_sp.json \
    "gs://${BUCKET_NAME}/speech_request_sp.json"
```

Upload the response file:

```bash
gcloud storage cp response_speech_sp.json \
    "gs://${BUCKET_NAME}/response_speech_sp.json"
```

Verify that the files were uploaded:

```bash
gcloud storage ls "gs://${BUCKET_NAME}"
```

Check that the following objects exist:

- `speech_request_sp.json`
- `response_speech_sp.json`

### Task Completion

Return to the lab page and click **Check my progress** for the Spanish transcription task.

---

## 🔍 Verification and Troubleshooting

### Verify the Speech API Is Enabled

```bash
gcloud services list --enabled \
    --filter="name:speech.googleapis.com"
```

### Verify the Cloud Storage Bucket

```bash
gcloud storage ls "gs://${BUCKET_NAME}"
```

### Verify the English Response

```bash
python3 -m json.tool speech_response_en.json
```

### Verify the Spanish Response

```bash
python3 -m json.tool response_speech_sp.json
```

### Common Problems

**1. API key error**

Confirm that the API key is correct and that the Speech-to-Text API is enabled for the relevant project. Check any API key restrictions configured in your project.

**2. Permission denied**

Check that the calling identity has the necessary permissions to access the API and upload files to Cloud Storage.

**3. Invalid audio configuration**

Verify that the language code, audio encoding, and channel count match the provided audio files. The WAV file may contain encoding details in its header; follow the lab's required configuration if an audio configuration error occurs.

**4. API response contains an error**

Inspect the response file:

```bash
cat speech_response_en.json
```

For the Spanish request:

```bash
cat response_speech_sp.json
```

The response may contain an error message explaining an invalid request, inaccessible audio file, or authorization problem.

**5. Missing request or response files**

Check your current directory:

```bash
pwd
ls -l
```

Make sure you are running the upload commands in the directory containing the JSON files.

**6. Bucket already exists**

Check whether the bucket exists:

```bash
gcloud storage ls "gs://${BUCKET_NAME}"
```

If it already exists, confirm that it belongs to your lab project and use the bucket required by the lab rather than creating a duplicate.

---

## ✅ Final Checklist

- [ ] Enabled the Speech-to-Text API.
- [ ] Created an API key.
- [ ] Configured the project ID and bucket name.
- [ ] Created the Cloud Storage bucket.
- [ ] Created `speech_request_en.json`.
- [ ] Called the Speech-to-Text API for English transcription.
- [ ] Saved the response as `speech_response_en.json`.
- [ ] Uploaded both English request and response files.
- [ ] Created `speech_request_sp.json`.
- [ ] Called the Speech-to-Text API for Spanish transcription.
- [ ] Saved the response as `response_speech_sp.json`.
- [ ] Uploaded both Spanish request and response files.
- [ ] Verified the uploaded files in Cloud Storage.
- [ ] Clicked **Check my progress** for all required tasks.

---

## 📚 Key Concepts Learned

| Concept | Description |
|---|---|
| Speech-to-Text API | A Google Cloud service that converts audio speech into written text. |
| API Key | A credential used to identify API requests and apply configured restrictions. |
| Cloud Storage | Object storage for audio files, JSON requests, and API responses. |
| WAV | An audio container format often used for uncompressed audio. |
| FLAC | A lossless audio compression format. |
| JSON | A structured data format used to send API requests and receive responses. |
| `curl` | A command-line tool for sending HTTP requests to APIs. |
| Transcription | The process of converting spoken words into written text. |
| Language Code | A code such as `en-US` or `es-ES` that identifies the language and locale for recognition. |
| Audio Channel Count | The number of audio channels in an audio recording. |

---

## 🧹 Optional Cleanup

If you are using your own Google Cloud project, remove resources that are no longer required in accordance with your project's retention and security policies.

Do not delete the bucket or response files before completing the assessment in a managed training lab. Follow the lab's cleanup instructions.

---

## 🎯 Result

Successfully enabled the Google Cloud Speech-to-Text API, configured an API key and Cloud Storage bucket, transcribed English WAV and Spanish FLAC audio using JSON requests and `curl`, and uploaded the corresponding request and response files to Cloud Storage.
