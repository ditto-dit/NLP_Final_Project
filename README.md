# 🎓 SCU Finance Department ChatBot

> **Course:** Natural Language Processing (NLP)
> **Team:** Kai Yang, Sangita Poudel, Bilwa Khaparde, Sami Shikhare

---

## 📌 Project Overview

Students in the Santa Clara University (SCU) Finance Department frequently struggle to find quick, accurate answers to questions about major requirements, course prerequisites, program pathways, and advising FAQs. These answers are often buried across multiple PDFs, spreadsheets, and web pages — and the advising office isn't always available.

This project builds **FinChatBot**, a domain-specific AI-powered chatbot for SCU's Finance Department. It uses **Retrieval-Augmented Generation (RAG)** — a technique where an AI model is grounded in real, curated documents rather than relying solely on general training data — to deliver accurate, cited, 24/7 academic advising to students. When questions fall outside the knowledge base, the bot either searches the SCU website directly or refers students to the Drahmann Academic Advising Center with full contact details.

---

## 🎯 Problem & Solution

| Problem | Solution |
|---|---|
| Finance requirements and prerequisites are scattered across many documents | Unified chatbot that searches all sources at once |
| Advising office has limited hours | 24/7 instant responses grounded in official documentation |
| Students unsure which questions can be answered vs. need human help | Smart routing: local KB → SCU web search → Drahmann Center fallback |
| General knowledge questions mixed in with academic ones | Automatic question classifier separates SCU-specific from general queries |

---

## 📊 Knowledge Base (Data)

The chatbot was built on **6 official documents** provided by the SCU Finance Department:

| # | Document | Format | Purpose |
|---|---|---|---|
| 1 | Finance Requirements List | PDF | Degree requirements checklist for finance majors |
| 2 | Real Estate Minor Check Sheet | Excel (.xlsx) | Requirements for students adding the real estate minor |
| 3 | FAQ Advising Questions | Google Doc | Frequently asked advising questions and answers |
| 4 | Finance Prerequisites Flowchart | PDF | Visual guide to course sequencing and prerequisites |
| 5 | 2025–2026 Tentative Finance Course Offerings | Excel (.xlsx) | Upcoming course availability by quarter |
| 6 | Webpages for Finance Bot | Google Sheet | Curated list of relevant SCU web pages |

All documents were parsed, chunked, embedded, and stored in a persistent vector database for real-time retrieval.

---

## 🛠️ Tools & Technologies

| Tool / Library | Role |
|---|---|
| **Python** | Core programming language |
| **LangChain** | Document loading, text splitting, embeddings pipeline |
| **OpenAI Embeddings + GPT-4o-mini** | Text vectorization and response generation |
| **ChromaDB** | Persistent on-disk vector store (primary knowledge base) |
| **FAISS** | In-memory vector store (fast session-level retrieval) |
| **CrewAI** | Agentic AI framework — defines the Finance Advisor persona, tasks, and tool use |
| **SerperDev (Google Search API)** | Web search tool for queries not covered by local documents |
| **PyPDF / python-docx / openpyxl** | Parsing PDFs, Word documents, and Excel files |
| **Streamlit** | Interactive web-based chat interface |
| **Ngrok** | Tunneling to expose the local Streamlit app publicly from Google Colab |
| **Google Colab + Google Drive** | Development environment and document storage |

---

## 🏗️ How It Works — System Architecture

The chatbot has three main subsystems that work together:

### 1. Ingestion Pipeline (Loading & Indexing Documents)
When the app starts (or when faculty re-index the knowledge base), documents are:
- Loaded from Google Drive using format-specific parsers (PyPDF, Word, Excel)
- Split into overlapping chunks (1,000 characters with 150-character overlap) to preserve context across sections
- Converted into numerical vector representations (embeddings) using OpenAI's embedding model
- Stored in **two vector stores**: ChromaDB (persistent, survives restarts) and FAISS (in-memory, faster for the current session)

### 2. Retrieval & Routing
When a user asks a question:
- An **SCU classifier** checks whether the question is Finance/SCU-related or general knowledge
- The **Retriever Module** searches both ChromaDB and FAISS across both the main knowledge base and any user-uploaded documents, de-duplicating results
- Retrieval priority: Primary KB (Chroma) → Backup KB (FAISS) → User Uploads (Chroma & FAISS)

