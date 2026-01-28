# Azure AI Foundry Network-Secured Agent Setup

A comprehensive toolkit for managing Azure AI Foundry capability hosts for network-secured agent deployments.

## 📖 Overview

This toolkit helps you configure and troubleshoot **capability hosts** for Azure AI Foundry projects with network-secured agents. Capability hosts enable:

- **VNET Integration**: Deploy agents in your private virtual network
- **BYO Resources**: Use your own Storage, Cosmos DB, and AI Search
- **Network Security**: Keep agent execution within your network boundaries
- **Resource Control**: Manage connections to your data sources

### What are Capability Hosts?

Capability hosts are Azure resources that:

1. **Account Capability Host**: Provides VNET integration for the AI Foundry account
2. **Project Capability Host**: Links project-specific connections (Storage, Cosmos DB, AI Search)

Both are required for network-secured agent deployments.

### When Do You Need This?

You need this toolkit when:

- ✅ Setting up AI Foundry with BYO network (VNET injection)
- ✅ Troubleshooting capability host failures
- ✅ Checking the status of your capability hosts
- ✅ Recovering from "Failed" provisioning states
- ✅ Validating agent connectivity

## 🎯 Prerequisites

Before using this toolkit, ensure you have:

### Required

1. **Azure Subscription** with an AI Foundry project deployed
2. **Azure CLI** installed and logged in
   ```bash
   az login
   az account set --subscription "your-subscription-id"
   ```
3. **Python 3.7+** with pip
   ```bash
   python --version
   ```
4. **Permissions**: Contributor or Owner role on:
   - AI Foundry project
   - Virtual Network (if creating capability hosts)
   - Resource Group

### Optional (for VNET injection)

5. **Virtual Network** with a dedicated subnet
   - Must be in the same region as AI Foundry
   - Minimum /28 address space (16 addresses)
   - Should be delegated to `Microsoft.CognitiveServices/accounts`

## 🚀 Setup Instructions

### 1. Clone the Repository

```bash
git clone https://github.com/cryptorators/ai-foundry-config-testing.git
cd ai-foundry-config-testing/utils
```

### 2. Create Environment File

Copy the example environment file:

```bash
cp .env.example .env
```

### 3. Fill in Configuration Values

Edit `.env` with your actual values:

```bash
# REQUIRED
projectResourceId=/subscriptions/{subscriptionId}/resourceGroups/{rg}/providers/Microsoft.CognitiveServices/accounts/{foundryName}/projects/{projectName}

# OPTIONAL (only if creating capability hosts)
subnetId=/subscriptions/{subscriptionId}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnetName}/subnets/{subnetName}

# OPTIONAL (for multi-tenant scenarios)
tenantId={your-tenant-id}
```

**How to find these values:**

- **projectResourceId**: Azure Portal → Your AI Foundry Project → Properties → Resource ID
- **subnetId**: Azure Portal → Virtual Network → Subnets → Properties → Resource ID
- **tenantId**: Azure Portal → Azure Active Directory → Properties → Tenant ID

### 4. Install Dependencies

```bash
pip install -r requirements.txt
```

Or install manually:

```bash
pip install requests azure-identity python-dotenv jupyter
```

### 5. Launch Jupyter Notebook

```bash
jupyter notebook network-secured-agent-setup.ipynb
```

## 📚 Usage Guide

### ⚠️ Important: Run Cell-by-Cell

**DO NOT use "Run All"!** This notebook contains operations that:
- Create Azure resources (costs money)
- Delete Azure resources (permanent)
- Require manual configuration before running

**Always execute cells one by one** and read the comments before running.

### Decision Tree: What Should I Do?

#### 🆕 First Time Setup

**Goal**: Set up capability hosts for a new AI Foundry project

**Steps**:
1. Run: Install dependencies cell
2. Run: Setup and authentication cell
3. Run: **Get Account Capability Host** (check if exists)
4. Run: **Get Project Capability Host** (check if exists)
5. If not found: Edit and run **Create Account Capability Host**
6. If not found: Edit and run **Create Project Capability Host**
7. Run: **POST to Azure OpenAI Service** (validate connectivity)

**Expected time**: 15-20 minutes (creation takes time)

#### 🔧 Troubleshooting Failed Deployment

**Goal**: Recover from a capability host in "Failed" state

**Steps**:
1. Run: **Get Account Capability Host** → Look for "Failed" in provisioningState
2. Run: **Get Project Capability Host** → Look for "Failed" in provisioningState
3. If Failed: Run **Delete Project Capability Host** first (type YES)
4. If Failed: Run **Delete Account Capability Host** (type YES)
5. Wait 5-10 minutes for deletion to complete
6. Re-run: **Get Account Capability Host** (verify deletion)
7. Re-run: **Get Project Capability Host** (verify deletion)
8. Run: **Create Account Capability Host** (with correct subnet)
9. Run: **Create Project Capability Host** (with correct connections)

**Expected time**: 30-40 minutes (includes deletion wait time)

#### 👀 Just Checking Status

**Goal**: View current capability host configuration (read-only)

