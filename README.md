# MCP Migration Framework

A multi-agent Model Context Protocol (MCP) framework for automating Java application modernization. This framework orchestrates the migration of legacy Java 8 applications to Java 17, converts monolithic architectures to Spring Boot 3.x microservices, and prepares applications for cloud deployment.

## Overview

This framework uses a coordinated multi-agent approach where each agent has a specific responsibility in the migration pipeline:

| Agent | Role | Description |
|-------|------|-------------|
| **Agent 1** | Repository Analyzer | Analyzes legacy codebase and creates a comprehensive migration plan |
| **Agent 2** | Java 17 Uplift Engine | Transforms Java 8 code to Java 17 syntax and APIs |
| **Agent 3** | Microservice Converter | Converts to Spring Boot 3.x microservice architecture |
| **Agent 4** | Consistency Validator | Validates structural correctness and completeness |
| **Agent 5** | Cloud Readiness Builder | Prepares for cloud deployment with Docker and Kubernetes configs |
| **Master** | Orchestrator | Supervises all agents and manages migration state |

## Project Structure

```
mcp_migration/
├── README.md                  # This file
├── migration_state.json       # Tracks agent completion status
├── mcp/
│   └── mcp.json              # MCP configuration and tool definitions
└── agent_prompts/
    ├── MASTER.md             # Master orchestrator instructions
    ├── AGENT1_ANALYSIS.md    # Repository analysis agent
    ├── AGENT2_JAVA17.md      # Java 17 uplift agent
    ├── AGENT3_MICROSERVICE.md # Microservice conversion agent
    ├── AGENT4_CONSISTENCY.md  # Validation agent
    └── AGENT5_CLOUD_READY.md  # Cloud readiness agent
```

## How It Works

### Migration Pipeline

1. **Analysis Phase (Agent 1)**
   - Scans all files in `legacy_repo/`
   - Identifies deprecated APIs, architectural gaps, and migration requirements
   - Outputs analysis to `migrated_code/docs/ANALYSIS_REPORT.md`

2. **Java 17 Uplift Phase (Agent 2)**
   - Transforms code from Java 8 to Java 17
   - Applies modern Java features (lambdas, streams, records, var keyword)
   - Outputs to `migrated_code/lifting_java17/`

3. **Microservice Conversion Phase (Agent 3)**
   - Converts to Spring Boot 3.x architecture
   - Creates controllers, services, repositories, and DTOs
   - Outputs to `migrated_code/microserviceized/`

4. **Validation Phase (Agent 4)**
   - Validates package structures and dependencies
   - Checks for missing imports and circular dependencies
   - Outputs report to `migrated_code/docs/CONSISTENCY_REPORT.md`

5. **Cloud Readiness Phase (Agent 5)**
   - Generates Dockerfile and docker-compose.yml
   - Creates Kubernetes deployment manifests
   - Outputs to `migrated_code/cloud_ready/`

### State Management

The `migration_state.json` file tracks the completion status of each agent:

```json
{
  "agent1_completed": false,
  "agent2_completed": false,
  "agent3_completed": false,
  "agent4_completed": false,
  "agent5_completed": false,
  "last_processed_file": null
}
```

This enables resumability - if the migration is interrupted, it can continue from the last completed agent.

## Usage

### Prerequisites

- An MCP-compatible AI assistant or runtime
- Legacy Java codebase in a `legacy_repo/` directory

### Running the Migration

1. Place your legacy Java code in the `legacy_repo/` directory
2. Run the Master agent (`agent_prompts/MASTER.md`) to orchestrate the migration
3. The Master agent will:
   - Check `migration_state.json` for current progress
   - Execute each agent in sequence (1 → 5)
   - Update state after each agent completes
   - Generate final documentation

### Output Structure

After migration completes:

```
migrated_code/
├── lifting_java17/           # Java 17 uplifted code
├── microserviceized/         # Spring Boot 3.x microservices
├── cloud_ready/              # Cloud deployment configs
└── docs/
    ├── ANALYSIS_REPORT.md
    ├── JAVA17_UPLIFT_REPORT.md
    ├── MICROSERVICE_CONVERSION_REPORT.md
    ├── CONSISTENCY_REPORT.md
    ├── CLOUD_READY_REPORT.md
    ├── EXECUTIVE_SUMMARY.md
    ├── FULL_CHANGELOG.md
    └── MANUAL_REVIEW_STEPS.md
```

## Key Principles

- **Read-Only Legacy Code**: Original source files are never modified
- **Business Logic Preservation**: All transformations preserve existing business logic
- **Incremental Migration**: Each agent builds on the previous agent's output
- **Comprehensive Documentation**: Every transformation is documented
- **Resumable Process**: Migration can be resumed from any point using state tracking

## MCP Configuration

The `mcp/mcp.json` file defines the tools available to agents:

- `list_files` - Scan directories
- `read_file` - Read file contents
- `write_file` / `create_file` - Create output files
- `delete_file` - Remove files
- `rename_file` - Move/rename files
- `search` - Search file contents
- `stat` - Get file metadata

## License

See repository license for details.