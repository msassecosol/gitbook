[[_TOC_]]

# Management of business rules

## Connector

To manage business rules, use the `IBusinessRuleManagement` interface.

```csharp
/// <summary>
/// Business rule management operations.
/// </summary>
public interface IBusinessRuleManagement
{
    /// <summary>
    /// Creates a new rule for a specified target.
    /// </summary>
    /// <param name="targetId">Identifier of a target the rule will be assigned to.</param>
    /// <param name="rule">The rule to create.</param>
    /// <param name="cancellationToken">Cancellation token.</param>
    /// <returns>The created rule.</returns>
    /// <exception cref="ArgumentNullException"></exception>
    /// <exception cref="ArgumentException"></exception>
    /// <exception cref="AuthenticationException"></exception>
    /// <exception cref="UnauthorizedAccessException"></exception>
    /// <exception cref="InvalidOperationException"></exception>
    /// <exception cref="BRMSInternalException"></exception>
    /// <exception cref="BRMSDataException"></exception>
    /// <exception cref="ApiException"></exception>
    public Task<RuleResponse> CreateRuleAsync(
        string targetId,
        CreateRuleRequest rule,
        CancellationToken cancellationToken = default);

    /// <summary>
    /// Retrieves the rule associated with a specified target.
    /// </summary>
    /// <param name="targetId">Identifier of a target the rule is assigned to.</param>
    /// <param name="cancellationToken">Cancellation token.</param>
    /// <returns>The rule associated with the target.</returns>
    public Task<RuleResponse> GetRuleAsync(
        string targetId,
        CancellationToken cancellationToken = default);

    /// <summary>
    /// Updates the rule associated with a specified target.
    /// </summary>
    /// <param name="targetId">Identifier of a target the rule is assigned to.</param>
    /// <param name="updates">The updates to apply to the rule using fluent API (name, description, status, schemas).</param>
    /// <param name="cancellationToken">Cancellation token.</param>
    /// <returns>The updated rule.</returns>
    /// <exception cref="ArgumentNullException">Thrown when <paramref name="targetId"/> or <paramref name="updates"/> is null.</exception>
    /// <exception cref="ArgumentException">Thrown when the update data is invalid.</exception>
    /// <exception cref="AuthenticationException">Thrown when the request is unauthorized.</exception>
    /// <exception cref="UnauthorizedAccessException">Thrown when the user is not authorized to execute the request.</exception>
    /// <exception cref="InvalidOperationException">Thrown when the rule for the target is not found.</exception>
    /// <exception cref="BRMSInternalException">Thrown when the server encounters an internal error.</exception>
    /// <exception cref="ApiException"></exception>
    public Task<RuleResponse> UpdateRuleAsync(
        string targetId,
        RuleUpdates updates,
        CancellationToken cancellationToken = default);

    /// <summary>
    /// Retrieves all rule values.
    /// </summary>
    /// <param name="cancellationToken">Cancellation token.</param>
    /// <returns>A collection of all rule values for the application. The response contains polymorphic items with type discriminator ($type): DEFAULT, TENANT, ORGANIZATION, or DRAFT. Each item includes common properties (targetId, name, description, status, createdOn, createdBy) and type-specific properties (organizationId for ORGANIZATION, id/organizationId/draftDescription for DRAFT).</returns>
    /// <exception cref="AuthenticationException"></exception>
    /// <exception cref="UnauthorizedAccessException"></exception>
    /// <exception cref="BRMSInternalException"></exception>
    /// <exception cref="ApiException"></exception>
    public Task<GetAllApplicationRulesValuesResponse> GetAllRuleValuesAsync(
        CancellationToken cancellationToken = default);

    /// <summary>
    /// Creates a new draft rule value for a specified target.
    /// </summary>
    /// <param name="targetId">Identifier of a target the draft rule value will be assigned to.</param>
    /// <param name="draftRuleValue">The draft rule value to create.</param>
    /// <param name="cancellationToken">Cancellation token.</param>
    /// <returns>The created draft rule value.</returns>
    /// <exception cref="ArgumentNullException"></exception>
    /// <exception cref="ArgumentException"></exception>
    /// <exception cref="AuthenticationException"></exception>
    /// <exception cref="UnauthorizedAccessException"></exception>
    /// <exception cref="BRMSInternalException"></exception>
    /// <exception cref="ApiException"></exception>
    public Task<DraftRuleValueResponse> CreateDraftRuleValueAsync(
        string targetId,
        CreateTargetDraftRuleValueRequest draftRuleValue,
        CancellationToken cancellationToken = default);

    /// <summary>
    /// Retrieves the draft rule value.
    /// </summary>
    /// <param name="id">Identifier of the draft rule value.</param>
    /// <param name="cancellationToken">Cancellation token.</param>
    /// <returns>The draft rule value.</returns>
    public Task<DraftRuleValueResponse> GetDraftRuleValueAsync(
        string id,
        CancellationToken cancellationToken = default);

    /// <summary>
    /// Updates the draft rule value.
    /// </summary>
    /// <param name="id">Identifier of the draft rule value.</param>
    /// <param name="updates">The updates to apply to the draft rule value using fluent API (value, description, organizationId).</param>
    /// <param name="cancellationToken">Cancellation token.</param>
    /// <returns>The updated draft rule value.</returns>
    /// <exception cref="ArgumentNullException">Thrown when <paramref name="id"/> or <paramref name="updates"/> is null.</exception>
    /// <exception cref="ArgumentException">Thrown when the update data is invalid.</exception>
    /// <exception cref="AuthenticationException">Thrown when the request is unauthorized.</exception>
    /// <exception cref="UnauthorizedAccessException">Thrown when the user is not authorized to execute the request.</exception>
    /// <exception cref="InvalidOperationException">Thrown when the draft rule value is not found.</exception>
    /// <exception cref="BRMSInternalException">Thrown when the server encounters an internal error.</exception>
    /// <exception cref="ApiException"></exception>
    public Task<DraftRuleValueResponse> UpdateDraftRuleValueAsync(
        string id,
        DraftRuleValueUpdates updates,
        CancellationToken cancellationToken = default);

    /// <summary>
    /// Deletes the draft rule value.
    /// </summary>
    /// <param name="id">Identifier of the draft rule value.</param>
    /// <param name="cancellationToken">Cancellation token.</param>
    public Task DeleteDraftRuleValueAsync(
        string id,
        CancellationToken cancellationToken = default);

    /// <summary>
    /// Retrieves all rules for the application, optionally paged.
    /// </summary>
    /// <param name="paging">Paging filter (offset, limit).</param>
    /// <param name="cancellationToken">Cancellation token.</param>
    /// <returns>Paged list of rules.</returns>
    /// <exception cref="AuthenticationException"></exception>
    /// <exception cref="UnauthorizedAccessException"></exception>
    /// <exception cref="BRMSInternalException"></exception>
    /// <exception cref="ApiException"></exception>
    public Task<GetAllApplicationRulesResponse> GetAllRulesAsync(
        PagingFilter? paging = null,
        CancellationToken cancellationToken = default);

    /// <summary>
    /// Sets the default rule value for a specified target.
    /// </summary>
    /// <param name="targetId">Identifier of a target the rule is assigned to.</param>
    /// <param name="value">Default rule value to set.</param>
    /// <param name="cancellationToken">Cancellation token.</param>
    /// <returns>The created or updated default rule value.</returns>
    /// <exception cref="ArgumentNullException"></exception>
    /// <exception cref="ArgumentException"></exception>
    /// <exception cref="AuthenticationException"></exception>
    /// <exception cref="UnauthorizedAccessException"></exception>
    /// <exception cref="InvalidOperationException"></exception>
    /// <exception cref="BRMSInternalException"></exception>
    /// <exception cref="ApiException"></exception>
    public Task<RuleValueResponse> SetDefaultRuleValueAsync(
        string targetId,
        JsonElement value,
        CancellationToken cancellationToken = default);

    /// <summary>
    /// Gets the default rule value for a specified target.
    /// </summary>
    /// <param name="targetId">Identifier of a target the rule is assigned to.</param>
    /// <param name="cancellationToken">Cancellation token.</param>
    /// <returns>The default rule value.</returns>
    /// <exception cref="ArgumentNullException"></exception>
    /// <exception cref="AuthenticationException"></exception>
    /// <exception cref="UnauthorizedAccessException"></exception>
    /// <exception cref="InvalidOperationException"></exception>
    /// <exception cref="BRMSInternalException"></exception>
    /// <exception cref="ApiException"></exception>
    public Task<RuleValueResponse> GetDefaultRuleValueAsync(
        string targetId,
        CancellationToken cancellationToken = default);

    /// <summary>
    /// Sets the tenant rule value for a specified target.
    /// </summary>
    /// <param name="targetId">Identifier of a target the rule is assigned to.</param>
    /// <param name="value">Tenant rule value to set.</param>
    /// <param name="cancellationToken">Cancellation token.</param>
    /// <returns>The created or updated tenant rule value.</returns>
    /// <exception cref="ArgumentNullException"></exception>
    /// <exception cref="ArgumentException"></exception>
    /// <exception cref="AuthenticationException"></exception>
    /// <exception cref="UnauthorizedAccessException"></exception>
    /// <exception cref="InvalidOperationException"></exception>
    /// <exception cref="BRMSInternalException"></exception>
    /// <exception cref="ApiException"></exception>
    public Task<RuleValueResponse> SetTenantRuleValueAsync(
        string targetId,
        JsonElement value,
        CancellationToken cancellationToken = default);

    /// <summary>
    /// Gets the tenant rule value for a specified target.
    /// </summary>
    /// <param name="targetId">Identifier of a target the rule is assigned to.</param>
    /// <param name="cancellationToken">Cancellation token.</param>
    /// <returns>The tenant rule value, or null if no tenant-specific value exists.</returns>
    /// <exception cref="ArgumentNullException"></exception>
    /// <exception cref="AuthenticationException"></exception>
    /// <exception cref="UnauthorizedAccessException"></exception>
    /// <exception cref="InvalidOperationException"></exception>
    /// <exception cref="BRMSInternalException"></exception>
    /// <exception cref="ApiException"></exception>
    public Task<RuleValueResponse?> GetTenantRuleValueAsync(
        string targetId,
        CancellationToken cancellationToken = default);

    /// <summary>
    /// Deletes the tenant rule value for a specified target.
    /// </summary>
    /// <param name="targetId">Identifier of a target the rule is assigned to.</param>
    /// <param name="cancellationToken">Cancellation token.</param>
    /// <exception cref="ArgumentNullException"></exception>
    /// <exception cref="AuthenticationException"></exception>
    /// <exception cref="UnauthorizedAccessException"></exception>
    /// <exception cref="InvalidOperationException"></exception>
    /// <exception cref="BRMSInternalException"></exception>
    /// <exception cref="ApiException"></exception>
    public Task DeleteTenantRuleValueAsync(
        string targetId,
        CancellationToken cancellationToken = default);

    /// <summary>
    /// Sets the organization rule value for a specified target.
    /// </summary>
    /// <param name="targetId">Identifier of a target the rule is assigned to.</param>
    /// <param name="organizationId">Identifier of the organization the rule value is assigned to.</param>
    /// <param name="value">Organization rule value to set.</param>
    /// <param name="cancellationToken">Cancellation token.</param>
    /// <returns>The created or updated organization rule value.</returns>
    /// <exception cref="ArgumentNullException"></exception>
    /// <exception cref="ArgumentException"></exception>
    /// <exception cref="AuthenticationException"></exception>
    /// <exception cref="UnauthorizedAccessException"></exception>
    /// <exception cref="InvalidOperationException"></exception>
    /// <exception cref="BRMSInternalException"></exception>
    /// <exception cref="ApiException"></exception>
    public Task<RuleValueResponse> SetOrganizationRuleValueAsync(
        string targetId,
        string organizationId,
        JsonElement value,
        CancellationToken cancellationToken = default);

    /// <summary>
    /// Gets the organization rule value for a specified target.
    /// </summary>
    /// <param name="targetId">Identifier of a target the rule is assigned to.</param>
    /// <param name="organizationId">Identifier of the organization the rule value is assigned to.</param>
    /// <param name="cancellationToken">Cancellation token.</param>
    /// <returns>The organization rule value, or null if no organization-specific value exists.</returns>
    /// <exception cref="ArgumentNullException"></exception>
    /// <exception cref="AuthenticationException"></exception>
    /// <exception cref="UnauthorizedAccessException"></exception>
    /// <exception cref="InvalidOperationException"></exception>
    /// <exception cref="BRMSInternalException"></exception>
    /// <exception cref="ApiException"></exception>
    public Task<RuleValueResponse?> GetOrganizationRuleValueAsync(
        string targetId,
        string organizationId,
        CancellationToken cancellationToken = default);

    /// <summary>
    /// Gets the history of organization rule values for a specified target.
    /// </summary>
    /// <param name="targetId">Identifier of a target the rule is assigned to.</param>
    /// <param name="organizationId">Identifier of the organization the rule value is assigned to.</param>
    /// <param name="cancellationToken">Cancellation token.</param>
    /// <returns>The history of organization rule values.</returns>
    /// <exception cref="ArgumentNullException"></exception>
    /// <exception cref="AuthenticationException"></exception>
    /// <exception cref="UnauthorizedAccessException"></exception>
    /// <exception cref="InvalidOperationException"></exception>
    /// <exception cref="BRMSInternalException"></exception>
    /// <exception cref="ApiException"></exception>
    public Task<GetOrganizationRuleValueHistoryResponse> GetOrganizationRuleValueHistoryAsync(
        string targetId,
        string organizationId,
        CancellationToken cancellationToken = default);

    /// <summary>
    /// Deletes the organization rule value for a specified target.
    /// </summary>
    /// <param name="targetId">Identifier of a target the rule is assigned to.</param>
    /// <param name="organizationId">Identifier of the organization the rule value is assigned to.</param>
    /// <param name="cancellationToken">Cancellation token.</param>
    /// <exception cref="ArgumentNullException"></exception>
    /// <exception cref="AuthenticationException"></exception>
    /// <exception cref="UnauthorizedAccessException"></exception>
    /// <exception cref="InvalidOperationException"></exception>
    /// <exception cref="BRMSInternalException"></exception>
    /// <exception cref="ApiException"></exception>
    public Task DeleteOrganizationRuleValueAsync(
        string targetId,
        string organizationId,
        CancellationToken cancellationToken = default);
}
```

