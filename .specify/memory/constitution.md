### Project Constitution: Blazor Museum Client/API
| Field | Value |
| ------ | ------ |
| **Project Name** | Blazor Museum Client/API |
| **Project Type** | Hosted Blazor WASM (Client) & C# Minimal API (Server) |

--------------------------------------------------------------------------------

##### 1. Governance and Roles
| Role/Status | Details |
| ------ | ------ |
| **Role Structure** | Solo Developer (Primary Role) [8, 9] |
| **Developer Responsibilities** | Responsible for ensuring project alignment with principles. Excels at breaking down complex requirements into detailed, sequential **Product Requirements Documents (PRDs)** using **Agile methodologies**. Primary task is to generate multiple, granular PRDs for a phased project rollout based on user requests. Defines `/specify`, approves `/plan`, executes `/implement`, and ensures constitutional compliance. |
| **Developer Strategy** | **Prioritize Incremental Changes:** Focus on identifying minimal, non-disruptive changes to the existing foundation. **Avoid rebuilding core functionality** [8, 9]. |

--------------------------------------------------------------------------------

##### 2. Core Principles
2.  **Architectural Consistency:** All technical decisions must align with the defined **Opinionated Stack** and constraints [9, 11].
3.  **Test-First Development:** Every feature requires clear **Acceptance Criteria** and mandatory **Contract Tests** (e.g., Specmatic) [9, 11, 12].
4.  **Security First:** All development must adhere to security policies, including role-based access checks [5, 13, 14].
5.  **Generated API Code:** Follow **RESTful** design principles.  Where ever possible adhere to SOLID principles.
6.  **Maintainability:** Code coverage above 70%.
--------------------------------------------------------------------------------

##### 3. Architectural Constraints
###### 3.1. Solution Structure & Technology Stack
| Component | Technology / Rule | Rationale |
| ------ | ------ | ------ |
| **Client Frontend** | **Blazor WebAssembly (WASM)** [7] | Runs in the browser. Must handle token acquisition using MSAL [7]. |
| **Server Backend** | **C# Minimal API (.NET 9 or above)** [15, 16] | Hosts the client files and provides protected endpoints [7]. |
| **Shared Models** | .NET Library | Must contain all Data Transfer Objects (DTOs) used by both Client and Server [7]. |
| **DTO Standard** | **C# record types** for all request/response data models [14, 17, 18]. | Ensures clear, immutable contracts [14, 17]. |
| **Database** | Sqlite |

###### 3.2. API & Backend Governance
| Constraint | Details |
| ------ | ------ |
| **Business Logic** | **No inline business logic** is permitted directly in API route definitions or `app.Map*` calls. All logic must be encapsulated in **injectable services** [15, 17]. |
| **HTTP Standards** | All endpoints must consistently return **standard HTTP status codes** (e.g., 200, 201, 400, 404) [9, 14]. Use the correct HTTP verbs (MapGet for retrieving, MapPost for creating, MapPut/MapPatch for updating, MapDelete for deleting) [14]. |
| **Asynchronous Operations** | All I/O-bound operations must use **asynchronous programming** (async/await) [19, 20]. |
| **Authentication/Authorization** | Server must configure JWT Bearer Authentication [21]. Authorization must enforce specific roles, such as **Administrator** ("MuseumAdmin") and **User** ("MuseumUser") [22, 23]. |
| **Configuration** | Application settings must be kept in `appsettings.json` and environmental variables [14]. |
| **Naming Convention** | CamelCase for Parameters: Use camelCase for route parameters and query string parameters.
PascalCase for Types and Methods: Follow standard C# naming conventions for classes, interfaces, and methods. |
| **Input Validation** | Input Validation: Implement validation for incoming request data using attributes (e.g., [Required], [StringLength]) or a custom validation pipeline.
Avoid Trusting Client Input: Always validate data received from clients before processing it. |
| **Authentication and Authorization** | Middleware for Global Auth: Configure app.UseAuthentication() and app.UseAuthorization() in Program.cs.
Endpoint-Level Authorization: Use [Authorize] attributes or policies to secure specific endpoints. |
 **Code Readability** | Concise Lambdas: Leverage the conciseness of lambda expressions in Minimal APIs but ensure they remain readable.
