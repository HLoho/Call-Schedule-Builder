**Resident Call Schedule Optimizer: System Guide**

This system automates the generation of resident call schedules using Constraint Programming (OR-Tools). It integrates data from individual Google Sheets, applies residency-specific blackout rules, validates the feasibility of requests, and produces a fair, optimized calendar.

1. Input Requirements
Google Drive Setup
Create a dedicated folder in Google Drive.
Place one Google Sheet per resident inside this folder.
Copy the Folder ID (the long string at the end of the folder's URL) and paste it into the Input Settings cell below.
Sheet Formatting
Each resident's spreadsheet must have a header row with the following exact column names (case-insensitive):

Date: The requested day off (e.g., 2026-07-04).
Type of Request: Must contain keywords like vacation or blackout.
Priority (Optional): High or Low (defaults to Low).
2. Core Logic & Functions
Blackout Rule Engine
The system automatically adds "Rule-Based Blackouts" to protect resident wellness:

Whole Week Rule: If a resident requests Monday through Friday off, the system automatically blackouts both surrounding weekends (Saturday/Sunday).
Sandwich Rule: If a resident takes Thursday/Friday off and the following Monday/Tuesday off, the system automatically blackouts the intervening weekend to ensure a continuous break.
Validation Suite
Before running the optimizer, the Schedule Validation cell checks for:

High Density: Days where too many people are off simultaneously.
Consecutive Weekdays: Ensuring no one takes 3+ same-weekdays (e.g., 3 Mondays) off in a row.
Restricted Periods: Flags requests during mandatory training weeks or exam days (PRITE).
Optimization Engine
The solver uses a Minimax Objective. It doesn't just look for a valid schedule; it specifically tries to minimize the "Penalty Score" of the person with the hardest schedule, ensuring that shifts, weekends, and holidays are distributed as evenly as possible.

3. Output Products
Product	Format	Description
Compiled Requests	.csv	A master list of every resident's original and rule-based requests.
Validation Report	Console	Real-time warnings about staffing shortages or rule violations.
Optimized Schedule	.csv	The raw data of who is assigned to every single day of the year.
Visual Calendar	.xlsx	A multi-sheet Excel file with monthly calendar views and color-coded shifts.
Fairness Audit	Console/Excel	A detailed breakdown of total shifts, weekend counts, and penalty scores per resident.
Example Fairness Metrics
Resident          Total  Weekdays  Weekends  Holidays  Penalty Score
Adolfo_Ocampo     25     18        6         1         6
Anouk_Ackerman    25     17        8         0         7