__Examples__

Create a rule and retrieve it:

```csharp
// IBusinessRuleManagement is resolved from DI (or use IBRMSClient.Management)
internal sealed class RuleProvisioningService(IBusinessRuleManagement management)
{
    public async Task<RuleResponse> EnsureOrderApprovalRuleAsync(CancellationToken ct)
    {
        const string targetId = "Orders/Approval"; // app-defined identifier

        var request = new CreateRuleRequest
        {
            Name = new LocalizedValue<string>(("en-US", "Order Approval")),
            Description = new LocalizedValue<string>(("en-US", "Determines whether an order requires approval.")),
            Status = RuleStatusModel.Active,
            // JSON Schemas as JsonElement
            InputDataSchema = JsonSerializer.SerializeToElement(new
            {
                type = "object",
                properties = new
                {
                    orderId = new { type = "string" },
                    amount = new { type = "number" },
                    customer = new { type = "object" }
                }
            }),
            OutputDataSchema = JsonSerializer.SerializeToElement(new
            {
                type = "object",
                properties = new
                {
                    approved = new { type = "boolean" }
                }
            }),
            DefaultValue = JsonSerializer.SerializeToElement(new { approved = false }),
            ForerunnerId = null
        };

        try
        {
            // Creates a new rule; throws InvalidOperationException if one already exists for the targetId
            return await management.CreateRuleAsync(targetId, request, ct);
        }
        catch (InvalidOperationException)
        {
            // Rule already exists — fetch it
            return await management.GetRuleAsync(targetId, ct);
        }
    }
}
```

