# Final VAPT Assessment Report
## OWASP Juice Shop — Product Search SQL Injection

**Project Type:** Web Application Vulnerability Assessment and Penetration Testing (VAPT)  
**Target:** OWASP Juice Shop  
**Target URL:** `http://127.0.0.1:3000`  
**Assessment Environment:** Local lab using Docker and Kali Linux  
**Assessment Type:** Authorized testing of an intentionally vulnerable application  
**Finding ID:** VAPT-01  
**Finding Title:** SQL Error Disclosure in Product Search  
**Status:** Remediation Proposed — Pending Retest

---

## 1. Executive Summary

A security assessment was performed against a locally hosted OWASP Juice Shop application, focusing on the product search API.

Testing identified that the product search endpoint returned an internal server error when supplied with a malformed search value containing a single quote. The response disclosed a SQLite SQL syntax error. Source-code review also identified unsafe construction of the SQL query through string interpolation.

These observations indicate a SQL injection vulnerability in the product search functionality. An attacker may be able to manipulate query behavior, depending on the database driver, query context, and application behavior.

A parameterized SQL query is recommended to separate user-supplied input from SQL instructions. Remediation has not yet been verified through a successful build and retest; therefore, the finding remains open pending verification.

## 2. Scope and Objectives

### In scope
- Local OWASP Juice Shop web application.
- Product search API endpoint:
  `/rest/products/search`
- Handling of search input and SQL query construction.
- Error responses and relevant source-code behavior.

### Objectives
- Identify input-handling weaknesses.
- Reproduce observable security-relevant behavior.
- Review relevant source code.
- Document the finding and recommend remediation.
- Define validation steps for retesting.

### Out of scope
- Testing third-party or public systems.
- Extracting user credentials or database contents.
- Denial-of-service testing.
- Assessing unrelated application components.

## 3. Methodology

The assessment followed a focused, manual testing approach:

1. **Endpoint identification:** Identified the product search API.
2. **Baseline testing:** Submitted a normal search query and observed the response.
3. **Input validation testing:** Submitted malformed search input containing a single quote.
4. **Response analysis:** Compared HTTP status codes and error output.
5. **Source-code review:** Inspected the search route and identified SQL query construction using user-controlled input.
6. **Remediation planning:** Selected parameterized queries as the recommended corrective control.
7. **Retest planning:** Defined the checks required to verify remediation.

## 4. Finding Summary

| ID | Finding | Severity | Status |
|---|---|---|---|
| VAPT-01 | SQL Error Disclosure in Product Search — SQL injection risk | Medium, provisional | Pending Retest |

**Severity rationale:** The application exposes a database syntax error, and source-code review confirms unsafe SQL query construction. Medium is a provisional rating for this lab finding; final severity should be determined using demonstrated impact and the organization's risk-rating methodology. Exploitation and data exposure have not been established by the tests documented here.

## 5. Detailed Finding — VAPT-01

### SQL Error Disclosure in Product Search

**Affected endpoint**

`GET /rest/products/search?q=<search-term>`

**Affected component**

Product search route and its SQL query construction.

### Description

The product search functionality incorporates the supplied search criteria into a SQL statement through string interpolation.

The relevant vulnerable pattern identified during source review was:

```typescript
models.sequelize.query(
  `SELECT * FROM Products
   WHERE ((name LIKE '%${criteria}%'
   OR description LIKE '%${criteria}%')
   AND deletedAt IS NULL)
   ORDER BY name`
)
```

Because user-controlled input is embedded directly into the SQL statement, specially crafted input may alter the intended query structure.

### Evidence and reproduction

**Test A — Normal search input**

Request:

```http
GET /rest/products/search?q=apple HTTP/1.1
Host: 127.0.0.1:3000
```

Observed result:
- HTTP status: `200 OK`
- The endpoint returned JSON containing product search results.

**Test B — Malformed search input**

Request:

```http
GET /rest/products/search?q=apple%27 HTTP/1.1
Host: 127.0.0.1:3000
```

