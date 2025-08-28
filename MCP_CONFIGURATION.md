# Neo4j MCP Setup & Configuration Guide

Complete guide to set up Neo4j with APOC plugin and configure MCP servers in VS Code.

## 1. Install Neo4j with APOC

### Option A: Neo4j Desktop (Recommended)
1. Download [Neo4j Desktop](https://neo4j.com/download/)
2. Create a new database
3. Go to **Plugins** tab → Install **APOC**
4. Start the database
5. Set password (default user: `neo4j`)

### Option B: Docker
```bash
docker run \
    --name neo4j-apoc \
    -p7474:7474 -p7687:7687 \
    -d \
    -v $HOME/neo4j/data:/data \
    -v $HOME/neo4j/logs:/logs \
    --env NEO4J_AUTH=neo4j/password \
    --env NEO4J_PLUGINS='["apoc"]' \
    neo4j:latest
```

### Option C: Manual Installation
1. Download Neo4j Community Edition
2. Download APOC jar from [releases](https://github.com/neo4j-contrib/neo4j-apoc-procedures/releases)
3. Place APOC jar in `plugins/` directory
4. Add to `conf/neo4j.conf`:
```
dbms.security.procedures.unrestricted=apoc.*
dbms.security.procedures.allowlist=apoc.*
```
5. Start Neo4j: `bin/neo4j start`

## 2. Verify Installation

Access Neo4j Browser at `http://localhost:7474` and run:
```cypher
CALL apoc.help("apoc")
```

## 3. VS Code MCP Setup

### Install Claude Dev Extension
1. Install **Claude Dev** extension in VS Code
2. Configure with your Anthropic API key

## Configuration File: `.vscode/mcp.json`

```json
{
  "servers": {
    "neo4j-mcp": {
      "command": "uvx",
      "args": [ "mcp-neo4j-cypher@0.2.3"],
      "env": {
        "NEO4J_URI": "bolt://localhost:7687",
        "NEO4J_USER": "neo4j",
        "NEO4J_PASSWORD": "password"
      }
    },
    "neo4j-graph-data-modeling": {
      "command": "uvx",
      "args": [ "mcp-neo4j-data-modeling@0.1.1" ]
    }
  }
}
```

## Server Configurations

### 1. neo4j-mcp (Cypher Query Server)
- **Package**: `mcp-neo4j-cypher@0.2.3`
- **Purpose**: Execute Cypher queries and database operations
- **Environment Variables**:
  - `NEO4J_URI`: Database connection URI
  - `NEO4J_USER`: Database username
  - `NEO4J_PASSWORD`: Database password

**Available Tools**:
- Execute Cypher queries
- Database schema inspection
- Connection testing

### 2. neo4j-graph-data-modeling
- **Package**: `mcp-neo4j-data-modeling@0.1.1`
- **Purpose**: Graph data modeling assistance
- **Environment**: Uses default Neo4j connection

**Available Tools**:
- Graph modeling recommendations
- Schema design assistance
- Data modeling best practices

## Usage in VS Code

### Activate MCP Servers
1. Open Command Palette (`Cmd+Shift+P`)
2. Run: **Claude Dev: Restart MCP Servers**

### Use Neo4j Cypher Server
```
@neo4j-mcp MATCH (n) RETURN count(n) as total_nodes
```

### Use Data Modeling Server
```
@neo4j-graph-data-modeling Help me design a graph schema for an e-commerce system
```

## Environment Setup

### Prerequisites
- Neo4j database running on `localhost:7687`
- APOC plugin installed
- Claude Dev extension in VS Code

### Security Notes
- Password stored in plain text in config
- Consider using environment variables:

```json
{
  "servers": {
    "neo4j-mcp": {
      "command": "uvx",
      "args": [ "mcp-neo4j-cypher@0.2.3"],
      "env": {
        "NEO4J_URI": "${NEO4J_URI}",
        "NEO4J_USER": "${NEO4J_USER}",
        "NEO4J_PASSWORD": "${NEO4J_PASSWORD}"
      }
    }
  }
}
```

## Troubleshooting

### Server Not Starting
- Check if `uvx` is installed: `uvx --version`
- Verify package versions exist
- Check Neo4j database is running

### Connection Issues
- Verify Neo4j URI and credentials
- Test connection: `python connect_test.py`
- Check firewall/network settings

### Package Updates
```bash
# Update to latest versions
uvx install mcp-neo4j-cypher@latest
uvx install mcp-neo4j-data-modeling@latest
```

## Sample Queries

```cypher
-- Create sample data
CREATE (alice:Person {name: 'Alice', age: 30})
CREATE (bob:Person {name: 'Bob', age: 25})
CREATE (techcorp:Company {name: 'TechCorp'})
CREATE (alice)-[:WORKS_FOR]->(techcorp)
CREATE (bob)-[:WORKS_FOR]->(techcorp)
CREATE (alice)-[:KNOWS]->(bob)

-- Query with APOC
CALL apoc.meta.graph()

-- Schema overview
CALL apoc.meta.schema()
```

## Connection Testing

### APOC Verification
```cypher
-- Check APOC installation
CALL dbms.procedures() YIELD name 
WHERE name STARTS WITH 'apoc' 
RETURN count(name) as apoc_procedures
```

### Python Connection Test
```bash
python connect_test.py
```