**Steps** (all safe):
1. Run: Setup and authentication cell
2. Run: **Get Account Capability Host** ✅
3. Run: **Get Project Capability Host** ✅
4. Run: **Get Connections** ✅

**Expected time**: 1-2 minutes

#### 🧪 Testing Connectivity

**Goal**: Verify your AI Foundry setup works end-to-end

**Steps**:
1. Run: Setup and authentication cell
2. Run: **POST to Azure OpenAI Service** (sends test prompt)
3. Run: **Get Agents** (lists configured agents)

**Expected time**: 1-2 minutes

### Understanding the Output

#### Account Capability Host Output

```json
{
  "value": [
    {
      "id": "/subscriptions/.../capabilityHosts/...",
      "properties": {
        "provisioningState": "Succeeded",  // ← Check this!
        "customerSubnet": "/subscriptions/.../subnets/subnet-agents",
        "capabilityHostKind": "Agents"
      }
    }
  ]
}
```

**What to check**:
- ✅ `provisioningState: "Succeeded"` - All good!
- ⚠️ `provisioningState: "Provisioning"` - Wait for it to complete
- ❌ `provisioningState: "Failed"` - Needs troubleshooting

#### Project Capability Host Output

```json
{
  "value": [
    {
      "properties": {
        "provisioningState": "Succeeded",
        "vectorStoreConnections": ["search-connection"],
        "storageConnections": ["storage-connection"],
        "threadStorageConnections": ["cosmos-connection"]
      }
    }
  ]
}
```

**What to check**:
- ✅ All connection arrays have values
- ✅ Connection names match your project's actual connections
- ❌ Empty arrays or wrong names = configuration error

## 🔍 Common Scenarios

### Scenario 1: Creating Capability Hosts from Scratch

**When**: First time setup, no capability hosts exist

**Cells to run (in order)**:

| Step | Cell | Action | Notes |
|------|------|--------|-------|
| 1 | Install dependencies | Run as-is | Safe |
| 2 | Setup | Run as-is | Reads .env file |
| 3 | Get Account Capability Host | Run as-is | Should return empty |
| 4 | Create Account Capability Host | **Edit subnet ID first!** | Takes 10-15 min |
| 5 | Get Connections | Run as-is | Find your connection names |
| 6 | Create Project Capability Host | **Edit connections first!** | Takes 5-10 min |
| 7 | POST to Azure OpenAI | Run as-is | Validate setup |

### Scenario 2: Recovering from Failed State

**When**: Capability host shows `"provisioningState": "Failed"`

**Cells to run (in order)**:

| Step | Cell | Action | Notes |
|------|------|--------|-------|
| 1 | Setup | Run as-is | Authenticate |
| 2 | Get Project Capability Host | Run as-is | Check state |
| 3 | Delete Project Capability Host | Type YES | Delete project first! |
| 4 | Get Account Capability Host | Run as-is | Check state |
| 5 | Delete Account Capability Host | Type YES | Then delete account |
| 6 | Wait | - | 5-10 minutes |
| 7 | Get Account Capability Host | Run as-is | Verify deletion |
| 8 | Create Account Capability Host | Edit subnet | Recreate |
| 9 | Create Project Capability Host | Edit connections | Recreate |

### Scenario 3: Checking Status Only

**When**: Just want to see current configuration

**Cells to run**:

| Step | Cell | Action | Safe? |
|------|------|--------|-------|
| 1 | Setup | Run as-is | ✅ Yes |
| 2 | Get Account Capability Host | Run as-is | ✅ Yes |
| 3 | Get Project Capability Host | Run as-is | ✅ Yes |
| 4 | Get Connections | Run as-is | ✅ Yes |

## 🐛 Troubleshooting

### Error: "projectResourceId environment variable is not set"

**Cause**: `.env` file not found or not configured

**Solution**:
1. Check that `.env` file exists in the `utils/` directory
2. Verify `projectResourceId` is set in `.env`
3. Re-run the Setup cell

### Error: "Invalid projectResourceId format"

**Cause**: Incorrect resource ID format

**Solution**:
Check your `projectResourceId` matches this format exactly:
```
/subscriptions/{subscriptionId}/resourceGroups/{rg}/providers/Microsoft.CognitiveServices/accounts/{foundryName}/projects/{projectName}
```

### Error: Capability host is in "Failed" state

**Cause**: Common reasons:
- Invalid subnet ID
- Subnet in different region
- Insufficient subnet address space
- Network configuration issues
- Wrong connection names

**Solution**:
1. Run "Get Account Capability Host" or "Get Project Capability Host"
2. Look for error details in the response
3. Delete the failed capability host
4. Wait 5-10 minutes
5. Fix the configuration issue
6. Recreate the capability host

### Error: "No subnet found. Foundry is NOT VNET INJECTED"

**Cause**: No account capability host exists

**Solution**:
This is normal if you haven't created one yet. If you need VNET injection:
1. Ensure you have a subnet configured in `.env`
2. Run "Create Account Capability Host" cell

### Error: Authentication failures

**Cause**: Not logged into Azure CLI or expired credentials

