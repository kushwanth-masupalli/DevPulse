# DevPulse — GitHub Activity Tracker

DevPulse is a lightweight system that listens to GitHub webhook events (pushes and merged pull requests), scores each contribution based on commit/PR type and difficulty, stores the activity in MongoDB, and displays it on a live leaderboard dashboard.
i API.

## Features

- **GitHub Webhook Listener** — Receives `push` and `pull_request` events from GitHub.
- **Automatic Scoring** — Parses commit messages and PR titles for a `[type]` tag (and optional `(difficulty)` tag on PRs) to calculate points:
  - Commit types: `feature`, `fix`, `perf`, `refactor`, `test`, `docs`, `style`, `chore`
  - PR types: `feature`, `bug`, `refactor`, `performance`, `test`, `documentation`, `chore` (with `easy` / `medium` / `hard` difficulty multipliers where applicable)
- **Leaderboard API** — Aggregates points per contributor, excluding configured usernames.
- **User Detail & Repo Issue Endpoints** — Per-user activity history and open-issue summaries pulled live from the GitHub API.
- **Dashboard UI** (`index.html`) — A dark-themed frontend that displays the leaderboard, user activity, and repo issues.


## Tech Stack

- **Backend:** Node.js, Express
- **Database:** MongoDB (via Mongoose)
- **Frontend:** Static HTML/CSS/JavaScript (no framework/build step)
- **External APIs:** GitHub REST API, Google Gemini API

## Project Structure

```
code_space_csi/
├── models/
│   └── Activity.js      # Mongoose schema for commits/PRs
├── index.html            # DevPulse leaderboard dashboard
├── index3.html            # RLChat widget (Gemini-powered)
├── server.js              # Express app: webhook handler + API routes
├── package.json
└── package-lock.json
```

## Prerequisites

- [Node.js](https://nodejs.org/) (v18+ recommended, for native `fetch` support)
- A MongoDB instance (local or hosted, e.g. MongoDB Atlas)
- A GitHub personal access token with repo/issue read access
- (Optional) A Google Gemini API key, if you want to use the RLChat widget

## Setup

1. **Clone the repository**
   ```bash
   git clone https://github.com/kushwanth-masupalli/code_space_csi.git
   cd code_space_csi
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Configure environment variables**

   Create a `.env` file in the project root:
   ```env
   PORT=10000
   MONGO_URI=your_mongodb_connection_string
   GITHUB_TOKEN=your_github_personal_access_token
   GITHUB_ORG=your_github_org_or_username
   ```

4. **Run the server**
   ```bash
   npm start      # production
   npm run dev     # development, with nodemon auto-reload
   ```

   The server starts on `http://localhost:10000` (or your configured `PORT`).



## Commit & PR Message Conventions

To have your points calculated correctly, prefix commit messages and PR titles with a bracketed type, e.g.:

```
[feature] Add user authentication flow
[fix] Correct off-by-one error in pagination
```

For pull requests, you can also add a difficulty tag:

```
[feature] Add real-time notifications (hard)
```

## API Endpoints

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/` | Health check — confirms the server is running |
| `POST` | `/webhook/github` | Receives GitHub webhook events (push / pull_request) |
| `GET` | `/leaderboard` | Returns aggregated points per contributor |
| `GET` | `/user-details/:username` | Returns full activity history and total points for a user |
| `GET` | `/repo-issues` | Returns open issue counts across all tracked repos |
| `GET` | `/repo-issues/:repoName` | Returns detailed open issues for a specific repo |

## Frontend

Open `index.html` in a browser (or serve it as a static file) to view the DevPulse leaderboard dashboard. Update the API base URL inside the file if your backend isn't running on `localhost`.


## Security Notes
- The `GITHUB_TOKEN` should have the minimum scopes necessary (read access to issues/repos).
- Consider verifying the GitHub webhook signature (`X-Hub-Signature-256`) in production to ensure requests genuinely originate from GitHub.

## License

No license has been specified for this repository. Add a `LICENSE` file if you intend to open source this project.
