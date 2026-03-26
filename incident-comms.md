Release Tracker App (For SRE / Support Teams)  
🎯 Problem It Solves  
“What changed?” during incidents  
Lack of visibility into deployments across services  
No quick mapping between incident ↔ release ↔ owner  
***


Build a full-stack MVP Release Tracker application for SRE teams.

Tech Stack:
- Frontend: React (functional components + hooks)
- Backend: FastAPI (Python)
- Database: SQLite
- API communication: Axios
- Calendar UI: react-big-calendar

CRITICAL TIME REQUIREMENTS:

1. All timestamps MUST be stored in UTC in the backend.
2. APIs must return timestamps in ISO 8601 UTC format (e.g., 2026-03-25T10:00:00Z).
3. The frontend must support:
   - Default view: UTC
   - Toggle: "Show Local Time"
4. Timezone conversion must ONLY happen in the frontend.
5. Backend must NEVER store or depend on local timezone.

---

1. Backend (FastAPI):

POST /release
- Accept:
  {
    service_name: string,
    release_time: datetime,
    environment: string (prod/staging),
    owner: string,
    summary: string
  }

- Logic:
  - If datetime has no timezone → assume UTC
  - Convert all timestamps to UTC before storing
  - Store in ISO UTC format

---

GET /releases/today
- Return releases for current UTC day (00:00–23:59 UTC)

---

GET /releases/recent?hours=2
- Return releases from last N hours based on current UTC time

---

Backend Requirements:
- Use SQLite
- Use Pydantic
- Use timezone-aware datetime (datetime.now(timezone.utc))
- Always return:
  "release_time": "2026-03-25T10:00:00Z"

---

2. Frontend (React):

Global Feature:
- Add a toggle switch:
  [ ] Show Local Time

- Default: OFF (UTC mode)

---

Timezone Handling:

- If toggle OFF:
  - Display raw UTC time
  - Label: "All times in UTC"

- If toggle ON:
  - Convert UTC → user's local timezone using JS:
    new Date(utc_time).toLocaleString()

  - Label: "Showing Local Time"

---

A. Add Release Page:
- Input: datetime-local
- Convert selected time to UTC before sending:
  new Date(input).toISOString()

---

B. Calendar View:

- Use react-big-calendar
- Feed UTC dates directly
- Apply conversion only for display if toggle is ON

- Event title:
  "service_name (environment)"

- Color coding:
  - Red → prod
  - Orange → staging

---

Event Click Modal:
Show:
- Service name
- Owner
- Summary
- Release time:
  - UTC OR Local (based on toggle)

---

3. Routing:
- "/" → Calendar
- "/add" → Add Release

---

4. Extra Feature:
- Button: "Show Last 2 Hours"
- Respect toggle:
  - Display times in UTC or Local accordingly

---

5. UI Details:
- Add label on top:
  - "Timezone: UTC" OR "Timezone: Local"
- Toggle should instantly update all visible times

---

6. Edge Cases:
- Avoid double conversion of time
- Ensure calendar does not shift events incorrectly
- Handle browser timezone differences safely

---

Goal:

Provide an SRE-friendly release tracker where:
- Data is always consistent (UTC internally)
- Users can view times in their local timezone when needed
- Incident debugging remains accurate and fast

