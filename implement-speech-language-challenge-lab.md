# 🗣️ Implement Speech and Language Solutions with Pre-trained APIs: Challenge Lab

A step-by-step guide to completing the **Implement Speech and Language Solutions with Pre-trained APIs: Challenge Lab (ARC132)** using Google Cloud Text-to-Speech, Speech-to-Text, Cloud Translation API, Cloud Storage, API keys, JSON requests, and `curl`.

In this lab, you will generate synthetic speech, transcribe French audio, translate Japanese text into English, and detect the language of a given text string using Google's pre-trained machine learning APIs.

---

## 🎯 Lab Objectives

By completing this lab, you will learn how to:

- Enable the required Google Cloud APIs.
- Create and configure an API key.
- Create a Cloud Storage bucket.
- Convert text into synthetic MP3 audio using Text-to-Speech.
- Decode Base64 audio data using Python.
- Transcribe French audio using Speech-to-Text.
- Translate Japanese text into English.
- Detect the language of a text string.
- Upload API requests, responses, and audio files to Cloud Storage.

---

## ⚙️ Task 1: Create an API Key and Enable Services

**Objective:** Enable the required APIs and configure an API key for making API requests.

### Step 1A: Open Cloud Shell

1. Open the Google Cloud Console.
2. Click the **Activate Cloud Shell (`>_`)** icon in the top-right corner.
3. Wait for Cloud Shell to initialize.

### Step 1B: Enable the Required APIs

Run the following command:

```bash
gcloud services enable \
    speech.googleapis.com \
    texttospeech.googleapis.com \
    translation.googleapis.com
```

Wait for the command to complete successfully.

### Step 1C: Create an API Key

1. In the Google Cloud Console, navigate to **Navigation Menu → APIs & Services → Credentials**.
2. Click **Create credentials**.
3. Select **API key**.
4. Copy the generated API key.

### Step 1D: Export the API Key

Replace `YOUR_API_KEY` with your actual API key:

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

> **Security Note:** Keep your API key private. Do not commit it to a public GitHub repository.

---

## 🎧 Task 2: Create Synthetic Speech Using the Text-to-Speech API

**Objective:** Convert text into MP3 audio using the Text-to-Speech API, decode the Base64 response using Python, and upload the resulting files to Cloud Storage.

### Step 2A: Create the Cloud Storage Bucket

Run the following command:

```bash
gcloud storage buckets create gs://speech-bucket --location=US
```

Verify the bucket:

```bash
gcloud storage ls gs://speech-bucket
```

> **Note:** Bucket names must be globally unique. If your lab specifies a different bucket name, use the exact name provided by the lab.

### Step 2B: Create the Text-to-Speech Request

Create the JSON request file:

```bash
cat <<'EOF' > synthesize-text.json
{
  "input": {
    "text": "Cloud Text-to-Speech API allows developers to include natural-sounding, synthetic human speech as playable audio in their applications. The Text-to-Speech API converts text or Speech Synthesis Markup Language (SSML) input into audio data like MP3 or LINEAR16 (the encoding used in WAV files)."
  },
  "voice": {
    "languageCode": "en-GB",
    "name": "en-GB-Standard-A",
    "ssmlGender": "FEMALE"
  },
  "audioConfig": {
    "audioEncoding": "MP3"
  }
}
EOF
```

### Step 2C: Call the Text-to-Speech API

Run the following command to generate the API response:

```bash
curl -fSs -X POST \
    -H "X-Goog-Api-Key: ${API_KEY}" \
    -H "Content-Type: application/json; charset=utf-8" \
    --data-binary @synthesize-text.json \
    "https://texttospeech.googleapis.com/v1/text:synthesize" \
    -o synthesize-text.txt
```

The response will be saved in `synthesize-text.txt`. The generated audio is returned as Base64-encoded data in the `audioContent` field.

### Step 2D: Create the Python Decoding Script

Create a Python script to decode the returned audio data:

```bash
cat <<'EOF' > tts_decode.py
import argparse
import base64
import json


def decode_tts_output(input_file, output_file):
    with open(input_file, "r", encoding="utf-8") as source:
        response = json.load(source)

    audio_data = response["audioContent"]
    decoded_audio = base64.b64decode(audio_data)

    with open(output_file, "wb") as destination:
        destination.write(decoded_audio)

    print(f"Audio file created: {output_file}")


if __name__ == "__main__":
    parser = argparse.ArgumentParser(
        description="Decode output from Cloud Text-to-Speech"
    )
    parser.add_argument(
        "--input",
        required=True,
        help="JSON response from the Text-to-Speech API"
    )
    parser.add_argument(
        "--output",
        required=True,
        help="Output audio filename"
    )

    args = parser.parse_args()
    decode_tts_output(args.input, args.output)
EOF
```

