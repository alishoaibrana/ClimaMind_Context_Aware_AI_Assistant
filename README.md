#   ClimaMind Context Aware AI Assistant

A full-stack, context-aware Retrieval-Augmented Generation (RAG) AI assistant built with **FastAPI**, **LangChain**, **Groq LLMs**, **FAISS**, and **React (Vite + Tailwind CSS)**.

Features real-time document search, Google OAuth 2.0 multi-user authentication, voice-to-text input with **Whisper Large v3 Turbo**, and permanent SQLite chat history persistence.

---

##  Features

-  **Context-Aware RAG Pipeline**: Powered by LangChain, Groq LLM, and FAISS vector embeddings with history-aware query reformulation.
-  **Multi-Format Document Ingestion**: Upload PDF, DOCX, TXT, XLSX, and CSV documents to index and chat with custom files dynamically.
-  **Voice Speech-to-Text**: Real-time microphone audio recording with instant transcription powered by **Groq Whisper Large v3 Turbo**.
-  **Google OAuth 2.0 Authentication**: Secure sign-in with JWT token exchange and user-isolated data.
-  **Permanent SQLite Storage**: Chat sessions, message history, and user metadata are saved permanently in local SQLite (`rag_chatbot.db`).
-  **Modern Sleek UI**: Built with React, Tailwind CSS, Lucide icons, glassmorphism, and responsive design.

---

##  Architecture

```
RAG-Chatbot-LangChain/
├── backend/
│   ├── main.py               # FastAPI application & API endpoints
│   ├── auth.py               # Google OAuth & JWT authentication
│   ├── models.py             # SQLAlchemy models (User, ChatSession, ChatMessage)
│   ├── database.py           # SQLite database engine & session factory
│   ├── document_loader.py    # Multi-format document parser & splitter
│   ├── config.py             # App configuration & environment loader
│   ├── .env.example          # Environment variables template
│   └── requirements.txt      # Python dependencies
├── frontend/
│   ├── src/
│   │   ├── components/       # UI Components (ChatPanel, Sidebar, MessageInput, EmptyState)
│   │   ├── context/          # React Auth Context
│   │   ├── pages/            # Login & OAuth callback handlers
│   │   └── services/         # Axios API client
│   ├── package.json          # Node dependencies
│   └── tailwind.config.js    # Styling configuration
└── knowledge_base.txt        # Default global warming knowledge base
```

---

##  Quick Start

### 1. Backend Setup

```bash
# Navigate to project root
# Create and activate virtual environment
python -m venv chatbotenv
chatbotenv\Scripts\activate       # On Windows
# source chatbotenv/bin/activate  # On macOS/Linux

# Install dependencies
pip install -r backend/requirements.txt

# Configure environment variables
cp backend/.env.example backend/.env
# Open backend/.env and add your GROQ_API_KEY and Google OAuth credentials

# Start backend server
uvicorn backend.main:app --reload --host 0.0.0.0 --port 8000
```

### 2. Frontend Setup

```bash
# In a second terminal:
cd frontend

# Install Node dependencies
npm install

# Start Vite dev server
npm run dev
```

### 3. Open Application
Open [http://localhost:5173](http://localhost:5173) in your browser.

---

## ☁️ Production Deployment (Render + Vercel)

The backend is hosted on Render and the Vite frontend on Vercel. Use the same GitHub repository for both services.

### 1. Create the production database and backend on Render

1. Push the latest code to GitHub.
2. In Render, create a PostgreSQL database and a Web Service connected to this repository.
3. Set the Web Service **Root Directory** to `backend`, the **Build Command** to `pip install -r requirements.txt`, and the **Start Command** to `uvicorn main:app --host 0.0.0.0 --port $PORT`.
4. Add these environment variables to the backend service:

   | Variable | Value |
   | :--- | :--- |
   | `DATABASE_URL` | Internal connection URL for the Render PostgreSQL database |
   | `GROQ_API_KEY` | Your Groq API key |
   | `GOOGLE_CLIENT_ID` | OAuth client ID from Google Cloud |
   | `GOOGLE_CLIENT_SECRET` | OAuth client secret from Google Cloud |
   | `GOOGLE_REDIRECT_URI` | `https://YOUR-RENDER-SERVICE.onrender.com/auth/google/callback` |
   | `JWT_SECRET` | A long, random production secret |
   | `SESSION_SECRET_KEY` | A separate long, random production secret |
   | `FRONTEND_URL` | `https://YOUR-VERCEL-DOMAIN` |
   | `ALLOWED_ORIGINS` | `https://YOUR-VERCEL-DOMAIN` |

   Replace the example domains with the actual service URLs. Do not include a trailing slash in `FRONTEND_URL` or `ALLOWED_ORIGINS`. Keep secrets out of GitHub.

### 2. Configure Google OAuth

In the Google Cloud OAuth client settings, add the Vercel domain to **Authorized JavaScript origins** and the Render callback URL above to **Authorized redirect URIs**. The redirect URI must exactly match `GOOGLE_REDIRECT_URI` in Render.

### 3. Deploy the frontend on Vercel

1. Import the same GitHub repository into Vercel and set **Root Directory** to `frontend`.
2. Use `npm run build` as the build command and `dist` as the output directory.
3. Add the environment variable `VITE_API_URL` with the public Render backend URL, for example `https://YOUR-RENDER-SERVICE.onrender.com`.
4. Deploy, then update Render's `FRONTEND_URL` and `ALLOWED_ORIGINS` with the actual Vercel domain and redeploy the backend if needed.

The frontend includes a Vercel rewrite for the `/auth/callback` route used by Google sign-in. Verify the backend at `https://YOUR-RENDER-SERVICE.onrender.com/health`, then test Google sign-in and a chat request on the Vercel site.

Uploaded document indexes and chat working state are held in backend memory, so a backend restart or redeploy clears that transient state. Chat records and user accounts are stored in PostgreSQL.

---

## Environment Configuration (`backend/.env`)

| Variable | Description |
| :--- | :--- |
| `GROQ_API_KEY` | Your Groq API key (for LLM and Whisper STT) |
| `GOOGLE_CLIENT_ID` | Google Cloud Console OAuth Client ID |
| `GOOGLE_CLIENT_SECRET`| Google Cloud Console OAuth Client Secret |
| `GOOGLE_REDIRECT_URI` | `http://localhost:8000/auth/google/callback` |
| `JWT_SECRET` | Secret key for signing user JWT tokens |
| `SESSION_SECRET_KEY` | Secret key for Starlette session middleware |
| `FRONTEND_URL` | `http://localhost:5173` |

---

## License
MIT License.
