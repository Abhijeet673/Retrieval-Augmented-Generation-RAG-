\# AI-Powered RAG API Service



This project provides a backend API service for a Retrieval-Augmented Generation (RAG) system. It leverages Google's Gemini models and LangChain to answer questions based on a dynamic, ingestible knowledge base. This service can power any chatbot, application, or script that needs to query a specific set of documents.



\## Core Features



\* \*\*Intelligent RAG Backend:\*\* A Python-based service using Flask that leverages Google's Gemini models to understand and answer user queries.

\* \*\*Dynamic Knowledge Base:\*\* Exposes API endpoints to ingest and process various document types (`.pdf`, `.docx`, `.txt`, `.json`) and URLs, allowing the knowledge base to be updated dynamically.

\* \*\*Vector Search:\*\* Utilizes FAISS (Facebook AI Similarity Search) for efficient and fast retrieval of relevant context from the knowledge base.

\* \*\*Containerized:\*\* Includes a `Dockerfile` for easy, consistent deployment.



\## How It Works (Mechanism)



The project operates on two primary workflows, both accessible via API: \*\*Data Ingestion\*\* and \*\*Querying\*\*.



\### 1. Data Ingestion Workflow (Learning)



This workflow is for feeding new information into the system.



1\.  \*\*API Call:\*\* An administrator sends a file (`.pdf`, `.docx`, etc.) or a URL to a specific backend endpoint (`/ingest\_file` or `/ingest\_url`).

2\.  \*\*Validation:\*\* The backend first identifies the type of input using `uploadValidification.py`.

3\.  \*\*Processing:\*\* `TextProcessor.py` then converts the input into clean, plain text. For example, it extracts text from a PDF or scrapes it from a URL.

4\.  \*\*Storage:\*\* The extracted text is saved to a central file (`output.txt`).

5\.  \*\*Indexing:\*\* The next time a query is made, the `rag.py` module detects the new information in `output.txt`, creates vector embeddings, and updates the FAISS vector database (`faiss\_index`).



\### 2. Querying Workflow (Answering)



This is the main workflow for asking questions.



1\.  \*\*API Call:\*\* A client application makes a `POST` request to the `/rag` endpoint on the Flask server, sending the user's question in the request body.

2\.  \*\*Retrieval:\*\* The `rag.py` module on the backend receives the question. It first converts the question into a vector embedding and uses FAISS to retrieve the most relevant text chunks from its vector database.

3\.  \*\*Generation:\*\* The retrieved text chunks (the "context") and the original user question are sent to the Google Gemini LLM.

4\.  \*\*Response:\*\* The LLM generates a coherent, human-like answer based on the provided context. This answer is sent back to the client as a JSON response.



\## Tech Stack



\* \*\*Backend:\*\*

&nbsp;   \* Python

&nbsp;   \* Flask

&nbsp;   \* LangChain

&nbsp;   \* Google Gemini (via `langchain-google-genai`)

&nbsp;   \* FAISS (for vector storage)

\* \*\*Deployment:\*\*

&nbsp;   \* Docker



\## Project Structure \& Role of Each File



\### File Roles:



\* \*\*`api.py`\*\*: The entry point for all API communication. It handles HTTP requests, calls the appropriate logic, and sends back JSON responses.

\* \*\*`rag.py`\*\*: The "brain" of the operation. It orchestrates the process of turning a question into an answer using the vector database and the LLM.

\* \*\*`TextProcessor.py`\*\*: The data converter. Its job is to ensure that no matter the source, all knowledge is standardized into plain text for the RAG model to use.

\* \*\*`uploadValidification.py`\*\*: A utility script that acts as a gatekeeper, identifying input types to ensure they can be processed correctly.

\* \*\*`main.py`\*\*: A developer utility for quick, isolated testing of the `RAG` function without needing to run the web server. It is not used in the production flow.

\* \*\*`Dockerfile`\*\*: Provides a blueprint for creating a portable, self-contained environment for the application, ensuring it runs consistently anywhere.

\* \*\*`requirementsrag.txt`\*\*: Lists all the Python libraries the backend needs to function.

\* \*\*`output.txt`\*\*: The consolidated knowledge base in plain text format.

\* \*\*`faiss\_index/`\*\*: A pre-computed numerical representation of the knowledge base, allowing for extremely fast similarity searches.



\## Setup and Installation



\### Prerequisites



\* Python 3.8+ and pip

\* Docker (optional, for containerized deployment)



\### Backend Setup



1\.  \*\*Clone the repository:\*\*

&nbsp;   ```bash

&nbsp;   git clone <https://github.com/Abhijeet673/Retrieval-Augmented-Generation-RAG-.git>

&nbsp;   cd <repository-folder>

&nbsp;   ```



2\.  \*\*Create a virtual environment:\*\*

&nbsp;   ```bash

&nbsp;   python -m venv venv

&nbsp;   source venv/bin/activate  # On Windows, use `venv\\Scripts\\activate`

&nbsp;   ```



3\.  \*\*Install Python dependencies:\*\*

&nbsp;   ```bash

&nbsp;   pip install -r requirementsrag.txt

&nbsp;   ```



4\.  \*\*Create a `.env` file\*\* in the root directory and add your Google API key:

&nbsp;   ```

&nbsp;   GOOGLE\_API\_KEY="YOUR\_API\_KEY\_HERE"

&nbsp;   ```



5\.  \*\*Run the Flask server:\*\*

&nbsp;   ```bash

&nbsp;   python api.py

&nbsp;   ```

&nbsp;   The backend will be running at `http://localhost:5000`.



