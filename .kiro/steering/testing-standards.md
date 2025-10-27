---
inclusion: fileMatch
fileMatchPattern: "**/*.test.*"
title: Testing Standards
summary: BDD testing standards, contract tests, version matrix, and webhook tests.
tags: [testing, bdd, api, versioning]
---

# Testing Standards

## BDD (Behavior-Driven Development) Standards

### Python Test Structure
- **MUST** use Given/When/Then format for all test methods
- **MUST NOT** include docstrings on test classes or methods
- **MUST** use descriptive test method names that clearly indicate what is being tested

### Python Comment Format
```python
def test_method_name(self):
    # Given: Context and preconditions
    setup_code()
    
    # When: Action being performed
    result = action()
    
    # Then: Expected outcome
    assert result == expected
    
    # And: Additional assertions (optional)
    assert additional_condition
```

### Python Test Organization
- **MUST** group related tests in classes (e.g., `TestJWTUtilities`, `TestKasClient`)
- **MUST** use clear, descriptive test method names
- **SHOULD** follow the pattern: `test_<component>_<scenario>`

### Python Test Execution
- **MUST NOT** use `pytest.skip()` in tests
- **RATIONALE**: Tests run in consistent environments (local + CI), so skipped tests are always skipped and provide no value
- **ALTERNATIVE**: Remove tests that would be skipped, or restructure to always run meaningful assertions

### Python Examples
```python
class TestJWTUtilities:
    def test_get_payload_from_jwt_valid(self):
        # Given: A valid JWT token with test payload
        payload = {"appian_site_id": 12345, "aud": "test"}
        token = jwt.encode(payload, "secret", algorithm="HS256")

        # When: The payload is extracted from the JWT
        result = get_payload_from_jwt(token)

        # Then: The payload should be correctly extracted
        assert result["appian_site_id"] == 12345
        assert result["aud"] == "test"
```

### Go Test Structure
- **MUST** use Given/When/Then format for all test functions
- **MUST** use descriptive test function names following Go conventions (`TestFunctionName_Scenario`)
- **MUST** structure tests with clear BDD comments for setup, action, and verification phases
- **SHOULD** use table-driven tests where appropriate with BDD comments in the test execution

### Go Comment Format
```go
func TestFunctionName_Scenario(t *testing.T) {
    // Given: Context and preconditions
    setupCode()
    
    // When: Action being performed
    result := performAction()
    
    // Then: Expected outcome verification
    if result != expected {
        t.Errorf("Expected %v, got %v", expected, result)
    }
}
```

### Go Table-Driven Test Format
```go
func TestBuildModel(t *testing.T) {
    tests := []struct {
        name          string
        modelName     string
        expectedError bool
    }{
        {
            name:          "single model",
            modelName:     "claude-3-sonnet",
            expectedError: false,
        },
    }

    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            // Given: a global state with specific model configuration
            withGlobalState(t, &GlobalState{ModelName: tt.modelName}, func() {
                // When: building the model configuration
                model, err := buildModel()

                // Then: should handle model selection and validation appropriately
                if tt.expectedError {
                    assert.Error(t, err, "expected error for test case")
                    return
                }
                assert.NoError(t, err, "building model should not error")
            })
        })
    }
}
```

### Go Test Organization
- **MUST** place test files alongside source files with `_test.go` suffix
- **MUST** use package-level test functions with descriptive names
- **SHOULD** group related functionality tests in the same test file
- **SHOULD** use helper functions for common setup/teardown patterns

## API Testing Standards

### Contract Tests
- **MUST** run for **all supported versions** (matrix).
- **MUST** validate success codes, error schema, and pagination invariants (`has_more`, cursor behavior).
- **MUST** ensure additive changes in latest version do not break older versions.

### Idempotency Tests
- Retry POST with same `Idempotency-Key` returns identical status/body/headers.
- Reuse same key with different body yields `409` idempotency error.

### Webhook Tests
- Verify signature rejection on tampered payloads and expired timestamps.
- Ensure deduplication by `event.id`.
- Simulate retry/backoff; handler remains idempotent and fast to ACK.

## Performance/SLO
- Budget timeouts per endpoint; assert P95/P99 in CI perf tests where applicable.
- Enforce request/response size limits; document and test truncation/streaming behavior.

## Golden Examples
- Keep request/response fixtures per version under `tests/golden/<version>/...`.
- Regenerate fixtures when releasing a new version; retain old fixtures until version sunset.

---
Refs: #[[file:api/openapi.yaml]] #[[file:tests/README.md]]
