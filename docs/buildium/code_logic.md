## Code & Implementation

This section outlines the Python logic used to synchronize Buildium and Google Sheets.

<u>**!!! warning "Security Notice"
    NEVER hardcode API keys directly into this script.  
    Use a `.env` file to manage secrets locally and securely.**</u>

---

## 1. Setup & Requirements

Install the required packages in your virtual environment:

```bash
pip install requests pandas gspread python-dotenv
```

---

## 2. Configuration (`.env`)

Create a `.env` file in the root of your project directory.  
This file is ignored by Git and **must not** be committed or shared.

Example:

```env
BUILDIUM_CLIENT_ID="insert_your_client_id_here"
BUILDIUM_CLIENT_SECRET="insert_your_client_secret_here"
GOOGLE_SHEETS_CREDENTIALS="path/to/service_account.json"
```

---

## 3. The Daily Sync Script (`main.py`)

This script runs daily via CRON and authenticates using the `.env` variables defined above.

```python
import os
import requests
import datetime
from datetime import date
from dotenv import load_dotenv

# 1. Load Environment Variables (Safety First)
load_dotenv()

API_KEY = os.getenv("BUILDIUM_CLIENT_ID")
API_SECRET = os.getenv("BUILDIUM_CLIENT_SECRET")
BASE_URL = 'https://api.buildium.com/v1'

def get_headers():
    """
    Constructs the headers for Buildium API requests.
    """
    return {
        'x-buildium-client-id': API_KEY,
        'x-buildium-client-secret': API_SECRET,
        'Content-Type': 'application/json'
    }

def is_late_fee_day():
    """
    Determines if today is a late-fee eligible day.
    """
    today = date.today()

    # Fees are paused on the 1st through the 5th
    if today.day <= 5:
        print(f"Grace Period: Today is the {today.day}th.")
        return False
    else:
        return True

def sync_daily_collections():
    """
    Main Logic Loop
    """
    if not is_late_fee_day():
        print("Skipping fee application logic.")
        return

    # Pseudo-code for fetching sheet data
    # leases = get_google_sheet_data()

    print("Starting daily sync...")

    # Example Loop
    # for lease in leases:
    #     check_balance(lease['id'])
```