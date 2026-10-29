# gcp-enterprise-ai-skills
Architectural breakdowns and code solutions for Google Cloud compute, IAM security, and multimodal GenAI applications.
# ☁️ Google Cloud & Generative AI: Enterprise Learning Portfolio

Welcome to my Google Cloud and AI portfolio! 🚀 

This repository documents my hands-on journey mastering Google Cloud infrastructure, enterprise security, Applied Machine Learning, and cutting-edge Generative AI. Below is a detailed breakdown of the architectural concepts, cloud services, and AI integrations I have implemented. 

For fellow learners, I have included links to the official Google Cloud Skills Boost labs, alongside my detailed insights on how these technologies apply to real-world enterprise environments.

*(Note: If you are looking for my code solutions and bug fixes for these labs, please check the `solutions/` folder in this repository!)*

---

## 🏗️ Core Cloud Infrastructure & Security

### 1. [The Basics of Google Cloud Compute](https://www.skills.google/course_templates/754)
**Focus:** Virtual Machines (VMs), Cloud Architecture, and Infrastructure as a Service (IaaS).
* **Technical Overview:** Explored the foundational building blocks of Google Cloud by provisioning Compute Engine virtual machines. This included configuring regional and zonal availability, attaching persistent disks, and managing SSH access.
* **Enterprise Value:** Understanding compute basics is essential for migrating legacy on-premise workloads to the cloud and ensuring high availability and fault tolerance across different geographic zones.

### 2. [Configure Service Accounts and IAM Roles](https://www.skills.google/course_templates/702)
**Focus:** Identity and Access Management (IAM), Security, and The Principle of Least Privilege.
* **Technical Overview:** Deep dive into cloud security by configuring custom IAM roles and Service Accounts. Instead of relying on user credentials, I learned how to assign precise, machine-to-machine permissions so that a specific compute instance can securely access a storage bucket without exposing the rest of the project.
* **Enterprise Value:** In a production environment, misconfigured permissions are the leading cause of security breaches. Mastering IAM ensures secure, compliant, and auditable cloud operations.

### 3. [Build a Website on Google Cloud](https://www.skills.google/course_templates/638)
**Focus:** Web Hosting, Networking, and Scalable Deployments.
* **Technical Overview:** Transitioned from raw compute infrastructure to application deployment. This lab covered the end-to-end process of hosting a web application, configuring HTTP firewalls, mapping ports, and serving live internet traffic.
* **Enterprise Value:** Demonstrates the ability to take raw code and deploy it to a globally accessible, scalable cloud environment, forming the backbone of modern DevOps and site reliability engineering (SRE).

---

## 🧠 Applied Machine Learning (Pre-Trained APIs)

### 4. [Analyze Images on Google Cloud](https://www.skills.google/course_templates/633)
**Focus:** Computer Vision, Optical Character Recognition (OCR), and JSON API integration.
* **Technical Overview:** Leveraged the Google Cloud Vision API to perform complex image analysis without training custom models. I utilized bash scripting and `curl` to send JSON payloads to the API, successfully extracting text (OCR) and identifying geographic landmarks directly from Cloud Storage buckets.
* **Enterprise Value:** Computer vision enables automated content moderation, digital asset management, and data extraction from scanned documents, saving thousands of hours of manual data entry.

### 5. [Using the Google Cloud Speech API](https://www.skills.google/course_templates/756)
**Focus:** Automatic Speech Recognition (ASR) and Audio Data Processing.
* **Technical Overview:** Handled raw audio data processing by passing FLAC and WAV files into the Cloud Speech-to-Text API. I learned how to configure API requests to handle different channel counts (stereo vs. mono) and sample rates to ensure accurate transcriptions.
* **Enterprise Value:** Speech-to-text is the foundation of accessible media, automated customer service logging, and voice-activated enterprise tools. 

### 6. [Implement Speech and Language Solutions](https://www.skills.google/course_templates/700)
**Focus:** Natural Language Processing (NLP) and Multi-Language Translation.
* **Technical Overview:** Expanded on the Speech API by building a broader language processing pipeline. This involved processing non-English audio (like Spanish), transcribing it accurately, and interacting with Google's broader language models to understand and manipulate human speech.
* **Enterprise Value:** Global companies require localized, multi-lingual pipelines to analyze international customer feedback, translate documents on the fly, and build global support systems.

---

## 🤖 Generative AI & Autonomous Agents

### 7. [Explore Generative AI in Agent Platform](https://www.skills.google/course_templates/959)
**Focus:** AI Agents, Reasoning Engines, and Tool Integration.
* **Technical Overview:** Transitioned from standard LLM generation to autonomous AI Agents. Explored how to provide an AI with a set of tools (like APIs or databases) and allow it to independently reason, plan steps, and execute actions to solve complex, multi-step user queries.
* **Enterprise Value:** AI Agents represent the future of enterprise automation. Building agents allows businesses to automate complex workflows like dynamic research, automated ticket resolution, and intelligent data retrieval without hardcoding every single step.

---
*If you are exploring Google Cloud or Vertex AI, feel free to connect with me on LinkedIn or fork this repository!*
