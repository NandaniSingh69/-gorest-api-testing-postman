# GoRest API Testing Project

[![API Test Automation](https://github.com/NandaniSingh69/-gorest-api-testing-postman/actions/workflows/newman-tests.yml/badge.svg)](https://github.com/NandaniSingh69/-gorest-api-testing-postman/actions/workflows/newman-tests.yml)

A complete API testing project built using Postman, Newman, and GitHub Actions. This project covers manual test case design, automated test scripts, data-driven testing, and a CI/CD pipeline that runs the full test suite automatically on every push.

## Project Overview

This project tests the [GoRest](https://gorest.co.in) public REST API, covering the **Users** and **Posts** modules. It demonstrates the complete QA workflow: writing test scenarios and test cases first, building and automating them in Postman, running them via the command line with Newman, and wiring them into a CI pipeline with GitHub Actions.

## Tech Stack

- **Postman** – building requests, environments, and test scripts
- **JavaScript (Chai assertions)** – writing automated test scripts inside Postman
- **Newman** – running the Postman collection from the command line
- **newman-reporter-htmlextra** – generating HTML test reports
- **GitHub Actions** – CI pipeline that runs the test suite on every push
- **GitHub Secrets** – securely injecting the API token during CI runs
- **Excel** – test scenario and test case documentation

## API Used

[GoRest](https://gorest.co.in/public/v2) – a free, public REST API for practicing CRUD operations on Users and Posts, with real Bearer token authentication.

## Test Coverage

18 test cases across 6 scenarios, covering positive and negative cases for each operation.

| Module | Operations Covered |
|---|---|
| Users | GET (list, pagination, invalid ID), POST (valid, no auth, missing field, invalid format, duplicate email), PUT/PATCH (valid update, non-existent user), DELETE (valid delete, double delete) |
| Posts | GET (valid user, non-existent user), POST (valid, no auth, missing field) |

Full test scenario and test case documentation, including pre-conditions, test steps, test data, and expected results, is available in [`test-cases/GoRest_API_Test_Cases.xlsx`](./test-cases/GoRest_API_Test_Cases.xlsx).

## Project Structure

```
gorest-api-testing-postman/
├── .github/workflows/
│   └── newman-tests.yml          # CI pipeline configuration
├── collection/
│   └── GoRest_API_Collection.json
├── environment/
│   └── GoRest_Environment.json
├── data/
│   └── user_data.csv             # data-driven test inputs
├── reports/
│   ├── test-report-chained.html
│   └── test-report-datadriven.html
├── screenshots/
│   ├── chained-run-all-pass.png
│   ├── data-driven-run-3-iterations.png
│   └── github-actions-ci-passing.png
├── test-cases/
│   └── GoRest_API_Test_Cases.xlsx
└── README.md
```

## Key Features

**Test case design before automation** – every automated check maps back to a documented test case (scenario → test case → script), matching real-world QA practice rather than writing scripts ad hoc.

**Request chaining** – the user created in `TC05` is captured via `pm.environment.set()` and reused across `TC10`–`TC18`, so update, delete, and post-creation tests run against a real, valid resource without manual copy-pasting of IDs.

**Self-cleaning test data** – `TC12` deletes the test user at the end of the run, allowing the full collection to be re-run repeatedly without leftover data or manual cleanup.

**Data-driven testing** – the collection can be run against multiple input sets using `data/user_data.csv`, demonstrating the same test logic validated across different data combinations.

**CI/CD pipeline** – every push to `main` triggers GitHub Actions, which installs Newman, runs the full collection, and uploads the HTML report as a build artifact. The real API token is never committed to the repository; it's stored as a GitHub Secret and injected at runtime via `--env-var`.

## How to Run Locally

**1. Clone the repository**
```bash
git clone https://github.com/NandaniSingh69/-gorest-api-testing-postman.git
cd -gorest-api-testing-postman
```

**2. Install Newman and the HTML reporter**
```bash
npm install -g newman
npm install -g newman-reporter-htmlextra
```

**3. Add your own GoRest token**

Generate a free token at [gorest.co.in](https://gorest.co.in) (sign in → profile → access token), then open `environment/GoRest_Environment.json` and replace the placeholder `token` value with your own.

**4. Run the collection**
```bash
newman run collection/GoRest_API_Collection.json -e environment/GoRest_Environment.json -r htmlextra --reporter-htmlextra-export reports/test-report-chained.html
```

**5. Run with data-driven inputs (optional)**
```bash
newman run collection/GoRest_API_Collection.json -e environment/GoRest_Environment.json -d data/user_data.csv -r htmlextra --reporter-htmlextra-export reports/test-report-datadriven.html
```

## CI Pipeline

The GitHub Actions workflow ([`.github/workflows/newman-tests.yml`](./.github/workflows/newman-tests.yml)) runs automatically on every push to `main`:

1. Checks out the repository
2. Sets up Node.js
3. Installs Newman and the HTML reporter
4. Injects the API token from a GitHub Secret (`GOREST_TOKEN`) at runtime
5. Runs the full collection and generates an HTML report
6. Uploads the report as a downloadable build artifact

This means the test suite is validated automatically every time the project changes, without any manual steps.

## Sample Results

**Chained run (full 18 test case flow, single iteration):** all tests passing.

**Data-driven run (3 iterations from CSV):** all tests passing across all data sets.

See the `screenshots/` folder for visual proof, or open the HTML files directly in `reports/`.

## Observations and Findings

While building this suite, a few real API behaviors were discovered that differed from initial assumptions, and test cases were updated to reflect actual behavior rather than assumptions:

- `GET /users/{id}/posts` for a non-existent user ID returns `200` with an empty array, rather than a `404`. The endpoint does not validate user existence before listing posts.

## What This Project Demonstrates

- Writing structured test scenarios and test cases before automation
- Building and organizing a Postman collection with environments and variable chaining
- Writing automated assertions in JavaScript (status codes, response body validation, response time)
- Positive and negative test design (valid inputs, missing fields, invalid formats, auth failures, duplicate data, non-existent resources)
- Data-driven testing using external CSV files
- Command-line test execution and HTML reporting with Newman
- CI/CD pipeline setup with GitHub Actions
- Secure handling of API credentials using GitHub Secrets

## Author

Built by Nandani Singh as a hands-on project while learning API testing with Postman.


