# Research Phase: Basic Blazor Museum Foundation

**Branch**: `001-basic-blazor-museum`  
**Date**: October 31, 2025  
**Phase**: 0 - Research and Technology Decisions

## Research Tasks Completed

### JWT Authentication Implementation for Blazor WASM + Minimal API

**Decision**: Use System.IdentityModel.Tokens.Jwt and Microsoft.AspNetCore.Authentication.JwtBearer for server-side JWT validation, with manual token management in Blazor WASM client.

**Rationale**: 
- Built-in ASP.NET Core JWT middleware provides robust token validation
- Blazor WASM requires custom implementation for token storage and HTTP client integration
- Avoids complex MSAL integration for this foundational phase
- Provides foundation for future MSAL integration if needed

**Alternatives considered**: 
- Microsoft Authentication Library (MSAL) - too complex for foundation phase
- IdentityServer integration - overkill for current requirements
- Custom authentication system - reinventing the wheel

### API Versioning Strategy

**Decision**: Use Asp.Versioning.Http package with URI path versioning (e.g., `/api/v1/users`, `/api/v2/users`)

**Rationale**:
- Clear and explicit versioning visible in URLs
- Follows constitutional requirement for semantic versioning
- Easy to test and document different API versions
- Supported by OpenAPI/Swagger generation

**Alternatives considered**:
- Header-based versioning - less visible and harder to test
- Query parameter versioning - can be easily overlooked
- Media type versioning - overly complex for current needs

### Structured Logging with Correlation

**Decision**: Use Serilog with custom enrichers for TraceId and SpanId, configured to write to console and file sinks

**Rationale**:
- Serilog provides excellent structured logging capabilities
- TraceId/SpanId correlation enables tracking requests across client and server
- Multiple sinks allow development (console) and production (file) logging
- Easily integrates with ASP.NET Core logging infrastructure

**Alternatives considered**:
- Built-in .NET logging - lacks advanced structured logging features
- NLog - good alternative but Serilog has better JSON formatting
- Application Insights - too heavy for foundational phase

### Database Strategy

**Decision**: Use Entity Framework Core with SQLite and Code-First migrations

**Rationale**:
- SQLite requires no external dependencies for development
- Entity Framework Core provides robust ORM with async support
- Code-First approach aligns with constitution requirements
- Easy to migrate to other databases later if needed

**Alternatives considered**:
- Dapper - more performance but less productivity for rapid development
- Raw SQL - too much boilerplate and maintenance overhead
- In-memory collections - not persistent and doesn't test real database scenarios

### Exception Handling Strategy

**Decision**: Implement custom exception handling middleware that returns structured error responses with proper HTTP status codes

**Rationale**:
- Centralized error handling as required by constitution
- Consistent error response format across all API endpoints
- Proper HTTP status code mapping
- Security - doesn't expose internal system details

**Alternatives considered**:
- Try-catch in each endpoint - violates DRY principle and hard to maintain
- Built-in ASP.NET Core error handling - less control over response format
- Third-party libraries - unnecessary dependency for straightforward requirement

### Testing Strategy

**Decision**: Use xUnit with WebApplicationFactory for integration tests, focused on API endpoint testing and authentication flows

**Rationale**:
- WebApplicationFactory provides in-memory testing without external dependencies
- Can test full HTTP request/response cycle including authentication
- xUnit is the standard for .NET testing with good async support
- Integration tests provide more confidence than unit tests for API functionality

**Alternatives considered**:
- Pure unit testing - doesn't test integration between components
- End-to-end testing with real browser - too slow and complex for foundation
- MSTest or NUnit - xUnit has better async support and cleaner syntax

## Technology Stack Summary

| Component | Technology | Version | Purpose |
|-----------|------------|---------|---------|
| Client Framework | Blazor WebAssembly | .NET 9 | Browser-based UI |
| Server Framework | ASP.NET Core Minimal API | .NET 9 | REST API backend |
| Authentication | JWT Bearer | Built-in | Token-based auth |
| Database | SQLite + EF Core | Latest | Data persistence |
| Logging | Serilog | Latest | Structured logging |
| API Versioning | Asp.Versioning.Http | Latest | URI path versioning |
| Testing | xUnit + WebApplicationFactory | Latest | Integration testing |
| Documentation | Swashbuckle/OpenAPI | Built-in | API documentation |

## Implementation Priorities

1. **Project Structure**: Create the three-project solution structure
2. **Server Foundation**: Set up Minimal API with basic middleware pipeline
3. **Authentication**: Implement JWT configuration and protection
4. **Logging**: Configure Serilog with correlation
5. **API Versioning**: Set up versioned endpoint structure
6. **Client Foundation**: Create basic Blazor WASM with API connectivity
7. **Testing**: Implement integration test foundation
8. **Documentation**: Ensure Swagger/OpenAPI is properly configured

All research items have been resolved with clear technology decisions that align with the constitutional requirements.