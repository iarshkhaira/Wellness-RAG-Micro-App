🧘 Wellness RAG Micro-App
Ask Me Anything About Yoga

A single-file, full-stack AI micro-application that answers yoga-related questions using a Retrieval-Augmented Generation (RAG) pipeline with built-in safety guardrails for health-sensitive queries.

This project is designed to be simple, explainable, and evaluation-friendly, making it ideal for Unstop challenges and technical assessments.

🚀 Project Overview

The Wellness RAG Micro-App allows users to ask questions about yoga practices, benefits, and precautions.
The system retrieves relevant knowledge from a curated yoga dataset and generates context-grounded AI answers, while ensuring safe and responsible responses for sensitive health topics.

Key goals:

Demonstrate RAG design and retrieval logic

Implement safety-first AI behavior

Log complete interaction data in MongoDB

Keep architecture clean and easy to evaluate

✨ Key Features
🔍 Retrieval-Augmented Generation (RAG)

Lightweight embedding & similarity search

Top-K document retrieval

AI answers grounded strictly in retrieved context

⚠️ Safety Guardrails (Mandatory)

Detects health-sensitive queries (pregnancy, BP, surgery, etc.)

Prevents medical advice or risky instructions

Returns clear safety warnings and professional guidance

🗄️ MongoDB Logging

Each user interaction stores:

User query

Retrieved knowledge sources

AI-generated answer

Safety flag (isUnsafe)

Optional user feedback

Timestamp

🔎 Transparent Responses

API response includes sources used

Easy to display in any frontend (React, Streamlit, Postman)

🛠️ Tech Stack

Backend

Node.js

Express.js

MongoDB (Mongoose)

AI

OpenAI API

Context-grounded prompting

Architecture

Single-file backend (app.js)

REST APIs

🧠 How the RAG Pipeline Works
User Query
   ↓
Safety Filter (keyword-based)
   ↓
Query Embedding
   ↓
Similarity Search (Top-3 Chunks)
   ↓
Prompt Construction (Context + Question)
   ↓
AI Answer Generation
   ↓
MongoDB Logging
   ↓
Response with Sources & Safety Flag

📚 Knowledge Base

Curated yoga content (expandable to 20–50 entries)

Topics covered:

Yoga asanas

Benefits

Contraindications

Pranayama

Beginner-friendly practices

All content is self-written summaries based on publicly available knowledge
(No raw scraping or copyrighted redistribution)

⚠️ Safety Logic
Unsafe Query Detection

Queries are flagged unsafe if they include keywords related to:

Pregnancy

High blood pressure

Glaucoma

Hernia

Heart conditions

Recent surgery

Unsafe Behavior

When a query is unsafe:

isUnsafe = true is stored

AI does not give pose instructions

Response includes:

Safety warning

Gentle alternatives (breathing, relaxation)

Advice to consult a professional

⚠️ No medical diagnosis or treatment is provided

🔌 API Endpoints
POST /ask

Request

{
  "query": "Can pregnant women do headstand?"
}


Response

{
  "answer": "⚠️ Safety Notice: ...",
  "sources": [
    "Source 1: Contraindications of Headstand"
  ],
  "isUnsafe": true,
  "queryId": "65f1c9..."
}

POST /feedback

Request

{
  "queryId": "65f1c9...",
  "helpful": true
}


Response

{
  "message": "Feedback recorded"
}

🗃️ MongoDB Schema
{
  query: String,
  retrievedChunks: [String],
  answer: String,
  isUnsafe: Boolean,
  feedback: Boolean,
  createdAt: Date
}

⚙️ How to Run Locally
1️⃣ Clone the Repository
git clone https://github.com/your-username/wellness-rag-yoga-app.git
cd wellness-rag-yoga-app

2️⃣ Install Dependencies
npm install

3️⃣ Environment Variables

Create a .env file using .env.example:

OPENAI_API_KEY=your_openai_key
MONGODB_URI=mongodb://127.0.0.1:27017/yoga_rag
PORT=5000

4️⃣ Start the Server
npm start


Server will run at:

http://localhost:5000

🧪 Sample Test Queries
Safe Query
{
  "query": "What are the benefits of Shavasana?"
}

Unsafe Query
{
  "query": "Can pregnant women do headstand?"
}

📈 Evaluation Alignment
Evaluation Criteria	Covered
RAG Design & Retrieval	✅
Safety & Guardrails	✅
Backend Architecture	✅
MongoDB Logging	✅
Explainability	✅
Code Simplicity	✅
