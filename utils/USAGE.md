# Network-Secured Agent Setup Notebook - Cell-by-Cell Usage Guide

This document provides detailed instructions for each cell in the `network-secured-agent-setup.ipynb` notebook.

## 📋 Quick Reference Table

| Cell # | Type | Cell Name | Safe to Run? | Dependencies | Purpose |
|--------|------|-----------|--------------|--------------|---------|
| 1 | Markdown | Title & Quick Start | N/A | None | Introduction and warnings |
| 2 | Code | Install Dependencies | ✅ Yes | None | Installs required Python packages |
| 3 | Markdown | Prerequisites | N/A | None | Setup requirements |
| 4 | Code | Setup & Authentication | ✅ Yes | .env file | Loads config and authenticates |
| 5 | Markdown | Decision Tree | N/A | None | Workflow guidance |
| 6 | Markdown | Diagnostic Section Header | N/A | None | Section divider |
| 7 | Markdown | Get Account Capability Host Header | N/A | None | Cell description |
| 8 | Code | Get Account Capability Host | ✅ Yes | Cell 4 | Reads account config |
| 9 | Markdown | Get Project Capability Host Header | N/A | None | Cell description |
| 10 | Code | Get Project Capability Host | ✅ Yes | Cell 4 | Reads project config |
| 11 | Markdown | Modification Section Header | N/A | None | Section divider |
| 12 | Markdown | Create Account Capability Host Header | N/A | None | Cell description |
| 13 | Code | Create Account Capability Host | ⚠️ NO | Cell 4, Edit required | Creates account resource |
| 14 | Markdown | Create Project Capability Host Header | N/A | None | Cell description |
| 15 | Code | Create Project Capability Host | ⚠️ NO | Cell 4,8,13, Edit required | Creates project resource |
| 16 | Markdown | Delete Account Capability Host Header | N/A | None | Cell description |
| 17 | Code | Delete Account Capability Host | 🚨 NO | Cell 4, Type YES | Deletes account resource |
| 18 | Markdown | Delete Project Capability Host Header | N/A | None | Cell description |
| 19 | Code | Delete Project Capability Host | 🚨 NO | Cell 4, Type YES | Deletes project resource |
| 20 | Markdown | Validation Section Header | N/A | None | Section divider |
| 21 | Markdown | POST to Azure OpenAI Header | N/A | None | Cell description |
| 22 | Code | POST to Azure OpenAI Service | ✅ Yes | Cell 4 | Tests connectivity |
| 23 | Markdown | Connections Section Header | N/A | None | Section divider |
| 24 | Markdown | Get Connections Header | N/A | None | Cell description |
| 25 | Code | Get Connections | ✅ Yes | Cell 4 | Lists project connections |
| 26 | Markdown | Update Connection Header | N/A | None | Cell description |
| 27 | Code | Update Connection | ⚠️ NO | Cell 4, Edit required | Updates a connection |
| 28 | Markdown | Agents Section Header | N/A | None | Section divider |
| 29 | Markdown | Get Agents Header | N/A | None | Cell description |
| 30 | Code | Get Agents | ✅ Yes | Cell 4 | Lists configured agents |

## 📖 Detailed Cell-by-Cell Instructions

### Cell 1: Title & Quick Start (Markdown)

**Type**: Markdown  
**Safe to Run**: N/A  
**Purpose**: Provides introduction, warnings, and quick start guide

**What it contains**:
- Notebook title and description
- ⚠️ Critical warning about not using "Run All"
- Quick start instructions for read-only exploration
- Overview of safe cells to run

**When to read**: Before starting any operations

**Action required**: Read carefully, especially the warnings

---

### Cell 2: Install Dependencies (Code)

**Type**: Code  
**Safe to Run**: ✅ Yes  
**Dependencies**: None  
**Purpose**: Installs required Python packages

**What it does**:
```bash
pip install requests azure.identity python-dotenv
```

**Installs**:
- `requests`: HTTP client for API calls
- `azure.identity`: Azure authentication
- `python-dotenv`: Environment variable management

