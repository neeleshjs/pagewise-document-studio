# Pagewise — Document Studio

A Streamlit document workspace for PDFs, scanned files, Word documents, PowerPoint presentations, text files, and images. Pagewise extracts text, creates a quick local summary, and provides retrieval-augmented chat that cites relevant pages or slides. Optional Groq-powered tools create structured summaries, study guides with quizzes, and action-item lists.

## Run locally

1. Install Python 3.10+ and the Tesseract OCR engine (`tesseract-ocr` on Debian/Ubuntu; `tesseract` on macOS; Windows installer from the UB Mannheim Tesseract project).
2. Create and activate a virtual environment.
3. Run `pip install -r requirements.txt`.
4. Copy `.env.example` to `.env`, add `GROQ_API_KEY`, then run `streamlit run app.py`.

Digital PDFs are read directly; scanned PDF pages and images use Tesseract OCR. The app currently accepts PDFs up to 40 pages and files up to 20 MB. The quick extractive summary works without an API key. AI chat, structured summaries, study guides, and action lists require a Groq API key, which is read server-side and never exposed in the browser. Longer AI summaries and study tools process the document in batches before combining the notes, so results can cover the full file. Chat retrieves the most relevant text passages with local BM25 search before sending those passages to the language model; answers are prompted to cite page or slide labels and avoid unsupported claims.

## Configuration

- `GROQ_API_KEY`: required for chat only
- AI summaries, document chat, study guides, and action lists use OpenAI's open-weight GPT-OSS 120B through Groq's chat completions API.
- `TESSERACT_CMD`: optional path to the Tesseract executable on Windows

## Deploy

The included `Dockerfile` and `render.yaml` deploy Pagewise as a Render web service with Tesseract installed. Add `GROQ_API_KEY` as a server-side secret named `groq.env` with a single line `GROQ_API_KEY=...`; the app reads it from `/etc/secrets/groq.env`. Never commit `.env` or the secret file. The local quick summary and OCR work without Groq; AI summaries, chat, study guides, and action lists need the key.

## Privacy

Uploaded files and chat history are held in the current Streamlit session. This demo does not persist documents to a database. Do not upload confidential documents to a hosted instance unless you control its hosting and data policy.
