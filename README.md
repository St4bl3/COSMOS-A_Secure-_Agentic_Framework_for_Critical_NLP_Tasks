# COSMOS-NLP: A Secure, Agentic Framework for Critical NLP Tasks

COSMOS-NLP is a sophisticated, multi-agent system designed to handle a variety of critical Natural Language Processing tasks with a strong emphasis on security. The framework leverages a central orchestrator to delegate tasks to specialized agents, each an expert in its domain, ranging from generating Standard Operating Procedures (SOPs) for disaster response to creating secure system commands for space debris management. A key feature of COSMOS-NLP is its implementation of Post-Quantum Cryptography (PQC) to ensure secure inter-agent communication, making it suitable for sensitive and mission-critical applications.

---

## 🚀 Features

* **Multi-Agent Architecture:** A central orchestrator routes tasks to a suite of specialized agents, including:
    * `SOPAgent`: Generates Standard Operating Procedures for critical events.
    * `CodeGenAgent`: Creates code snippets in various programming languages.
    * `DisasterAgent`: Provides comprehensive analysis of disaster events.
    * `SystemCmdAgent`: Generates secure system commands (e.g., for satellite maneuvers).
    * `AsteroidAgent`: Offers detailed threat assessments for space objects.
    * `GroundCmdAgent`: Creates commands for ground control systems.
* **Retrieval-Augmented Generation (RAG):** Enhances LLM responses by retrieving relevant context from a vector database, ensuring more accurate and informed outputs.
* **Post-Quantum Cryptography (PQC):** Implements state-of-the-art PQC for secure inter-agent communication, using a KEM (Kyber) + DEM (AES-GCM) scheme and Dilithium for digital signatures.
* **Flexible Data Ingestion:** Supports various data formats including JSON, raw text, and files (PDF, TXT, EPUB) via local paths or URLs.
* **Task Memory and Context:** Maintains a history of tasks and leverages it to enrich the context for current requests, enabling more coherent and informed agent responses.
* **Async and Scalable:** Built with FastAPI and asynchronous processing to handle multiple requests efficiently.

---

## 🛠️ System Architecture

The COSMOS-NLP system is orchestrated by the `OrchestrationAgent`. Here's a high-level overview of the workflow:

1.  **Data Ingestion:** The system receives data through a FastAPI endpoint, which can be in various formats. The `DataParser` processes this input into a standardized `OrchestratorInput` model.

2.  **Orchestration:** The `OrchestrationAgent` analyzes the input and determines which specialized agent(s) are required for the task. It enriches the context by searching for related past tasks in the `TaskMemory` module.

3.  **Secure Communication:** When PQC is enabled, the orchestrator uses the `SecureCommunicator` to encrypt and sign the payload, which is then sent to the appropriate agent in a `SecureEnvelope`.

4.  **Task Execution:** The specialized agent receives the task. If the payload is encrypted, it's first decrypted. The agent then processes the request, potentially using the `RAGModule` to retrieve additional context from the vector database to generate a more accurate response.

5.  **Response:** The agent's output is securely packed into a `SecureEnvelope` and sent back to the orchestrator, which then returns the final result.

---

## 💻 Technologies Used

* **Backend:** FastAPI, Uvicorn
* **LLM:** Groq (configurable, e.g., `llama3-70b-8192`)
* **Embeddings:** SentenceTransformers (e.g., `all-MiniLM-L6-v2`) or OpenAI
* **Vector Database:** ChromaDB or Pinecone
* **Task Memory:** SQLite
* **PQC Libraries:** `kyber-py`, `dilithium-py`, `pycryptodomex`
* **Data Handling:** Pydantic

---

## 🛡️ Security Features

COSMOS-NLP is designed with security as a primary concern. The `pqc_crypto_package` provides a suite of tools for Post-Quantum Cryptography:

* **Key Management:** The `KeyManager` generates and manages Kyber and Dilithium key pairs for each agent, stored in `agent_pqc_keys.json`.
* **Hybrid Encryption:** A hybrid encryption scheme is used for secure communication:
    * **KEM:** Kyber is used as the Key Encapsulation Mechanism to securely wrap a temporary AES key.
    * **DEM:** AES-GCM is used as the Data Encapsulation Mechanism to encrypt the actual payload with the temporary key.
* **Digital Signatures:** Dilithium is used to create digital signatures, ensuring the authenticity and integrity of messages exchanged between agents.

These security measures can be enabled or disabled via the `ENABLE_PQC_SECURITY` flag in the `config.py` file.

---

## 🚀 Getting Started

To get a local copy up and running, follow these simple steps.

### Prerequisites

* Python 3.8+
* Pip for package management

### Installation

1.  **Clone the repo**
    ```sh
    git clone [https://github.com/your_username/cosmos-nlp.git](https://github.com/your_username/cosmos-nlp.git)
    cd cosmos-nlp
    ```
2.  **Install Python packages**
    ```sh
    pip install -r requirements.txt # Make sure to create a requirements.txt file
    pip install -r pqc_crypto_package/requirements.txt
    ```
3.  **Set up environment variables**
    Create a `.env` file in the root directory and add the following:
    ```env
    GROQ_API_KEY="YOUR_GROQ_API_KEY"
    VECTOR_DB_PROVIDER="chromadb" # or "pinecone"
    PINECONE_API_KEY="YOUR_PINECONE_API_KEY" # if using pinecone
    PINECONE_ENVIRONMENT="YOUR_PINECONE_ENVIRONMENT" # if using pinecone
    ENABLE_PQC_SECURITY="true"
    ```
4.  **Generate PQC Keys**
    The system will automatically generate the `agent_pqc_keys.json` file on the first run if it doesn't exist.

5.  **Ingest RAG Documents**
    To populate the vector database for the RAG module, run the `ingest_rag_docs.py` script:
    ```sh
    python ingest_rag_docs.py --dir rag_documents/sops/pdf --doc_type sop
    ```

### Running the Application

```sh
uvicorn main:app --reload
