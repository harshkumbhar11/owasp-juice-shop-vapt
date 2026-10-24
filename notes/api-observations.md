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
- Screenshot to be saved as evidence/burp/product-search.png.