Retrieve all rules (with optional paging):

```csharp
// IBusinessRuleManagement is resolved from DI (or use IBRMSClient.Management)
internal sealed class RuleListingService(IBusinessRuleManagement management)
{
    public async Task<GetAllApplicationRulesResponse> GetAllRulesAsync(CancellationToken ct)
    {
        // Example: get first 10 rules
        var paging = new PagingFilter { Offset = 0, Limit = 10 };
        return await management.GetAllRulesAsync(paging, ct);
    }
}
```

Update a rule:

```csharp
// IBusinessRuleManagement is resolved from DI (or use IBRMSClient.Management)
internal sealed class RuleUpdateService(IBusinessRuleManagement management)
{
    public async Task<RuleResponse> UpdateOrderApprovalRuleAsync(CancellationToken ct)
    {
        const string targetId = "Orders/Approval";

        // Build the updates using the fluent API
        var updates = new RuleUpdates()
            .UpdateStatus(RuleStatusModel.Active)
            .UpdateDescription(desc => desc.AddOrUpdate("en-US", "Updated description"))
            .UpdateName(name => name.AddOrUpdate("en-US", "Order Approval v2"));

        // Apply the updates
        return await management.UpdateRuleAsync(targetId, updates, ct);
    }

    public async Task<RuleResponse> UpdateRuleSchemasAsync(string targetId, CancellationToken ct)
    {
        // You can also update schemas (be careful with breaking changes)
        var updates = new RuleUpdates()
            .UpdateInputDataSchema(JsonSerializer.SerializeToElement(new
            {
                type = "object",
                properties = new
                {
                    orderId = new { type = "string" },
                    amount = new { type = "number" },
                    priority = new { type = "string" }
                }
            }))
            .UpdateOutputDataSchema(JsonSerializer.SerializeToElement(new
            {
                type = "object",
                properties = new
                {
                    approved = new { type = "boolean" },
                    reason = new { type = "string" }
                }
            }));

        return await management.UpdateRuleAsync(targetId, updates, ct);
    }
}
```

