# Finding 01: SQL Error Disclosure in Product Search

## 1. Project Overview

This finding is part of my Web Application Vulnerability Assessment and Penetration Testing (VAPT) project. Testing was performed against OWASP Juice Shop running locally on Kali Linux.

## 2. Finding Details

- **Finding ID:** VAPT-001
- **Title:** SQL Error Disclosure in Product Search
- **Affected Endpoint:** `/rest/products/search`
- **HTTP Method:** GET
- **Affected Parameter:** `q`
- **Testing Tool:** Burp Suite Professional
- **Environment:** Local OWASP Juice Shop lab
- **Status:** Potential vulnerability; further validation required

## 3. Objective

To observe how the product search endpoint handles malformed input and whether the application exposes database error details to the client.

## 4. Steps to Reproduce

1. Start the local OWASP Juice Shop application.
2. Open Burp Suite and navigate to Proxy > HTTP history.
3. Find the product search request.
4. Send the request to Repeater.
5. Send a baseline request using the search term `apple`.
6. Send a test request using the encoded single quote in `apple%27`.
7. Compare the HTTP status codes and response bodies.

## 5. Observed Results

### Baseline Request

- **Request:** `GET /rest/products/search?q=apple`
- **HTTP Status:** 200 OK
- **Response:** JSON containing matching product records.

### Malformed Input Request

- **Request:** `GET /rest/products/search?q=apple%27`
- **HTTP Status:** 500 Internal Server Error
- **Observed Error:** `SQLITE_ERROR: near "'%'" : syntax error`
- **Response Type:** HTML error page containing database error details.

## 6. Security Impact

Exposing detailed database errors may reveal information about the backend database and how the application processes search input.

This information could assist further investigation of input-handling weaknesses.

**Important:** This test demonstrates an error-handling weakness, but it does not independently confirm SQL injection. Additional evidence is required before assigning a definitive vulnerability classification or severity.

### Preliminary Risk Assessment

- **Preliminary severity:** Low — provisional
- **Confidence:** Moderate that detailed database error disclosure occurred
- **Evidence:** The malformed search request returned HTTP 500 and exposed a SQLite syntax error.
- **Potential impact:** An attacker may learn limited backend implementation details that could assist further investigation.
- **Limitations:** No sensitive data exposure or successful SQL injection has been demonstrated by this test. The actual impact depends on the deployment and additional evidence.
- **Severity status:** Provisional; reassess after evaluating the complete response and application context.

This rating is an initial assessment, not a confirmed CVSS score. The observation should be reported as database error disclosure / improper error handling unless further testing establishes a more serious vulnerability.

## 7. Recommended Remediation

- Use parameterized SQL queries or prepared statements.
- Validate and safely handle user-supplied search input.
- Return generic error messages to clients.
- Record detailed database errors in server-side logs.
- Add regression tests for malformed search input.
- Verify that unexpected input does not cause unhandled server errors.

## 8. Evidence Collected

- Baseline search request and response captured in Burp Suite.
- Malformed input response returned HTTP 500.
- SQLite error message observed in the response.
- Screenshot should be stored at `evidence/burp/finding-01-error.png`.

## 9. Conclusion

The product search endpoint returned a detailed SQLite error after receiving malformed input. The behavior indicates an error-handling issue that requires remediation and further validation.

The assessment was performed only against the local OWASP Juice Shop practice environment.

## 10. Validation Status

- [x] Baseline request captured
- [x] Malformed input response captured
- [x] Error message documented
- [x] Screenshot saved in the evidence directory
- [ ] Remediation applied and validated
- [ ] Final severity assigned after further assessment
