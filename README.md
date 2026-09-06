# DBeaver MCP Server (Read-Only & Security-Hardened Fork)

A secure, read-only Model Context Protocol (MCP) server that seamlessly integrates with DBeaver to provide AI assistants safe access to 200+ database types through your existing DBeaver connections.

This is a security-hardened fork of [PXSR/dbeaver-mcp-server](https://github.com/PXSR/dbeaver-mcp-server) specifically designed for safe, zero-risk AI paired exploration and analysis.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Node.js](https://img.shields.io/badge/node-%3E%3D18.0.0-brightgreen.svg)](https://nodejs.org/)

---

## 🛡️ Security Enhancements in this Fork

This fork addresses key security risks present in database MCP implementations:

1. **🔒 Strict Read-Only Query Whitelist:**
   - Write tools (`write_query`, `create_table`, `alter_table`, `drop_table`) have been **completely removed**.
   - `execute_query` utilizes a strict **whitelist** allowing only safe query prefixes: `SELECT`, `SHOW`, `DESCRIBE`, `EXPLAIN`, `PRAGMA`, and `WITH` (CTEs).
   - Strips leading SQL line/block comments to prevent obfuscation.
   - Blocks stacked/multi-statement queries containing write operations (e.g. `SELECT 1; DROP TABLE users`).

2. **💉 SQL Injection Defenses:**
   - Table and schema names interpolated into introspection queries (`buildSchemaQuery`, `buildListTablesQuery`) are strictly sanitized with `sanitizeIdentifier`.
   - Rejects illegal characters, quotes, and reserved keywords.

3. **🔐 Credential Protection & Password Redaction:**
   - Connection credentials decrypted for database connections are **never exposed back to LLM context**.
   - `list_connections` and `get_connection_info` redact passwords with `********`.

---

## 🚀 Key Features

### 🔗 Universal Database Connectivity
- **200+ Database Types**: PostgreSQL, MySQL, Oracle, SQL Server, SQLite, and more.
- **Zero Configuration**: Reads existing DBeaver workspaces directly.
- **Cross-Platform**: Windows, macOS, and Linux support.

### 📊 Safe Data Operations & Analysis
- **Query Inspection**: Execute SELECT queries with automatic `LIMIT` enforcement.
- **Data Export**: Export query results into CSV or JSON formats.
- **Schema Exploration**: Introspect tables, columns, and views without any mutation risk.
- **Business Insights**: Persist analysis notes and intelligence memos.

---

## 🛠️ Available Tools

| Category | Tool | Description | Mode |
|---|---|---|---|
| **Connections** | `list_connections` | List available DBeaver connections (passwords masked) | Read-only |
| | `get_connection_info` | Detailed connection metadata (passwords masked) | Read-only |
| | `test_connection` | Test database connectivity | Safe |
| **Query & Export** | `execute_query` | Execute safe SELECT / read-only queries | Read-only (whitelisted) |
| | `export_data` | Export SELECT query results to CSV/JSON | Read-only |
| **Schema** | `list_tables` | List tables and views in database | Read-only |
| | `get_table_schema` | Get column definitions and data types | Read-only |
| **Analytics** | `get_database_stats`| Fetch database statistics and metadata | Read-only |
| | `append_insight` | Add business insights to note storage | Safe |
| | `list_insights` | Retrieve stored analysis notes | Safe |

> [!NOTE]
> All mutation/DDL tools (`write_query`, `create_table`, `alter_table`, `drop_table`) have been permanently disabled in this fork.

---

## 📋 Prerequisites

- **Node.js**: >= 18.0.0
- **DBeaver**: Community or Enterprise Edition installed with configured connections.
- **MCP Client**: Claude Desktop, Cursor, Antigravity, or any standard MCP client.

---

## 🛠️ Installation & Setup

### 1. Clone & Build
```bash
git clone https://github.com/A-viral-dev/dbeaver-mcp-server.git
cd dbeaver-mcp-server
npm install
npm run build
```

### 2. Configure MCP Client

#### Claude Desktop / Cursor Config (`claude_desktop_config.json`):
```json
{
  "mcpServers": {
    "dbeaver": {
      "command": "node",
      "args": ["<PATH_TO_REPO>/dist/index.js"],
      "env": {
        "DBEAVER_DEBUG": "false",
        "DBEAVER_TIMEOUT": "30000"
      }
    }
  }
}
```

### 3. Environment Variables
- `DBEAVER_PATH`: Explicit path to DBeaver executable (auto-detected across OS platforms by default).
- `DBEAVER_TIMEOUT`: Query timeout in milliseconds (default: `30000`).
- `DBEAVER_DEBUG`: Enable verbose logging (`true`/`false`).

---

## 🤝 Upstream & Attribution

- **Original Project:** [PXSR/dbeaver-mcp-server](https://github.com/PXSR/dbeaver-mcp-server) (by Sarthak Jain)
- **Protocol:** [Model Context Protocol](https://modelcontextprotocol.io/) by Anthropic
- **License:** MIT License