### 3. Generation Engine (CrewAI Agent)
Retrieved context is passed to a **CrewAI agent** configured as a "Finance Advisor" with:
- A professional but friendly tone and style
- Source citation rules (web sources → clickable Markdown links; local docs → document name)
- Routing logic: SCU questions use `site:scu.edu` web search if local context is insufficient; general questions go directly to web search
- A Drahmann Center fallback when neither local context nor web search can answer the question
- Built-in safety guardrails (see below)

---

## ✨ Features

### 🎙️ Voice Input
Students can speak their question instead of typing. A microphone button is embedded directly in the chat input box with a live "Listening..." indicator. Built using the Web Speech API (JavaScript), injected into the Streamlit UI.

### 💬 Persistent Chat History
Previous conversations are preserved across the session, organized by user (identified via a hashed API key). Users can start new sessions or reference past ones — making it easy to pick up where they left off.

### 📁 End-User Document Upload
Students can upload their own files (4-year plans, transcripts, course history) in PDF, Word, or Excel format. The bot incorporates those documents into its retrieval process and can reference them when answering questions like "Can I add the Real Estate Minor and still graduate in 4 years?"

### 🌐 Web Search Integration
When the local knowledge base doesn't have sufficient information, the bot automatically searches the web — prioritizing `site:scu.edu` for SCU-related questions and using broader search for general queries. All web sources are cited with clickable links in the response.

### 🗄️ Live Knowledge Base Re-Indexing (Faculty Feature)
Faculty or admins can add or remove documents from the knowledge base and immediately re-vectorize them via the sidebar — no code changes required. This ensures the bot always reflects the most current advising documents.

### 🌙 Dark / Light Mode
Users can toggle between dark and light display themes for visual comfort.

### 🛡️ Safety Guardrails
The agent is configured to:
- Refuse harmful, dangerous, illegal, or discriminatory requests with a polite redirect
- Avoid providing personalized financial or investment advice
- Fall back to the Drahmann Center with full contact info when it cannot confidently answer an academic question

**Drahmann Academic Advising Center fallback info provided automatically:**
- Location: Kenna Hall 101
- Phone: (408) 554-4318
- Email: drahmanncenter@scu.edu
- Hours: Monday–Friday, 9:00am–4:00pm PDT

---

## 📈 Key Results & Insights

- The bot successfully answered SCU Finance questions grounded in official documents, including prerequisite chains, graduation requirements, and real estate minor eligibility
- The dual vector store strategy (Chroma + FAISS) balanced retrieval accuracy with speed
- End-user document upload enabled personalized answers: uploading a 4-year plan let students ask "can I add the Real Estate Minor and still graduate on time?" and get a context-aware answer with cited sources
- Safety guardrails correctly handled inappropriate inputs (e.g., profanity, harmful requests) without crashing or hallucinating
- The Drahmann Center fallback provided a safe escalation path for questions outside the bot's knowledge

---

## 🔮 Future Enhancements

- **Cloud hosting on AWS** to replace Ngrok for stable, long-term access
- **Image understanding** to let students upload and reference JPG/JPEG files (e.g., scanned documents)
- **Mobile-friendly UI** for better accessibility on phones
- **Starter question prompts** — three auto-suggested questions to help users begin without needing to type a query
- **SCU Workday integration** to pull personalized student data (completed courses, remaining requirements)
- **Google Calendar integration** for deadline reminders (add/drop periods, registration windows)
- **Multi-language support** for international students

---

## ▶️ How to Run

This project runs on **Google Colab** with Google Drive as the document storage backend.

1. Upload the knowledge base documents to a folder in your Google Drive (e.g., `MyDrive/FinChatBot/`)
2. Open `FinChatBot_Final_Version.ipynb` in Google Colab
3. Run Cell 1 to install all dependencies
4. Mount your Google Drive when prompted
5. In the app sidebar, enter your **OpenAI API key** and optionally your **Serper API key** (for web search)
6. Run the final cells to launch Streamlit and create a public Ngrok URL
7. Open the Ngrok URL in your browser to interact with the chatbot

### Required API Keys
- **OpenAI API key** — [platform.openai.com](https://platform.openai.com)
- **Serper API key** (optional, for web search) — [serper.dev](https://serper.dev)
- **Ngrok auth token** (for public URL) — [ngrok.com](https://ngrok.com)

### Dependencies (installed automatically in notebook)
```
crewai[tools]  langchain  langchain-openai  langchain-community  langchain-chroma
faiss-cpu  unstructured[all-docs]  pypdf  python-docx  openpyxl  streamlit  pyngrok
```
