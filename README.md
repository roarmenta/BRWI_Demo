# BRWI Demo - Power Platform ALM with GitHub Actions

This repository demonstrates **Application Lifecycle Management (ALM)** for Microsoft Power Platform solutions using **GitHub Actions**. It showcases a complete DevOps pipeline for exporting, versioning, building, and deploying Power Platform solutions across different environments.

## 🎯 What This Demo Shows

This demo illustrates how organizations can implement modern ALM practices for Power Platform development, including:

- **Source Control Integration** - Store Power Platform solutions in Git
- **Automated Export** - Extract solutions from development environments
- **Branch Management** - Create feature branches for collaborative development  
- **Automated Building** - Pack solutions into deployment artifacts
- **Environment Promotion** - Deploy solutions from Dev → Test → Production
- **Release Management** - Coordinate releases using GitHub's release features

## 🏗️ Repository Structure

```
BRWI_Demo/
├── .github/workflows/          # GitHub Actions workflow definitions
│   ├── export-and-branch-solution.yml     # Export from DEV and create branch
│   ├── pack-solution.yml                  # Pack solution into zip file
│   ├── release-solution-to-test-with-spn.yml  # Deploy to Test environment
│   └── release-action-call.yml            # Reusable release workflow
├── solutions/                  # Source-controlled solution files
│   └── BRWISolution/          # Unpacked BRWI solution components
└── README.md                  # This documentation
```

## 🔄 Workflows Overview

### 1. **Export and Branch Solution** (`export-and-branch-solution.yml`)

**Purpose**: Extract the latest solution from your Power Platform DEV environment and prepare it for source control.

**When to use**: 
- After making changes in the Power Platform maker portal
- Before starting new feature development
- To sync local repository with environment changes

**What it does**:
1. 🔐 Authenticates to Power Platform using Service Principal
2. 📤 Exports the solution from DEV environment as a `.zip` file
3. 📂 Unpacks the solution into source-friendly files
4. 🌿 Creates a new Git branch with the changes
5. 💾 Commits and pushes the unpacked solution files

**Trigger**: Manual (workflow_dispatch)

**Required Secrets**:
- `PowerPlatformSPN` - Service Principal client secret

### 2. **Pack Solution** (`pack-solution.yml`)

**Purpose**: Convert source-controlled solution files back into a deployable `.zip` file.

**When to use**:
- Building artifacts for deployment
- Creating release packages
- Validating solution integrity

**What it does**:
1. 📥 Checks out the repository
2. 🛠️ Installs Power Platform CLI
3. 📦 Packs the solution files into a `.zip` archive
4. ⬆️ Uploads the packed solution as a GitHub artifact

**Trigger**: Manual (workflow_dispatch)

### 3. **Release to Test Environment** (`release-solution-to-test-with-spn.yml`)

**Purpose**: Deploy solutions to the Test environment with managed solution conversion.

**When to use**:
- Promoting solutions from DEV to TEST
- Validating solutions in a production-like environment
- Part of release pipeline

**What it does**:

**Job 1: Convert to Managed**
1. 📦 Packs the unmanaged solution from source control
2. 📥 Imports the unmanaged solution to BUILD environment  
3. 📤 Exports it as a managed solution
4. ⬆️ Uploads managed solution as artifact

**Job 2: Deploy to Test**
1. ⬇️ Downloads the managed solution artifact
2. 🚀 Imports the managed solution to TEST environment
3. 📢 Publishes all customizations

**Trigger**: Manual (workflow_dispatch) or Release creation

**Required Secrets**:
- `PowerPlatformSPN` - Service Principal client secret

### 4. **Release Action Call** (`release-action-call.yml`)

**Purpose**: Reusable workflow that orchestrates solution releases.

**When to use**:
- Automated deployments triggered by GitHub releases
- Standardized release process across multiple solutions

**What it does**:
1. 🔗 Calls the release workflow with predefined parameters
2. 📋 Passes environment URLs and authentication details
3. 🔄 Reuses the release logic for consistency

**Trigger**: When a GitHub release is created

## 🌐 Environment Configuration

The demo uses three Power Platform environments:

| Environment | Purpose | URL |
|------------|---------|-----|
| **DEV** | Development & Testing | `https://roar-brwi-dev.crm.dynamics.com/` |
| **BUILD** | Managed Solution Conversion | `https://roar-brwi-dev.crm.dynamics.com/` |
| **TEST** | User Acceptance Testing | `https://roar-brwi-test.crm.dynamics.com/` |

## 🔐 Security & Authentication

This demo uses **Service Principal authentication** for secure, automated access to Power Platform environments.

**Required GitHub Secrets**:
- `PowerPlatformSPN` - Client secret for the registered Azure AD application

**Environment Variables** (configured in workflows):
- `CLIENT_ID` - Azure AD Application (Client) ID
- `TENANT_ID` - Azure AD Tenant ID
- `ENVIRONMENT_URL` - Power Platform environment URL

## 🚀 Getting Started

### Prerequisites
1. **Power Platform environments** (DEV, TEST, and optionally separate BUILD)
2. **Azure AD App Registration** with appropriate permissions
3. **GitHub repository** with secrets configured
4. **Power Platform solution** to manage

### Setup Steps

1. **Clone this repository**
   ```bash
   git clone https://github.com/roarmenta/BRWI_Demo.git
   ```

2. **Configure GitHub Secrets**
   - Go to Settings → Secrets and variables → Actions
   - Add `PowerPlatformSPN` with your service principal client secret

3. **Update Environment URLs**
   - Edit workflow files to match your Power Platform environment URLs
   - Update `CLIENT_ID` and `TENANT_ID` values

4. **Run Export Workflow**
   - Go to Actions → "Export and Branch BRWI Solution"
   - Click "Run workflow" to extract your solution

## 📋 Usage Scenarios

### Scenario 1: Developer Makes Changes
1. Developer modifies solution in Power Platform maker portal
2. Run **Export and Branch** workflow to capture changes
3. Review and merge the generated pull request
4. Changes are now in source control

### Scenario 2: Deploy to Test Environment  
1. Ensure latest changes are in main branch
2. Run **Release to Test** workflow
3. Solution is converted to managed and deployed to TEST
4. Validate functionality in test environment

### Scenario 3: Production Release
1. Create a GitHub release from main branch
2. **Release Action Call** workflow automatically triggers
3. Solution is built and deployed through the pipeline
4. Track deployment status in GitHub Actions

## 🎓 Learning Objectives

After exploring this demo, you'll understand how to:

- ✅ **Set up automated solution export** from Power Platform environments
- ✅ **Implement source control** for Power Platform solutions  
- ✅ **Create deployment pipelines** using GitHub Actions
- ✅ **Convert unmanaged to managed solutions** automatically
- ✅ **Deploy across multiple environments** safely
- ✅ **Integrate with release management** processes
- ✅ **Use service principal authentication** for automation
- ✅ **Handle artifacts and dependencies** in CI/CD pipelines

## 🔗 Additional Resources

- [Power Platform ALM Documentation](https://docs.microsoft.com/en-us/power-platform/alm/)
- [GitHub Actions for Power Platform](https://github.com/microsoft/powerplatform-actions)
- [Power Platform CLI Reference](https://docs.microsoft.com/en-us/power-platform/developer/cli/introduction)
- [Service Principal Setup Guide](https://docs.microsoft.com/en-us/power-platform/admin/powershell-create-service-principal)

## 🤝 Contributing

This is a demo repository designed for learning and demonstration purposes. Feel free to fork and adapt these workflows for your own Power Platform ALM needs.

## 📄 License

This project is provided as-is for educational and demonstration purposes.

