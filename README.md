# 🚑 **n8n AI Automation System**  
### **Comprehensive SOS Detection, Medical Diagnosis, and Speech-to-Text Workflow**  

This repository provides an advanced **AI-powered automation system** built with **n8n** for real-time emergency monitoring and medical diagnosis. The system leverages **Google Gemini AI** for analysis and integrates seamlessly with **Telegram** to provide immediate, actionable alerts. Deployed within a **Docker** environment, the system is designed for reliability and scalability, with persistent storage for logs, workflows, and databases.

---

## 📌 **Key Features**

### 1. **SOS – Emergency Heart Rate Monitoring**
- **Objective**: Monitor heart rate in real-time from mobile apps or wearable devices.
- **AI-Powered Analysis**: Utilizes **Google Gemini AI** to detect abnormalities such as:
  - **Tachycardia** (elevated heart rate)
  - **Sudden Spikes/Drops** in heart rate
  - **Dangerous Abnormal Trends** indicating potential medical emergencies
- **Key Metrics**:  
  - `lastBpm`, `maxBpm`, `minBpm`, `averageBpm`, `delta`, `trend`, `status`
- **Real-time Alerts**: Sends emergency notifications via **Telegram** if dangerous patterns are detected.
- **Automatic Workflow Trigger**: If a risk is identified, the **Diagnostic Workflow** is automatically initiated.

#### User Flow:
- When the user triggers the **SOS** function (via a smartwatch or mobile device), the system evaluates the heart rate data.
- If the heart rate indicates a critical condition, the system sends an immediate alert and triggers the diagnostic workflow for further evaluation.

#### Workflow:
1. The **smartwatch** (integrated with Google Fit) collects heart-rate data.
2. The **Tasker app** on the phone sends an HTTP request to the **n8n** webhook.
3. **n8n** processes the heart rate data and sends it to **Google Gemini AI** for analysis.
4. **Telegram** notifies the user with the result and assessment.

---

### 2. **AI-Powered Diagnostic System**
- **Objective**: Interpret user symptoms and provide AI-driven medical assessments.
- **Capabilities**:
  - Understands user-provided symptoms (text and voice-to-text)
  - Categorizes symptom severity
  - Suggests potential medical actions or treatments
  - Locates nearby doctors based on the user's location, sorted by proximity, specialty, and ratings
- **Output**: Detailed medical summaries, diagnostic suggestions, and nearby doctor recommendations delivered via **Telegram**.

---

### 3. **Speech-to-Text Conversion Pipeline**
- **Objective**: Convert user voice messages into text, triggering further processing within the diagnostic workflow if needed.
- **Workflow Steps**:
  1. Receive voice message from **Telegram**.
  2. Download the audio file and initialize the upload session.
  3. Upload the audio to a **Google Gemini** transcription endpoint.
  4. Perform **Speech-to-Text** conversion.
  5. Extract and clean transcribed text.
  6. Return the transcribed text or trigger the diagnostic workflow.

- **Automation**: Fully automated with clear logs for easy debugging.

---

## 📂 **Repository Structure**

```bash
.
├── docker-compose.yml          # Docker Compose configuration for n8n deployment
├── README.md                   # Documentation (this file)
├── n8n-data/                   # Persistent n8n data volume
│   ├── database.sqlite         # SQLite database for n8n data storage
│   ├── n8nEventLog.log         # Main event log for system operations
│   ├── n8nEventLog-1.log       # Historical event logs for debugging
│   ├── n8nEventLog-2.log       # Further historical logs
│   ├── n8nEventLog-3.log       # Additional historical logs
│   ├── config/                 # n8n configuration files
│   ├── nodes/                  # Custom nodes (if applicable)
│   ├── crash.journal           # Logs for system crashes
│   └── New Text Document.txt   # Miscellaneous text files
└── workflows/                  # n8n exported workflows (optional)
```

---

## 🐳 **Docker Deployment Instructions**

This project uses **Docker Compose** to deploy **n8n** within a containerized environment. The `docker-compose.yml` file configures necessary services, including the AI processing system.

### Example Configuration:

```yaml
services:
  n8n:
    image: n8nio/n8n:latest
    container_name: n8n
    restart: always
    ports:
      - "5680:5678"
    environment:
      - N8N_HOST=0.0.0.0
      - N8N_PORT=5678
      - WEBHOOK_URL=https://your-public-url/
      - N8N_EDITOR_BASE_URL=https://your-public-url/
      - GENERIC_TIMEZONE=Africa/Cairo
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_BASIC_AUTH_USER=admin
      - N8N_BASIC_AUTH_PASSWORD=admin123
      - DB_TYPE=sqlite
      - DB_SQLITE_VACUUM_ON_STARTUP=true
    volumes:
      - ./n8n-data:/home/node/.n8n
```

### Steps to Run the System:
1. **Start Docker**  
   Run the following command to deploy the system:
   ```bash
   docker-compose up -d
   ```

2. **Access the n8n Editor**  
   Open a web browser and navigate to:
   ```
   https://your-public-url/
   ```

3. **Import or Review Workflows**  
   Workflows are stored in `n8n-data/database.sqlite`. Import or review them to get started:
   - **SOS_AI_HeartMonitor**
   - **Diagnostic_Full_System**
   - **Speech_to_Text**

---

## 🧩 **Workflow Overview**

### 🚑 **1. SOS_AI_HeartMonitor**
- **Purpose**: Detect abnormal heart-rate patterns in real-time.
- **Inputs**: A table of heart rate values with corresponding timestamps.
- **AI Analysis**:  
    The system assesses heart-rate trends using **Google Gemini**:
    ```json
    {
      "status": "normal/danger",
      "lastBpm": "...",
      "averageBpm": "...",
      "trend": "...",
      "assessment": "..."
    }
    ```
- **Outputs**:
  - **Alert**: If danger is detected, an emergency alert is sent to the user via Telegram.
  - **Status Update**: If heart rate is normal, a status update is sent.
  - **Trigger Diagnostic Workflow**: If necessary, triggers the **Diagnostic Workflow**.

### 🧠 **2. Diagnostic_Full_System**
- **Purpose**: Provide an AI-based medical assessment based on user symptoms.
- **Features**:
  - Symptom interpretation
  - Severity categorization
  - Medical recommendations
  - Doctor search (sorted by proximity, specialty, and ratings)
- **Triggers**: 
  - SOS alerts
  - User messages
  - Speech-to-Text transcriptions

### 🎤 **3. Speech_to_Text**
- **Purpose**: Converts voice messages into text for further processing.
- **Steps**:
  - Download audio from Telegram
  - Upload audio for transcription via **Google Gemini**
  - Extract text from transcription
  - Trigger diagnostic workflow or return transcribed text

---

## 🔧 **Environment Variables**

| **Variable**                      | **Description**                              |
|------------------------------------|----------------------------------------------|
| `WEBHOOK_URL`                      | Public URL for webhook (e.g., ngrok/domain)  |
| `N8N_EDITOR_BASE_URL`              | URL for accessing n8n editor                 |
| `N8N_BASIC_AUTH_USER`              | Username for n8n basic authentication        |
| `N8N_BASIC_AUTH_PASSWORD`          | Password for n8n basic authentication        |
| `DB_TYPE`                          | Database type (`sqlite`)                     |
| `GENERIC_TIMEZONE`                 | System timezone (e.g., `Africa/Cairo`)       |

---

## 📈 **Logging & Debugging**

Logs are stored in the `n8n-data/` directory for easy troubleshooting.

- **Key Logs**:
  - `n8nEventLog.log`: Main event log for the system.
  - `n8nEventLog-1.log`, `n8nEventLog-2.log`, `n8nEventLog-3.log`: Historical event logs.
  - `crash.journal`: System crash logs.

Logs provide insights into:
- Workflow failures
- Telegram message errors
- AI processing issues
- Webhook triggers
- Successful execution logs

---

## 🔮 **Future Enhancements**

- **Caching layer**: Improve efficiency of doctor search results.
- **Improved diagnostics**: Enhance the medical diagnosis prompt for more accuracy.
- **Monitoring & error reporting**: Build a system for tracking the health of the system.
- **Unit testing**: Implement tests for custom nodes.
- **Analytics dashboards**: Visualize user data and system performance.

---

## 🏁 **Conclusion**

This repository offers a fully integrated **AI-driven automation system** for real-time **emergency monitoring**, **medical diagnosis**, and **speech-to-text conversion**. Leveraging **n8n**, **Docker**, and **Google Gemini**, the system provides seamless interaction through **

### 👤 **Authors**

- **MuhammadAshraf221B**
- **Mohamed Elsyed** 
---