**When to run**: 
- First time using the notebook
- After updating Python environment
- If you get import errors

**Expected output**:
```
Successfully installed requests-X.X.X azure-identity-X.X.X python-dotenv-X.X.X
```

**Troubleshooting**:
- If pip not found: Ensure Python is installed
- If permission error: Use `pip install --user` instead
- If version conflict: Consider using a virtual environment

**Time to complete**: 10-30 seconds

---

### Cell 3: Prerequisites (Markdown)

**Type**: Markdown  
**Safe to Run**: N/A  
**Purpose**: Lists setup requirements

**What it contains**:
- Azure subscription requirements
- Azure CLI setup instructions
- Python version requirements
- Permission requirements
- .env file format

**When to read**: Before first run

**Action required**: Verify you meet all prerequisites

---

### Cell 4: Setup & Authentication (Code)

**Type**: Code  
**Safe to Run**: ✅ Yes (read-only)  
**Dependencies**: `.env` file must exist  
**Purpose**: Loads configuration and authenticates with Azure

**What it does**:
1. Loads environment variables from `.env` file
2. Validates `projectResourceId` is set
3. Parses resource ID into components
4. Authenticates using `DefaultAzureCredential`
5. Prepares HTTP headers for API calls
6. Displays parsed configuration

**Variables set**:
- `project_resource_id`: Full project resource ID
- `subnet_id`: Subnet resource ID (optional)
- `subscription_id`: Azure subscription ID
- `rg`: Resource group name
- `foundry_name`: AI Foundry account name
- `project_name`: AI Foundry project name
- `tenant_id`: Azure AD tenant ID (optional)
- `base_url`: Base URL for API calls
- `credential`: Azure credential object
- `token`: Access token for management API
- `headers`: HTTP headers with authorization

**When to run**:
- First cell to run (after installing dependencies)
- After changing `.env` file
- When credentials expire (typically after 1 hour)

**Expected output**:
```
✅ Parsed project resource ID: /subscriptions/...
   📦 Subscription: xxxx-xxxx-xxxx
   📁 Resource Group: my-rg
   🏭 Foundry: my-foundry
   📋 Project: my-project

🔧 Azure CLI commands to get capability hosts:
   ...

✅ Setup complete! Ready to execute operations.
```

**Troubleshooting**:
- **Error: "projectResourceId environment variable is not set"**
  - Check `.env` file exists in `utils/` directory
  - Verify `projectResourceId` is set (not empty)
  
- **Error: "Invalid projectResourceId format"**
  - Check format matches: `/subscriptions/{sub}/resourceGroups/{rg}/providers/Microsoft.CognitiveServices/accounts/{foundry}/projects/{project}`
  
- **Error: "DefaultAzureCredential failed"**
  - Run `az login` in terminal
  - Verify you're logged into correct subscription
  - Check you have access to the project

**Time to complete**: 2-5 seconds

---

### Cell 5: Decision Tree (Markdown)

**Type**: Markdown  
**Safe to Run**: N/A  
**Purpose**: Provides workflow guidance based on your scenario

**What it contains**:
- First time setup workflow
- Troubleshooting failed deployment workflow
- Status checking workflow
- Connectivity testing workflow

**When to read**: Before deciding which cells to run

**Action required**: Choose your scenario and follow the recommended steps

---

### Cells 6-7: Diagnostic Operations Section Headers (Markdown)

**Type**: Markdown  
**Safe to Run**: N/A  
**Purpose**: Section divider and introduction to read-only operations

---

### Cell 8: Get Account Capability Host (Code)

**Type**: Code  
**Safe to Run**: ✅ Yes (read-only)  
**Dependencies**: Cell 4 (Setup)  
**Purpose**: Retrieves account-level capability host configuration

**What it does**:
1. Makes GET request to account capability hosts endpoint
2. Checks if subnet is configured (VNET injection)
3. Displays full JSON response

