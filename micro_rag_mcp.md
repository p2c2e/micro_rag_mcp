# micro_rag_mcp

**Description:** Minimal local RAG MCP server for document folders, invoked via uvx.

**Invocation:** `uvx micro-rag-mcp --data-folder /path/to/docs --title "My Server" --inst "Instance description" [--index-folder /path/to/index] [--exts .txt,.md,.pdf,.docx]`

**Arguments:**
- `--data-folder`: Required. Path to folder containing documents to index.
- `--title`: Required. FastMCP server name (used for MCP server identity).
- `--inst`: Required. FastMCP instructions (passed verbatim, can be a sentence or multi-line).
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
    "team_alpha": {
      "command": "uvx",
      "args": [
        "micro-rag-mcp@0.1.4",
        "--data-folder", "/docs/teamA",
        "--title", "Secret Islands",
        "--inst", "Team Alpha read-only mode with nightly refresh"
      ]
    },
    "team_beta": {
      "command": "uvx",
      "args": [
        "micro-rag-mcp@0.1.4",
        "--data-folder", "/docs/teamB",
        "--title", "Secret Islands",
        "--inst", "Team Beta write-enabled with manual refresh"
      ]
    }
  }
}
```

**How to pass sentences with spaces or newlines in --inst:**
- Each item in the `args` array is a single argument; spaces and newlines are preserved as part of the string.
- For multi-line instructions, use `\n` inside the string:
  - `"--inst", "Team Alpha\nRead-only\nRefresh: 02:00 IST"`

**Dependencies:** mcp, sentence-transformers, faiss-cpu, numpy, pypdf, python-docx, pydantic, charset-normalizer