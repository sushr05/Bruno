# Bruno
Bruno is a powerful Retrieval-Augmented Generation (RAG) chatbot inspired by Anthropic’s Contextual Retrieval architecture. It combines semantic search with BM25 sparse retrieval and contextual chunking to deliver accurate and relevant answers over unstructured documents such as PDFs, DOCX, and TXT files.

# Key Features
- Ensemble retrieval combining semantic search and BM25 for better recall
- Contextual chunking of documents using LLMs for meaningful splits
- Session-aware chat memory with LangGraph for conversation continuity
- Support for multiple file formats: PDF, DOCX, TXT
- Streaming token-by-token chatbot responses powered by Groq-hosted LLaMA 3
- Low latency and scalable RAG architecture

# Working of Contextual Retrieval RAG in Bruno
# Upon file upload
- Parsing text from PDF
-Uses PyPDFLoader from langchain to extract text from each page of a provided PDF file, combining it into a single string.
- Splitting the text into chunks
-Uses RecursiveCharacterTextSplitter to split into chunks
- Adding context to chunks
-Run inference on llm for each chunk, by asking it to modify the chunk to contain context with respect to entire document. This step is skipped if document is too big.
- Creating TF-IDF encodings
-Created TF-IDF encodings implicitly while initialising BM25 retriever with new chunks.
- Creating vector embeddings
-Created vector embedding for the new chunks using embeddings model from sentence-transformer in HuggingFace.
- Store vector embeddings in Pinecone
-Stores vector embeddings in Pinecone vector database and intialises vector retriever for it.
- Define ensemble retriever
-Creates and ensemble retriever with BM25 retriever and Pinecone vector retriever, implicity perform Rerank and deduplication.
# Inference from RAG pipeline
- History based prompt modification
-Modifies prompt based on previous chat message to have their context.
- Retrieving chunks using similarity and key word search
Uses the new prompt to perform similarity search on vector database and retrieve most relevant chunk. Likewise, use the prompt to retrieve with BM25 retriever to get results.
- Rerank and deduplicate
-Ensemble retriever implicitly handles Rerank all the retrieved chunks and removing duplicates
- LLM prompting after augmentation
-Llama 3 8b is prompted with the new history based prompt and context from pdf(most relevant modified chunk).
- Streaming result and storing to chat history
-The resulting answer is streamed and the original prompt and the answer are appended to temporary chat history

# Specifications
-LLM: Llama3-8b
-LLM Hosting: Groq
-Parser: PyPDFLoader from LangChain
-Chunking: Contextual chunking
-Embedding model: sentence-transformers/all-mpnet-base-v2 from HuggingFace
-Retriever: Ensemble retriever (BM25 Retriever, vectordb retriever), implicit reranking
-Vector database: Pinecone



