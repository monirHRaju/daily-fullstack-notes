# Vector Databases for AI Applications

## Overview
Vector databases are specialized databases designed to store, index, and query high-dimensional vector embeddings efficiently. They enable similarity search, which is fundamental for applications like semantic search, recommendation systems, retrieval-augmented generation (RAG), and anomaly detection. Unlike traditional relational databases that excel at exact matches and scalar comparisons, vector databases use approximate nearest neighbor (ANN) algorithms to find vectors that are close in vector space, measured by distance metrics such as cosine similarity, Euclidean distance, or dot product.

## Why It Matters
As AI models (especially large language models) generate rich embeddings for text, images, audio, and other modalities, the need to store and retrieve these embeddings at scale becomes critical. Vector databases provide:
- **Low-latency similarity search** across millions or billions of vectors.
- **Scalability** to handle growing embedding datasets.
- **Integration** with ML pipelines and orchestration tools.
- **Support for hybrid queries** combining vector similarity with traditional filters.

Without vector databases, building efficient AI-powered features would require brute-force comparisons or building custom ANN indexes, which are complex and error-prone.

## Real-world Usage
- **Semantic Search**: Powering search engines that understand user intent beyond keyword matching (e.g., searching for "affordable vacation spots" returns results about budget travel even if those exact words aren’t present).
- **Recommendation Systems**: Finding items similar to a user’s preferences by comparing embedding vectors of items and user profiles.
- **Retrieval-Augmented Generation (RAG)**: Enhancing LLMs with up-to-date or proprietary knowledge by retrieving relevant document chunks from a vector store before generation.
- **Anomaly Detection**: Identifying outliers by flagging vectors that are far from their neighbors in embedding space.
- **Computer Vision**: Searching for visually similar images or detecting duplicate content.

## Code Example
Below is a simplified example using the `pgvector` extension for PostgreSQL to store and query embeddings for a text search application.

```sql
-- Enable the pgvector extension (run once)
CREATE EXTENSION IF NOT EXISTS vector;

-- Create a table to store documents and their embeddings
CREATE TABLE documents (
    id SERIAL PRIMARY KEY,
    content TEXT,
    embedding vector(384) -- dimension depends on the embedding model, e.g., 384 for all-MiniLM-L6-v2
);

-- Insert a document with its embedding (assuming we have a 384-dim vector from a model)
INSERT INTO documents (content, embedding)
VALUES (
    'The quick brown fox jumps over the lazy dog',
    '[0.1, 0.2, 0.3, ...]'::vector -- 384 floats
);

-- Query for the top 3 most similar documents to a given embedding
SELECT id, content, 1 - (embedding <=> '[0.15, 0.25, 0.35, ...]'::vector) AS cosine_similarity
FROM documents
ORDER BY embedding <=> '[0.15, 0.25, 0.35, ...]'::vector
LIMIT 3;
```

In a Node.js application using the `pg` library:

```javascript
const { Pool } = require('pg');
const pool = new Pool({ connectionString: process.env.DATABASE_URL });

async function searchSimilarDocuments(queryEmbedding, limit = 3) {
    const res = await pool.query(
        `SELECT id, content, 1 - (embedding <=> $1::vector) AS cosine_similarity
         FROM documents
         ORDER BY embedding <=> $1::vector
         LIMIT $2`,
        [queryEmbedding, limit]
    );
    return res.rows;
}
```

## Common Mistakes
- **Choosing the wrong distance metric**: Using Euclidean distance when cosine similarity is more appropriate for normalized embeddings can degrade search quality.
- **Ignoring quantization/index tuning**: Failing to tune index parameters (e.g., `lists` for IVF, `M` for HNSW) leads to poor recall or excessive latency.
- **Storing high-dimensional vectors without dimensionality reduction**: Very high dimensions (e.g., 1536 from OpenAI Ada) increase storage and query cost; consider PCA or product quantization if needed.
- **Neglecting metadata filtering**: Combining vector search with scalar filters (e.g., date, category) requires hybrid indexes; some vector databases support this natively, others need post‑filtering which can reduce recall.
- **Not updating embeddings when models change**: Embeddings are model‑specific; switching embedding models requires re‑embedding all data or using model‑agnostic techniques.

## Interview Question
**Question**: How would you design a system to find the top‑k similar products to a user‑viewed item in an e‑commerce catalog using embeddings, and what trade‑offs would you consider when selecting a vector database?

**Answer Outline**:
1. **Embedding Generation**: Use a pretrained model (e.g., a transformer fine‑tuned on product data) to convert product images, titles, and descriptions into a unified embedding vector.
2. **Storage & Indexing**: Store vectors in a vector database that supports ANN search (e.g., Milvus, Pinecone, Weaviate, or pgvector). Choose an index type (HNSW for low latency, IVF for memory efficiency) based on QPS and dataset size.
3. **Query Pipeline**: When a user views a product, compute its embedding and query the vector store for the nearest neighbors.
4. **Hybrid Filtering**: Apply category, price, or availability filters either via the vector database’s metadata filtering or via post‑processing.
5. **Trade‑offs**:
   - **Latency vs. Recall**: HNSW offers low latency with high recall but uses more memory; IVF can be tuned for lower memory at the cost of slightly higher latency.
   - **Consistency**: Some vector databases are eventually consistent; for real‑time updates, ensure the DB supports strong consistency or use a write‑through cache.
   - **Cost**: Managed services (Pinecone, Weaviate Cloud) reduce operational overhead but may be pricier than self‑hosted solutions.
   - **Scalability**: Sharding and replication strategies vary; pick a solution that scales horizontally with your expected growth.
6. **Evaluation**: Use metrics like recall@k, query latency, and throughput to benchmark options under realistic loads.

## Key Takeaways
- Vector databases turn unstructured AI embeddings into searchable assets.
- They enable semantic search, recommendations, RAG, and more at scale.
- Choose the right distance metric and index type for your data and query patterns.
- Combine vector similarity with metadata filtering for practical applications.
- Monitor latency, recall, and cost when evaluating vector database options.