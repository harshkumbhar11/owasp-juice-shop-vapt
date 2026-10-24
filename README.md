# OWASP Juice Shop — VAPT Practice Project

## Project Overview

This project documents hands-on web application security testing performed against a locally hosted OWASP Juice Shop instance in an authorized lab environment.

The goal is to practice HTTP traffic analysis, API testing, error handling assessment, evidence collection, and security reporting.

## Scope

- **Target:** OWASP Juice Shop
- **Environment:** Local lab (`127.0.0.1:3000`)
- **Tools:** Burp Suite Professional, Kali Linux, curl, Git
- **Testing areas:** HTTP response headers, product search API, malformed input handling, and error disclosure

All testing was performed against the local lab instance.

## Key Observations

### 1. Product Search API

- Endpoint: `/rest/products/search`
- Method: `GET`
- Normal search input returned an HTTP `200 OK` response with JSON product data.
- A malformed search input returned an HTTP `500 Internal Server Error`.

### 2. Error Handling

The malformed request produced a database-related error message in the response.

**Potential impact:** Detailed internal error messages can disclose implementation details and help an attacker understand backend behavior.

**Important limitation:** This observation alone does not confirm SQL injection. Further controlled testing is required to establish exploitability and impact.

### 3. HTTP Response Headers

The application's HTTP response headers were recorded and reviewed for security-relevant configuration.

See `notes/baseline-headers.txt` for the captured baseline.

## Evidence and Reports

- `notes/burp-observations.md` — Burp Suite observations
- `notes/api-observations.md` — API testing notes
- `notes/finding-01-sql-error.md` — Detailed finding draft
- `notes/final-assessment.md` — Final assessment summary
- `notes/retest-checklist.md` — Retesting checklist
- `evidence/` — Screenshots supporting the observations

## Recommended Improvements

- Return generic error messages to clients.
- Keep detailed database errors in server-side logs.
- Validate and safely handle user-supplied search input.
- Review security headers against the application's requirements.
- Retest after remediation before marking any finding as fixed.

## Status and Limitations

This is a learning project, not a comprehensive security audit. Findings should be confirmed through further testing before assigning a final severity or claiming exploitability.

## Disclaimer

Testing was performed in a local, intentionally vulnerable training environment. Do not test systems without explicit authorization.