The `RuleUpdates` class provides a fluent API for building JSON Patch operations:
- `UpdateName(Action<LocalizedValueUpdates<string>>)` - Update individual localizations of the name
- `ReplaceName(LocalizedValue<string>)` - Replace the entire name with a new localized value
- `UpdateDescription(Action<LocalizedValueUpdates<string>>)` - Update individual localizations of the description
- `ReplaceDescription(LocalizedValue<string>)` - Replace the entire description with a new localized value
- `UpdateStatus(RuleStatusModel)` - Update the rule status
- `UpdateInputDataSchema(JsonElement)` - Update the input JSON Schema
- `UpdateOutputDataSchema(JsonElement)` - Update the output JSON Schema

Update a draft rule value:

```csharp
// IBusinessRuleManagement is resolved from DI (or use IBRMSClient.Management)
internal sealed class DraftRuleValueUpdateService(IBusinessRuleManagement management)
{
    public async Task<DraftRuleValueResponse> UpdateDraftAsync(string draftId, CancellationToken ct)
    {
        // Build the updates using the fluent API
        var updates = new DraftRuleValueUpdates()
            .UpdateValue(JsonSerializer.SerializeToElement(new
            {
                approved = true,
                reason = "High-value customer"
            }))
            .UpdateDescription("Draft for VIP customers")
            .UpdateOrganizationId("org-123");

        // Apply the updates
        return await management.UpdateDraftRuleValueAsync(draftId, updates, ct);
    }

    public async Task<DraftRuleValueResponse> UpdateDraftValueOnlyAsync(string draftId, CancellationToken ct)
    {
        // Update just the decision value
        var updates = new DraftRuleValueUpdates()
            .UpdateValue(JsonSerializer.SerializeToElement(new { approved = false }));

        return await management.UpdateDraftRuleValueAsync(draftId, updates, ct);
    }
}
```