**API Call**:
```
GET https://management.azure.com/subscriptions/{sub}/resourceGroups/{rg}/providers/Microsoft.CognitiveServices/accounts/{foundry}/capabilityHosts?api-version=2025-06-01
```

**When to run**:
- To check if account capability host exists
- To verify VNET injection is configured
- To check provisioning state
- To get subnet ID being used

**Expected output (if exists)**:
```json
{
  "value": [
    {
      "id": "/subscriptions/.../capabilityHosts/...",
      "name": "foundry-name-caphost",
      "properties": {
        "provisioningState": "Succeeded",
        "customerSubnet": "/subscriptions/.../subnets/subnet-agents",
        "capabilityHostKind": "Agents"
      }
    }
  ]
}
```

**Expected output (if not exists)**:
```json
{
  "value": []
}
```

**What to check**:
- ✅ `provisioningState: "Succeeded"` - All good
- ⚠️ `provisioningState: "Provisioning"` - Wait for completion
- ❌ `provisioningState: "Failed"` - Needs troubleshooting (delete and recreate)
- ✅ `customerSubnet` matches your expected subnet
- ✅ `capabilityHostKind` is "Agents"

**Troubleshooting**:
- **Status code 404**: Resource not found (normal if not created yet)
- **Status code 403**: Permission denied (check your role assignments)
- **Status code 401**: Authentication failed (re-run Cell 4)

**Time to complete**: 1-2 seconds

---

### Cell 10: Get Project Capability Host (Code)

**Type**: Code  
**Safe to Run**: ✅ Yes (read-only)  
**Dependencies**: Cell 4 (Setup)  
**Purpose**: Retrieves project-level capability host configuration

**What it does**:
1. Makes GET request to project capability hosts endpoint
2. Displays status code
3. Displays full JSON response

**API Call**:
```
GET https://management.azure.com/subscriptions/{sub}/resourceGroups/{rg}/providers/Microsoft.CognitiveServices/accounts/{foundry}/projects/{project}/capabilityHosts?api-version=2025-06-01
```

**When to run**:
- To check if project capability host exists
- To verify connections are configured
- To check provisioning state
- Before creating project capability host

**Expected output (if exists)**:
```json
{
  "value": [
    {
      "id": "/subscriptions/.../capabilityHosts/projcaphost",
      "name": "projcaphost",
      "properties": {
        "provisioningState": "Succeeded",
        "capabilityHostKind": "Agents",
        "vectorStoreConnections": ["search-connection"],
        "storageConnections": ["storage-connection"],
        "threadStorageConnections": ["cosmos-connection"]
      }
    }
  ]
}
```

**What to check**:
- ✅ `provisioningState: "Succeeded"`
- ✅ All three connection arrays have values
- ✅ Connection names match your project's actual connections
- ❌ Empty arrays = configuration error

**Troubleshooting**:
- **Empty value array**: Not created yet (normal for new projects)
- **Missing connections**: Run Cell 25 to see available connections
- **Failed state**: Delete and recreate

**Time to complete**: 1-2 seconds

---

### Cell 13: Create Account Capability Host (Code)

**Type**: Code  
**Safe to Run**: ⚠️ NO - Creates Azure resource  
**Dependencies**: Cell 4 (Setup), subnet ID configured  
**Purpose**: Creates account-level capability host with VNET injection

**⚠️ BEFORE RUNNING**:
1. Ensure subnet ID is configured in `.env` OR
2. Edit the `customerSubnet` value in the payload
3. Verify subnet is in SAME REGION as AI Foundry
4. Ensure subnet has at least /28 address space

**What it does**:
1. Validates subnet ID is not a placeholder
2. If invalid: Prints error and exits
3. If valid: Creates account capability host via PUT request
4. Displays request payload and response

**API Call**:
```
PUT https://management.azure.com/subscriptions/{sub}/resourceGroups/{rg}/providers/Microsoft.CognitiveServices/accounts/{foundry}/capabilityHosts/{foundry}caphost?api-version=2025-06-01

Body:
{
  "properties": {
    "capabilityHostKind": "Agents",
    "customerSubnet": "/subscriptions/.../subnets/subnet-agents"
  }
}
```

