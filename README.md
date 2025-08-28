# Neo4j Graph RAG

🚧 **Work in Progress** 🚧

> **Note**: This project is currently under active development. Features and documentation may change frequently.

A Graph RAG application using Neo4j for enhanced retrieval-augmented generation.

## Setup

1. Install dependencies:
```bash
uv sync
```

2. Run the application:
```bash
python main.py
```

## Neo4j MCP Integration

This project integrates with Neo4j MCP servers for standardized database operations via VS Code.

### MCP Servers Configured

- `mcp-neo4j-cypher@0.2.3` - Execute Cypher queries
- `mcp-neo4j-data-modeling@0.1.1` - Graph modeling assistance

### Usage in VS Code

1. Install Claude Dev extension
2. Configure MCP servers (see `MCP_CONFIGURATION.md`)
3. Use in chat:

```
@neo4j-mcp MATCH (n) RETURN count(n)
@neo4j-graph-data-modeling Help design an e-commerce schema
```

## Dependencies

- `neo4j-graphrag[ollama]>=1.9.1` - Core Graph RAG functionality