The `DraftRuleValueUpdates` class provides a fluent API for building JSON Patch operations:
- `UpdateValue(JsonElement)` - Update the decision value (JSON matching the rule's output schema)
- `UpdateDescription(string?)` - Update the draft description
- `UpdateOrganizationId(string?)` - Update the organization ID associated with the draft

Error handling behavior

- `ArgumentNullException` when targetId or rule is null.
- `ArgumentException` when request data is invalid according to BRMS validation. Validation details are attached to the exception's Data dictionary (key = field, value = error).
- `AuthenticationException` on 401 responses; ensure your token provider is configured.
- `UnauthorizedAccessException` on 403 responses.
- `InvalidOperationException`:
  - `CreateRuleAsync`: on 409 when a rule already exists for the given targetId.
  - `GetRuleAsync`: on 404 when no rule exists for the given targetId.
  - `UpdateRuleAsync`: on 404 when no rule exists for the given targetId.
  - `GetDraftRuleValueAsync`: on 404 when the draft rule value is not found.
  - `UpdateDraftRuleValueAsync`: on 404 when the draft rule value is not found.
  - `DeleteDraftRuleValueAsync`: on 404 when the draft rule value is not found.
- `BRMSInternalException` on 500 responses with Code populated from server payload.
- `BRMSDataException` reserved for data issues (none currently emitted by these endpoints, included for completeness).
- `ApiException` on other HTTP statuses.

Notes and tips

- Target IDs are free-form strings; choose a stable naming convention (e.g., Feature/Area/Decision) to simplify governance.
- Use `LocalizedValue<string>` to provide multilingual names/descriptions. The constructor accepts tuples: new `LocalizedValue<string>(("en-US", "Name"), ("cs-CZ", "Název"))`.
- `InputDataSchema` and `OutputDataSchema` are JSON Schema strings. Keep in mind that changing their values later can be problematic:
  - Removing fields from `InputDataSchema` or making required fields optional may cause breaking changes, as the rule evaluation process can rely on their presence.
  - Adding required fields to `OutputDataSchema` or making optional fields required may cause breaking changes, as the rule evaluation process may not produce those fields.

## HTTP API

All endpoints require authentication and application declaration (policy: ApplicationDeclared).

Common headers

- Authorization: Bearer << token >>
- X-Application-Code: << your-app-code >>

### Create rule endpoint

- Method: POST
- URL: `api/v1/targets/{targetId}/rules`
- Consumes: application/json
- Produces: application/json

Path parameters

- targetId (string, required): Identifier of the target to which the rule will be assigned. Example: `Orders/Approval`.

Request body (application/json)

- Shape matches CreateRuleRequest:
  - name: `LocalizedValue<string>`
  - description: `LocalizedValue<string>`
  - status: `RuleStatusModel` (`InPreparation` | `Active` | `Deprecated` | `Abandoned`)
  - inputDataSchema: `object` (JSON Schema)
  - outputDataSchema: `object` (JSON Schema)
  - defaultValue: `object` (JSON matching output schema)
  - forerunnerId: `string?` (rule ID this one supersedes)

Responses

- `201` Created: RuleResponse body with the created rule (controller uses CreatedAtAction to return the resource).
- `400` Bad Request: Invalid JSON body or invalid request data.
- `401` Unauthorized: Missing/invalid bearer token.
- `403` Forbidden: Authenticated but not authorized (application not declared or insufficient permissions).
- `409` Conflict: Rule already exists for the given targetId.
- `500` Internal Server Error: Unhandled error while creating the rule.

### Get rule endpoint

- Method: GET
- URL: `api/v1/targets/{targetId}/rules`
- Produces: application/json

Path parameters

- targetId (string, required): Identifier of the target whose rule is requested.

Responses

- `200` OK: RuleResponse body with the rule.
- `401` Unauthorized: Missing/invalid bearer token.
- `403` Forbidden: Authenticated but not authorized.
- `404` Not Found: No rule exists for the given targetId.
- `500` Internal Server Error: Unhandled error while retrieving the rule.

### Update rule endpoint

- Method: PATCH
- URL: `api/v1/targets/{targetId}/rules`
- Consumes: application/json
- Produces: application/json

Path parameters

- targetId (string, required): Identifier of the target whose rule is to be updated.

Request body (application/json)

- Shape matches JSON Patch Document (RFC 6902) for `RulePatchModel`:
  - Array of patch operations (e.g., `[{ "op": "replace", "path": "/name", "value": {...} }]`)
  - Supported paths:
    - `/name`: `LocalizedValue<string>` - Keep in mind that localized value items are stored alphabetically by their locales so adding at a specified position cannot behava as expected.
    - `/description`: `LocalizedValue<string>` - Keep in mind that localized value items are stored alphabetically by their locales so adding at a specified position cannot behava as expected.
    - `/status`: `RuleStatusModel` (`InPreparation` | `Active` | `Deprecated` | `Abandoned`)
    - `/inputDataSchema`: `object` (JSON Schema)
    - `/outputDataSchema`: `object` (JSON Schema)
    - `/defaultValue`: `object` (JSON matching output schema)

Responses

- `200` OK: RuleResponse body with the updated rule.
- `400` Bad Request: Invalid JSON Patch document or invalid request data.
- `401` Unauthorized: Missing/invalid bearer token.
- `403` Forbidden: Authenticated but not authorized.
- `404` Not Found: No rule exists for the given targetId.
- `500` Internal Server Error: Unhandled error while updating the rule.

### Get all application rule values endpoint

- Method: GET
- URL: `api/v1/rule-values`
- Produces: application/json

Path parameters

- None

Responses

- `200` OK: GetAllApplicationRulesValuesResponse body containing a collection of all rule values for the application. The response includes:
  - items: Array of polymorphic `GetAllApplicationRulesValuesResponseItem` objects. Each item contains:
    - Common properties (all types):
      - targetId: `string` - Identifier of the target the rule is assigned to
      - name: `LocalizedValue<string>` - Localized name of the rule
      - description: `LocalizedValue<string>?` - Localized description of the rule
      - status: `RuleStatusModel` enum (`InPreparation` | `Active` | `Deprecated` | `Abandoned`)
      - createdOn: `DateTime` - When the rule value was created
      - createdBy: `string` - Who created the rule value
    - Type discriminator: `$type` field with one of: `"DEFAULT"`, `"TENANT"`, `"ORGANIZATION"`, `"DRAFT"`
    - Type-specific properties:
      - `GetAllApplicationRulesValuesResponseDefaultItem` (type: `"DEFAULT"`): No additional properties
      - `GetAllApplicationRulesValuesResponseTenantItem` (type: `"TENANT"`): No additional properties
      - `GetAllApplicationRulesValuesResponseOrganizationItem` (type: `"ORGANIZATION"`):
        - organizationId: `string` - Organization identifier
      - `GetAllApplicationRulesValuesResponseDraftItem` (type: `"DRAFT"`):
        - id: `string` - The unique identifier of the draft rule value
        - organizationId: `string?` - Optional organization identifier associated with the draft
        - draftDescription: `string?` - Optional description of the draft
  - totalCount: Total number of rule values in the collection
- `401` Unauthorized: Missing/invalid bearer token.
- `403` Forbidden: Authenticated but not authorized (application not declared or insufficient permissions).
- `500` Internal Server Error: Unhandled error while retrieving rule values.
  
### Get all rules endpoint

- Method: GET
- URL: `api/v1/rules`
- Produces: application/json

Query parameters

- offset (integer, optional): Zero-based number of items to skip.
- limit (integer, optional): Maximum number of items to return.

Responses

- `200` OK: GetAllApplicationRulesResponse body with the rules.
- `401` Unauthorized: Missing/invalid bearer token.
- `403` Forbidden: Authenticated but not authorized (application not declared or insufficient permissions).
- `500` Internal Server Error: Unhandled error while retrieving the rules.

### Create draft rule value endpoint

- Method: POST
- URL: `api/v1/targets/{targetId}/rule-values/drafts`
- Consumes: application/json
- Produces: application/json

Path parameters

- targetId (string, required): Identifier of the target the draft rule value will be assigned to.

Request body (application/json)

- Shape matches CreateTargetDraftRuleValueRequest:
    - value: `object` (JSON matching the rule's output schema)
    - organizationId: `string?` (optional organization identifier)
    - description: `string?` (optional description)

Responses

- `201` Created: DraftRuleValueResponse body with the created draft; Location header points to the resource returned by GetDraftRuleValue.
- `400` Bad Request: Invalid request data (e.g., decision value doesn't conform to the rule's output schema).
- `401` Unauthorized: Missing/invalid bearer token.
- `403` Forbidden: Authenticated but not authorized.
- `404` Not Found: No rule exists for the given targetId.
- `500` Internal Server Error: Unhandled error while creating the draft rule value.

### Get draft rule value endpoint

- Method: GET
- URL: `api/v1/rule-values/drafts/{id}`
- Produces: application/json

Path parameters

- id (string, required): Identifier of the draft rule value.

Responses

- `200` OK: DraftRuleValueResponse body with the draft rule value.
- `401` Unauthorized: Missing/invalid bearer token.
- `403` Forbidden: Authenticated but not authorized.
- `404` Not Found: Draft rule value not found.
- `500` Internal Server Error: Unhandled error while retrieving the draft rule value.

### Update draft rule value endpoint

- Method: PATCH
- URL: `api/v1/rule-values/drafts/{id}`
- Consumes: application/json
- Produces: application/json

Path parameters

- id (string, required): Identifier of the draft rule value to update.

Request body (application/json)

- Shape matches JSON Patch Document (RFC 6902) for `DraftRuleValuePatchModel`:
  - Array of patch operations (e.g., `[{ "op": "replace", "path": "/value", "value": {...} }]`)
  - Supported paths:
    - `/value`: `object` (JSON matching the rule's output schema)
    - `/description`: `string?` (optional description)
    - `/organizationId`: `string?` (optional organization identifier)

Responses

- `200` OK: DraftRuleValueResponse body with the updated draft rule value.
- `400` Bad Request: Invalid JSON Patch document or invalid request data.
- `401` Unauthorized: Missing/invalid bearer token.
- `403` Forbidden: Authenticated but not authorized.
- `404` Not Found: Draft rule value not found.
- `500` Internal Server Error: Unhandled error while updating the draft rule value.

### Delete draft rule value endpoint

- Method: DELETE
- URL: `api/v1/rule-values/drafts/{id}`

Path parameters

- id (string, required): Identifier of the draft rule value to delete.

Responses

- `204` No Content: Draft rule value deleted.
- `401` Unauthorized: Missing/invalid bearer token.
- `403` Forbidden: Authenticated but not authorized.
- `404` Not Found: Draft rule value not found.
- `500` Internal Server Error: Unhandled error while deleting the draft rule value.

### Set default rule value endpoint

- Method: POST
- URL: `api/v1/targets/{targetId}/rule-values/defaults`
- Consumes: application/json
- Produces: application/json

Path parameters

- targetId (string, required): Identifier of the target the rule is assigned to.

Request body (application/json)

- Shape matches SetRuleValueRequest:
  - value: `object` (JSON matching the rule's output schema)

Responses

- `201` Created: RuleValueResponse body with the created or updated default rule value; Location header points to the resource returned by GetTargetDefaultRuleValue.
- `400` Bad Request: Invalid request data (e.g., decision value doesn't conform to the rule's output schema).
- `401` Unauthorized: Missing/invalid bearer token.
- `403` Forbidden: Authenticated but not authorized.
- `404` Not Found: No rule exists for the given targetId.
- `500` Internal Server Error: Unhandled error while setting the default rule value.

### Get default rule value endpoint

- Method: GET
- URL: `api/v1/targets/{targetId}/rule-values/defaults`
- Produces: application/json

Path parameters

- targetId (string, required): Identifier of the target the rule is assigned to.

Responses

- `200` OK: RuleValueResponse body with the default rule value.
- `401` Unauthorized: Missing/invalid bearer token.
- `403` Forbidden: Authenticated but not authorized.
- `404` Not Found: No rule or default rule value exists for the given targetId.
- `500` Internal Server Error: Unhandled error while retrieving the default rule value.

### Set tenant rule value endpoint

- Method: POST
- URL: `api/v1/targets/{targetId}/rule-values/tenants`
- Consumes: application/json
- Produces: application/json

Path parameters

- targetId (string, required): Identifier of the target the rule is assigned to.

Request body (application/json)

- Shape matches SetRuleValueRequest:
  - value: `object` (JSON matching the rule's output schema)

Responses

- `201` Created: RuleValueResponse body with the created or updated tenant rule value; Location header points to the resource returned by GetTargetTenantRuleValue.
- `400` Bad Request: Invalid request data (e.g., decision value doesn't conform to the rule's output schema).
- `401` Unauthorized: Missing/invalid bearer token.
- `403` Forbidden: Authenticated but not authorized.
- `404` Not Found: No rule exists for the given targetId.
- `500` Internal Server Error: Unhandled error while setting the tenant rule value.

### Get tenant rule value endpoint

- Method: GET
- URL: `api/v1/targets/{targetId}/rule-values/tenants`
- Produces: application/json

Path parameters

- targetId (string, required): Identifier of the target the rule is assigned to.

Responses

- `200` OK: RuleValueResponse body with the tenant rule value.
- `401` Unauthorized: Missing/invalid bearer token.
- `403` Forbidden: Authenticated but not authorized.
- `404` Not Found: No rule exists for the given targetId, or no tenant-specific value exists (returns null in connector).
- `500` Internal Server Error: Unhandled error while retrieving the tenant rule value.

### Delete tenant rule value endpoint

- Method: DELETE
- URL: `api/v1/targets/{targetId}/rule-values/tenants`

Path parameters

- targetId (string, required): Identifier of the target the rule is assigned to.

Responses

- `204` No Content: Tenant rule value deleted.
- `401` Unauthorized: Missing/invalid bearer token.
- `403` Forbidden: Authenticated but not authorized.
- `404` Not Found: No rule exists for the given targetId, or no tenant-specific value exists.
- `500` Internal Server Error: Unhandled error while deleting the tenant rule value.

### Set organization rule value endpoint

- Method: POST
- URL: `api/v1/targets/{targetId}/rule-values/organizations/{organizationId}`
- Consumes: application/json
- Produces: application/json

Path parameters

- targetId (string, required): Identifier of the target the rule is assigned to.
- organizationId (string, required): Identifier of the organization the rule value is assigned to.

Request body (application/json)

- Shape matches SetRuleValueRequest:
  - value: `object` (JSON matching the rule's output schema)

Responses

- `201` Created: RuleValueResponse body with the created or updated organization rule value; Location header points to the resource returned by GetTargetOrganizationRuleValue.
- `400` Bad Request: Invalid request data (e.g., decision value doesn't conform to the rule's output schema).
- `401` Unauthorized: Missing/invalid bearer token.
- `403` Forbidden: Authenticated but not authorized.
- `404` Not Found: No rule exists for the given targetId.
- `500` Internal Server Error: Unhandled error while setting the organization rule value.

### Get organization rule value endpoint

- Method: GET
- URL: `api/v1/targets/{targetId}/rule-values/organizations/{organizationId}`
- Produces: application/json

Path parameters

- targetId (string, required): Identifier of the target the rule is assigned to.
- organizationId (string, required): Identifier of the organization the rule value is assigned to.

Responses

- `200` OK: RuleValueResponse body with the organization rule value.
- `401` Unauthorized: Missing/invalid bearer token.
- `403` Forbidden: Authenticated but not authorized.
- `404` Not Found: No rule exists for the given targetId, or no organization-specific value exists (returns null in connector).
- `500` Internal Server Error: Unhandled error while retrieving the organization rule value.

### Get organization rule value history endpoint

- Method: GET
- URL: `api/v1/targets/{targetId}/rule-values/organizations/{organizationId}/history`
- Produces: application/json

Path parameters

- targetId (string, required): Identifier of the target the rule is assigned to.
- organizationId (string, required): Identifier of the organization the rule value is assigned to.

Responses

- `200` OK: GetOrganizationRuleValueHistoryResponse body with the history of organization rule values.
- `401` Unauthorized: Missing/invalid bearer token.
- `403` Forbidden: Authenticated but not authorized.
- `404` Not Found: No rule exists for the given targetId.
- `500` Internal Server Error: Unhandled error while retrieving the organization rule value history.


### Delete organization rule value endpoint

- Method: DELETE
- URL: `api/v1/targets/{targetId}/rule-values/organizations/{organizationId}`

Path parameters

- targetId (string, required): Identifier of the target the rule is assigned to.
- organizationId (string, required): Identifier of the organization the rule value is assigned to.

Responses

- `204` No Content: Organization rule value deleted.
- `401` Unauthorized: Missing/invalid bearer token.
- `403` Forbidden: Authenticated but not authorized.
- `404` Not Found: No rule exists for the given targetId, or no organization-specific value exists.
- `500` Internal Server Error: Unhandled error while deleting the organization rule value.