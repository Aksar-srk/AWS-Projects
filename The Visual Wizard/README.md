# 🧙‍♂️ The Visual Wizard

Automatically tag and search images using AWS Rekognition, Lambda, API Gateway, and DynamoDB — all accessible via a web frontend.

---

## 📌 Table of Contents

- [Project Overview](#project-overview)
- [Architecture Diagram](#architecture-diagram)
- [Real-World Use Cases](#real-world-use-cases)
- [Phase-wise Implementation Plan](#phase-wise-implementation-plan)
- [Complete Step-by-Step Setup Guide](#complete-step-by-step-setup-guide)
- [IAM Policies](#iam-policies)
- [Frontend Code](#frontend-code)
- [Screenshots](#screenshots)
- [Final Thoughts](#final-thoughts)

---

## 🚀 Project Overview

**The Visual Wizard** is an intelligent image processing pipeline that allows users to:

- Upload images to S3 via a simple web interface
- Automatically analyze images using AWS Rekognition
- Store identified labels and image metadata in DynamoDB
- Search images based on tags via API Gateway
- View the matched images directly on the same web interface

**AWS Services Used:**

- S3 (Image Storage + Web Hosting)
- Lambda (Image Processing + API Handling)
- Rekognition (Auto-tagging of Images)
- DynamoDB (Metadata Storage)
- API Gateway (Tag Search API)
- CloudWatch (Logs + Debugging)

---

## 🧱 Architecture Diagram

>

---

## 🌍 Real-World Use Cases

- **E-Commerce**: Automatically categorize product images for easier inventory management.
- **Medical Imaging**: Auto-label and search through X-rays, MRI scans, etc.
- **Security & Surveillance**: Tag and search security camera footage to identify people, objects, or scenes.
- **Photo Management Apps**: Create smart photo search by themes or objects.

---

## 📋 Phase-wise Implementation Plan

| Phase | Task                                        | Tools/Services       |
| ----- | ------------------------------------------- | -------------------- |
| 1️⃣   | Set up S3 to upload images                  | S3                   |
| 2️⃣   | Trigger Lambda when image is uploaded       | Lambda               |
| 3️⃣   | Auto-label image using Rekognition          | Rekognition          |
| 4️⃣   | Store labels + image info                   | DynamoDB             |
| 5️⃣   | Create API to search images by tags         | API Gateway + Lambda |
| 6️⃣   | Build web page to upload/search/view images | HTML + JS            |
| 7️⃣   | Host the webpage for free                   | S3 Static Hosting    |

---

## 🛠️ Complete Step-by-Step Setup Guide

### 📁 Phase 1: Upload Images to S3

1. Create a bucket named `visual-wizard-photos`
2. Inside it, create a folder named `images/`
3. Set the bucket policy for public upload and read access:

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
```

### ⚙️ Phase 2: Create Lambda Function for Image Labeling

1. Name: `LabelImageFunction`
2. Trigger: S3 PUT on `visual-wizard-photos/images/`
3. Add the following IAM Policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "rekognition:DetectLabels",
        "s3:GetObject",
        "dynamodb:PutItem",
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents"
      ],
      "Resource": "*"
    }
  ]
}
```

4. Store the labels and metadata in DynamoDB table `imagetable`
   - Partition Key: `ImageKey` (string)

### 🔍 Phase 3–4: Setup Rekognition and DynamoDB

- No extra setup for Rekognition (built-in AWS)
- Create DynamoDB Table: `imagetable`
  - Partition key: `ImageKey`
  - Store labels as string list and metadata (e.g., timestamp)

### 🌐 Phase 5: API Gateway + Search Lambda

1. Create Lambda: `SearchImageByTag`
2. Add IAM Policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["dynamodb:Scan", "logs:*"] ,
      "Resource": "*"
    }
  ]
}
```

3. Create a GET method with query string param `tag` using API Gateway
4. Integrate this API with `SearchImageByTag` Lambda

### 🧑‍💻 Phase 6–7: Frontend + Static Hosting

1. Create a new S3 bucket: `visual-wizard-site`
2. Upload `index.html` file
3. Enable static hosting
4. CORS config for `visual-wizard-photos`:

```json
[
  {
    "AllowedHeaders": ["*"],
    "AllowedMethods": ["GET", "PUT", "POST"],
    "AllowedOrigins": ["*"]
  }
]
```

---

## 🧑‍💻 Frontend Code (index.html)

```html
<!DOCTYPE html>
<html>
<head>
  <title>The Visual Wizard</title>
  <style>
    body { font-family: Arial; padding: 20px; }
    img { height: 200px; margin: 10px; border: 1px solid #ccc; }
  </style>
</head>
<body>
  <h1>📷 The Visual Wizard</h1>

  <h3>Upload Image</h3>
  <input type="file" id="imageInput">
  <button onclick="uploadImage()">Upload</button>
  <p id="uploadStatus"></p>

  <h3>Search by Tag</h3>
  <input type="text" id="tagInput" placeholder="Enter tag (e.g., dog)">
  <button onclick="searchImages()">Search</button>
  <div id="result"></div>

  <script>
    const s3BucketName = "visual-wizard-photos";
    const apiGatewayUrl = "YOUR_API_GATEWAY_URL";

    function uploadImage() {
      const file = document.getElementById("imageInput").files[0];
      if (!file) return;
      const fileName = `images/${file.name}`;

      fetch(`https://${s3BucketName}.s3.amazonaws.com/${fileName}`, {
        method: "PUT",
        body: file,
        headers: { "Content-Type": file.type }
      })
        .then(res => {
          if (res.ok) {
            document.getElementById("uploadStatus").innerText = "✅ Upload successful!";
          } else {
            document.getElementById("uploadStatus").innerText = "❌ Upload failed.";
          }
        })
        .catch(err => {
          document.getElementById("uploadStatus").innerText = `❌ Upload error: ${err}`;
        });
    }

    function searchImages() {
      const tag = document.getElementById("tagInput").value;
      fetch(`${apiGatewayUrl}?tag=${tag}`)
        .then(res => res.json())
        .then(data => {
          const container = document.getElementById("result");
          container.innerHTML = "";
          data.forEach(item => {
            const img = document.createElement("img");
            img.src = `https://${s3BucketName}.s3.amazonaws.com/${item.ImageKey}`;
            container.appendChild(img);
          });
        });
    }
  </script>
</body>
</html>
```

---

## 🖼️ Screenshots

>

>

>

>

---

## ✅ Final Thoughts

This project demonstrates how to use AWS services to build a powerful and searchable image tagging application. It covered:

- Uploading to S3
- Image recognition using Rekognition
- Tag storage in DynamoDB
- RESTful search via API Gateway + Lambda
- Web frontend hosted on S3

🔐 You also learned to solve:

- CORS issues
- IAM role permissions
- API Gateway mapping
- Debugging with CloudWatch

You now have a production-style full-stack cloud project for your portfolio!

---

📌 **Project Tags**: `AWS`, `Lambda`, `S3`, `DynamoDB`, `Rekognition`, `API Gateway`, `CloudWatch`, `Serverless`, `Full Stack`, `Portfolio`

🧠 Made with ❤️ for learning and cloud mastery!

