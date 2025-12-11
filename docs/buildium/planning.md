# Buildium Collections Automation

## Overview
This document describes the automated collections process that synchronizes tenant balances between Buildium (source of truth) and a Google Sheet (display layer). The system applies late fees, updates balances, and ensures accuracy across both platforms.

---

## System Architecture

The system runs on a daily schedule and follows three operational phases: validation, looping through tenants, and taking appropriate actions.

```mermaid
graph TD

    Timer((Daily Timer)) --> Check{Date Check}

    subgraph Phase_1_Validation["Phase 1: Validation"]
        Check -->|Days 1–5| Pause[Grace Period — No Fees]
        Check -->|Days 6+| Active[Active Collection Phase]
    end

    subgraph Phase_2_Loop["Phase 2: Tenant Loop"]
        Active --> GetSheet[Read Google Sheet]
        GetSheet --> Loop{For Each Row}
        Loop --> Override{Manual Override?}
        Override -->|Yes| Skip[Skip Tenant]
        Override -->|No| Fetch[GET /leases/{id}/balance]
    end

    subgraph Phase_3_Action["Phase 3: Action"]
        Fetch --> BalanceCheck{Balance > 0?}
        BalanceCheck -->|No| MarkPaid[Update Sheet: Status = Paid]
        BalanceCheck -->|Yes| ApplyFee[POST Late Fee: $5.00]
        ApplyFee --> UpdateSheet[Update Sheet with New Balance]
    end

    Timer --> Check
```

---

## Business Rules

### 1. Grace Period (Days 1–5)
- No late fees are applied.
- The script runs only to detect payments and update balances accordingly.

### 2. Active Collection Phase (Day 6+)
- If a tenant balance is greater than zero, a daily late fee of **$5.00** is applied.
- The fee is recorded in Buildium, then reflected in the Google Sheet.

### 3. Source of Truth
- **Buildium:** Authoritative ledger for all balances and charges.
- **Google Sheet:** Read-only display layer; updated by the automation.

### 4. Record Identification
Tenant names cannot be reliably matched due to duplicates and name changes.  
The automation relies exclusively on:

- `Lease_ID` (unique integer from Buildium)

---

## Monthly Process (Reset Cycle)

**Trigger:** Occurs on the **8th of each month**.

**Actions:**
1. Query Buildium for all leases with outstanding balances.
2. Clear or archive the Google Sheet.
3. Populate new entries with:
   - Lease_ID  
   - Tenant Name  
   - Property Address  
   - Current Balance  

This creates a clean slate for each collection cycle.

---

## Daily Process (Maintenance Cycle)

**Trigger:** Runs daily at **2:00 AM**.

**Process:**

1. Read each `Lease_ID` from the Google Sheet.

2. Request the live balance from Buildium:
   - `GET /leases/{id}/balance`

3. Logic:

   1. If balance = 0:
      - Update Sheet → Status = "Paid"

   2. If balance > 0 **and** Day > 5:
      - Apply late fee:
        - `POST /leases/{id}/charges` → $5.00
        - Update Sheet with new balance



---

## API & Quota Management

To avoid exceeding Google Sheets rate limits (approximately 60 writes/min):

- All calculations occur in memory.
- A single `spreadsheets.values.batchUpdate` call updates all rows at once.

This ensures efficient and scalable performance.

---

## Google Sheet Schema

| Column | Header Name          | Description                                         |
|--------|------------------------|-----------------------------------------------------|
| A      | Buildium_Lease_ID     | Permanent unique key used for API lookups          |
| B      | Tenant_Name           | Display only                                        |
| C      | Property_Address      | Display only                                        |
| D      | Last_Updated          | Timestamp from automation                           |
| E      | Current_Balance       | Overwritten daily with Buildium data                |
| F      | Status                | Active, Paid, or Eviction                           |
| G      | Manual_Override       | Boolean flag; if TRUE, row is skipped by automation |

---

## Summary

This system ensures:
- Accurate synchronization of balances
- Automated application of late fees
- A clear operational workflow for collections teams
- Structured, predictable monthly resets
- Reliable daily maintenance

The architecture is deterministic, auditable, and scalable for future enhancements.

