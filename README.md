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