# YouTube Video Summarizer & Q&A

A powerful tool that downloads YouTube videos, transcribes the audio, generates concise summaries, and allows you to ask questions about the video content using RAG (Retrieval-Augmented Generation).

## 🚀 Features

- **Video Ingestion**: Downloads audio from YouTube videos using `yt-dlp`.
- **Intelligent Transcription**:
  - **Auto-Switching**: Automatically chooses between **Single-threaded** (short videos) and **Parallel Processing** (long videos > 5 mins) for optimal speed.
  - **Whisper Integration**: Uses OpenAI's Whisper model for high-accuracy speech-to-text.
- **Advanced RAG Pipeline**:
  - **Hybrid Search**: Retrieves top candidates using Vector Search (FAISS).
  - **Re-ranking**: Refines results using a Cross-Encoder (`ms-marco-MiniLM-L-6-v2`) to ensure high relevance.
- **Background Processing**:
  - **Asynchronous Architecture**: Uses **Redis** and **RQ (Redis Queue)** to handle long-running tasks without blocking the UI.
  - **Real-time ETA**: Calculates and displays estimated time remaining based on video duration and current stage.
- **Summarization & Q&A**: Generates concise summaries and answers questions using **Llama 3.3 70B** via Groq.

## 🛠️ Tech Stack

- **Backend**: FastAPI
- **Frontend**: Streamlit
- **LLM / Inference**: Groq API (Llama 3.3-70b-versatile)
- **Transcription**: OpenAI Whisper (Local) with Parallel Execution
- **Vector DB**: FAISS
- **Re-ranking**: SentenceTransformers (`cross-encoder/ms-marco-MiniLM-L-6-v2`)
- **Queue System**: Redis + RQ
- **Video Processing**: `yt-dlp`, `ffmpeg-python`, `pydub`

## 📋 Prerequisites

- Python 3.8+
- [FFmpeg](https://ffmpeg.org/download.html) installed and added to PATH.
- [Redis](https://redis.io/download/) installed and running locally.
- A **Groq API Key**.

## ⚙️ Installation

1.  **Clone the repository**:
    ```bash
    git clone <repository-url>
    cd video-summarizer
    ```

2.  **Create and activate a virtual environment**:
    ```bash
    python -m venv .venv
    # Windows
    .venv\Scripts\activate
    # Mac/Linux
    source .venv/bin/activate
    ```

3.  **Install dependencies**:
    ```bash
    pip install -r requirements.txt
    ```

4.  **Configure Environment**:
    Create a `.env` file in the root directory:
    ```env
    GROQ_API_KEY=your_groq_api_key_here
    ```

## 🏃‍♂️ Running the Application

This app uses an async worker architecture. You need **3 terminals**:

### 1. Start Redis Server
Ensure your Redis server is running.
```bash
redis-server
```

### 2. Start the Worker (Background Processor)
This handles the heavy lifting (transcription, embedding).
```bash
rq worker ingestion --worker-class rq.worker.SimpleWorker
```

### 3. Start the Backend API
```bash
uvicorn app.main:app --reload
```

### 4. Start the Frontend UI
```bash
streamlit run frontend/streamlit_app.py
```

## 📂 Project Structure

```
├── app/
│   ├── api/            # FastAPI Routes
│   ├── db/             # Vector Database (FAISS)
│   ├── rag/            # Chunking & Prompting
│   ├── services/       # Core Logic (YouTube, STT, QA, Re-ranker)
│   ├── utils/          # Redis connection, Task Queue, Audio utils
│   ├── workers/        # RQ Worker logic (ingest_worker.py)
│   └── main.py         # App Entry Point
├── data/               # Persistent storage for indices and summaries
├── frontend/           # Streamlit Interface
├── scripts/            # Testing scripts
├── .env                # Secrets
└── requirements.txt    # Dependencies
```
