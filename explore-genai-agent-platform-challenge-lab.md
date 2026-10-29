# 🤖 Explore Generative AI with the Gemini API in Agent Platform: Challenge Lab

A step-by-step guide to completing the **Explore Generative AI with the Gemini API in Agent Platform: Challenge Lab (GSP515)** using Google Cloud, Gemini, Cloud Shell, Vertex AI, Agent Platform Workbench, JupyterLab, and Python.

In this lab, you will generate text using the Gemini API, explore function calling, and analyse video content using Gemini's multimodal capabilities.

---

## 🎯 Lab Objectives

By completing this lab, you will learn how to:

- Enable the required Google Cloud APIs.
- Generate an access token using Cloud Shell.
- Send requests to the Gemini API using `curl`.
- Generate text using a Gemini model.
- Work with Jupyter notebooks in Agent Platform Workbench.
- Define functions and tools for Gemini function calling.
- Generate content using function declarations.
- Analyse video content using a multimodal Gemini model.
- Verify task completion using the lab progress checker.

---

## ⚙️ Task 1: Generate Text Using Gemini via `curl`

**Objective:** Enable the required API, configure environment variables, and send a text-generation request to Gemini.

### Step 1A: Open Cloud Shell

1. Open the Google Cloud Console.
2. Click the **Activate Cloud Shell (`>_`)** icon in the top-right corner.
3. Wait for Cloud Shell to initialize.

### Step 1B: Enable the Required API

Run the following command:

```bash
gcloud services enable aiplatform.googleapis.com
```

Wait for the command to finish successfully.

### Step 1C: Configure Environment Variables

Run the following commands in Cloud Shell:

```bash
# Get the active Google Cloud project ID
export PROJECT_ID="$(gcloud config get-value project)"

# Set the API location
export LOCATION="global"

# Replace this with the model ID specified by your lab
export MODEL_ID="YOUR_MODEL_ID"

# Generate an access token
export TOKEN="$(gcloud auth print-access-token)"

# Display the configured project and model
echo "Project ID: $PROJECT_ID"
echo "Location: $LOCATION"
echo "Model ID: $MODEL_ID"
```

Replace `YOUR_MODEL_ID` with the exact model ID specified by your lab instructions.

### Step 1D: Send a Request to Gemini

Run the following command to ask Gemini, "Why is the sky blue?":

```bash
curl -X POST \
    -H "Authorization: Bearer ${TOKEN}" \
    -H "Content-Type: application/json" \
    "https://aiplatform.googleapis.com/v1/projects/${PROJECT_ID}/locations/${LOCATION}/publishers/google/models/${MODEL_ID}:generateContent" \
    -d '{
      "contents": [
        {
          "role": "user",
          "parts": [
            {
              "text": "Why is the sky blue?"
            }
          ]
        }
      ]
    }'
```

The response should contain Gemini's generated text.

> **Note:** Use the endpoint and model ID specified by your lab if they differ from this example. Some model IDs used in older training materials may no longer be available.

### Task Completion

Return to the challenge lab page and click **Check my progress** for Task 1.

---

## 📓 Task 2: Open Agent Platform Workbench

**Objective:** Open the provided Jupyter notebook and prepare the environment for function calling and multimodal video analysis.

### Step 2A: Open Workbench

1. Open the Google Cloud Console.
2. Navigate to **Vertex AI / Agent Platform → Workbench → Instances**, following the menu structure in your lab.
3. Locate the Workbench instance provided by the lab.
4. Click **Open JupyterLab**.
5. Wait for JupyterLab to load.

### Step 2B: Open the Challenge Notebook

1. Locate the notebook provided by your lab, such as `gemini-explorer-challenge.ipynb`.
2. Open the notebook.
3. Select the **Python 3 (Local)** kernel if required.
4. Run the introductory cells in order.
5. Ensure that the required Python libraries are imported and the Gemini client is configured.

> **Important:** Use the notebook supplied by the challenge lab. Its setup cells may already define the `client`, helper functions, and required imports.

---

## 🛠️ Task 3: Create a Function Call Using Gemini

**Objective:** Define a weather function, register it as a Gemini tool, and generate content using function calling.

### Step 3A: Set the Model Name

Locate the Task 3 section in the notebook and replace the relevant placeholder with:

```python
model_id = "gemini-1.5-flash"
```

Use the model specified by your lab if a different model ID is required.

### Step 3B: Declare the Weather Function

Replace the function declaration placeholder with:

```python
get_current_weather_func = FunctionDeclaration(
    name="get_current_weather",
    description="Get the current weather in a given location",
    parameters={
        "type": "object",
        "properties": {
            "location": {
                "type": "string",
                "description": "Location"
            }
        },
        "required": ["location"]
    },
)
```

This declaration describes a function called `get_current_weather` that accepts a location as input.

### Step 3C: Create the Tool

Register the function declaration as a tool:

```python
weather_tool = Tool(
    function_declarations=[get_current_weather_func],
)
```

A tool allows the model to request that an application call a specified function.

### Step 3D: Generate Content Using the Tool

Replace the corresponding placeholder with:

```python
prompt = "What is the weather like in Boston?"

response = client.models.generate_content(
    model=model_id,
    contents=prompt,
    config=GenerateContentConfig(
        tools=[weather_tool],
        temperature=0,
    ),
)

response
```

Run the cell to inspect Gemini's response.

### Step 3E: Verify Function Calling

Check the response for a function call to `get_current_weather` and its arguments, such as the requested location.

