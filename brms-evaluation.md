# Evaluation of business rule

## Connector

To evaluate a business rule, use the `IBusinessRuleEvaluation` interface.

```csharp
/// <summary>
/// Business rule evaluation operations
/// </summary>
public interface IBusinessRuleEvaluation
{
    /// <summary>
    /// Evaluates a specified rule with the provided data.
    /// </summary>
    /// <param name="targetId">Identifier of the target to which the rule is assigned.</param>
    /// <param name="data">Data to evaluate against the rule.</param>
    /// <param name="cancellationToken">Cancellation token.</param>
    /// <returns>Rule evaluation result.</returns>
    /// <exception cref="ArgumentNullException"></exception>
    /// <exception cref="ArgumentException"></exception>
    /// <exception cref="AuthenticationException"></exception>
    /// <exception cref="UnauthorizedAccessException"></exception>
    /// <exception cref="InvalidOperationException"></exception>
    /// <exception cref="BRMSInternalException"></exception>
    /// <exception cref="BRMSDataException"></exception>
    /// <exception cref="ApiException"></exception>
    public Task<JsonElement> EvaluateRuleAsync(
        string targetId,
        JsonElement data,
        CancellationToken cancellationToken = default);

    /// <summary>
    /// Evaluates an ad-hoc rule with the provided data.
    /// </summary>
    /// <param name="ruleValue">The rule value in ZenEngine format.</param>
    /// <param name="data">Data to evaluate against the rule.</param>
    /// <param name="cancellationToken">Cancellation token.</param>
    /// <returns>Rule evaluation result.</returns>
    /// <exception cref="ArgumentNullException"></exception>
    /// <exception cref="ArgumentException"></exception>
    /// <exception cref="AuthenticationException"></exception>
    /// <exception cref="UnauthorizedAccessException"></exception>
    /// <exception cref="InvalidOperationException"></exception>
    /// <exception cref="BRMSInternalException"></exception>
    /// <exception cref="ApiException"></exception>
    public Task<JsonElement> EvaluateAdhocRuleAsync(
        JsonElement ruleValue,
        JsonElement data,
        CancellationToken cancellationToken = default);
}
```

### EvaluateRuleAsync

__Example__: Evaluate a rule

```csharp
internal sealed record OrderApprovalResult(bool Approved);

// IBusinessRuleEvaluation is resolved from DI (or use IBRMSClient.Evaluation)
internal sealed class OrderApprovalService(IBusinessRuleEvaluation evaluation)
{
    public async Task<bool> IsOrderApprovedAsync(Order order, CancellationToken ct)
    {
        var payload = JsonSerializer.SerializeToElement(new
        {
            orderId = order.Id,
            amount = order.Amount,
            customer = new { order.CustomerId, order.Segment }
        });

        var result = await evaluation.EvaluateRuleAsync(
            targetId: "Orders/Approval", // app-defined identifier
            data: payload,
            cancellationToken: ct);

        return JsonSerializer.Deserialize<OrderApprovalResult>(result).Approved;
    }
}
```

Error handling behavior

- `ArgumentNullException` when `targetId` or `data` is null.
- `ArgumentException` when input data is invalid according to BRMS validation. Validation details are attached to the exception's Data dictionary (key = field, value = error).
- `AuthenticationException` on 401 responses; ensure your token provider is configured.
- `UnauthorizedAccessException` on 403 responses.
- `InvalidOperationException` on 404 when no rule exists for the given `targetId`.
- `BRMSInternalException` on 500 responses with `Code` populated from server payload.
- `BRMSDataException` on 501 when a rule value is missing for the requested context.
- `ApiException` on other http statuses.

### EvaluateAdhocRuleAsync

__Example__: Evaluate an ad-hoc rule

```csharp
internal sealed class RuleTestingService(IBusinessRuleEvaluation evaluation)
{
    public async Task<JsonElement> TestRuleAsync(
        JsonElement ruleValue,
        JsonElement testData,
        CancellationToken ct)
    {
        var result = await evaluation.EvaluateAdhocRuleAsync(
            ruleValue: ruleValue,      // ZenEngine rule definition
            data: testData,             // test input data
            cancellationToken: ct);

        return result;
    }
}
```

Error handling behavior

- `ArgumentNullException` when `ruleValue` or `data` is null.
- `ArgumentException` when the rule value or input data is invalid. Validation details are attached to the exception's Data dictionary (key = field, value = error).
- `AuthenticationException` on 401 responses; ensure your token provider is configured.
- `UnauthorizedAccessException` on 403 responses.
- `InvalidOperationException` on 404 responses.
- `BRMSInternalException` on 500 responses with `Code` populated from server payload.
- `ApiException` on other http statuses.


## HTTP API

### Evaluate rule endpoint

- Method: POST
- URL: `api/v1/targets/{targetId}/rule-evaluations`
- Auth: Requires authentication and application declaration (policy: `ApplicationDeclared`).
- Headers:
    - `Authorization: Bearer << token >>`
    - `X-Application-Code: << your-app-code >>`

Path parameters

- `targetId` (string, required): Identifier of the target to which the rule is assigned. Choose a stable convention, e.g. `Orders/Approval`.

Request body

- Arbitrary JSON payload (as object).

Responses

- 200 OK: JSON result.
- 400 Bad Request: Invalid JSON body or invalid request data.
- 401 Unauthorized: Missing/invalid bearer token.
- 403 Forbidden: Authenticated but not authorized (application not declared or insufficient permissions).
- 404 Not Found: Rule not found.
- 500 Internal Server Error: Unhandled error while processing the rule.
- 501 Not Implemented: Rule value not found for the requested context.

### Evaluate ad-hoc rule endpoint

- Method: POST
- URL: `api/v1/adhoc-rule-evaluations`
- Auth: Requires authentication (no application declaration required).
- Headers:
    - `Authorization: Bearer << token >>`

Request body

JSON object with the following properties:
- `ruleValue` (object, required): The rule value in ZenEngine format to evaluate.
- `data` (object, required): Arbitrary JSON payload containing the input data for evaluation.

Example:
```json
{
  "ruleValue": {
    "contentType": "application/vnd.gorules.decision",
    "nodes": [...],
    "edges": [...]
  },
  "data": {
    "orderId": "12345",
    "amount": 1500
  }
}
```

Responses

- 200 OK: JSON result.
- 400 Bad Request: Invalid JSON body, invalid rule value, or invalid request data.
- 401 Unauthorized: Missing/invalid bearer token.
- 403 Forbidden: Authenticated but not authorized.
- 404 Not Found: Resource not found.
- 500 Internal Server Error: Unhandled error while processing the rule.