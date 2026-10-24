## Test Case 1: Product Search API

### Objective
Inspect the product search endpoint and document its normal behavior.

### Environment
- Application: OWASP Juice Shop
- Operating System: Kali Linux
- Tool: Burp Suite Professional
- Target: http://127.0.0.1:3000

### Request
- Method: GET
- Endpoint: /rest/products/search
- Query parameter: q
- Test input: apple

### Observed Response
- HTTP status: 200 OK
- Content-Type: application/json; charset=utf-8
- Result: Three matching product records returned
- Response format: JSON

### Security Observations
- The endpoint accepts a search query and returns product information.
- The response contains product names, descriptions, prices, and image filenames.
- The response includes Access-Control-Allow-Origin: *.
- The security impact of the CORS header has not yet been established.
- No confirmed vulnerability identified in this test.

### Conclusion
The normal product search request was successfully captured and analyzed using Burp Suite. Further input validation and security testing are required.

### Evidence
- Request and response captured in Burp Suite HTTP History.
- Screenshot to be saved as evidence/burp/search-api.png.
### Additional Test: CORS Header Verification

**Request**

- Method: `GET`
- Endpoint: `/rest/products/search?q=apple`
- Tool: `curl`
- Target: Local OWASP Juice Shop (`127.0.0.1:3000`)

**Observed Results**

- HTTP status: `200 OK`
- CORS header: `Access-Control-Allow-Origin: *`
- Content type: `application/json; charset=utf-8`
- Response: Three product records

**Security Assessment**

The wildcard CORS header permits cross-origin browser access to this response where browser CORS rules allow it. The observed response contains ordinary product information, and this test did not demonstrate exposure of sensitive data or unauthorized access.

**Conclusion:** Record this as a configuration observation, not a confirmed vulnerability. Further assessment would be needed if authenticated or sensitive endpoints use the same policy.

**Evidence:** Terminal response captured on 24 October 2026. Save a screenshot only if one has actually been captured.
