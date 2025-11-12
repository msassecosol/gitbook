[[_TOC_]]

# Overview

Business rules define and automate the logic and policies that guide business operations.

# Services
## BRMS (Business rule management system)

Provides a centralized platform for defining and governing business rules, ensuring consistency while enabling scoped customization across three hierarchical levels—global, tenant, and organization. Each rule is independently versioned within its level, allowing safe evolution and auditability without impacting other contexts.

### Prerequisites

Business rule evaluation is based on the [ZenEngine](https://github.com/gorules/zen?tab=MIT-1-ov-file) open source.

### Installation

Install the NuGet package `ASOL.BRMS.Connector` to get started.

### Configuration

Use the `AddBusinessRuleManagement` extension method to register and configure BRMS functionality. The following interfaces are registered in the service collection:
- `IBRMSClient`
- `IBusinessRuleEvaluation`

If the `configure` action is provided when calling the `AddBusinessRuleManagement` method and all properties are assigned, BRMS is ready to use.

Alternatively, you can configure `ApplicationCodeHeaderProviderOptions` and `BRMSClientOptions` separately from the `AddBusinessRuleManagement` method.

#### Typical setup in a .NET application

- Register services using the single convenience method:

    ```csharp
    services.AddBusinessRuleManagement(options =>
    {
            options.ApplicationCode = "<<your-app-code>>";        // sent as X-Application-Code header
            options.BaseUrl = "<<brms-base-url>>";                // base URL of BRMS API
            options.CertificateAuthenticationSupported = false;   // set true if mTLS is used
    });
    ```

- Or configure components separately:

    ```csharp
    services.AddBusinessRuleManagement();
    ...
    services
        .AddApplicationCodeHeaderProvider("<<your-app-code>>")
        .ConfigureBusinessRuleManagementConnection(
                baseUrl: "<<brms-base-url>>",
                certificateAuthenticationSupported: false;
    ```

### Description

Each rule is assigned to a `TargetId`, which serves as an identifier to connect a decision process with a specific rule stored in the BRMS. The `TargetId` is defined by the user of the BRMS and has no format restrictions. Note that `TargetId` is case-sensitive.

Once a `TargetId` (and its connected rule) is created, it cannot be deleted. Only a successor rule can be created, and the user must ensure that the original rule is no longer used.

#### Rule properties
- **Name**: 
    - Multilingual value
    - Required
- **Description**: 
    - Multilingual value
    - Required
- **Status**: Indicates which lifecycle stage the rule is in. The user sets this value and controls the lifecycle. The rule status does not interfere with rule usage.
    - Values:
        - **InPreparation**: The rule is being prepared and is not yet active.
        - **Active**: The rule is currently active.
        - **Deprecated**: The rule is currently active but will be deactivated in the future.
        - **Abandoned**: The rule is no longer active.
    - Required
- **InputDataSchema**: Describes the input data that the rule evaluation process expects. Uses JSON Schema format.
- **OutputDataSchema**: Describes the output data that the rule evaluation process produces. Uses JSON Schema format.
- **ForerunnerId**: Identifier of the rule that the current rule supersedes.
- **Value**: Used in the evaluation process.

##### Rule value
The rule value is the actual value used by the rule evaluation process. A special ZenEngine data format is required (see [ZenEngine](https://github.com/gorules/zen)). Every rule has a global value whose default value is set during rule creation. The global value can be overriden for each tenant or organization. The history of rule values for each tenant and organization is maintained and can be requested, along with the complete rule value history.

##### Draft rule value
For rule value development and testing purposes, a draft rule value can be created. Although the draft rule value is associated with a `TargetId`, it is not involved in production rule evaluation or rule value history. The ad-hoc rule evaluation endpoint can be used for evaluating draft rule values.

#### Rule evaluation
To evaluate a rule, only its `TargetId` and input data conforming to the rule's input schema are needed. The evaluation process considers the current context (tenant and organization of the process executor) to select the appropriate rule value type (global, tenant, or organization) and returns data conforming to the output schema. The rule value creator is responsible for ensuring that the evaluation result conforms to the rule's output schema.

### Usage

#### HTTP API
- To manage business rules and their values, see [Rule Management](./brms-management.md), the HTTP API section.
- To evaluate business rules, see [Rule Evaluation](./brms-evaluation.md), the HTTP API section.

#### .NET
The basic interface for using the business rule management system is `IBRMSClient`, which enables access to all available functionality. However, any intermediate interface can be accessed directly without using `IBRMSClient`.

Further details can be found in dedicated chapters:
- To manage business rules and their values, see [Rule Management](./brms-management.md)
- To evaluate business rules, see [Rule Evaluation](./brms-evaluation.md)