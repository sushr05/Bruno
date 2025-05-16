# Bruno
Bruno is a powerful Retrieval-Augmented Generation (RAG) chatbot inspired by Anthropic’s Contextual Retrieval architecture. It combines semantic search with BM25 sparse retrieval and contextual chunking to deliver accurate and relevant answers over unstructured documents such as PDFs, DOCX, and TXT files.

![image](https://github.com/user-attachments/assets/c3b82322-3725-4136-a554-bbde08ac02d5)

# Key Features
- Ensemble retrieval combining semantic search and BM25 for better recall
- Contextual chunking of documents using LLMs for meaningful splits
- Session-aware chat memory with LangGraph for conversation continuity
- Support for multiple file formats: PDF, DOCX, TXT
- Streaming token-by-token chatbot responses powered by Groq-hosted LLaMA 3
- Low latency and scalable RAG architecture

# Working of Contextual Retrieval RAG in Bruno

## Upon file upload
- **Parsing text from PDF**
  - Uses PyPDFLoader from LangChain to extract text from each page of a provided PDF file, combining it into a single string.
- **Splitting the text into chunks**
  - Uses `RecursiveCharacterTextSplitter` to split into chunks.
- **Adding context to chunks**
  - Runs inference on LLM for each chunk by asking it to modify the chunk to contain context with respect to the entire document. This step is skipped if the document is too big.
- **Creating TF-IDF encodings**
  - Created TF-IDF encodings implicitly while initializing BM25 retriever with new chunks.
- **Creating vector embeddings**
  - Creates vector embeddings for the new chunks using an embedding model from sentence-transformers on HuggingFace.
- **Store vector embeddings in Pinecone**
  - Stores vector embeddings in Pinecone vector database and initializes vector retriever for it.
- **Define ensemble retriever**
  - Creates an ensemble retriever with BM25 retriever and Pinecone vector retriever, implicitly performs rerank and deduplication.

## Inference from RAG pipeline
- **History-based prompt modification**
  - Modifies prompt based on previous chat messages to preserve context.
- **Retrieving chunks using similarity and keyword search**
  - Uses the new prompt to perform similarity search on vector database and retrieve the most relevant chunks.
  - Likewise, uses the prompt to retrieve chunks with the BM25 retriever.
- **Rerank and deduplicate**
  - Ensemble retriever implicitly reranks all retrieved chunks and removes duplicates.
- **LLM prompting after augmentation**
  - LLaMA 3 8B is prompted with the history-based prompt and context from the PDF (most relevant modified chunk).
- **Streaming result and storing to chat history**
  - The resulting answer is streamed, and the original prompt and the answer are appended to the temporary chat history.

  ![image](https://github.com/user-attachments/assets/7ba54316-cd51-4abf-b27c-3d2ceeb5385c)

# Specifications
- **LLM:** LLaMA 3-8B  
- **LLM Hosting:** Groq  
- **Parser:** PyPDFLoader from LangChain  
- **Chunking:** Contextual chunking  
- **Embedding model:** `sentence-transformers/all-mpnet-base-v2` from HuggingFace  
- **Retriever:** Ensemble retriever (BM25 Retriever, vector DB retriever), implicit reranking  
- **Vector database:** Pinecone




![image](https://github.com/user-attachments/assets/6d3be904-01d8-4ec8-807d-f34f3d25967b)




