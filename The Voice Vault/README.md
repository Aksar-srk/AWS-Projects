# The Voice Vault 🎙️

> A serverless AWS project that converts your text notes into podcast-style MP3 audio using Amazon Polly.

---

## 🧠 Project Overview

**The Voice Vault** is a simple and efficient serverless application that converts plain text files into spoken audio using Amazon Polly. It’s ideal for listening to your notes like podcasts — anytime, anywhere.

---

## 🛠️ Technologies Used

- **Amazon S3** – For storing `.txt` and `.mp3` files
- **AWS Lambda** – Automatically triggered to process uploads
- **Amazon Polly** – Converts uploaded text to speech
- **Amazon CloudWatch** – Logs Lambda executions

---

## 🔁 Workflow

1. User uploads `.txt` file to `/notes/` folder in S3
2. Lambda gets triggered
3. Text is converted to speech by Polly
4. Output MP3 is stored in `/audio/` folder in S3
5. Logs of the process are stored in CloudWatch

---

## 🏗️ Architecture Diagram

Here’s a visual representation of the project:

![Architecture Diagram](./images/architecture.png)

---

## 📸 Screenshots

### 🟢 1. Uploading a Text File to S3
![S3 Upload](./images/s3-upload.png)

---

### 🟢 2. MP3 File Generated in Audio Folder
![MP3 Output](./images/mp3-output.png)

---

### 🟢 3. Lambda Execution Logs in CloudWatch
![Lambda Logs](./images/lambda-logs.png)

---

### 🟢 4. Listening to Audio File from S3
![Listen in S3](./images/s3-listen.png)

---

##  How to Use

1. Upload any `.txt` note to your S3 bucket under `/notes/`
2. Wait a few seconds — Lambda will process it
3. Check the `/audio/` folder for your MP3 file
4. View logs in CloudWatch if needed

---




