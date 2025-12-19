Automation Hub

Centralized documentation and planning repository for internal automation tools.

This repository hosts the Automation Hub website, a static documentation site built with MkDocs that details architecture, API logic, and workflows for our internal projects (Buildium, Google Sheets, etc.).

Current Projects

1. Buildium Collections Automation

Goal: Automate daily late fee application and balance tracking.

Tech Stack: Python, Buildium API, Google Sheets API.

Status: In Planning / Active Development

Documentation: View Project Plan (When server is running)

Setup & Installation

Follow these steps to run the documentation site locally.

1. Prerequisites

Python 3.x

WSL / Linux / macOS (Recommended)

2. Installation

Clone the repository and set up the virtual environment:

# 1. Create the virtual environment
python3 -m venv .venv

# 2. Activate it
source .venv/bin/activate

# 3. Install dependencies (Force install into venv)
./.venv/bin/python -m pip install mkdocs mkdocs-material


Running the Documentation Server

To view the website, diagrams, and project plans:

Start the server:

./.venv/bin/mkdocs serve


Open your browser:
Go to: http://localhost:8000

How to Add New Projects

Create a new folder in docs/ (e.g., docs/lease-renewals/).

Add your markdown files (index.md, planning.md).

Update the mkdocs.yml navigation section:

nav:
  - Home: index.md
  - Buildium Project: ...
  - New Project:
      - Overview: new-project/index.md


Security Note

No API Keys: This repository is for documentation only.

Secret Management: Actual scripts and .env files containing API keys (BUILDIUM_CLIENT_ID, etc.) should reside in the separate buildium-scripts repository