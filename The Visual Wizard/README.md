# 🧙‍♂️ The Visual Wizard

An AWS-powered, intelligent image tagging and search application using Lambda, S3, Rekognition, DynamoDB, API Gateway, and a web frontend.

---

## 📌 Table of Contents

- [📖 Project Overview](#-project-overview)
- [📐 Architecture Diagram](#-architecture-diagram)
- [💡 Real-World Use Cases](#-real-world-use-cases)
- [🧱 Phase-wise Implementation Plan](#-phase-wise-implementation-plan)
- [🔧 Step-by-Step Setup](#-step-by-step-setup)
- [🧑‍💻 Frontend Code](#-frontend-code)
- [🔐 IAM Policies](#-iam-policies)
- [🖼️ Screenshots](#-screenshots)
- [✅ Final Thoughts](#-final-thoughts)

---

## 📖 Project Overview

**The Visual Wizard** is a full-stack cloud project that:

- Lets users upload images via a web page
- Uses AWS Rekognition to automatically tag them
- Stores image metadata + tags in DynamoDB
- Lets users search for images by tag
- Displays the results in a gallery-style frontend

### ✅ Tech Stack

- **Amazon S3** — store and host images + frontend
- **AWS Lambda** — process uploaded images, search by tag
- **Amazon Rekognition** — detect labels from images
- **DynamoDB** — store image metadata and labels
- **API Gateway** — enable tag-based image search
- **CloudWatch** — debug logs
- **HTML + JS** — simple frontend

---

## 📐 Architecture Diagram

> 📸 `architecture.png` (upload this directly in project root)

---

## 💡 Real-World Use Cases

- 🛍 E-commerce: Auto-tag product photos  
- 🧠 AI Training: Organize datasets by content  
- 🧪 Medical Imaging: Auto-label X-rays or scans  
- 🔒 Surveillance: Tag frames from security footage  
- 📸 Photo Gallery: Smart search by image content

---

## 🧱 Phase-wise Implementation Plan

| Phase | Description                               | AWS Services Used         |
|-------|-------------------------------------------|---------------------------|
| 1️⃣    | Upload images to S3                       | S3                        |
| 2️⃣    | Trigger Lambda on upload                  | Lambda, S3 Event          |
| 3️⃣    | Use Rekognition to detect image labels    | Rekognition               |
| 4️⃣    | Store image info and tags in DynamoDB     | DynamoDB                  |
| 5️⃣    | Create search API with API Gateway        | API Gateway + Lambda      |
| 6️⃣    | Build frontend to upload + search images  | HTML, JavaScript          |
| 7️⃣    | Host frontend on static S3 website        | S3 Static Hosting         |

---

## 🔧 Step-by-Step Setup

### 📁 Phase 1: Upload Images to S3

1. Create a bucket named: `visual-wizard-photos`
2. Add a folder named `images/`
3. Set bucket policy for **upload and public read**

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicUpload",
      "Effect": "Allow",
      "Principal": "*",
      "Action": ["s3:PutObject"],
      "Resource": "arn:aws:s3:::visual-wizard-photos/images/*"
    },
    {
      "Sid": "PublicRead",
      "Effect": "Allow",
      "Principal": "*",
      "Action": ["s3:GetObject"],
      "Resource": "arn:aws:s3:::visual-wizard-photos/images/*"
    }
  ]
}
