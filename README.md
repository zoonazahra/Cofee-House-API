# Coffee House API — Automated Test Suite

Automated API test collection for the Coffee House practice API (`valentinos-coffee.herokuapp.com`), built in Postman and run in CI via GitHub Actions using the Postman CLI.

## Tech Stack
- **Postman** — collection design, test scripts, dynamic variable handling
- **Postman CLI** — headless collection execution
- **GitHub Actions** — CI pipeline, runs the full suite on every push
- **Newman** (optional local runner) — CLI-based collection execution outside Postman's UI

## What's Tested
| Folder | Endpoint | Method |
|---|---|---|
| status | `/status` | GET |
| products | `/products` | GET |
| products | `/products/:id` | GET |
| clients | `/clients` | POST |
| orders | `/orders` | POST |
| orders | `/orders` | GET |
| orders | `/orders/:id` | GET |

Assertions cover status codes, response schema validation, and response body field checks (e.g. customer name, order ID format).

## Dynamic Auth Flow
`POST /clients` registers a new client and returns a fresh API token per run. A test script on that request captures the token and stores it as a collection variable (`apiKey`), which every subsequent authenticated request (`orders/*`) references via `{{apiKey}}`. This means the suite is fully self-contained — no hardcoded credentials, no manual token setup between runs.

## Running Locally

**Option 1 — Postman CLI**
```bash
postman login --with-api-key <your-postman-api-key>
postman collection run "Coffee House API.postman_collection.json"
```

**Option 2 — Newman**
```bash
npm install -g newman
newman run "Coffee House API.postman_collection.json"
```

Optional HTML report:
```bash
npm install -g newman-reporter-htmlextra
newman run "Coffee House API.postman_collection.json" -r htmlextra
```

## CI Pipeline
Defined in `.github/workflows/postman.yml`. On every push to `main`, the workflow:
1. Checks out the repo
2. Installs the Postman CLI
3. Authenticates using a `POSTMAN_API_KEY` GitHub secret
4. Runs the full collection headlessly

Check the **Actions** tab for run history and pass/fail status per commit.

## Project Structure
```
.
├── .github/workflows/postman.yml       # CI pipeline definition
└── Coffee House API.postman_collection.json   # Full test collection
```
