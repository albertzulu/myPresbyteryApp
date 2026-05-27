# Presbytery Management System Web Application

A comprehensive web application for managing presbytery operations, members, events, and congregations built with .NET 8 and deployed on Azure App Service with deployment slots.

## 🏗️ Architecture Overview

The application follows a clean architecture with separation of concerns:
- **Presentation Layer**: REST API (ASP.NET Core)
- **Business Logic Layer**: Data access and business rules
- **Data Access Layer**: Entity Framework Core with SQL Server
- **Infrastructure**: Azure App Service with deployment slots

## 📋 Technology Stack

- **Runtime**: .NET 8 LTS
- **Framework**: ASP.NET Core 8.0
- **Database**: Azure SQL Database
- **ORM**: Entity Framework Core 8.0
- **Logging**: Serilog with structured logging
- **Monitoring**: Azure Application Insights
- **CI/CD**: GitHub Actions
- **Infrastructure**: Azure ARM Templates

## 🚀 Quick Start - Local Development

### Prerequisites
- .NET 8 SDK or higher
- SQL Server (LocalDB or Express)
- Git

### Setup Steps

1. **Clone and navigate to repository**
   ```bash
   git clone https://github.com/albertzulu/myPresbyteryApp.git
   cd myPresbyteryApp
   ```

2. **Restore dependencies**
   ```bash
   dotnet restore
   ```

3. **Configure database connection**
   Edit `src/PresbyteryApp.API/appsettings.Development.json`:
   ```json
   {
     "ConnectionStrings": {
       "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=PresbyteryDB;Trusted_Connection=true;"
     }
   }
   ```

4. **Create and migrate database**
   ```bash
   cd src/PresbyteryApp.API
   dotnet ef database update --project ../PresbyteryApp.Data
   ```

5. **Run the application**
   ```bash
   dotnet run
   ```
   
   API available at: `https://localhost:5001`
   Swagger docs: `https://localhost:5001/swagger`

## 📦 Project Structure

```
src/
├── PresbyteryApp.Models/       # Domain models (Presbytery, Member, Event, etc.)
├── PresbyteryApp.Data/         # Entity Framework Core DbContext & configurations
└── PresbyteryApp.API/          # ASP.NET Core REST API with controllers

deployment/
├── azure-deploy.json           # ARM template for Azure resources
└── deploy.sh                   # Deployment automation script

.github/workflows/
├── deploy-to-staging.yml       # CI/CD for develop branch
└── deploy-to-production.yml    # CI/CD for main branch
```

## 📊 Database Schema

### Core Entities
- **Presbytery**: Regional organizational units
- **Congregation**: Individual churches
- **Member**: Church members with roles
- **MemberRole**: Role assignments (Pastor, Elder, Deacon, etc.)
- **Event**: Church events and gatherings
- **EventAttendance**: Attendance tracking
- **EventPayment**: Payment records
- **DeceasedMember**: Historical member records

## 🔄 CI/CD Deployment Pipeline

### Workflow Triggers
- **Staging Slot**: Push to `develop` branch → Automatic deployment
- **Production Slot**: Push to `main` branch → Automatic deployment with validation

### Deployment Features
✅ Automated testing before deployment  
✅ Health check validation  
✅ Zero-downtime blue-green deployment  
✅ Automatic rollback on failure  
✅ Structured logging and monitoring  

## ☁️ Azure Deployment Setup

### Prerequisites
- Azure Subscription
- Azure CLI installed
- Service Principal created

### Deploy Infrastructure

```bash
# 1. Login to Azure
az login

# 2. Create Resource Group
az group create \
  --name myPresbyteryRG \
  --location eastus

# 3. Deploy via ARM template
az deployment group create \
  --resource-group myPresbyteryRG \
  --template-file deployment/azure-deploy.json \
  --parameters appServiceName=myPresbyteryApp appServicePlanName=myPresbyteryPlan

# 4. Get Application Insights key
az resource show \
  --resource-group myPresbyteryRG \
  --name myPresbyteryApp-ai \
  --resource-type "Microsoft.Insights/components" \
  --query properties.InstrumentationKey
```