**Solution**:
```bash
az login
az account set --subscription "your-subscription-id"
```

Then re-run the Setup cell.

### Error: "No model deployments found"

**Cause**: No models deployed in your AI Foundry project

**Solution**:
1. Go to Azure Portal → Your AI Foundry Project
2. Navigate to Deployments
3. Deploy a model (e.g., GPT-4, GPT-3.5)
4. Wait for deployment to complete
5. Re-run the test cell

### Issue: Creation takes too long (>20 minutes)

**Symptom**: Capability host stuck in "Provisioning" state

**Solution**:
1. Check Azure Portal for any deployment errors
2. Verify subnet configuration is correct
3. Ensure region compatibility
4. If stuck for >30 minutes, contact Azure support

### Issue: Deletion doesn't complete

**Symptom**: Capability host still shows after deletion

**Solution**:
1. Wait 10 minutes (deletions can be slow)
2. Re-run the Get cell to check status
3. If still present after 20 minutes, try deleting via Azure Portal
4. Last resort: Contact Azure support

## 📖 API Reference

### Azure AI Foundry REST API

- **API Version**: `2025-06-01`
- **Base URL**: `https://management.azure.com`
- **Authentication**: Azure Active Directory Bearer Token

### Capability Hosts Endpoints

| Method | Endpoint | Purpose |
|--------|----------|---------|
| GET | `/subscriptions/{sub}/resourceGroups/{rg}/providers/Microsoft.CognitiveServices/accounts/{foundry}/capabilityHosts` | List account capability hosts |
| PUT | `/subscriptions/{sub}/resourceGroups/{rg}/providers/Microsoft.CognitiveServices/accounts/{foundry}/capabilityHosts/{name}` | Create/update account capability host |
| DELETE | `/subscriptions/{sub}/resourceGroups/{rg}/providers/Microsoft.CognitiveServices/accounts/{foundry}/capabilityHosts/{name}` | Delete account capability host |
| GET | `/subscriptions/{sub}/resourceGroups/{rg}/providers/Microsoft.CognitiveServices/accounts/{foundry}/projects/{project}/capabilityHosts` | List project capability hosts |
| PUT | `/subscriptions/{sub}/resourceGroups/{rg}/providers/Microsoft.CognitiveServices/accounts/{foundry}/projects/{project}/capabilityHosts/{name}` | Create/update project capability host |
| DELETE | `/subscriptions/{sub}/resourceGroups/{rg}/providers/Microsoft.CognitiveServices/accounts/{foundry}/projects/{project}/capabilityHosts/{name}` | Delete project capability host |

### Official Documentation

- [Azure AI Foundry Documentation](https://learn.microsoft.com/en-us/azure/ai-foundry/)
- [Virtual Networks for Agents](https://learn.microsoft.com/en-us/azure/ai-foundry/agents/how-to/virtual-networks)
- [BYO Resources Setup](https://learn.microsoft.com/en-us/azure/ai-foundry/agents/how-to/use-your-own-resources)
- [Azure AI Foundry REST API](https://learn.microsoft.com/en-us/rest/api/aiservices/)

## 🔒 Security Best Practices

1. **Never commit `.env` file**: It contains sensitive resource IDs
2. **Use Managed Identity**: When running in Azure (VMs, App Service)
3. **Rotate credentials**: Regularly update access keys
4. **Limit permissions**: Use least-privilege principle
5. **Audit access**: Enable Azure Activity Logs

## 🤝 Contributing

Found an issue or have a suggestion? Please:
1. Check existing issues in the repository
2. Create a new issue with details
3. Include notebook cell outputs if reporting a bug

## 📄 License

See the LICENSE file in the repository root.

## 💡 Tips and Tricks

### Tip 1: Use Azure CLI for Quick Checks

```bash
# Get capability hosts
az rest --method get \
  --url "https://management.azure.com/subscriptions/{sub}/resourceGroups/{rg}/providers/Microsoft.CognitiveServices/accounts/{foundry}/capabilityHosts?api-version=2025-06-01"

# Get project capability hosts
az rest --method get \
  --url "https://management.azure.com/subscriptions/{sub}/resourceGroups/{rg}/providers/Microsoft.CognitiveServices/accounts/{foundry}/projects/{project}/capabilityHosts?api-version=2025-06-01"
```

### Tip 2: Save Cell Outputs

When troubleshooting, save the JSON outputs:
```python
with open('output.json', 'w') as f:
    json.dump(response_json, f, indent=4)
```

### Tip 3: Check Azure Activity Logs

Azure Portal → Monitor → Activity Log → Filter by resource

### Tip 4: Use VS Code for Better Experience

Install extensions:
- Jupyter
- Python
- Azure Account

## 📞 Support

- **Azure Support**: [Azure Portal → Help + Support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
- **Documentation**: [Microsoft Learn](https://learn.microsoft.com/azure/)
- **Community**: [Microsoft Q&A](https://learn.microsoft.com/answers/)

---

**Last Updated**: January 2026  
**Maintained by**: cryptorators/ai-foundry-config-testing contributors
