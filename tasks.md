# MVP Build Plan: Step-by-Step Tasks

Below is a granular, testable task list for building the MVP. Each task focuses on a single concern, with a clear start and finish.

---

## 1. Project Initialization

1. **Initialize Git repository**
   - **Start**: Run `git init` in project root  
   - **End**: Empty repository with initial commit  
   - **Test**: `git status` shows no untracked files  

2. **Create folder structure**
   - **Start**: Copy folder tree outline from `architecture.md`  
   - **End**: All directories (`infra/`, `services/ingestion/`, etc.) exist  
   - **Test**: `tree .` matches structure  

3. **Add README.md**
   - **Start**: Create `README.md` with project title and high‑level overview  
   - **End**: README contains title and one‑sentence description  
   - **Test**: Open `README.md` and verify content  

---

## 2. Infrastructure Bootstrapping

4. **Write `docker-compose.yml` skeleton**
   - **Start**: Create file with empty `services:` block  
   - **End**: `docker-compose.yml` is valid YAML (no errors)  
   - **Test**: Run `docker-compose config` without errors  

5. **Define Postgres service**
   - **Start**: Add `postgres` service in `docker-compose.yml` with image `postgres:15`  
   - **End**: Service defined with ports and volumes  
   - **Test**: `docker-compose up -d postgres` runs and container is healthy  

6. **Define Neo4j service**
   - **Start**:Add `graph-mock` service (lightweight Python image) that will host an in-memory graph API stub  
   - **End**: Service defined with ports  
   - **Test**: `docker-compose up graph-mock` and `curl http://localhost:<port>/health` returns OK  

7. **Define RabbitMQ service**
   - **Start**: Add `messaging-mock` service (lightweight Python image) with a simple REST or in-memory queue stub  
   - **End**: Service defined with ports  
   - **Test**: `docker-compose up messaging-mock` and `curl http://localhost:<port>/health` returns OK 

8. **Verify multi-container network**
   - **Start**: `docker-compose up -d postgres graph-mock messaging-mock`  
   - **End**: All three containers are `healthy`  
   - **Test**: `docker-compose ps` shows healthy state  

---

## 3. Database Schemas

9. **Create `infra/postgres/init.sql`**
   - **Start**: Write `CREATE SCHEMA raw; CREATE SCHEMA clean;` statements  
   - **End**: File saved  
   - **Test**: Connect to Postgres and run  
     ```sql
     SELECT schema_name 
     FROM information_schema.schemata;
     ```  
     to see `raw`, `clean`  

10. **Skip Neo4j import config**  
   - **Start**: Confirm no `neo4j/import.conf` needed for dummy graph  
   - **End**: Remove or leave placeholder file  
   - **Test**: No errors on graph-mock startup 

---

## 4. Shared Library

11. **Scaffold `shared/config`**
    - **Start**: Create `shared/config/__init__.py` and `loader.py` with placeholder function `load_config()`  
    - **End**: Module imports without errors  
    - **Test**: In Python REPL, `import shared.config.loader` works  

12. **Scaffold `shared/logging`**
    - **Start**: Create `shared/logging/logger.py` with placeholder `get_logger()`  
    - **End**: Module imports without errors  
    - **Test**: In Python REPL, `import shared.logging.logger` works  

---

## 5. Ingestion Service

13. **Scaffold ingestion Dockerfile**
    - **Start**: Create `services/ingestion/Dockerfile` with Python base image  
    - **End**: Dockerfile exists  
    - **Test**: `docker build -t ingestion-svc services/ingestion` succeeds  

14. **Scaffold `generic_connector.py`**
    - **Start**: Create connector class with `connect()` stub  
    - **End**: File saved  
    - **Test**:  
      ```bash
      python -c "from connectors.generic_connector import Connector; Connector()"
      ```  
      runs without error  

15. **Implement extraction task stub**
    - **Start**: In `tasks.py`, add `def extract(): pass`  
    - **End**: Function defined  
    - **Test**: Importing `tasks.extract` raises no errors  

16. **Write `main.py` launcher**
    - **Start**: Add CLI entrypoint that calls `extract()`  
    - **End**: Script prints `Extraction complete`  
    - **Test**:  
      ```bash
      python main.py
      ```  
      outputs message  

---

## 6. Transformer Service

17. **Scaffold transformer Dockerfile**
    - **Start**: Create `services/transformer/Dockerfile`  
    - **End**: File exists  
    - **Test**: `docker build -t transformer-svc services/transformer` succeeds  

18. **Add normalization stub**
    - **Start**: In `transforms/normalize.py`, add `def normalize(record): return record`  
    - **End**: Function present  
    - **Test**:  
      ```python
      from transforms.normalize import normalize
      normalize({})
      ```  
      returns `{}`  

19. **Implement `tasks.py` stub**
    - **Start**: Add `def transform(): pass`  
    - **End**: Function defined  
    - **Test**: Import runs without errors  

20. **Write `main.py` launcher**
    - **Start**: CLI calls `transform()`, prints `Transform complete`  
    - **End**: Script exists  
    - **Test**: `python main.py` prints message  

---

## 7. Graph-Builder Service (Using Dummy Graph)

21. **Scaffold graph-builder Dockerfile**  
   - **Start**: Create `services/graph-builder/Dockerfile`  
   - **End**: File exists  
   - **Test**: `docker build -t graph-builder-svc services/graph-builder`

22. **Add `schema.cypher` placeholder**  
   - **Start**: Create file with `# placeholder`  
   - **End**: File exists  
   - **Test**: File is not empty

23. **Implement builder stub (`builder.py`)**  
   - **Start**: Add `def build_graph(data): return {}` stub that calls graph-mock API  
   - **End**: Function defined  
   - **Test**: `python -c "from services.graph-builder.src.graph.builder import build_graph; build_graph([])"`

24. **Write `main.py` launcher**  
   - **Start**: Call `build_graph([])` and print `Graph built (mock)`  
   - **End**: Script exists  
   - **Test**: `python services/graph-builder/src/main.py`

---

## 8. API Service

25. **Scaffold API Dockerfile**
    - **Start**: Create `services/api/Dockerfile`  
    - **End**: File exists  
    - **Test**: `docker build -t api-svc services/api` succeeds  

26. **Define `graph_api.py` route stub**
    - **Start**: Create FastAPI app stub with `/health` endpoint  
    - **End**: File saved  
    - **Test**: Running the API returns  
      ```json
      {"status":"ok"}
      ```  
      on `/health`  

27. **Add Pydantic schema stub**
    - **Start**: In `schemas/`, add empty request/response models  
    - **End**: Files exist  
    - **Test**: Importing models succeeds  

28. **Write `main.py` to launch FastAPI**
    - **Start**: Add Uvicorn launch code in `main.py`  
    - **End**: Script runs server  
    - **Test**: `uvicorn main:app` responds on port 8000  

---

## 9. End-to-End Smoke Test

29. **Compose full stack**  
   - **Start**: `docker-compose up --build`  
   - **End**: All containers running  
   - **Test**: `/health` (API), graph-mock `/health`, messaging-mock `/health` all return OK; `psql` accessible

30. **Document next steps**  
   - **Start**: Add TODOs in `README.md` for replacing mocks with Neo4j and RabbitMQ  
   - **End**: README updated with “Next Steps”  
   - **Test**: README contains mocks-to-real transition

---

*Each task can now be handed off individually to an LLM or engineer, verified, and then merged into the MVP branch.*  
*You seem to be using an outdated version of Cursor. Please upgrade to the latest version by [downloading Cursor again from our website](https://www.cursor.com/). All your settings will be preserved.*
