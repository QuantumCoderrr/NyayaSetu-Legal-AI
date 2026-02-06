# System Design: NyayaSahayak

## 1. High-Level Architecture
The system follows a **Serverless Event-Driven Architecture**. This ensures low operational costs (pay-per-use) and high scalability. The core interaction happens asynchronously: User sends data -> Webhook triggers -> AI processes -> Response sent back.

---

## 2. Component Architecture

### 2.1 Frontend Layer (User Interface)
* **Platform:** WhatsApp (Mobile App).
* **Gateway:** Twilio API for WhatsApp / WhatsApp Business API.
* **Role:** Acts as the entry and exit point for all user interactions (Images, Audio, Text).

### 2.2 Orchestration Layer (Backend)
* **Service:** AWS Lambda (Python runtime).
* **Role:**
    * Receives webhooks from Twilio.
    * Validates user input.
    * Orchestrates calls to various AWS AI services.
    * Manages session state (optionally via DynamoDB).

### 2.3 AI & Intelligence Layer
This is the core differentiator of the platform, utilizing the AWS ecosystem.

| Feature | AWS Service | Purpose |
| :--- | :--- | :--- |
| **Vision** | **Amazon Textract** | Extracts raw text from uploaded document images (OCR). |
| **Reasoning** | **Amazon Bedrock** | Uses **Claude 3 Sonnet** to analyze the extracted text, simplify legal jargon, and summarize it. |
| **Translation** | **Amazon Translate** / **AI4Bharat IndicTrans2** | Translates the simplified English summary into the target regional language. |
| **Speech** | **Amazon Polly** | Converts the translated text into a human-like neural audio file (Text-to-Speech). |

### 2.4 Data & Storage Layer
* **Amazon S3:** Stores temporary images uploaded by users and generated audio files. Uses Lifecycle Policies to delete data after 24 hours (Privacy).
* **Amazon DynamoDB:** Stores user metadata (Phone Number, Language Preference) and conversation context for multi-turn chats.

---

## 3. Data Flow Diagram

1.  **Ingest:** User uploads image -> Twilio Webhook -> **AWS Lambda**.
2.  **Storage:** Lambda saves image to **S3 Bucket** (temp).
3.  **Vision:** Lambda triggers **Amazon Textract** to read S3 image -> Returns Text.
4.  **Logic:** Lambda sends Text to **Bedrock (Claude 3)** with prompt *"Simplify this legal text for a layman"*.
5.  **Localization:** Lambda sends Simplified Text to **IndicTrans2** for translation.
6.  **Voice Gen:** Lambda sends Translated Text to **Amazon Polly** -> Returns Audio Stream.
7.  **Response:** Lambda saves Audio to S3 -> Generates public link -> Sends link to User via **Twilio**.

---

## 4. API Contract (Internal)

### `POST /process-document`
* **Input:** `{"image_url": "s3://...", "user_language": "hi-IN"}`
* **Output:**
    ```json
    {
      "summary_text": "aapka zameen ka vivad...",
      "audio_url": "[https://s3.aws](https://s3.aws).../response.mp3",
      "status": "success"
    }
    ```

## 5. Technology Stack Summary
* **Languages:** Python 3.9+
* **Cloud Provider:** AWS
* **IaC (Infrastructure as Code):** AWS SAM or Serverless Framework.
* **Version Control:** GitHub.
