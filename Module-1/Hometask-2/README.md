# Homework 2 – Retrieval-Augmented Generation (RAG)

## Overview

This assignment explores how to build, evaluate, and improve a Retrieval-Augmented Generation (RAG) system using financial question answering.

The objective is to provide an LLM with relevant information retrieved from financial reports, enabling it to answer questions that require specific information beyond its pretrained knowledge.

The assignment covers three main components:

1. **Naive generation:** Using Meta-Llama-3.3-70B-Instruct to answer financial questions without external context.
2. **RAG implementation:** Building a document retrieval pipeline using financial reports, embeddings, and a FAISS vector store.
3. **RAG evaluation and improvement:** Measuring answer correctness, faithfulness, and retrieval performance, followed by experiments to improve the pipeline.

**## Technologies and Libraries**

- **Python:** Main programming language.
- **Pandas:** Dataset loading, manipulation, and result analysis.
- **Hugging Face Datasets:** Loading the FinanceBench dataset.
- **LangChain:** PDF document loading, text splitting, embeddings, and vector store integration.
- **PyPDFLoader:** Extracting text and metadata from PDF documents.
- **Sentence Transformers:** Generating embeddings and reranking retrieved chunks.
- **FAISS:** Vector database for efficient similarity search.
- **Ragas:** Evaluating answer faithfulness.
- **OpenAI Python SDK:** API communication with Nebius Token Factory.
- **AsyncIO:** Concurrent API calls for LLM-based evaluation.
- **OpenPyXL:** Excel export.
- **Nebius Token Factory:** Hosted LLM inference.

**## Models**

| Role | Model |
|---|---|
| Naive generation and RAG | Meta-Llama-3.3-70B-Instruct |
| Embedding model | BAAI/bge-small-en-v1.5 |
| Reranker | BAAI/bge-reranker-base |
| LLM evaluator | DeepSeek-V3.2 |

The LLMs are accessed through the Nebius Token Factory API using the OpenAI-compatible Python SDK. The embedding and reranking models are loaded through Hugging Face and Sentence Transformers.

**## Workflow**

**### 1. Naive generation baseline**

The FinanceBench dataset is loaded and filtered to exclude metrics-generated questions.

Five questions are selected from each remaining question type.

For each question:
1. Construct a system prompt defining the financial analyst's role and behavioral constraints.
2. Send the question directly to Llama 3.3 70B without external context.
3. Store the generated answer alongside the ground-truth answer.

This establishes a baseline for evaluating the model's ability to answer financial questions using its pretrained knowledge alone.

**### 2. Understand the RAG pipeline**

The three main stages of RAG are examined:

- **Indexing:** Converting raw documents into searchable numerical representations.
- **Retrieval:** Searching the vector store for relevant information based on the user's question.
- **Generation:** Producing an answer using the retrieved context and the original question.

Potential failure points at each stage are considered.

**### 3. Build the document index**

Financial reports are downloaded from the FinanceBench GitHub repository.

The documents are processed through the following steps:

1. **PDF extraction:** PyPDFLoader extracts text from each PDF, creating a separate Document object for each page.
2. **Metadata enrichment:** Each page is associated with its document name, company, reporting period, and page number.
3. **Text chunking:** RecursiveCharacterTextSplitter divides the documents into chunks of 1,000 characters with 150-character overlap.
4. **Embedding generation:** BAAI/bge-small-en-v1.5 converts each text chunk into a numerical vector. Embeddings are normalized.
5. **Vector storage:** FAISS stores the embeddings together with their corresponding text and metadata.

The resulting vector store is saved locally for subsequent retrieval.

**### 4. Implement RAG**

The FAISS index is loaded and used to retrieve the top-k most similar chunks for each question.

The retrieved chunks are formatted with their document names and page numbers and passed to Llama 3.3 70B as context.

The system prompt instructs the model to:
- Answer only using the retrieved context.
- Refuse to answer when the context does not contain sufficient information.
- Avoid unsupported assumptions and outside knowledge.
- Cite factual claims using document names and page numbers.

**### 5. Compare naive generation and RAG**

The same ten questions used in the naive generation baseline are evaluated using RAG.

The generated answers are compared against the naive answers and ground-truth answers to assess whether retrieval improves performance, particularly for novel questions requiring information from financial reports.

**### 6. Evaluate RAG performance**

The RAG pipeline is evaluated across three dimensions:

- **Correctness:** An LLM-as-a-Judge (DeepSeek V3.2) compares generated answers against ground-truth answers and classifies them as correct or incorrect.
- **Faithfulness:** Ragas evaluates whether the generated answer is supported by the retrieved context. This metric is calculated on a subset of 20 questions.
- **Retrieval performance:** Page hit rate measures whether at least one of the retrieved chunks comes from a page identified by FinanceBench as containing evidence. Evaluated at k=1, 3, and 5.

**### 7. Improvement Cycles**

Three experiments explore ways to improve RAG performance.

**\*\*Experiment 1 – Stricter prompting\*\***

Introduces explicit instructions to extract exact figures, show calculation steps, and cite every factual claim. The objective is to improve numerical correctness and grounding.

**\*\*Experiment 2 – Increasing retrieval size\*\***

Increases the number of retrieved chunks from k=5 to k=8, testing whether providing more context improves answer correctness and faithfulness.

**\*\*Experiment 3 – Reranking\*\***

Introduces a cross-encoder reranker. FAISS first retrieves the top 20 candidate chunks, which are then scored by BAAI/bge-reranker-base. The top 4 reranked chunks are passed to the LLM.

The objective is to improve retrieval relevance by evaluating the question and each candidate chunk together.

**## Key Machine Learning Concepts**

| Concept | What it covers |
|---|---|
| Retrieval-Augmented Generation (RAG) | Combining information retrieval with LLM generation |
| Embeddings | Representing text as numerical vectors |
| Semantic similarity | Measuring similarity between text representations |
| Vector databases | Efficient storage and retrieval of embeddings |
| Chunking | Splitting documents into smaller text segments |
| Cosine similarity | Measuring similarity between embedding vectors |
| Metadata filtering | Using document attributes to constrain retrieval |
| FAISS | Efficient similarity search over vector representations |
| Cross-encoder reranking | Scoring query-document pairs to improve retrieval ordering |
| Hallucination | Generation of unsupported or incorrect information |
| LLM-as-a-Judge | Using an LLM to evaluate another LLM's output |
| Faithfulness | Measuring whether generated claims are supported by retrieved context |
| Retrieval evaluation | Measuring whether relevant evidence is retrieved |
| Async programming | Running concurrent API requests to improve execution efficiency |

**## Main Takeaways**

- RAG enables LLMs to answer questions using external, domain-specific information rather than relying solely on pretrained knowledge.
- RAG performance depends on every stage of the pipeline, including document processing, chunking, embedding generation, retrieval, and generation.
- Retrieval quality directly influences the context available to the LLM, but retrieving relevant information does not guarantee a correct answer.
- More retrieved context can improve answer correctness and faithfulness when relevant information is otherwise missed.
- Stricter grounding instructions can improve faithfulness but may also make the model more conservative.
- Reranking can improve answer quality without necessarily improving retrieval of the dataset's designated evidence pages.
- Evaluating correctness, faithfulness, and retrieval performance separately provides a more complete understanding of RAG system behavior.
- Metadata-filtered retrieval is a potential improvement when questions are associated with known companies and reporting periods.
