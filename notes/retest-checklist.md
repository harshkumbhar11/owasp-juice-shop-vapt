# Retest Checklist — Product Search Error Handling

## Objective

Verify whether malformed product-search input exposes detailed internal database errors to the client after remediation.

## Environment

- Target: Local OWASP Juice Shop instance
- Endpoint: `/rest/products/search`
- Method: `GET`
- Tool: Burp Suite

## Test Cases

### TC-01: Baseline Search

- [ ] Send `GET /rest/products/search?q=apple`
- [ ] Record the HTTP status and response format
- [ ] Confirm expected search behavior

### TC-02: Malformed Input

- [ ] Send `GET /rest/products/search?q=apple%27`
- [ ] Record the HTTP status and response body
- [ ] Check whether detailed SQLite or stack-trace information is exposed

### TC-03: Post-Remediation Verification

- [ ] Apply a fix in an authorized test copy of the application
- [ ] Repeat TC-01 and TC-02
- [ ] Confirm expected search behavior is preserved
- [ ] Confirm detailed internal errors are not returned to the client
- [ ] Save before-and-after evidence

## Results

- Retest status: Not performed
- Remediation status: Not verified
- SQL injection: Not confirmed by the current evidence

## Acceptance Criteria

The retest passes when malformed input is handled safely, no detailed internal database error is exposed to the client, and normal product search still works.

Do not mark remediation as validated until the post-remediation tests have actually been run and their results documented.
