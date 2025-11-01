# AzurePEAS-USGOV
CarlosPolop's AzurePEAS for US Government cloud version 

OG version: https://github.com/carlospolop/CloudPEASS/blob/main/AzurePEAS.py

### AzurePEAS 💼🖥️

**AzurePEAS** is dedicated to **enumerating the principals permissions** within your **Azure** and **Entra ID** environments, with a special focus on detecting **privilege escalation pathways** and identifying **potential security risks**. It can also **enumerate several Microsoft 365** services for a quick recon. Here are the key features and requirements:

- **Comprehensive Permissions Check**  
    AzurePEAS finds all resources accessible to the principal and the permisions he has over them. It retrieves permissions for both **Azure (ARM API)** and **Entra ID (Graph API)**, ensuring a thorough analysis of your cloud permissions.
    
- **Authentication Requirements**  
    To operate effectively, AzurePEAS requires:
    
    - A token with access to the **Azure ARM API** to find all the resources and permissions of the principal has inside Azure Management.
    - A token with access to the **Azure Graph API** to find all the resources and permissions of the principal has inside Entra ID.
    - If a **FOCI refresh token** or valid **credentials (username and password)** are provided, AzurePEAS can generate the previous tokens itself and also enumerate various Microsoft 365 services.
    
    **Note:** Most permissions can be collected without needing extra enumeration privileges. However, some specific operations might need additional scopes.
    
- **Microsoft 365 Enumeration (M356)**  
    If you provide AzurePEAS with a **FOCI refresh token** or valid **credentials (username and password)**, it extends its scanning capabilities to enumerate various **Microsoft 365** services, including:
    
    - **SharePoint** 📂
    - **OneDrive** ☁️
    - **Outlook** 📧
    - **Teams** 💬
    - **OneNote** 📝
    - **Contacts** 👥
    - **Tasks** ✅
    
    This additional enumeration is intended to indicate whether any data exists in these services, enabling further manual investigation if needed. The process is not exhaustive but serves as a useful preliminary check.
    

### AzurePEAS Help
To see the complete list of options, run:

```shell
python3 ./AzurePEAS.py --help
usage: AzurePEAS.py [-h] [--tenant-id TENANT_ID] [--arm-token ARM_TOKEN] [--graph-token GRAPH_TOKEN] [--foci-refresh-token FOCI_REFRESH_TOKEN] [--not-enumerate-m365]
                    [--username USERNAME] [--password PASSWORD] [--out-json-path OUT_JSON_PATH] [--threads THREADS] [--not-use-hacktricks-ai]

Run AzurePEASS to find all your current privileges in Azure and EntraID and check for potential privilege escalation attacks. To check for Azure permissions an ARM token
is needed. To check for Entra ID permissions a Graph token is needed.

options:
  -h, --help            show this help message and exit
  --tenant-id TENANT_ID
                        Indicate the tenant id
  --arm-token ARM_TOKEN
                        Azure Management authentication token
  --graph-token GRAPH_TOKEN
                        Azure Graph authentication token
  --foci-refresh-token FOCI_REFRESH_TOKEN
                        FOCI Refresh Token
  --not-enumerate-m365  Don't enumerate M365 permissions
  --username USERNAME   Username for authentication
  --password PASSWORD   Password for authentication
  --out-json-path OUT_JSON_PATH
                        Output JSON file path (e.g. /tmp/azure_results.json)
  --threads THREADS     Number of threads to use
  --not-use-hacktricks-ai
                        Don't use Hacktricks AI to analyze permissions
```

### AzurePEAS Usage Examples
**1. Obtaining Tokens** 🔑

Before executing the script, generate your tokens with the following commands:

```shell
# Get Azure ARM token
export AZURE_ARM_TOKEN=$(az account get-access-token --resource-type arm | jq -r .accessToken)

# Get Azure Graph token
export AZURE_GRAPH_TOKEN=$(az account get-access-token --resource-type ms-graph | jq -r .accessToken)

# Get Graph Token with enough scopes (use powershell)
Connect-MgGraph -Scopes "RoleAssignmentSchedule.Read.Directory"
$Parameters = @{
    Method     = "GET"
    Uri        = "/v1.0/me"
    OutputType = "HttpResponseMessage"
}
$Response = Invoke-MgGraphRequest @Parameters
$Headers = $Response.RequestMessage.Headers
$Headers.Authorization.Parameter
```

**2. Running AzurePEAS Using Tokens**

You can run AzurePEAS by either providing the tokens via the command line or by having them set as environment variables:

```shell
python3 AzurePEAS.py [--arm-token <AZURE_MANAGEMENT_TOKEN>] [--graph-token <AZURE_GRAPH_TOKEN>]
```

**3. Running AzurePEAS Using Credentials/FOCI token for improved enumeration**

For additional enumeration of Microsoft 365 services, you can supply:

- A **FOCI refresh token**:
    
    ```shell
    python3 AzurePEAS.py [--tenant-id <TENANT_ID>] [--foci-refresh-token <FOCI_REFRESH_TOKEN>]
    ```
    
- **Username and password credentials**:
    
    ```shell
    python3 AzurePEAS.py [--username <USERNAME>] [--password <PASWORD>]
    ```