### Step 2E: Decode the Response into an MP3 File

Run the Python script:

```bash
python3 tts_decode.py \
    --input synthesize-text.txt \
    --output synthesize-text-audio.mp3
```

The generated MP3 file will be saved as `synthesize-text-audio.mp3`.

### Step 2F: Upload the Files to Cloud Storage

Upload the Text-to-Speech API response:

```bash
gcloud storage cp synthesize-text.txt gs://speech-bucket/
```

Upload the generated audio:

```bash
gcloud storage cp synthesize-text-audio.mp3 gs://speech-bucket/
```

Verify the uploaded files:

```bash
gcloud storage ls gs://speech-bucket/
```

### Task Completion

Return to the lab page and click **Check my progress** for the Text-to-Speech task.

---

## 🇫🇷 Task 3: Perform Speech-to-Text Transcription in French

**Objective:** Transcribe a pre-recorded French FLAC audio file from Cloud Storage into text using the Speech-to-Text API.

### Step 3A: Create the French Transcription Request

Create the JSON request file:

```bash
cat <<'EOF' > request.json
{
  "config": {
    "encoding": "FLAC",
    "languageCode": "fr-FR"
  },
  "audio": {
    "uri": "gs://cloud-samples-data/speech/corbeau_renard.flac"
  }
}
EOF
```

The request specifies the FLAC encoding and French (France) as the recognition language.

### Step 3B: Call the Speech-to-Text API

Run the following command:

```bash
curl -fSs -X POST \
    -H "Content-Type: application/json" \
    "https://speech.googleapis.com/v1/speech:recognize?key=${API_KEY}" \
    --data-binary @request.json \
    -o response.json
```

The API response will be saved in `response.json`.

### Step 3C: Review the Transcription Response

Display the response:

```bash
cat response.json
```

For formatted JSON output:

```bash
python3 -m json.tool response.json
```

A successful response may contain the recognized French text in the `transcript` field.

### Step 3D: Upload the Request and Response Files

Upload the request:

```bash
gcloud storage cp request.json gs://speech-bucket/
```

Upload the response:

```bash
gcloud storage cp response.json gs://speech-bucket/
```

Verify the uploaded files:

```bash
gcloud storage ls gs://speech-bucket/
```

### Task Completion

Return to the lab page and click **Check my progress** for the French transcription task.

---

## 🌐 Task 4: Translate Text Using the Cloud Translation API

**Objective:** Translate a Japanese sentence into English using the Cloud Translation API.

### Step 4A: Create the Translation Request

Create the JSON request file:

```bash
cat <<'EOF' > translation_request.json
{
  "q": "これは日本語です。",
  "target": "en"
}
EOF
```

The request contains a Japanese sentence and specifies English as the target language.

### Step 4B: Call the Cloud Translation API

Run the following command:

```bash
curl -fSs -X POST \
    -H "Content-Type: application/json" \
    --data-binary @translation_request.json \
    "https://translation.googleapis.com/language/translate/v2?key=${API_KEY}" \
    -o translation_response.txt
```

The translation result will be saved in `translation_response.txt`.

### Step 4C: Review the Translation Response

Display the response:

```bash
cat translation_response.txt
```

For formatted JSON output:

```bash
python3 -m json.tool translation_response.txt
```

The response may contain the translated English sentence and the detected source language.

### Step 4D: Upload the Translation Files

Upload the request:

```bash
gcloud storage cp translation_request.json gs://speech-bucket/
```

Upload the response:

```bash
gcloud storage cp translation_response.txt gs://speech-bucket/
```

Verify the uploaded files:

```bash
gcloud storage ls gs://speech-bucket/
```

### Task Completion

Return to the lab page and click **Check my progress** for the Translation API task.

---

## 🔍 Task 5: Detect a Language Using the Cloud Translation API

**Objective:** Detect the language of a text string using the Cloud Translation API's language detection endpoint.

### Step 5A: Create the Language Detection Request

Create the JSON request file:

```bash
cat <<'EOF' > detect_request.json
{
  "q": "Este é japonês."
}
EOF
```

The example text is Portuguese. The API should identify the language based on the submitted text.

### Step 5B: Call the Language Detection API

Run the following command:

```bash
curl -fSs -X POST \
    -H "Content-Type: application/json" \
    --data-binary @detect_request.json \
    "https://translation.googleapis.com/language/translate/v2/detect?key=${API_KEY}" \
    -o detection_response.txt
```

The language detection result will be saved in `detection_response.txt`.

### Step 5C: Review the Detection Response

Display the response:

```bash
cat detection_response.txt
```

For formatted JSON output:

```bash
python3 -m json.tool detection_response.txt
```

The response may contain the detected language code, confidence score, and whether the language is considered reliable.

### Step 5D: Upload the Detection Files

Upload the request:

```bash
gcloud storage cp detect_request.json gs://speech-bucket/
```

Upload the response:

```bash
gcloud storage cp detection_response.txt gs://speech-bucket/
```

Verify the uploaded files:

```bash
gcloud storage ls gs://speech-bucket/
```

### Task Completion

Return to the lab page and click **Check my progress** for the Language Detection task.

---

## 🔍 Verification and Troubleshooting

### Verify the Required APIs Are Enabled

```bash
gcloud services list --enabled \
    --filter="name:(speech.googleapis.com OR texttospeech.googleapis.com OR translation.googleapis.com)"
```

### Verify the Cloud Storage Bucket

```bash
gcloud storage ls gs://speech-bucket/
```

### Verify the Text-to-Speech Response

```bash
python3 -m json.tool synthesize-text.txt
```

### Verify the French Speech-to-Text Response

```bash
python3 -m json.tool response.json
```

### Verify the Translation Response

```bash
python3 -m json.tool translation_response.txt
```

### Verify the Language Detection Response

```bash
python3 -m json.tool detection_response.txt
```

### Common Problems

**1. API key error**

Verify that the API key is correct, that the required API is enabled, and that the key's restrictions allow the intended API requests.

**2. Permission denied**

Ensure that your Google Cloud identity has permission to use the required services and upload files to the bucket.

**3. Text-to-Speech response does not contain audio**

Inspect `synthesize-text.txt` for an API error. Confirm that the request JSON and voice configuration are valid before running the decoding script.

**4. Python decoding error**

Verify that `synthesize-text.txt` contains a valid JSON response with an `audioContent` field rather than an error message.

**5. Speech transcription fails**

Check that the audio file exists and that the encoding and language code match the recording. Verify that the request was accepted by the API.

**6. Translation request fails**

Confirm that the request file contains valid JSON and that the Translation API is enabled.

**7. Language detection returns an unexpected result**

Use a clear sentence containing enough text for language identification. The detected language depends on the content submitted to the API.

**8. Bucket already exists**

Check the bucket:

```bash
gcloud storage ls gs://speech-bucket/
```

If it already exists, confirm that it belongs to your lab project and use the bucket specified by the lab instructions.

---

## ✅ Final Checklist

- [ ] Enabled the Speech-to-Text API.
- [ ] Enabled the Text-to-Speech API.
- [ ] Enabled the Cloud Translation API.
- [ ] Created and exported an API key.
- [ ] Created the Cloud Storage bucket.
- [ ] Created `synthesize-text.json`.
- [ ] Generated the Text-to-Speech API response.
- [ ] Created `tts_decode.py`.
- [ ] Decoded the response into `synthesize-text-audio.mp3`.
- [ ] Uploaded the Text-to-Speech response and audio file.
- [ ] Created `request.json` for French transcription.
- [ ] Transcribed the French FLAC audio.
- [ ] Uploaded the French transcription request and response.
- [ ] Created `translation_request.json`.
- [ ] Translated the Japanese sentence into English.
- [ ] Uploaded the translation request and response.
- [ ] Created `detect_request.json`.
- [ ] Detected the language of the submitted text.
- [ ] Uploaded the language detection request and response.
- [ ] Verified the uploaded files in Cloud Storage.
- [ ] Clicked **Check my progress** for all required tasks.

---

## 📚 Key Concepts Learned

| Concept | Description |
|---|---|
| Text-to-Speech API | Converts written text into synthetic audio. |
| Speech-to-Text API | Converts spoken audio into written text. |
| Cloud Translation API | Translates text between supported languages and identifies source languages. |
| API Key | A credential used to identify API requests and enforce configured restrictions. |
| Cloud Storage | Object storage for audio files, request payloads, and API responses. |
| Base64 Encoding | A method of representing binary data as text. |
| MP3 | A compressed digital audio format. |
| FLAC | A lossless audio compression format. |
| JSON | A structured format used for API requests and responses. |
| `curl` | A command-line tool for sending HTTP requests. |
| Language Detection | Identifies the likely language of a text sample. |

---

## 🧹 Optional Cleanup

If you are using your own Google Cloud project, remove resources that are no longer needed according to your project's retention and security policies.

Do not delete the bucket or response files before completing the assessment in a managed training lab. Follow the lab's cleanup instructions.

---

## 🎯 Result

Successfully used Google's pre-trained APIs to generate synthetic speech, transcribe French audio, translate Japanese text into English, and detect the language of a text sample. Saved the API outputs and uploaded the required files to Cloud Storage.
