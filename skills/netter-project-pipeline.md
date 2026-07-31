---
name: Create a Netter project and run its pipeline
description: Create a data project, inspect its databases, run all pipeline steps, and read the run lineage using the Netter API.
api: openapi/netter-openapi-original.json
operations: [create_project_api_v1_projects__post, list_project_databases_api_v1_projects__project_id__databases_get, run_all_steps_api_v1_projects__project_id__run_all_post, list_project_runs_api_v1_projects__project_id__runs_get, get_project_lineage_api_v1_projects__project_id__lineage_get]
---

# Create a Netter project and run its pipeline

A Netter project holds a pipeline (a DAG of steps) that turns connected/uploaded data into queryable databases. This skill creates a project and runs it.

## Auth

Bearer API key (`Authorization: Bearer ntr_...`), base URL `https://api.netter.ai`. See the "Upload a file" skill for key creation.

## Steps

1. Create the project with `create_project_api_v1_projects__post` (`POST /api/v1/projects/`). Capture the returned `project_id`.
2. Inspect the databases the project produces with `list_project_databases_api_v1_projects__project_id__databases_get` (`GET /api/v1/projects/{project_id}/databases`).
3. Execute the whole pipeline with `run_all_steps_api_v1_projects__project_id__run_all_post` (`POST /api/v1/projects/{project_id}/run-all`).
4. Track executions with `list_project_runs_api_v1_projects__project_id__runs_get` (`GET /api/v1/projects/{project_id}/runs`).
5. Read data lineage with `get_project_lineage_api_v1_projects__project_id__lineage_get` (`GET /api/v1/projects/{project_id}/lineage`).

## Rules

- Every list endpoint paginates with `limit`/`offset`.
- Running a pipeline is a state-changing (write) operation — treat it as `acting`; there is no idempotency key, so avoid duplicate triggers.
- `422` responses carry a FastAPI `detail[]` validation envelope.
- Promoting a raw project database into a typed ontology entity is a separate ontology operation (`promote_database_api_v1_ontology_databases__database_id__promote_post`).
