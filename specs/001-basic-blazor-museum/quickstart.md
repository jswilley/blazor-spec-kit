# Quickstart Guide: Blazor Museum Foundation

**Branch**: `001-basic-blazor-museum`  
**Date**: October 31, 2025  
**Prerequisites**: .NET 9 SDK, Visual Studio Code or Visual Studio

## Overview

This quickstart guide helps you set up and run the basic Blazor Museum application foundation. The application consists of a Blazor WebAssembly client, ASP.NET Core Minimal API server, and shared library for DTOs.

## Prerequisites

### Required Software
- [.NET 9 SDK](https://dotnet.microsoft.com/download/dotnet/9.0) or later
- [Visual Studio Code](https://code.visualstudio.com/) with C# extension, or
- [Visual Studio 2022](https://visualstudio.microsoft.com/) (17.8 or later)
- [Git](https://git-scm.com/) for version control

### Optional Tools
- [Azure Data Studio](https://docs.microsoft.com/en-us/sql/azure-data-studio/) or [DB Browser for SQLite](https://sqlitebrowser.org/) for database inspection
- [Postman](https://www.postman.com/) or similar for API testing

## Quick Setup

### 1. Clone and Navigate
```bash
git clone <repository-url>
cd blazor.museum
git checkout 001-basic-blazor-museum
```

### 2. Restore Dependencies
```bash
# From repository root
dotnet restore
```

### 3. Build Solution
```bash
dotnet build
```

### 4. Initialize Database
```bash
# Navigate to server project
cd src/BlazorMuseum.Server

# Create and run migrations
dotnet ef database update
```

### 5. Run the Application
```bash
# From repository root, run the server (this also serves the client)
cd src/BlazorMuseum.Server
dotnet run

# Or alternatively, run both projects simultaneously (if using Visual Studio)
# Set BlazorMuseum.Server as startup project and run
```

### 6. Access the Application
- **Client**: https://localhost:7001
- **API Documentation**: https://localhost:7001/swagger
- **Health Check**: https://localhost:7001/api/v1/health

## Project Structure

```
blazor.museum/
├── src/
│   ├── BlazorMuseum.Client/          # Blazor WebAssembly project
│   ├── BlazorMuseum.Server/          # ASP.NET Core API project  
│   └── BlazorMuseum.Shared/          # Shared DTOs and models
├── tests/
│   ├── BlazorMuseum.Server.Tests/    # Integration and unit tests
│   └── BlazorMuseum.Client.Tests/    # Client tests (if needed)
├── specs/                            # Feature specifications
└── BlazorMuseum.sln                  # Solution file
```

## Development Workflow

### 1. Running Tests
```bash
# Run all tests
dotnet test

# Run with coverage (requires coverlet)
dotnet test --collect:"XPlat Code Coverage"

# Run specific test project
dotnet test tests/BlazorMuseum.Server.Tests/
```

### 2. Database Operations
```bash
# Add new migration
cd src/BlazorMuseum.Server
dotnet ef migrations add <MigrationName>

# Update database
dotnet ef database update

# Drop database (development only)
dotnet ef database drop
```

### 3. API Development
```bash
# Watch mode for API development
cd src/BlazorMuseum.Server
dotnet watch run

# The API will restart automatically on code changes
```

### 4. Client Development
```bash
# Watch mode for client development (if running separately)
cd src/BlazorMuseum.Client
dotnet watch run

# Note: In this foundation, client is served by the server project
```

## Configuration

### Development Settings

The application uses `appsettings.Development.json` for local development:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "ConnectionStrings": {
    "DefaultConnection": "Data Source=museum.db"
  },
  "JwtSettings": {
    "SecretKey": "your-development-secret-key-minimum-32-characters",
    "Issuer": "BlazorMuseum",
    "Audience": "BlazorMuseum",
    "ExpirationMinutes": 60
  }
}
```

### Environment Variables

For production, use environment variables:

```bash
export ConnectionStrings__DefaultConnection="Data Source=/app/data/museum.db"
export JwtSettings__SecretKey="your-production-secret-key"
export ASPNETCORE_ENVIRONMENT="Production"
```

## Testing the Foundation

### 1. Health Check
```bash
curl https://localhost:7001/api/v1/health
```

Expected response:
```json
{
  "status": "Healthy",
  "timestamp": "2025-10-31T14:30:00Z",
  "version": "1.0.0",
  "dependencies": {
    "database": "Healthy",
    "logging": "Healthy"
  }
}
```

### 2. API Documentation
Visit https://localhost:7001/swagger to see the interactive API documentation.

### 3. Authentication Flow
```bash
# 1. Login (requires seeded user data)
curl -X POST https://localhost:7001/api/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email": "admin@museum.com", "password": "Admin123!"}'

# 2. Use returned token for authenticated requests
curl https://localhost:7001/api/v1/users/profile \
  -H "Authorization: Bearer <token-from-login>"
```

## Troubleshooting

### Common Issues

1. **Port conflicts**: If port 7001 is in use, modify `launchSettings.json`
2. **Database errors**: Ensure SQLite is available and migrations are applied
3. **JWT errors**: Verify the secret key is at least 32 characters
4. **CORS issues**: Development CORS policy allows all origins

### Debug Logging

Enable detailed logging by setting in `appsettings.Development.json`:
```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "Microsoft.EntityFrameworkCore": "Information"
    }
  }
}
```

### Performance

For development performance:
- SQLite database is in-memory by default
- Swagger is only enabled in Development environment
- Detailed errors are shown only in Development

## Next Steps

After the foundation is running:

1. **Add Museum Features**: Create additional entities (exhibits, artifacts, etc.)
2. **Enhance Authentication**: Integrate with external identity providers
3. **Add Client Features**: Build Blazor components for museum functionality
4. **Implement Testing**: Add comprehensive test coverage
5. **Deploy**: Set up production deployment pipeline

## Architecture Notes

### Authentication Flow
1. Client sends credentials to `/api/v1/auth/login`
2. Server validates and returns JWT token
3. Client stores token and includes in subsequent requests
4. Server validates token on protected endpoints

### API Versioning
- All endpoints use `/api/v1/` prefix
- Future versions will use `/api/v2/`, etc.
- Backward compatibility maintained across versions

### Logging Correlation
- Each request gets a unique TraceId
- Logs include TraceId for correlation between client and server
- Structured logging provides searchable JSON output

This foundation provides a solid base for building museum-specific features while following all constitutional requirements for architecture, security, and maintainability.