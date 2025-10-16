# micro_rag_mcp

**Description:** Minimal local RAG MCP server for document folders, invoked via uvx.

**Invocation:** `uvx micro-rag-mcp --data-folder /path/to/docs [--index-folder /path/to/index] [--exts .txt,.md,.pdf,.docx]`

**Arguments:**
- `--data-folder`: Required. Path to folder containing documents to index.
- `--index-folder`: Optional. Path to store FAISS index and metadata. Defaults to `<data-folder>/index`.
- `--exts`: Optional. Comma-separated file extensions. Defaults to `.txt,.md,.pdf,.docx`.
- `--rebuild-threshold`: Optional. Not implemented yet.

**Behavior:**
- Starts MCP stdio server with two tools: `search` and `reindex`.
- On startup, scans data folder and applies incremental reindexing based on file mtime and checksums.
- Stores persistent index under index folder.
- Logs to stderr; tool responses are structured JSON.

**Tools:**
- `search(query, top_k=5, score_threshold=0.2)`: Returns list of matching chunks with snippets.
- `reindex(force=False, path_glob=None)`: Updates index incrementally, returns stats.

**Example MCP config:**
```json
{
  "mcpServers": {
    "hr_rag": {
      "command": "uvx",
      "args": ["micro-rag-mcp", "--data-folder", "/some/hr_docs"]
    }
  }
}
```

**Dependencies:** mcp, sentence-transformers, faiss-cpu, numpy, pypdf, python-docx, pydantic, charset-normalizer