**Important:** Declaring a function does not automatically retrieve live weather data. The application must execute the requested function and return the result to the model if the notebook requires a complete function-calling cycle.

Run the remaining Task 3 notebook cells in order, following the lab's instructions.

### Task Completion

Return to the challenge lab page and click **Check my progress** for Task 3.

---

## 🎬 Task 4: Describe Video Contents Using Gemini

**Objective:** Use a multimodal Gemini model to analyse a video, describe its content, and generate information based on the video.

### Step 4A: Set the Multimodal Model

Locate the Task 4 section in the notebook and set the model name as required by your lab:

```python
multimodal_model = "gemini-1.5-pro"
```

Use the exact model ID specified in your lab if it differs from this example.

### Step 4B: Create the Video Analysis Prompt

Replace the relevant placeholder with:

```python
prompt = """
What is shown in this video?
Where should I go to see it?
What are the top 5 places in the world that look like this?
"""
```

The prompt asks Gemini to describe the video and provide related location suggestions.

### Step 4C: Load the Video

Use the following code to reference the video stored in Cloud Storage:

```python
video = Part.from_uri(
    file_uri="gs://github-repo/img/gemini/multimodality_usecases_overview/mediterraneansea.mp4",
    mime_type="video/mp4",
)

contents = [prompt, video]
```

The video is supplied to Gemini as a multimodal input alongside the text prompt.

### Step 4D: Generate the Video Description

Run the following code:

```python
responses = client.models.generate_content(
    model=multimodal_model,
    contents=contents
)

print("-------Prompt--------")
print_multimodal_prompt(contents)

print("\n-------Response--------")
print(responses.text)
```

The model should return a text response describing the video and answering the questions in the prompt.

### Step 4E: Verify the Results

1. Run the Task 4 notebook cells in the correct order.
2. Confirm that the video input is loaded successfully.
3. Check that the prompt and model response are displayed.
4. Review Gemini's description of the video.

> **Note:** The video must be accessible to the notebook and the selected model must support the requested video input. Follow the lab instructions if the provided model or video URI differs.

### Task Completion

Return to the challenge lab page and click **Check my progress** for Task 4.

---

## 🔍 Verification and Troubleshooting

### Verify the Active Google Cloud Project

In Cloud Shell, run:

```bash
gcloud config get-value project
```

### Verify the Required API Is Enabled

```bash
gcloud services list --enabled \
    --filter="name:aiplatform.googleapis.com"
```

### Refresh the Access Token

Access tokens expire. If an API request fails due to authentication, generate a fresh token:

```bash
export TOKEN="$(gcloud auth print-access-token)"
```

### Common Problems

**1. Authentication error**

Verify that the correct Google Cloud account is active and that the access token is valid.

**2. Permission denied**

Check that your lab identity has the necessary permissions to use the required API and Workbench resources.

**3. Model not found**

Verify that `MODEL_ID` matches the model required by the lab and that the model is available in the selected location.

**4. Invalid API request**

Confirm that the project ID, location, endpoint, request JSON, and model ID are correct.

**5. Notebook import error**

Run the notebook's setup cells first and ensure that the required Python packages are installed in the selected kernel.

**6. Function calling does not return weather information**

Gemini may return a function call request rather than actual weather data. The application must implement the function, execute it, and provide the result to the model when required.

**7. Video analysis fails**

Confirm that the video URI is correct, the file is accessible, and the selected Gemini model supports video input.

---

## ✅ Final Checklist

- [ ] Enabled the Vertex AI API.
- [ ] Configured the Google Cloud project and location.
- [ ] Generated an access token.
- [ ] Sent a text-generation request to Gemini using `curl`.
- [ ] Verified the response to the text-generation prompt.
- [ ] Opened Agent Platform Workbench.
- [ ] Opened the provided Jupyter notebook.
- [ ] Completed the function-calling setup.
- [ ] Declared the `get_current_weather` function.
- [ ] Registered the weather function as a tool.
- [ ] Generated content using Gemini function calling.
- [ ] Set the multimodal model.
- [ ] Loaded the provided video.
- [ ] Generated and reviewed the video description.
- [ ] Clicked **Check my progress** for every required task.

---

## 📚 Key Concepts Learned

| Concept | Description |
|---|---|
| Gemini API | Provides access to Gemini models for text and multimodal generation. |
| Vertex AI | Google Cloud's platform for accessing and building applications with AI models. |
| Cloud Shell | A browser-accessible command-line environment for managing Google Cloud resources. |
| Access Token | A credential used to authenticate requests to Google Cloud APIs. |
| `curl` | A command-line tool for making HTTP requests. |
| Jupyter Notebook | An interactive environment for running Python code and documenting results. |
| Agent Platform Workbench | A managed environment for developing AI and machine learning workflows. |
| Function Calling | Enables a model to request application functions using structured arguments. |
| Tools | Function declarations and other capabilities that a model can use through an application. |
| Multimodal AI | AI that can process multiple data types, including text, images, and video. |
| Video Analysis | Uses video and text prompts to generate descriptions and answer questions about visual content. |

---

## 🧹 Optional Cleanup

If you are using your own Google Cloud project, clean up unused resources when you no longer need them to avoid unnecessary charges.

For a managed training lab, follow its cleanup instructions and do not delete resources before completing the assessment.

---

## 🎯 Result

Successfully queried Gemini through the API, explored function calling in a Jupyter notebook, and used a multimodal Gemini model to analyse video content and generate a natural-language description.
