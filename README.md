<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>AI-Powered RAG API Service</title>
  <style>
    body { font-family: Arial, sans-serif; line-height: 1.6; max-width: 900px; margin: auto; padding: 20px; }
    h1, h2, h3 { color: #333; }
    code { background: #f4f4f4; padding: 2px 6px; border-radius: 4px; }
    pre { background: #f4f4f4; padding: 12px; border-radius: 4px; overflow-x: auto; }
    ul { margin-left: 20px; }
    li { margin-bottom: 6px; }
  </style>
</head>
<body>

<h1>AI-Powered RAG API Service</h1>

<p>This project provides a backend API service for a Retrieval-Augmented Generation (RAG) system. It leverages Google's Gemini models and LangChain to answer questions based on a dynamic, ingestible knowledge base. This service can power any chatbot, application, or script that needs to query a specific set of documents.</p>

<h2>Core Features</h2>
<ul>
  <li><strong>Intelligent RAG Backend:</strong> A Python-based service using Flask that leverages Google's Gemini models to understand and answer user queries.</li>
  <li><strong>Dynamic Knowledge Base:</strong> Exposes API endpoints to ingest and process various document types (<code>.pdf</code>, <code>.docx</code>, <code>.txt</code>, <code>.json</code>) and URLs, allowing the knowledge base to be updated dynamically.</li>
  <li><strong>Vector Search:</strong> Utilizes FAISS (Facebook AI Similarity Search) for efficient and fast retrieval of relevant context from the knowledge base.</li>
  <li><strong>Containerized:</strong> Includes a <code>Dockerfile</code> for easy, consistent deployment.</li>
</ul>

<h2>How It Works (Mechanism)</h2>

<p>The project operates on two primary workflows, both accessible via API: <strong>Data Ingestion</strong> and <strong>Querying</strong>.</p>

<h3>1. Data Ingestion Workflow (Learning)</h3>
<p>This workflow is for feeding new information into the system.</p>

<ol>
  <li><strong>API Call:</strong> An administrator sends a file (<code>.pdf</code>, <code>.docx</code>, etc.) or a URL to a specific backend endpoint (<code>/ingest_file</code> or <code>/ingest_url</code>).</li>
  <li><strong>Validation:</strong> The backend first identifies the type of input using <code>uploadValidification.py</code>.</li>
  <li><strong>Processing:</strong> <code>TextProcessor.py</code> then converts the input into clean, plain text. For example, it extracts text from a PDF or scrapes it from a URL.</li>
  <li><strong>Storage:</strong> The extracted text is saved to a central file (<code>output.txt</code>).</li>
  <li><strong>Indexing:</strong> The next time a query is made, the <code>rag.py</code> module detects the new information in <code>output.txt</code>, creates vector embeddings, and updates the FAISS vector database (<code>faiss_index</code>).</li>
</ol>

<h3>2. Querying Workflow (Answering)</h3>
<p>This is the main workflow for asking questions.</p>

<ol>
  <li><strong>API Call:</strong> A client application makes a <code>POST</code> request to the <code>/rag</code> endpoint on the Flask server, sending the user's question in the request body.</li>
  <li><strong>Retrieval:</strong> The <code>rag.py</code> module on the backend receives the question. It first converts the question into a vector embedding and uses FAISS to retrieve the most relevant text chunks from its vector database.</li>
  <li><strong>Generation:</strong> The retrieved text chunks (the "context") and the original user question are sent to the Google Gemini LLM.</li>
  <li><strong>Response:</strong> The LLM generates a coherent, human-like answer based on the provided context. This answer is sent back to the client as a JSON response.</li>
</ol>

<h2>Tech Stack</h2>

<ul>
  <li><strong>Backend:</strong>
    <ul>
      <li>Python</li>
      <li>Flask</li>
      <li>LangChain</li>
      <li>Google Gemini (via <code>langchain-google-genai</code>)</li>
      <li>FAISS (for vector storage)</li>
    </ul>
  </li>
  <li><strong>Deployment:</strong>
    <ul>
      <li>Docker</li>
    </ul>
  </li>
</ul>

<h2>Project Structure & Role of Each File</h2>

<h3>File Roles:</h3>
<ul>
  <li><strong><code>api.py</code>:</strong> The entry point for all API communication. It handles HTTP requests, calls the appropriate logic, and sends back JSON responses.</li>
  <li><strong><code>rag.py</code>:</strong> The "brain" of the operation. It orchestrates the process of turning a question into an answer using the vector database and the LLM.</li>
  <li><strong><code>TextProcessor.py</code>:</strong> The data converter. Its job is to ensure that no matter the source, all knowledge is standardized into plain text for the RAG model to use.</li>
  <li><strong><code>uploadValidification.py</code>:</strong> A utility script that acts as a gatekeeper, identifying input types to ensure they can be processed correctly.</li>
  <li><strong><code>main.py</code>:</strong> A developer utility for quick, isolated testing of the <code>RAG</code> function without needing to run the web server. It is not used in the production flow.</li>
  <li><strong><code>Dockerfile</code>:</strong> Provides a blueprint for creating a portable, self-contained environment for the application, ensuring it runs consistently anywhere.</li>
  <li><strong><code>requirementsrag.txt</code>:</strong> Lists all the Python libraries the backend needs to function.</li>
  <li><strong><code>output.txt</code>:</strong> The consolidated knowledge base in plain text format.</li>
  <li><strong><code>faiss_index/</code>:</strong> A pre-computed numerical representation of the knowledge base, allowing for extremely fast similarity searches.</li>
</ul>

<h2>Setup and Installation</h2>

<h3>Prerequisites</h3>
<ul>
  <li>Python 3.8+ and pip</li>
  <li>Docker (optional, for containerized deployment)</li>
</ul>

<h3>Backend Setup</h3>

<ol>
  <li><strong>Clone the repository:</strong>
    <pre><code>git clone https://github.com/Abhijeet673/Retrieval-Augmented-Generation-RAG-.git
cd &lt;repository-folder&gt;
</code></pre>
  </li>

  <li><strong>Create a virtual environment:</strong>
    <pre><code>python -m venv venv
source venv/bin/activate  # On Windows, use venv\Scripts\activate
</code></pre>
  </li>

  <li><strong>Install Python dependencies:</strong>
    <pre><code>pip install -r requirementsrag.txt</code></pre>
  </li>

  <li><strong>Create a <code>.env</code> file</strong> in the root directory and add your Google API key:
    <pre><code>GOOGLE_API_KEY="YOUR_API_KEY_HERE"</code></pre>
  </li>

  <li><strong>Run the Flask server:</strong>
    <pre><code>python api.py</code></pre>
    <p>The backend will be running at <code>http://localhost:5000</code>.</p>
  </li>
</ol>

</body>
</html>
