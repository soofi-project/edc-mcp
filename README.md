# EDC MCP Server

A Model Context Protocol (MCP) server for Eclipse Dataspace Components (EDC), enabling AI assistants like Claude to interact with EDC connectors for dataspace operations.

## Overview

This project provides a FastMCP-based server that exposes EDC Management API functionality through MCP tools. It allows AI assistants to perform dataspace operations such as managing assets, policies, catalogs, contract negotiations, and data transfers.

### What is EDC?

[Eclipse Dataspace Components (EDC)](https://github.com/eclipse-edc/Connector) is a framework for sovereign, inter-organizational data exchange. It implements the Dataspace Protocol and provides APIs for managing data assets, access policies, contract negotiations, and data transfers in a decentralized manner.

### What is MCP?

The [Model Context Protocol (MCP)](https://modelcontextprotocol.io/) is an open protocol that standardizes how applications provide context to LLMs. This server implements MCP to make EDC functionality accessible to AI assistants.

## Features

- **Asset Management**: Create, read, update, and delete data assets
- **Policy Definitions**: Manage access control policies for assets
- **Contract Definitions**: Define contractual terms for data access
- **Catalog Operations**: Browse and search data catalogs
- **Federated Catalog**: Query across multiple dataspaces using SPARQL
- **Contract Negotiations**: Initiate and manage contract agreements
- **Transfer Processes**: Execute data transfers (HTTP pull/push)
- **Dataset Operations**: Retrieve dataset metadata and thing descriptions
- **Background Refresh**: Automatic federated catalog updates

## Installation

### Prerequisites

- Python 3.11 or higher
- Access to an EDC connector instance

### From Source

```bash
# Clone the repository
git clone https://github.com/soofi-project/edc-mcp.git
cd edc-mcp

# Create a virtual environment
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate

# Install the package
pip install -e .
```

### Using Docker

```bash
# Pull the published multi-architecture image
docker pull ghcr.io/soofi-project/edc-mcp:latest

# Run the container
docker run --rm -p 8081:8081 \
  -e EDC_MANAGEMENT_URL=http://host.docker.internal:5173/api/management \
  -e EDC_CATALOG_URL=http://host.docker.internal:5173/api/catalog \
  ghcr.io/soofi-project/edc-mcp:latest
```

Replace `host.docker.internal` with the hostname or service name that is reachable from the container.

For local image development:

```bash
# Build the image
docker build -t edc-mcp .

# Run the container
docker run --rm -p 8081:8081 \
  -e EDC_MANAGEMENT_URL=http://host.docker.internal:5173/api/management \
  -e EDC_CATALOG_URL=http://host.docker.internal:5173/api/catalog \
  edc-mcp
```

## Configuration

Set the following environment variables before starting the server:

- `EDC_MANAGEMENT_URL`: EDC Management API endpoint (default: `http://localhost:5173/api/management`)
- `EDC_CATALOG_URL`: EDC catalog API endpoint used for federated catalog refreshes (default: `http://localhost:5173/api/catalog`)
- `CATALOG_REFRESH_INTERVAL`: Federated catalog refresh interval in seconds (default: `300`)
- `CATALOG_BASE_URI`: Base URI used when parsing catalog JSON-LD into RDF (default: `urn:edc:catalog/`)
- `LOG_LEVEL`: Python log level for this server (default: `INFO`)

Example:

```bash
export EDC_MANAGEMENT_URL=http://localhost:5173/api/management
export EDC_CATALOG_URL=http://localhost:5173/api/catalog
```

## Usage

### Starting the Server

```bash
# Run directly
python -m edc_mcp.main

# Or using the installed command
edc-mcp
```

The server will start on `http://0.0.0.0:8081` by default.

### Using with Claude Desktop

Add to your Claude Desktop configuration (`claude_desktop_config.json`):

```json
{
  "mcpServers": {
    "edc": {
      "url": "http://localhost:8081/mcp"
    }
  }
}
```

## Available Tools

### Asset Management

- `create_asset` - Create a new asset with properties and data address
- `list_assets` - List all assets with pagination
- `find_assets` - Search assets using filter expressions
- `get_asset` - Retrieve a specific asset by ID
- `update_asset_properties` - Update asset metadata
- `update_asset_private_properties` - Update private asset properties
- `update_asset_data_address` - Update asset data address configuration
- `delete_asset` - Remove an asset

### Policy Definitions

- `create_policy_definition` - Create a new access policy
- `list_policy_definitions` - List all policy definitions
- `get_policy_definition` - Retrieve a specific policy by ID
- `delete_policy_definition` - Remove a policy definition

### Contract Definitions

- `create_contract_definition` - Create a contract definition
- `list_contract_definitions` - List all contract definitions
- `find_contract_definitions_by_name` - Search contract definitions by name
- `get_contract_definition` - Retrieve a specific contract definition
- `delete_contract_definition` - Remove a contract definition

### Catalog Operations

- `get_catalog` - Retrieve catalog from a provider connector
- `get_dataset_from_catalog` - Get dataset details from catalog

### Federated Catalog

- `query_federated_catalog_sparql` - Execute SPARQL queries across federated catalogs
- `get_federated_catalog_stats` - Get statistics about cached catalog data
- `get_federated_catalog_example_queries` - Get example SPARQL queries

### Dataset Operations

- `get_policies_for_dataset` - Extract policies for a specific dataset
- `get_policy_for_dataset` - Get a specific policy from a dataset
- `get_thing_description_for_dataset` - Retrieve Thing Description (WoT) for a dataset

### Contract Negotiations

- `create_contract_negotiation` - Initiate a contract negotiation
- `list_contract_negotiations` - List all negotiations
- `get_contract_negotiation` - Get negotiation details by ID
- `get_contract_negotiation_by_agreement` - Find negotiation by agreement ID

### Contract Agreements

- `list_contract_agreements` - List all concluded agreements
- `get_contract_agreement` - Get agreement details by ID
- `get_contract_agreement_by_negotiation` - Get agreement by negotiation ID

### Transfer Processes

- `create_transfer_process_http_pull` - Start an HTTP pull transfer
- `create_transfer_process_http_push` - Start an HTTP push transfer
- `list_transfer_processes` - List all transfer processes
- `get_transfer_process` - Get transfer process details
- `get_data_address_for_http_pull_transfer_process` - Get EDR for pull transfers
- `perform_http_pull_request` - Execute HTTP pull data request

## Example Use Cases

1. **Browse Available Data**: Query catalogs and federated catalogs to discover datasets
2. **Negotiate Contracts**: Initiate and track contract negotiations with data providers
3. **Transfer Data**: Execute HTTP pull or push transfers after successful negotiations
4. **Manage Assets**: Create and configure data assets for sharing
5. **Define Policies**: Set up access control policies and contract terms
6. **SPARQL Queries**: Perform advanced queries across federated dataspace catalogs

## Resources

- [Eclipse Dataspace Components](https://github.com/eclipse-edc/Connector)
- [Model Context Protocol](https://modelcontextprotocol.io/)
- [FastMCP Documentation](https://gofastmcp.com/)
