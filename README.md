# Week 7: RAG Security Knowledge Assistant — Evaluation Report

## 1. Setup Summary

- **Capstone project:** AI-Capstone-Threat-Intel
- **Capstone component:** Feed Collector
- **Chatflow name:** Security Knowledge Assistant
- **LLM:** llama-3.3-70b-versatile via Groq
- **Temperature:** 0.3 for the final configuration
- **Embeddings:** sentence-transformers/all-MiniLM-L6-v2 via HuggingFace Inference Embeddings
- **Vector Store:** In-Memory Vector Store
- **Text Splitter:** Recursive Character Text Splitter with chunk size 1000 and chunk overlap 200
- **Return Source Documents:** Enabled
- **Flowise chat link:** https://cloud.flowiseai.com/chatbot/c382ce2c-89b3-44d7-a1ad-b11b9ac0bcbb

### Documents Loaded

I used four Feed Collector knowledge-base documents instead of the default MITRE/NIST documents because my capstone component is the Feed Collector for AI-Capstone-Threat-Intel.

| # | Document | Approx. Length | Purpose |
|---|----------|----------------|---------|
| 1 | feed-collector-overview-and-scope.txt | 1+ page | Defines the Feed Collector's purpose, scope, intake role, source traceability, and relationship to the larger threat intelligence system. |
| 2 | feed-ingestion-normalization-deduplication.txt | 1+ page | Explains how feed items should be ingested, normalized into shared fields, and deduplicated across public sources. |
| 3 | source-reliability-scoring-and-prioritization.txt | 1+ page | Describes how source reliability, indicator confidence, severity, exploitability, recency, and prioritization should be handled. |
| 4 | operational-security-error-handling-and-governance.txt | 1+ page | Covers operational safeguards such as rate limits, retry behavior, error handling, repeated runs, logging, and governance. |

## 2. Test Results

| # | Question | Used Documents? | Quality | Notes |
|---|----------|----------------|---------|-------|
| 1 | What is the main purpose of the Feed Collector in AI-Capstone-Threat-Intel? | Yes | Good | The assistant explained that the Feed Collector gathers threat intelligence from public sources, preserves useful source context, and prepares raw findings for later analysis and prioritization. This matched the purpose of my capstone component. |
| 2 | What metadata should the Feed Collector preserve for each collected threat intelligence item? | Yes | Good | The assistant listed source name, source URL, retrieval time, original title, publication date when available, and author or organization. This showed that the chatbot was using the uploaded Feed Collector knowledge base instead of giving a generic answer. |
| 3 | How should the Feed Collector normalize and deduplicate records from different public sources? | Yes | Good | The assistant described a shared normalized record structure, including fields such as source name, source URL, source type, collection time, publication time, title, item URL, raw text, extracted entities, content hash, and collection status. It also explained deduplication using content hashes, normalized URLs, CVE/product/date combinations, and indicator-based keys. |
| 4 | What factors should affect source reliability, indicator confidence, and prioritization? | Yes | Good | The assistant gave a structured answer covering official vendor advisories, government cybersecurity agencies, vulnerability databases, known research organizations, corroboration by multiple sources, evidence quality, age of the indicator, severity, exploitability, recency, confidence, and signs of active exploitation. |
| 5 | How should the Feed Collector handle rate limits, source failures, credentials, and repeated collection runs? | Yes | Partial | The assistant answered the parts about rate limits, source failures, and repeated collection runs well. It mentioned backoff and retry logic, logging errors, and idempotent retries using timestamps, hashes, source identifiers, normalized URLs, and unique keys. However, it said it was not sure about credentials because the retrieved context did not include enough information about credential handling. I rated this answer Partial because most of the answer was useful, but one part of the question was not fully supported by the documents. |

## 3. Edge Case Observations

- **Unrelated question:** I asked, “What is the weather like today?” The chatbot correctly responded that it was not sure based on the uploaded Feed Collector documents because the context did not provide weather information. This was a good result because the assistant did not try to answer an unrelated question from general knowledge.

- **Out-of-scope topic:** I asked, “Who do you think will win the World Cup?” The chatbot correctly responded that it was not sure based on the uploaded Feed Collector documents because the context did not provide information about the World Cup or sports. This showed that the RAG setup was constraining the answer to the uploaded knowledge base.

- **Topic adjacent to cybersecurity but not covered in the uploaded documents:** I asked, “What is MITRE ATT&CK?” The chatbot did not give a general explanation. Instead, it said the Feed Collector documents only mentioned ATT&CK technique references as part of extracted entities and did not provide enough information about what MITRE ATT&CK is. This was a good edge-case result because the assistant avoided making up information outside of the uploaded documents.

## 4. Settings Experiments

- **Temperature change:** I tested the same question at temperature 0.3 and temperature 0.7. At temperature 0.3, the chatbot gave a clearer and more direct answer, explaining that the Feed Collector creates a clean intake layer for the broader threat intelligence system and supports later retrieval, summarization, scoring, and alerting. At temperature 0.7, the answer became less consistent and less confident. It described some functions of the Feed Collector, but then said it was not sure about the main purpose. Based on this test, I kept temperature at 0.3 because it produced more stable and factual answers for a security knowledge assistant.

- **Chunk size change:** I did not change the chunk size for my final version. I kept the Recursive Character Text Splitter at chunk size 1000 and chunk overlap 200 because the default settings produced useful answers with enough context.

- **Top K change:** I did not change Top K for my final version. I left the vector store retrieval behavior at the default setting because the chatbot was already retrieving relevant document chunks for the Feed Collector questions.

## 5. Reflection

What surprised me about RAG is that the chatbot does not simply answer from the model's general knowledge. Instead, it retrieves relevant chunks from the uploaded documents and uses those chunks as the basis for the response. This was clear when the chatbot refused to answer questions about weather, sports, and MITRE ATT&CK beyond what was actually included in the Feed Collector documents.

I could improve this chatbot for real-world use by replacing the in-memory vector store with a persistent vector database, expanding the knowledge base with more complete source documentation, adding a live update process for public threat feeds, and including stronger guidance about credentials and secrets management. I would also improve the evaluation process by testing more edge cases and comparing the answers against known expected responses.

For my capstone project, RAG could be useful because the Feed Collector will need clear procedures for collecting, normalizing, deduplicating, and prioritizing public threat intelligence. A RAG assistant could help explain how feed items should be handled, why certain sources are more reliable, what metadata must be preserved, and how repeated collection runs should avoid creating duplicate records. This connects directly to AI-Capstone-Threat-Intel because the overall project depends on collecting, analyzing, and prioritizing cybersecurity threats from public sources.