Comments (when necessary): Add comments to explain complex logic or non-obvious design choices. Consistent Formatting: Adhere to a consistent code formatting style (e.g., using dotnet format or an IDE's formatting tools). |
| **Documention** | Use OpenAPI/Swagger. Always use the built-in Swagger integration to generate clear, discoverable documentation. This makes it easier for other developers to use your API. |
| **Request and Response Standardization** | Use records for data transfer objects (DTOs). Define specific record types for your requests and responses. This ensures clear, immutable contracts and avoids exposing internal domain models. Enforce validation. Use data annotations ([Required], [Range], etc.) on your record types. The Minimal API model binder will automatically handle validation. Return typed results. Instead of returning raw objects, use Results<T, TError> or other IResult factory methods (e.g., Results.Ok(), Results.NotFound()) to ensure consistent HTTP responses. |
| **Coding Style** | Favor implicit typing (var). Use var for local variables to reduce noise, especially when the type is obvious from the right-hand side. Align and organize chains. When chaining multiple calls on an endpoint (e.g., .WithName().WithSummary()), format each call on a new line for improved readability. Use a consistent naming convention for variables, parameters, and methods. Follow the standard C# naming conventions. Add comments where necessary. Use XML comments on methods to document what they do, their parameters, and what they return. This improves the generated OpenAPI documentation.|
| **Api Versioning** | use Asp.Versioning.Http to create api versioning support in program.cs file.  Use URI path versioning.  Use semantic versioning: Label breaking changes as a major version bump (e.g., v1 to v2). Deprecate gracefully: When retiring an old version, announce a deprecation timeline and notify clients. Document all versions: Provide clear and separate documentation for every version of your API. Use MapGroup and extension methods: For larger applications, organize your versioned endpoints into separate files and use MapGroup to apply the version set, as shown in the primary example.|

###### 3.3. Observability and Logging
| Constraint | Details |
| ------ | ------ |
| **Structured Logging** | Use **Serilog** for structured and informative logging [14, 24, 25]. |
| **Log Content** | Logging must be configured to include the **TraceId** and **SpanId** in every log event to enable correlation between the Blazor client and the Minimal API server [25, 26]. |
| **Error Handling** | Implement **Centralized Exception Handling** middleware to return consistent error responses [14]. |

--------------------------------------------------------------------------------


#### 3.4. Performance Requirements

| Requirement | Rule |
| :--- | :--- |
| **I/O Operations** | All I/O-bound operations must use **asynchronous programming** (`async`/`await` or equivalent). |

---
##### 4. Development Standards
| Standard | Rule |
| ------ | ------ |
| **Testing Priority** | **Integration tests** using in-memory runners (e.g., `WebApplicationFactory` for C#) are the preferred testing method [19, 27, 28]. |
| **Coverage Minimum** | Maintain a minimum automated test coverage percentage of **60%** or **70%** [11, 29]. |
| **Documentation Status** | Generated artifacts (`spec.md`, `plan.md`, `constitution.md`) are considered **living documents** that must be synchronized with the codebase [19, 29]. |

---
---

### 5. Decision Making

| Category | Details |
| :--- | :--- |
| **Process** | All decisions are made by the **Developer** and documented in the relevant `plan.md` artifact, ensuring decisions are grounded by this constitution. |
| **Tools** | Spec Kit Commands (`/specify`, `/plan`, `/tasks`), Git/Pull Requests. |
| **LLM Constraint** | Must avoid proposing a complete rebuild of the foundation. Instead: **Highlight specific areas** for adjustment (e.g., "Extend the User model to include Field Z") and provide **backward-compatible solutions** (e.g., "Add optional parameters to existing APIs"). |