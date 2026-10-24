# Web Application VAPT Project — OWASP Juice Shop

## Project Overview

This project demonstrates a basic Web Application Vulnerability Assessment and Penetration Testing (VAPT) workflow using OWASP Juice Shop in a local lab environment.

The purpose is to practice HTTP traffic analysis, API observation, error handling assessment, evidence collection, and security reporting.

## Lab Environment

- Operating System: Kali Linux
- Target Application: OWASP Juice Shop
- Interception and Testing Tool: Burp Suite Professional
- Target URL: `http://127.0.0.1:3000`
- Scope: Local lab only

## Project Structure

```text
vapt-project-1/
├── README.md
├── evidence/
│   ├── evidance_1.png
│   └── burp/
│       ├── burp_1.png
│       ├── sql_error.png
│       └── data
└── notes/
    ├── baseline-headers.txt
    ├── burp-observations.md
    ├── api-observations.md
    └── finding-01-sql-error.md
```

## Work Completed

- Set up OWASP Juice Shop locally.
- Verified that the application was accessible.
- Captured HTTP requests and responses using Burp Suite.
- Observed the product search API.
- Recorded baseline HTTP response headers.
- Tested malformed search input in the local lab.
- Observed an HTTP 500 response containing SQLite error details.
- Documented the observation and recommended remediation.

## Preliminary Security Observation

The product search endpoint returned a detailed database error after receiving malformed input.

This indicates an error-handling weakness requiring further validation. SQL injection has not been conclusively established by this observation alone.

## Recommended Remediation

- Use parameterized SQL queries.
- Handle unexpected input safely.
- Avoid exposing internal database errors to users.
- Keep detailed error information in server-side logs.
- Add regression tests for malformed requests.

## Evidence and Reports

Detailed notes and screenshots are available in the `notes/` and `evidence/` directories.

## Disclaimer

This project was conducted in a local, intentionally vulnerable training environment for educational purposes. Testing should only be performed on systems for which explicit authorization has been granted.

## Author

Harsh Kumbhar — Aspiring VAPT / Cybersecurity Analyst
