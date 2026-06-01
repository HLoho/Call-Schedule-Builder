# Resident Call Schedule Optimizer

An automated system for generating optimized, fair, and rule-compliant resident call schedules using Google OR-Tools.

## Overview

This codebase manages the complex logic required for medical residency scheduling. It fetches individual availability from Google Sheets, applies custom wellness rules, validates staffing density, and uses a constraint satisfaction solver to balance the workload across the team. This was created with Google Gemini and run in the Google Co-lab IDE. I've provided the main script in both notebook and regular python versions. I've also provided the fake test data used to create and optimize the model. To re-create this, open the .pynib file in google colab, upload the test files to a google drive folder, then paste the drive url into the script where it requests the drive url. 

## Input Requirements

### Google Drive Structure
1. **Root Folder**: Create a folder in Google Drive and note its **Folder ID** (found in the URL).
2. **Resident Sheets**: Each resident must have a separate Google Sheet inside that folder. The file name should be the resident's name (e.g., `John_Doe`).

### Formatting the Sheets
Each spreadsheet requires a header row with these exact columns:

| Column | Description | Example |
| :--- | :--- | :--- |
| **Date** | The requested day off | `2026-07-04` |
| **Type of Request** | Must include 'vacation' or 'blackout' | `Vacation` |
| **Priority** | Importance of request (Optional) | `High` or `Low` |

---

## ⚖️ Core Rules and Logic

### 1. Automatic Blackout Rules
To prevent isolated shifts and ensure recovery time, the system applies:
*   **Whole Week Rule**: If Mon–Fri are requested as vacation, the system automatically blackouts the weekends on both sides.
*   **Sandwich Rule**: If a resident is off on Thu/Fri and the following Mon/Tue, the intervening weekend is automatically marked as a blackout.

### 2. Validation Suite
Before optimization, the system runs a safety check to flag:
*   **Staffing Shortages**: Days where too many residents are unavailable.
*   **Consecutive Weekdays**: Residents requesting the same weekday (e.g., every Monday) off too many times in a row.
*   **Restricted Periods**: Requests during exams (PRITE), retreats, or orientation weeks.

### 3. Optimization Logic
The solver aims for a **Minimax** balance, meaning it works to make the "hardest" schedule as easy as possible. It weights variables in this order of priority:
1.  **Hard Constraints**: No shifts on blackouts, no back-to-back shifts.
2.  **Holiday Equity**: Ensuring everyone works a similar number of major holidays.
3.  **Weekend Equity**: Balancing total weekend shifts.
4.  **Shift Density**: Minimizing weeks where a resident has more than one shift.

---

## 📊 Final Output Products

| File | Type | Description |
| :--- | :--- | :--- |
| `Compiled_Resident_Requests.csv` | **Data** | Master list of all raw and rule-based requests. |
| `Final_Optimized_Schedule.csv` | **Schedule** | The raw output of the solver (Date, Assigned Resident). |
| `Call_Schedule_Visual_Calendar.xlsx` | **Report** | A formatted Excel workbook with color-coded monthly tabs and a fairness audit. |

### Example Fairness Audit Output
```text
Resident          Total Shifts  Weekends  Holidays  Penalty Score
Resident1         25            6         1         6
Resident2         25            8         0         7
```