### GitHub Secrets Configuration

1. Navigate to **Settings → Secrets and variables → Actions**
2. Add the following secrets:

```
AZURE_CREDENTIALS: <Service Principal JSON>
SONAR_TOKEN: <SonarCloud token for code analysis>
```

### Create Azure Service Principal

```bash
az ad sp create-for-rbac \
  --name GitHubPresbyteryApp \
  --role contributor \
  --scopes /subscriptions/{SUBSCRIPTION_ID} \
  --json-auth
```

## 🔐 Security Features

✅ HTTPS enforcement  
✅ TLS 1.2 minimum  
✅ SQL injection protection (parameterized queries)  
✅ CORS policy configuration  
✅ Structured logging without sensitive data  
✅ Health check endpoints  
✅ Database connection retry policies  
✅ Managed Identity support  

## 📈 Monitoring & Logging

### Application Insights
- Real-time performance metrics
- Dependency tracking
- Custom event logging
- Alert configuration

### Serilog Configuration
- Console output for development
- File-based rolling logs (30-day retention)
- Structured events for analysis

### Health Check Endpoint
- **URL**: `/health`
- **Status**: Returns API and database health

## 🧪 Testing

```bash
# Run all tests
dotnet test

# Run specific test project
dotnet test tests/PresbyteryApp.Tests/

# Run with code coverage
dotnet test /p:CollectCoverage=true
```

## 📝 API Documentation

### Base URL
```
https://mypresbyteryapp.azurewebsites.net/api
```

### Endpoints

#### Presbytery
- `GET /presbytery` - List all presbyteries
- `GET /presbytery/{id}` - Get presbytery details
- `POST /presbytery` - Create presbytery
- `PUT /presbytery/{id}` - Update presbytery
- `DELETE /presbytery/{id}` - Delete presbytery

*Additional CRUD endpoints available for Members, Congregations, Events, and Payments*

## 🐛 Troubleshooting

### Database Connection Issues
```bash
# Test Azure SQL connection
az sql db show \
  --resource-group myPresbyteryRG \
  --server mypresbyteryserver \
  --name PresbyteryDB
```

### View Application Logs
```bash
# Stream logs from Azure
az webapp log tail \
  --resource-group myPresbyteryRG \
  --name myPresbyteryApp
```

### Check Deployment Status
```bash
# List deployment slots
az webapp deployment slot list \
  --resource-group myPresbyteryRG \
  --name myPresbyteryApp
```

## 🔄 Blue-Green Deployment

Safe deployments enabled through Azure App Service slots:

1. Deploy to staging slot (separate environment)
2. Run smoke tests and validation
3. Swap slots to production (zero downtime)
4. Instant rollback if needed

```bash
# Manual slot swap
az webapp deployment slot swap \
  --resource-group myPresbyteryRG \
  --name myPresbyteryApp \
  --slot staging
```

## 🤝 Contributing

1. Create feature branch from `develop`
   ```bash
   git checkout develop
   git checkout -b feature/your-feature
   ```

2. Commit changes
   ```bash
   git commit -m "Add your feature"
   ```

3. Push and create Pull Request
   ```bash
   git push origin feature/your-feature
   ```

4. After approval, merge to main for production deployment

## 📚 Documentation

- [.NET 8 Documentation](https://learn.microsoft.com/dotnet/)
- [Entity Framework Core](https://learn.microsoft.com/ef/core/)
- [Azure App Service](https://learn.microsoft.com/azure/app-service/)
- [GitHub Actions](https://docs.github.com/actions)

## 📄 License

GNU General Public License v2.0 - See LICENSE file

## 📞 Support

For issues and questions:
- Create a GitHub Issue
- Contact: albertzulu@example.com

---

**Status**: Ready for Production ✅  
**Runtime**: .NET 8 LTS  
**Last Updated**: 2026-05-27
