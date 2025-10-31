# Data Model: Basic Blazor Museum Foundation

**Branch**: `001-basic-blazor-museum`  
**Date**: October 31, 2025  
**Phase**: 1 - Design and Contracts

## Core Entities

### User Authentication Context

**Purpose**: Manages user authentication state and role-based access control

```csharp
// Shared/Models/UserModels.cs
public record UserInfo(
    string Id,
    string Email,
    string Name,
    IReadOnlyList<string> Roles,
    DateTime LastLogin
);

public record LoginRequest(
    string Email,
    string Password
);

public record LoginResponse(
    string Token,
    DateTime Expires,
    UserInfo User
);

public record TokenValidationResult(
    bool IsValid,
    string? UserId,
    IReadOnlyList<string> Roles,
    string? ErrorMessage
);
```

**Validation Rules**:
- Email must be valid email format
- Password must be at least 8 characters for LoginRequest
- Token must be valid JWT format
- Roles must be from predefined list: "MuseumAdmin", "MuseumUser"
- UserId must be non-empty when IsValid is true

**State Transitions**:
- Unauthenticated → Authenticated (via successful login)
- Authenticated → Unauthenticated (via logout or token expiration)
- Role changes require re-authentication

### Application Configuration

**Purpose**: Manages application settings and environment-specific configuration

```csharp
// Shared/Models/ConfigurationModels.cs
public record JwtConfiguration(
    string SecretKey,
    string Issuer,
    string Audience,
    int ExpirationMinutes
);

public record DatabaseConfiguration(
    string ConnectionString,
    bool EnableSensitiveDataLogging,
    bool EnableDetailedErrors
);

public record LoggingConfiguration(
    string MinimumLevel,
    bool IncludeScopes,
    string OutputTemplate
);

public record ApiConfiguration(
    string BaseUrl,
    int DefaultVersion,
    string[] SupportedVersions
);
```

**Validation Rules**:
- SecretKey must be at least 32 characters for security
- ConnectionString must be valid SQLite connection format
- MinimumLevel must be valid Serilog level (Debug, Information, Warning, Error, Fatal)
- DefaultVersion must be positive integer
- SupportedVersions must contain DefaultVersion

### Common Response Models

**Purpose**: Standardized API response structures for consistency

```csharp
// Shared/Models/ApiModels.cs
public record ApiResponse<T>(
    bool Success,
    T? Data,
    string? Message,
    IReadOnlyList<string>? Errors
);

public record ApiError(
    string Code,
    string Message,
    string? Details,
    string? TraceId
);

public record HealthCheckResponse(
    string Status,
    DateTime Timestamp,
    string Version,
    Dictionary<string, string> Dependencies
);

public record ApiVersionInfo(
    int Version,
    string Description,
    bool IsDeprecated,
    DateTime? DeprecationDate
);
```

**Validation Rules**:
- Success must correlate with Data presence (Success = true implies Data != null for successful operations)
- Message should be provided when Success = false
- Errors list should be provided for validation failures
- TraceId should always be included for correlation
- Status must be one of: "Healthy", "Degraded", "Unhealthy"

## Entity Relationships

### Authentication Flow
```
User Input (LoginRequest) 
    → Authentication Service 
    → JWT Token Generation 
    → LoginResponse (with UserInfo)
    → Client Token Storage
    → Subsequent API Calls (with Bearer token)
    → Server Token Validation (TokenValidationResult)
```

### Configuration Hierarchy
```
Environment Variables 
    → appsettings.{Environment}.json 
    → appsettings.json 
    → Application Startup Configuration
    → Dependency Injection Container
```

### Error Response Flow
```
Application Exception 
    → Exception Handling Middleware 
    → ApiError Generation 
    → ApiResponse<T> with Success = false
    → HTTP Response with appropriate status code
```

## Database Schema (Entity Framework)

For this foundational phase, database entities are minimal and focused on authentication:

```csharp
// Server/Data/Entities/User.cs
public class User
{
    public string Id { get; set; } = Guid.NewGuid().ToString();
    public required string Email { get; set; }
    public required string PasswordHash { get; set; }
    public required string Name { get; set; }
    public DateTime CreatedAt { get; set; } = DateTime.UtcNow;
    public DateTime LastLogin { get; set; }
    public bool IsActive { get; set; } = true;
    
    // Navigation properties
    public ICollection<UserRole> UserRoles { get; set; } = new List<UserRole>();
}

// Server/Data/Entities/Role.cs
public class Role
{
    public string Id { get; set; } = Guid.NewGuid().ToString();
    public required string Name { get; set; }
    public string? Description { get; set; }
    
    // Navigation properties
    public ICollection<UserRole> UserRoles { get; set; } = new List<UserRole>();
}

// Server/Data/Entities/UserRole.cs
public class UserRole
{
    public required string UserId { get; set; }
    public required string RoleId { get; set; }
    public DateTime AssignedAt { get; set; } = DateTime.UtcNow;
    
    // Navigation properties
    public User User { get; set; } = null!;
    public Role Role { get; set; } = null!;
}
```

## Data Validation Attributes

All DTOs will use data annotations for validation:

```csharp
public record LoginRequest(
    [Required, EmailAddress] string Email,
    [Required, MinLength(8)] string Password
);

public record UserInfo(
    [Required] string Id,
    [Required, EmailAddress] string Email,
    [Required, StringLength(100)] string Name,
    [Required] IReadOnlyList<string> Roles,
    [Required] DateTime LastLogin
);
```

## Mapping Strategy

Use explicit mapping between entities and DTOs to maintain clean separation:

```csharp
// Server/Services/MappingExtensions.cs
public static class UserMappingExtensions
{
    public static UserInfo ToUserInfo(this User user)
    {
        return new UserInfo(
            user.Id,
            user.Email,
            user.Name,
            user.UserRoles.Select(ur => ur.Role.Name).ToList(),
            user.LastLogin
        );
    }
}
```

This data model provides the foundation for authentication and configuration management while following all constitutional requirements for DTOs as records, validation, and clean separation between entities and data transfer objects.