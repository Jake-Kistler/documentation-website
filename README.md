# Automation Hub

Centralized documentation and planning repository for internal automation tools.

A static documentation site (MkDocs) detailing architecture, API logic, and workflows for internal projects (Buildium, Google Sheets, etc.).

## Table of Contents

- [Current Projects](#current-projects)
- [Setup & Installation](#setup--installation)
- [Running the Documentation Server](#running-the-documentation-server)
- [How to Add New Projects](#how-to-add-new-projects)
- [Security Note](#security-note)

## Current Projects

### Buildium Collections Automation
- **Goal:** Automate daily late fee application and balance tracking.  
- **Tech Stack:** Python, Buildium API, Google Sheets API.  
- **Status:** In Planning / Active Development  
- **Documentation:** View project plan (served via MkDocs)

## Setup & Installation

Prerequisites
- Python 3.x
- WSL / Linux / macOS (recommended)

Installation
1. Create a Python virtual environment:
    ```bash
    python3 -m venv .venv
    ```

2. Activate the virtual environment:
    ```bash
    source .venv/bin/activate
    ```

3. Install dependencies into the venv:
    ```bash
    ./.venv/bin/python -m pip install mkdocs mkdocs-material
    ```

## Running the Documentation Server

Start the MkDocs dev server:
```bash
./.venv/bin/mkdocs serve
```

Open your browser to:
http://localhost:8000

## How to Add New Projects

1. Create a new folder under `docs/` (e.g., `docs/lease-renewals/`).
2. Add markdown files (e.g., `index.md`, `planning.md`).
3. Update `mkdocs.yml` navigation:
    ```yaml
    nav:
      - Home: index.md
      - Buildium Project:
          - Overview: buildium/index.md
      - New Project:
          - Overview: new-project/index.md
    ```

## Security Note

- This repository is documentation-only: do not store API keys here.
- Secret management: actual scripts and `.env` files containing secrets (e.g., `BUILDIUM_CLIENT_ID`) should be kept in the separate `buildium-scripts` repository or a secure vault.