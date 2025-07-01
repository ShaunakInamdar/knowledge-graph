# Architecture Overview

This application ingests data from arbitrary relational databases, normalizes and transforms it into a graph model, stores it in a graph database, and supports incremental enrichment. It consists of discrete services communicating via messaging, with persistent state in PostgreSQL (raw and staging data) and Neo4j (knowledge graph).

```text
├── docker-compose.yml
├── infra               # Infrastructure as Code
│   ├── postgres        # PostgreSQL setup
│   │   └── init.sql
│   └── neo4j           # Neo4j setup
│       └── import.conf
│   └── rabbitmq        # RabbitMQ config for messaging
│       └── rabbitmq.conf
├── services
│   ├── ingestion       # Data ingestion service
│   │   ├── Dockerfile
│   │   ├── src
│   │   │   ├── connectors
│   │   │   │   ├── __init__.py
│   │   │   │   └── generic_connector.py
│   │   │   ├── models  # SQLAlchemy models for staging
│   │   │   ├── tasks.py
│   │   │   └── main.py
│   │   └── requirements.txt
│   ├── transformer     # Data transformation and staging
│   │   ├── Dockerfile
│   │   ├── src
│   │   │   ├── transforms
│   │   │   │   └── normalize.py
│   │   │   ├── models
│   │   │   ├── tasks.py
│   │   │   └── main.py
│   │   └── requirements.txt
│   ├── graph-builder   # Graph construction service
│   │   ├── Dockerfile
│   │   ├── src
│   │   │   ├── graph
│   │   │   │   ├── builder.py
│   │   │   │   └── schema.cypher
│   │   │   ├── tasks.py
│   │   │   └── main.py
│   │   └── requirements.txt
│   ├── enrichment      # Enrichment service
│   │   ├── Dockerfile
│   │   ├── src
│   │   │   ├── enrichers
│   │   │   ├── tasks.py
│   │   │   └── main.py
│   │   └── requirements.txt
│   └── api             # REST/gRPC API service
│       ├── Dockerfile
│       ├── src
│       │   ├── routes
│       │   │   └── graph_api.py
│       │   ├── db       # Database connection utils
│       │   ├── schemas # Pydantic request/response models
│       │   └── main.py
│       └── requirements.txt
├── shared              # Shared libraries
│   ├── logging         # Custom logging & metrics
│   ├── config          # Configuration management
│   └── utils           # Helpers (retry, backoff)
└── README.md