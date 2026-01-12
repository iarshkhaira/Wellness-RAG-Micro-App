/************************************************
 * Wellness RAG Micro-App (Single File)
 * Ask Me Anything About Yoga
 ************************************************/

import express from "express";
import mongoose from "mongoose";
import cors from "cors";
import bodyParser from "body-parser";
import dotenv from "dotenv";
import OpenAI from "openai";

dotenv.config();

/* -------------------- APP SETUP -------------------- */
const app = express();
app.use(cors());
app.use(bodyParser.json());

/* -------------------- OPENAI -------------------- */
const openai = new OpenAI({
  apiKey: process.env.OPENAI_API_KEY,
});

/* -------------------- MONGODB -------------------- */
mongoose
  .connect(process.env.MONGODB_URI)
  .then(() => console.log("✅ MongoDB connected"))
  .catch((err) => console.error("MongoDB error:", err));

const QuerySchema = new mongoose.Schema({
  query: String,
  retrievedChunks: [String],
  answer: String,
  isUnsafe: Boolean,
  feedback: Boolean,
  createdAt: { type: Date, default: Date.now },
});

const QueryLog = mongoose.model("QueryLog", QuerySchema);

/* -------------------- YOGA KNOWLEDGE BASE -------------------- */
/* (Extend this to 20–50 entries for final submission) */

const yogaDocs = [
  {
    id: "A1",
    title: "Benefits of Shavasana",
    content:
      "Shavasana helps reduce stress, relax the nervous system, and improve mindfulness. It is suitable for all levels.",
  },
  {
    id: "A2",
    title: "Contraindications of Headstand",
    content:
      "Headstand should be avoided by people with neck injuries, glaucoma, high blood pressure, or during pregnancy.",
  },
  {
    id: "A3",
    title: "Pranayama for Beginners",
    content:
      "Breathing practices like Anulom Vilom help calm the mind and improve lung capacity.",
  },
];

/* -------------------- SIMPLE EMBEDDING -------------------- */
/* Lightweight similarity (for demo clarity) */

function embed(text) {
  const words = text.toLowerCase().split(/\W+/);
  const vector = {};
  words.forEach((w) => {
    if (!w) return;
    vector[w] = (vector[w] || 0) + 1;
  });
  return vector;
}

function similarity(vec1, vec2) {
  let dot = 0,
    normA = 0,
    normB = 0;

  for (let k in vec1) {
    dot += (vec1[k] || 0) * (vec2[k] || 0);
    normA += vec1[k] ** 2;
  }
  for (let k in vec2) normB += vec2[k] ** 2;

  return dot / (Math.sqrt(normA) * Math.sqrt(normB) || 1);
}

/* Pre-embed documents */
const embeddedDocs = yogaDocs.map((doc) => ({
  ...doc,
  embedding: embed(doc.content),
}));

/* -------------------- SAFETY FILTER -------------------- */
const unsafeKeywords = [
  "pregnant",
  "pregnancy",
  "hernia",
  "glaucoma",
  "high blood pressure",
  "surgery",
  "heart",
];

function isUnsafeQuery(query) {
  return unsafeKeywords.some((k) =>
    query.toLowerCase().includes(k)
  );
}

/* -------------------- ASK API -------------------- */
app.post("/ask", async (req, res) => {
  try {
    const { query } = req.body;
    if (!query) return res.status(400).json({ error: "Query required" });

    const unsafe = isUnsafeQuery(query);

    /* Retrieve top 3 chunks */
    const qEmbedding = embed(query);
    const scored = embeddedDocs
      .map((d) => ({
        ...d,
        score: similarity(qEmbedding, d.embedding),
      }))
      .sort((a, b) => b.score - a.score)
      .slice(0, 3);

    const context = scored.map((d) => d.content).join("\n");

    let answer;

    if (unsafe) {
      answer = `⚠️ Safety Notice:
Your question involves a condition where yoga practices may be risky.

Consider gentle breathing or relaxation practices.
Please consult a doctor or certified yoga therapist before practicing.`;
    } else {
      const completion = await openai.chat.completions.create({
        model: "gpt-4o-mini",
        messages: [
          {
            role: "system",
            content:
              "You are a yoga assistant. Answer ONLY using the provided context.",
          },
          {
            role: "user",
            content: `Context:\n${context}\n\nQuestion:\n${query}`,
          },
        ],
      });

      answer = completion.choices[0].message.content;
    }

    /* Store in MongoDB */
    const log = await QueryLog.create({
      query,
      retrievedChunks: scored.map((s) => s.title),
      answer,
      isUnsafe: unsafe,
    });

    res.json({
      answer,
      sources: scored.map(
        (s, i) => `Source ${i + 1}: ${s.title}`
      ),
      isUnsafe: unsafe,
      queryId: log._id,
    });
  } catch (error) {
    console.error(error);
    res.status(500).json({ error: "Internal server error" });
  }
});

/* -------------------- FEEDBACK API -------------------- */
app.post("/feedback", async (req, res) => {
  const { queryId, helpful } = req.body;
  await QueryLog.findByIdAndUpdate(queryId, {
    feedback: helpful,
  });
  res.json({ message: "Feedback saved" });
});

/* -------------------- SERVER -------------------- */
const PORT = process.env.PORT || 5000;
app.listen(PORT, () =>
  console.log(`🧘 Yoga RAG Server running on port ${PORT}`)
);