**When to run**:
- First time setup (after verifying it doesn't exist with Cell 8)
- After deleting a failed capability host
- When changing subnet configuration

**When NOT to run**:
- If capability host already exists and is "Succeeded"
- If capability host is "Provisioning" (wait for it)
- If you don't have subnet configured

**Expected output (success)**:
```json
{
  "id": "/subscriptions/.../capabilityHosts/...",
  "properties": {
    "provisioningState": "Provisioning",
    "customerSubnet": "/subscriptions/.../subnets/subnet-agents"
  }
}
```

**Expected behavior**:
- Operation returns immediately with "Provisioning" state
- Actual creation takes 10-15 minutes in background
- Re-run Cell 8 to check progress

**Validation errors**:
```
❌ ERROR: subnet_id is not configured!

Please do ONE of the following:
  1. Set 'subnetId' in your .env file, then re-run the Setup cell
  2. OR edit the 'customerSubnet' value in the payload below
```

**Troubleshooting**:
- **Validation error**: Set subnet ID in `.env` or edit cell
- **Status code 400**: Invalid request (check subnet format)
- **Status code 409**: Resource already exists
- **Fails with "Failed" state**: Check subnet region, address space, delegation

**Time to complete**: 
- API call: 2-5 seconds
- Background provisioning: 10-15 minutes

**Cost impact**: Creates Azure resource (minimal cost for capability host itself)

---

### Cell 15: Create Project Capability Host (Code)

**Type**: Code  
**Safe to Run**: ⚠️ NO - Creates Azure resource  
**Dependencies**: Cell 4 (Setup), Cell 8 (account host exists), Cell 25 (connection names)  
**Purpose**: Creates project-level capability host with connection configurations

**⚠️ BEFORE RUNNING**:
1. Run Cell 25 to get available connection names
2. Edit the three connection arrays in the cell:
   - `vector_store_connections`: AI Search connections
   - `storage_connections`: Storage account connections
   - `thread_storage_connections`: Cosmos DB connections
3. Ensure account capability host exists and is "Succeeded"

**What it does**:
1. Checks if connection names look like defaults (warns if yes)
2. Creates project capability host via PUT request
3. Displays request payload and response

**API Call**:
```
PUT https://management.azure.com/subscriptions/{sub}/resourceGroups/{rg}/providers/Microsoft.CognitiveServices/accounts/{foundry}/projects/{project}/capabilityHosts/projcaphost?api-version=2025-06-01

Body:
{
  "properties": {
    "capabilityHostKind": "Agents",
    "vectorStoreConnections": ["your-search-connection"],
    "storageConnections": ["your-storage-connection"],
    "threadStorageConnections": ["your-cosmos-connection"]
  }
}
```

**When to run**:
- After account capability host is created
- First time setup
- After deleting a failed project capability host
- When changing connection configurations

**When NOT to run**:
- If project capability host already exists
- If account capability host doesn't exist yet
- If you haven't verified connection names

**Expected output (success)**:
```json
{
  "id": "/subscriptions/.../capabilityHosts/projcaphost",
  "properties": {
    "provisioningState": "Provisioning",
    "vectorStoreConnections": ["..."],
    "storageConnections": ["..."],
    "threadStorageConnections": ["..."]
  }
}
```

**Warning if using default names**:
```
⚠️ WARNING: Connection names appear to be default values!
Please verify these are YOUR actual connection names.
Run the 'Get Connections' cell to see available connections.
```

**Troubleshooting**:
- **Status code 400**: Invalid connection names (run Cell 25 to verify)
- **Status code 404**: Account capability host doesn't exist
- **Fails with "Failed"**: Check connection names exist and are spelled correctly

**Time to complete**:
- API call: 2-5 seconds
- Background provisioning: 5-10 minutes

**Cost impact**: Creates Azure resource (minimal cost)

---

### Cell 17: Delete Account Capability Host (Code)

**Type**: Code  
**Safe to Run**: 🚨 NO - Deletes Azure resource permanently  
**Dependencies**: Cell 4 (Setup)  
**Purpose**: Deletes account-level capability host

**🚨 DANGER: This permanently deletes a resource!**

**⚠️ BEFORE RUNNING**:
1. Run Cell 8 to verify it's in "Failed" state (or you really want to delete)
2. Ensure project capability host is deleted first (Cell 19)
3. Understand deletion is permanent
4. Be prepared to type "YES" (exactly, all caps)

**When to use**:
- ✅ Capability host is in "Failed" state
- ✅ Need to change subnet (requires delete + recreate)
- ✅ Decommissioning VNET integration

**When NOT to use**:
- ❌ Capability host is "Provisioning" (wait for it)
- ❌ Capability host is "Succeeded" and working
- ❌ Haven't checked state first
- ❌ Project capability host still exists

**What it does**:
1. Displays danger warnings and checklist
2. Prompts user to type "YES"
3. If user types exactly "YES": Sends DELETE request
4. Otherwise: Cancels operation

**API Call**:
```
DELETE https://management.azure.com/subscriptions/{sub}/resourceGroups/{rg}/providers/Microsoft.CognitiveServices/accounts/{foundry}/capabilityHosts/{foundry}@aml_aiagentservice?api-version=2025-06-01
```

**Expected interaction**:
```
🚨 DANGER: You are about to DELETE the account capability host!

This will:
  - Remove VNET integration from your AI Foundry account
  - Require recreation if you need it again
  - Take 5-10 minutes to complete

⚠️ Before proceeding, verify:
  1. You've deleted the project capability host first
  2. The capability host is in 'Failed' state
  3. You really need to delete it

Type 'YES' (all caps) to continue with deletion or Ctrl+C to abort: 
```

**Expected output (success)**:
```
Deleting account capability host...
Response status: 202
✅ Deletion initiated. Wait 5-10 minutes for it to complete.
```

**Response codes**:
- **202**: Deletion initiated (async operation)
- **204**: Deletion completed immediately
- **404**: Resource not found (already deleted)
- **409**: Conflict (project capability host still exists)

**After deletion**:
1. Wait 5-10 minutes
2. Run Cell 8 to verify deletion
3. If you need to recreate: Wait for full deletion before running Cell 13

**Troubleshooting**:
- **409 Conflict**: Delete project capability host first (Cell 19)
- **Deletion doesn't complete**: Wait up to 20 minutes, then contact support

**Time to complete**: 5-10 minutes (async operation)

---

### Cell 19: Delete Project Capability Host (Code)

**Type**: Code  
**Safe to Run**: 🚨 NO - Deletes Azure resource permanently  
**Dependencies**: Cell 4 (Setup)  
**Purpose**: Deletes project-level capability host

**🚨 DANGER: This permanently deletes a resource!**

**⚠️ IMPORTANT**: Must be deleted BEFORE account capability host

**When to use**:
- ✅ Capability host is in "Failed" state
- ✅ Need to change connections
- ✅ Decommissioning agent capability

**What it does**:
1. Displays danger warnings
2. Prompts user to type "YES"
3. If confirmed: Sends DELETE request

**API Call**:
```
DELETE https://management.azure.com/subscriptions/{sub}/resourceGroups/{rg}/providers/Microsoft.CognitiveServices/accounts/{foundry}/projects/{project}/capabilityHosts/projcaphost?api-version=2025-06-01
```

**Time to complete**: 5-10 minutes

---

### Cell 22: POST to Azure OpenAI Service (Code)

**Type**: Code  
**Safe to Run**: ✅ Yes (sends one test request)  
**Dependencies**: Cell 4 (Setup)  
**Purpose**: Tests end-to-end connectivity

**What it does**:
1. Gets available model deployments
2. Selects first deployment
3. Sends test chat completion request
4. Displays response

**When to run**:
- After setting up capability hosts
- To validate connectivity
- To verify model deployments exist

**Expected output**:
```
Fetching available model deployments...
✅ Using deployment: gpt-4

Sending test chat completion request...

Response:
{
  "choices": [
    {
      "message": {
        "content": "Yes, many other Azure AI services also support..."
      }
    }
  ]
}
```

**Troubleshooting**:
- **No deployments found**: Deploy a model in Azure Portal
- **401 Unauthorized**: Re-run Cell 4
- **Connection error**: Check capability hosts are "Succeeded"

**Time to complete**: 2-5 seconds

**Cost impact**: Uses a few tokens from your model deployment

---

### Cell 25: Get Connections (Code)

**Type**: Code  
**Safe to Run**: ✅ Yes (read-only)  
**Dependencies**: Cell 4 (Setup)  
**Purpose**: Lists all project connections

**What it does**:
1. Gets all connections for the project
2. Displays summary (name, category, ID)
3. Displays full JSON

**When to run**:
- Before creating project capability host
- To find correct connection names
- To verify connections exist

**Expected output**:
```
📋 Available Connections:

  Name: search-connection-1
  Category: AISearch
  ID: /subscriptions/.../connections/search-connection-1
  ------------------------------------------------------------
  Name: storage-connection-1
  Category: AzureBlob
  ID: /subscriptions/.../connections/storage-connection-1
  ------------------------------------------------------------
```

**Connection categories**:
- **AISearch**: AI Search (vector store)
- **AzureBlob**: Storage accounts
- **CosmosDB**: Cosmos DB (thread storage)
- **ModelGateway**: External model connections

**Time to complete**: 1-2 seconds

---

### Cell 27: Update Connection (Code)

**Type**: Code  
**Safe to Run**: ⚠️ NO - Modifies a connection  
**Dependencies**: Cell 4 (Setup)  
**Purpose**: Example of updating a connection (template)

**⚠️ This is a template**: Edit extensively for your use case

**When to run**: Only if you need to update a specific connection configuration

**Time to complete**: 2-5 seconds

---

### Cell 30: Get Agents (Code)

**Type**: Code  
**Safe to Run**: ✅ Yes (read-only)  
**Dependencies**: Cell 4 (Setup)  
**Purpose**: Lists all configured agents (assistants)

**What it does**:
1. Gets all agents for the project
2. Displays full JSON response

**When to run**:
- To see what agents are configured
- To verify agent creation
- For inventory/audit purposes

**Time to complete**: 1-2 seconds

---

## 🎯 Recommended Execution Orders

### For First Time Setup

1. Cell 2: Install dependencies
2. Cell 4: Setup & authentication
3. Cell 8: Get account capability host (verify doesn't exist)
4. Cell 25: Get connections (find connection names)
5. Cell 13: Create account capability host (edit subnet first)
6. Wait 10-15 minutes
7. Cell 8: Get account capability host (verify "Succeeded")
8. Cell 15: Create project capability host (edit connections first)
9. Wait 5-10 minutes
10. Cell 10: Get project capability host (verify "Succeeded")
11. Cell 22: Test connectivity

### For Troubleshooting Failed State

1. Cell 4: Setup & authentication
2. Cell 10: Get project capability host (check state)
3. Cell 19: Delete project capability host (if Failed)
4. Wait 5 minutes
5. Cell 8: Get account capability host (check state)
6. Cell 17: Delete account capability host (if Failed)
7. Wait 5 minutes
8. Cell 8: Verify deletion complete
9. Cell 13: Create account capability host
10. Wait 10-15 minutes
11. Cell 15: Create project capability host
12. Wait 5-10 minutes
13. Cell 22: Test connectivity

### For Status Check Only

1. Cell 4: Setup & authentication
2. Cell 8: Get account capability host
3. Cell 10: Get project capability host
4. Cell 25: Get connections (optional)

---

## 📞 Need Help?

- Check the main [README-NETWORK-SETUP.md](README-NETWORK-SETUP.md) for detailed troubleshooting
- Review cell comments for inline guidance
- Check Azure Portal for resource status
- Contact Azure Support for persistent issues
