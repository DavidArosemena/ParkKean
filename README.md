# ParkKean

ParkKean is a campus parking assistant for Kean University, built with Node.js, Express, SQLite, and vanilla HTML/CSS/JavaScript.

It provides a browser dashboard for checking parking lot availability, reporting real-world lot status, and viewing a community leaderboard of parking updates. The application works with seeded local data by default and can optionally merge data from a live parking feed, making it usable as both a portfolio project and a foundation for a production campus mobility tool.

**Live demo:** https://parkkean-davidarosemena.onrender.com
>Side Note: Hosted on Render's free plan, so the first load may take up to a minute while the server wakes up.

## Portfolio Highlights

- Full-stack JavaScript application with an Express API, SQLite persistence, and a responsive browser UI.
- Practical domain model for parking lots, reports, users, live data snapshots, and leaderboard scoring.
- Resilient live-feed integration that accepts several common API payload shapes and falls back to stored values when the feed is unavailable.
- Release-ready documentation, environment variable guidance, ignore rules, and publication checklist.

## Key Features

- Parking lot dashboard with search, status filters, occupancy bars, walk-time estimates, and last-updated timestamps.
- User reporting flow for marking lots as open, limited, or full.
- Community leaderboard that awards points for submitted reports.
- Lightweight user switching without a full authentication system.
- Operations dashboard for scanning lot status, recent reports, and event-related reservations.
- SQLite persistence with automatic table creation and seed data on first run.
- Optional live parking API integration with configurable endpoint, API key header, and request timeout.

## Tech Stack

- Runtime: Node.js 18+
- Server: Express 4
- Database: SQLite via `sqlite3`
- Frontend: Vanilla HTML, CSS, and JavaScript
- Development tooling: Nodemon

## Requirements

- Node.js 18 or newer
- npm
- A modern browser such as Chrome, Safari, Firefox, or Microsoft Edge

Node 18+ is recommended because `liveData.js` uses the native `fetch` API when a live feed is configured.

## Setup

1. Install dependencies:

   ```sh
   npm install
   ```

2. Optional: copy the environment template and fill in live feed settings if needed.

   ```sh
   cp .env.example .env
   ```

3. Start the development server:

   ```sh
   npm run dev
   ```

4. Open the app:

   ```text
   http://localhost:3000
   ```

For a production-style local run, use:

```sh
npm start
```

## User Workflow

- Review parking lots by status: `Open`, `Limited`, or `Full`.
- Search for a lot by name or code.
- Submit a report with the latest observed lot status and an optional note.
- Check leaderboard standings for community reporting activity.

## Environment Variables

The app runs without environment variables. When no live feed is configured, it uses the local SQLite database and simulated refresh behavior.

| Variable                       | Required | Default         | Description                                           |
| ------------------------------ | -------- | --------------- | ----------------------------------------------------- |
| `PORT`                         | No       | `3000`          | Port used by the Express server.                      |
| `PARKKEAN_LIVE_API_URL`        | No       | unset           | HTTPS endpoint returning live parking lot data.       |
| `PARKKEAN_LIVE_API_KEY`        | No       | unset           | Optional credential sent with each live feed request. |
| `PARKKEAN_LIVE_API_KEY_HEADER` | No       | `Authorization` | Header name used for `PARKKEAN_LIVE_API_KEY`.         |
| `PARKKEAN_LIVE_TIMEOUT_MS`     | No       | `5000`          | Live feed request timeout in milliseconds.            |

Never commit real `.env` files or production credentials.

## Live Feed Payload

The live endpoint can return an array of lots:

```json
[
  {
    "code": "STADIUM",
    "name": "Stadium Parking",
    "capacity": 300,
    "occupancy": 275,
    "status": "LIMITED",
    "walk_time": 5,
    "full_by": "08:15",
    "last_updated": 1713556800000
  }
]
```

It can also return an object with a `lots` array. The server normalizes common field variants such as `lotCode`, `lot_code`, `occupied`, `walkTime`, `updated_at`, and `timestamp`. Status values are mapped to `OPEN`, `LIMITED`, or `FULL`; if status is missing, it is inferred from occupancy and capacity.

## API Overview

| Method | Route                   | Description                                                        |
| ------ | ----------------------- | ------------------------------------------------------------------ |
| `GET`  | `/api/lots`             | Returns lots with their latest report summaries.                   |
| `GET`  | `/api/lots/:id/reports` | Returns the 10 most recent reports for a lot.                      |
| `POST` | `/api/lots/refresh`     | Refreshes from the live feed or simulates small occupancy changes. |
| `POST` | `/api/reports`          | Submits a status report and awards user points.                    |
| `GET`  | `/api/leaderboard`      | Returns top reporting users.                                       |
| `POST` | `/api/users`            | Creates or retrieves a lightweight user profile.                   |
| `GET`  | `/api/users/:username`  | Looks up a user profile by username.                               |

## Project Structure

```text
.
├── data/
│   └── parkkean.db          # Runtime SQLite database, generated locally and ignored by Git
├── docs/
│   └── PORTFOLIO_REVIEW.md  # Portfolio-readiness assessment
├── public/
│   ├── app.js               # Frontend interaction and rendering logic
│   ├── index.html           # Main browser UI
│   └── styles.css           # Application styling
├── liveData.js              # Live parking feed fetch/normalization helpers
├── RELEASE_CHECKLIST.md     # Publication and cleanup checklist
├── server.js                # Express API, SQLite schema, and app startup
├── package.json             # npm scripts and dependencies
└── README.md
```

## Dependency Notes

Dependencies are declared in `package.json` and locked in `package-lock.json`.

- `express`: API server and static file hosting.
- `sqlite3`: Local database storage.
- `nodemon`: Development server restart tool.

This project does not need `requirements.txt`, `Pipfile`, or `pyproject.toml` because it is a Node.js application, not a Python application.

## Contributing

- Use feature branches for changes.
- Run `npm run check` before opening a pull request.
- Update documentation when behavior, setup, or environment variables change.
- Report issues with what happened, what was expected, reproduction steps, and screenshots when helpful.

## Current Limitations

- The admin dashboard is a functional prototype and does not include authentication or role-based access control.
- The SQLite database is suitable for local development and guided reviews; a production deployment should include migrations, backups, and environment-specific storage.
- Live parking data depends on an external endpoint supplied through environment variables.
- Local database files are ignored by Git and should not be committed with personal or test data.

## Future Improvements

- Add automated tests for API endpoints and live-feed normalization.
- Add authentication and role-based access before treating the admin dashboard as production-ready.
- Add database migrations for future schema changes.
- Add production deployment notes for hosting, logging, and backups.
- Replace the current Google Fonts import with a self-hosted font strategy if fully offline operation is required.
- Add a map view or campus coordinates for each parking lot.

## Author

David Arosemena - Software engineering portfolio project