Equivalent command:

```bash
curl -sS -i -G \
  --data-urlencode "q=apple'" \
  http://127.0.0.1:3000/rest/products/search
```

Observed result:
- HTTP status: `500 Internal Server Error`
- The response disclosed a SQLite error containing `SQLITE_ERROR` and a syntax error near the quoted search expression.

**Source-code evidence**

The search route used string interpolation to insert `criteria` directly into the SQL query. This confirms unsafe query construction in the assessed source.

**Evidence files recorded in the project**

- `evidence/burp/sql_error.png`
- `evidence/burp/search-api.png`
- `evidence/burp/burp_1.png`

These files should be included as supporting evidence where available.

### Impact

Depending on the database query context and exploitability, SQL injection can potentially allow an attacker to manipulate database queries, bypass intended search restrictions, or access information beyond the intended functionality.

The observed database error also exposes implementation details that can help an attacker understand the underlying database technology and query behavior.

**Assessment limitation:** The documented tests establish SQL error disclosure and unsafe SQL construction. They do not establish successful extraction of sensitive records, authentication bypass, or compromise of the entire application.

### Root cause

The primary root cause is the use of dynamic SQL string interpolation for user-controlled search criteria rather than parameter binding.

Returning raw internal database errors to clients also reveals unnecessary implementation details.

### Recommended remediation

**1. Use parameterized SQL queries**

Replace direct interpolation with a bound parameter:

```typescript
models.sequelize.query(
  'SELECT * FROM Products WHERE ((name LIKE $search OR description LIKE $search) AND deletedAt IS NULL) ORDER BY name',
  { bind: { search: `%${criteria}%` } }
)
```

This keeps the search value separate from the SQL statement. The exact syntax should be validated against the application's Sequelize version and supported database configuration.

**2. Handle errors safely**

- Return a generic error message to the client.
- Log diagnostic details securely on the server.
- Avoid exposing raw database errors or stack traces in HTTP responses.

**3. Preserve expected functionality**

- Retain existing search-length limits and intended search behavior.
- Confirm that quotes and other special characters are handled as ordinary search input.
- Ensure existing product filtering and ordering behavior remains intact.

**4. Add regression tests**

Test normal input, empty input, quoted input, wildcard characters, long input, and other relevant edge cases.

### Retest procedure

After applying remediation and successfully building the application:

1. Repeat the normal `apple` search and confirm that the expected product-search response still works.
2. Repeat the malformed-input request containing a single quote.
3. Confirm that the request no longer triggers a SQL syntax error or exposes internal database details.
4. Review the updated source and verify that user input is passed through parameter binding rather than SQL string interpolation.
5. Run the application's relevant automated tests, where available.
6. Record the actual response, screenshot, test date, and final outcome.

**Retest result:** Pending.  
**Closure criteria:** The fix builds successfully, normal search behavior is preserved, malformed input does not trigger SQL errors, and source review confirms safe parameter binding.

## 6. Overall Assessment

The assessment identified an input-handling weakness in the product search API. The combination of a reproducible database syntax error and unsafe SQL query construction warrants remediation.

Parameterized SQL, safe error handling, and regression testing are recommended before considering the finding closed.

This assessment was conducted in a controlled local lab against an intentionally vulnerable application. Results should not be generalized to other deployments without independent verification.

## 7. Project Evidence and Deliverables

The project repository should contain:

- This final assessment report.
- The detailed finding write-up.
- API testing notes.
- Burp Suite screenshots and supporting evidence.
- A retest checklist.
- Setup and reproduction instructions.

**Suggested report path:** `notes/final-assessment.md`

## 8. Conclusion

This project demonstrates a focused web application security testing workflow, including API reconnaissance, input manipulation, response analysis, source-code review, vulnerability documentation, and remediation planning.

The principal finding is SQL error disclosure associated with unsafe SQL query construction in the product search route. The proposed remediation is parameterized SQL with safe error handling.

**Final disposition: Remediation proposed; technical retest and closure remain pending.**
