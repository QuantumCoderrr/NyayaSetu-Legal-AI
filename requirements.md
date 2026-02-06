# Requirements: NyayaSahayak (Justice Assistant)

## 1. Introduction
**NyayaSahayak** is an AI-powered WhatsApp chatbot designed to bridge the legal and informational gap for the rural population of India ("Bharat"). It allows users to interact with complex legal documents, government notices, and schemes in their native language through a simple, voice-first interface.

### 1.1 Purpose
The purpose of this document is to define the functional and non-functional requirements for the NyayaSahayak system, ensuring it meets the needs of low-literacy users while leveraging high-performance AWS AI services.

### 1.2 Scope
The system encompasses a WhatsApp interface for users, a serverless backend for processing, and an AI pipeline for OCR, summarization, translation, and text-to-speech generation.

---

## 2. User Personas
* **Ramesh (The Farmer):** Low literacy in English/Hindi. Receive land notices he cannot read. Needs audio explanations in Bhojpuri.
* **Sita (The Daily Wage Worker):** Needs to understand government welfare schemes. Prefers voice interaction over typing.
* **Local NGO Volunteer:** Helps multiple villagers understand documents quickly using the tool.

---

## 3. Functional Requirements

### 3.1 Input Handling
* **REQ-1:** The system must accept image uploads (JPEG/PNG) containing documents (handwritten or printed) via WhatsApp.
* **REQ-2:** The system must accept voice notes in Indian languages for Q&A.
* **REQ-3:** The system must identify the user's preferred language automatically or via initial selection.

### 3.2 Core Processing (The "Brain")
* **REQ-4:** The system must extract text from images with high accuracy using Optical Character Recognition (OCR), supporting both English and Devanagari scripts.
* **REQ-5:** The system must summarize complex legal text into simple, 5th-grade level language (Simplification).
* **REQ-6:** The system must translate the simplified text into the user's target regional language (e.g., Hindi, Tamil, Marathi, Bengali).

### 3.3 Output Generation
* **REQ-7:** The system must generate a text summary of the document.
* **REQ-8:** The system must generate an **audio file (Voice Note)** of the summary to ensure accessibility for non-readers.
* **REQ-9:** The response time from image upload to audio receipt should be under 15 seconds.

---

## 4. Non-Functional Requirements

### 4.1 Scalability & Performance
* **NFR-1:** The backend must be serverless (AWS Lambda) to handle sudden spikes in traffic without manual scaling.
* **NFR-2:** The system must support concurrent processing of up to 100 requests per minute in the prototype phase.

### 4.2 Privacy & Security
* **NFR-3:** User uploaded documents must be stored temporarily for processing and deleted/anonymized after 24 hours to ensure privacy.
* **NFR-4:** All data in transit must be encrypted (HTTPS/TLS).

### 4.3 Reliability
* **NFR-5:** The system should gracefully handle OCR failures (e.g., blurry images) by asking the user to retake the photo.

---

## 5. Technical Constraints
* Must leverage **AWS Bedrock** for Generative AI capabilities.
* Must use **WhatsApp Business API** (via Twilio or Meta) for the frontend interface.
* Must support at least 3 major Indian languages for the MVP.
