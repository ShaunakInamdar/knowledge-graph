# Knowledge Graph MVP

A modular pipeline that ingests, transforms, and builds a knowledge graph from relational data using discrete services and modern data infrastructure.

## Service Containers

- **postgres**: PostgreSQL 15 database for raw and staging data.
- **graph-mock**: Lightweight Python container simulating a graph database API (serves HTTP on port 8001).
- **messaging-mock**: Lightweight Python container simulating a messaging/queue service (serves HTTP on port 8002).

## Basic Monitoring

- List running containers:
  ```sh
  docker-compose ps
  ```
- View logs for a container:
  ```sh
  docker logs <container_name>
  ```
- Check health endpoints (if implemented):
  ```sh
  curl http://localhost:8001/health  # graph-mock
  curl http://localhost:8002/health  # messaging-mock
  ```

## Next Steps

- [ ] Replace `graph-mock` with a real Neo4j service and implement the actual graph API.
- [ ] Replace `messaging-mock` with a real RabbitMQ service and implement real message queue logic.
- [ ] Update service integrations to use real infrastructure instead of mocks.

## TODO

### Ingestion Service
- [ ] Implement `connect()` method in `generic_connector.py`
- [ ] Implement real extraction logic in `extract()` (currently just `pass`)
- [ ] Replace mock connector with real CSV/Postgres handling

### Transformer Service
- [ ] Flesh out `normalize(record)` logic (currently just returns input)
- [ ] Implement real transformation logic in `transform()` (currently just `pass`)
- [ ] Add field validation for incoming data

### Graph Builder
- [ ] Replace dummy graph with Neo4j integration (currently using `graph-mock`)
- [ ] Build real Cypher schema from `schema.cypher` (currently a placeholder)
- [ ] Implement real logic in `build_graph(data)` (currently returns `{}`)

### Messaging
- [ ] Replace `messaging-mock` with RabbitMQ implementation

### API Service
- [ ] Flesh out `RequestModel` and `ResponseModel` in `schemas/` (currently just `pass`)

### Shared Library
- [ ] Implement `load_config()` in `shared/config/loader.py` (currently just `pass`)
- [ ] Implement `get_logger()` in `shared/logging/logger.py` (currently just `pass`)

### General
- [ ] Replace all `pass` and placeholder stubs in core services and shared modules
- [ ] Add unit tests for all core services 