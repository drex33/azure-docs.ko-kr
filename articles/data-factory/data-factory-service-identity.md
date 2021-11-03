---
title: 관리 ID
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse 관리 ID 사용에 대해 알아봅니다.
author: nabhishek
ms.service: data-factory
ms.subservice: security
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: abnarain
ms.custom: devx-track-azurepowershell, synapse
ms.openlocfilehash: ed7cd2836f601e412aca7f73c52f2a0e13173a8a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131011487"
---
# <a name="managed-identity-for-azure-data-factory-and-azure-synapse"></a>Azure Data Factory 및 Azure Synapse 관리 ID 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서는 관리 ID(이전의 관리 서비스 ID/MSI)와 Azure Data Factory 및 Azure Synapse 작동 방식을 이해하는 데 도움이 됩니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>개요

관리 ID를 사용하면 자격 증명을 관리할 필요가 없습니다. 관리 ID는 Azure AD(Azure Active Directory) 인증을 지원하는 리소스에 연결할 때 서비스 인스턴스에 대한 ID를 제공합니다. 예를 들어 서비스는 관리 ID를 사용하여 데이터 관리자가 자격 증명을 안전하게 저장하거나 스토리지 계정에 액세스할 수 있는 [Azure Key Vault](../key-vault/general/overview.md)같은 리소스에 액세스할 수 있습니다. 이 서비스는 관리 ID를 사용하여 Azure AD 토큰을 얻습니다.

지원되는 관리 ID에는 다음 두 가지 유형이 있습니다. 

- **시스템 할당:** 서비스 인스턴스에서 직접 관리 ID를 사용하도록 설정할 수 있습니다. 서비스를 만드는 동안 시스템 할당 관리 ID를 허용하면 해당 서비스 인스턴스의 수명 주기와 연결된 Azure AD에서 ID가 만들어집니다. 의도적으로 해당 Azure 리소스만 이 ID를 사용하여 Azure AD에서 토큰을 요청할 수 있습니다. 따라서 리소스가 삭제되면 Azure에서 자동으로 ID를 삭제합니다. Azure Synapse Analytics Synapse 작업 영역과 함께 시스템 할당 관리 ID를 만들어야 합니다.
- **사용자 할당:** 관리 ID를 독립 실행형 Azure 리소스로 만들 수도 있습니다. 사용자 [할당 관리 ID를 만들어](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) 데이터 팩터리 또는 Synapse 작업 영역의 하나 이상의 인스턴스에 할당할 수 있습니다. 사용자가 할당한 관리 ID는 이를 사용하는 리소스와 별도로 관리됩니다.

관리 ID는 다음과 같은 이점을 제공합니다.

- Azure Key Vault 에 [자격 증명을 저장합니다.](store-credentials-in-key-vault.md)이 경우 관리 ID는 Azure Key Vault 인증에 사용됩니다.
- Azure Blob Storage, Azure Data Explorer, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database, Azure SQL Managed Instance, Azure Synapse Analytics, REST, Databricks 활동, 웹 활동 등을 비롯한 관리 ID 인증을 사용하여 데이터 저장소 또는 컴퓨터에 액세스합니다. 자세한 내용은 커넥터 및 작업 문서를 확인하세요.
- 사용자 할당 관리 ID는 Azure Key Vault 저장된 고객 관리형 키를 사용하여 메타데이터를 암호화/암호 해독하는 데도 사용되며 이중 암호화를 제공합니다. 

## <a name="system-assigned-managed-identity"></a>시스템 할당 관리 ID 

>[!NOTE]
> 시스템 할당 관리 ID는 이전 버전과의 호환성을 위해 설명서 및 Data Factory Studio 및 Synapse Studio UI의 다른 곳에서 '관리 ID'라고도 합니다. 이를 참조할 때 '사용자가 할당한 관리 ID'가 명시적으로 설명됩니다. 

### <a name="generate-system-assigned-managed-identity"></a><a name="generate-managed-identity"></a> 시스템 할당 관리 ID 생성

시스템 할당 관리 ID는 다음과 같이 생성됩니다.

- **Azure Portal 또는 PowerShell을** 통해 데이터 팩터리 또는 Synapse 작업 영역을 만들 때 관리 ID는 항상 자동으로 생성됩니다.
- **SDK를** 통해 데이터 팩터리 또는 작업 영역을 만들 때 관리 ID는 생성을 위해 Synapse 작업 영역 개체의 팩터리 개체에 "Identity = new FactoryIdentity()"를 지정하거나 Identity = new ManagedIdentity"를 지정하는 경우에만 생성됩니다.  [.NET 빠른 시작 - 데이터 팩터리 만들기의 예제를](quickstart-create-data-factory-dot-net.md#create-a-data-factory)참조하세요.
- **REST API** 통해 데이터 팩터리 또는 Synapse 작업 영역을 만들 때 요청 본문에 "ID" 섹션을 지정하는 경우에만 관리 ID가 생성됩니다. [REST 빠른 시작 - 데이터 팩터리 만들기](quickstart-create-data-factory-rest-api.md#create-a-data-factory)에서 예제를 참조하세요.

관리 ID 검색 지침에 따라 서비스 인스턴스에 연결된 [관리 ID가](#retrieve-managed-identity) 없는 경우 ID 초기자를 프로그래밍 방식으로 업데이트하여 명시적으로 생성할 수 있습니다.

- [PowerShell을 사용하여 관리 ID 생성](#generate-system-assigned-managed-identity-using-powershell)
- [REST API를 사용하여 관리 ID 생성](#generate-system-assigned-managed-identity-using-rest-api)
- [Azure Resource Manager 템플릿을 사용하여 관리 ID 생성](#generate-system-assigned-managed-identity-using-an-azure-resource-manager-template)
- [SDK를 사용하여 관리 ID 생성](#generate-system-assigned-managed-identity-using-sdk)

>[!NOTE]
>
>- 관리 ID는 수정할 수 없습니다. 관리 ID가 이미 있는 서비스 인스턴스를 업데이트해도 아무 영향이 없으며 관리 ID는 변경되지 않고 유지됩니다.
>- 팩터리 또는 작업 영역 개체에 "identity" 매개 변수를 지정하지 않거나 REST 요청 본문에 "identity" 섹션을 지정하지 않고 이미 관리 ID가 있는 서비스 인스턴스를 업데이트하면 오류가 발생합니다.
>- 서비스 인스턴스를 삭제하면 연결된 관리 ID가 함께 삭제됩니다.

#### <a name="generate-system-assigned-managed-identity-using-powershell"></a>PowerShell을 사용하여 시스템 할당 관리 ID 생성

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

**Set-AzDataFactoryV2** 명령을 다시 호출하면 새로 생성된 "Identity" 필드가 표시됩니다.

```powershell
PS C:\> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```
# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

**New-AzSynapseWorkspace** 명령을 호출하면 새로 생성되는 "ID" 필드가 표시됩니다.

```powershell
PS C:\> $creds = New-Object System.Management.Automation.PSCredential ("ContosoUser", $password)
PS C:\> New-AzSynapseWorkspace -ResourceGroupName <resourceGroupName> -Name <workspaceName> -Location <region> -DefaultDataLakeStorageAccountName <storageAccountName> -DefaultDataLakeStorageFileSystem <fileSystemName> -SqlAdministratorLoginCredential $creds

DefaultDataLakeStorage           : Microsoft.Azure.Commands.Synapse.Models.PSDataLakeStorageAccountDetails
ProvisioningState                : Succeeded
SqlAdministratorLogin            : ContosoUser
VirtualNetworkProfile            :
Identity                         : Microsoft.Azure.Commands.Synapse.Models.PSManagedIdentity
ManagedVirtualNetwork            :
PrivateEndpointConnections       : {}
WorkspaceUID                     : <workspaceUid>
ExtraProperties                  : {[WorkspaceType, Normal], [IsScopeEnabled, False]}
ManagedVirtualNetworkSettings    :
Encryption                       : Microsoft.Azure.Commands.Synapse.Models.PSEncryptionDetails
WorkspaceRepositoryConfiguration :
Tags                             :
TagsTable                        :
Location                         : <region>
Id                               : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/
                                   Microsoft.Synapse/workspaces/<workspaceName>
Name                             : <workspaceName>
Type                             : Microsoft.Synapse/workspaces
```
---

#### <a name="generate-system-assigned-managed-identity-using-rest-api"></a>REST API를 사용하여 시스템 할당 관리 ID 생성

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

> [!NOTE]
> 팩터리 개체에 ID 매개 변수를 지정하거나 REST 요청 본문에 **ID** 섹션을 제공하지 않고 이미 관리 **ID가** 있는 서비스 인스턴스를 업데이트하려고 하면 오류가 발생합니다.

요청 본문의 "ID" 섹션을 통해 아래 API를 호출합니다.

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**요청 본문**: "identity": { "type": "SystemAssigned" }를 추가합니다.

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**응답**: 관리 ID가 자동으로 생성되고 "identity" 섹션이 그에 따라 채워집니다.

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```
# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

> [!NOTE]
> 작업 영역 개체에 ID 매개 변수를 지정하거나 REST 요청 본문에 **ID** 섹션을 제공하지 않고 이미 관리 **ID가** 있는 서비스 인스턴스를 업데이트하려고 하면 오류가 발생합니다.

요청 본문의 "ID" 섹션을 통해 아래 API를 호출합니다.

```
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Synapse/workspaces/{workspaceName}?api-version=2018-06-01
```

**요청 본문**: "identity": { "type": "SystemAssigned" }를 추가합니다.

```json
{
    "name": "<workspaceName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**응답**: 관리 ID가 자동으로 생성되고 "identity" 섹션이 그에 따라 채워집니다.

```json
{
    "name": "<workspaceName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2021-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Synapse/workspaces/<workspaceName>",
    "type": "Microsoft.Synapse/workspaces",
    "location": "<region>"
}
```
---

#### <a name="generate-system-assigned-managed-identity-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 시스템 할당 관리 ID 생성

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
**템플릿**: "identity": { "type": "SystemAssigned" }를 추가합니다.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```
# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
**템플릿**: "identity": { "type": "SystemAssigned" }를 추가합니다.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<workspaceName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.Synapse/workspaces",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```
---

#### <a name="generate-system-assigned-managed-identity-using-sdk"></a>SDK를 사용하여 시스템 할당 관리 ID 생성

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
Identity=new FactoryIdentity()를 통해 create_or_update 함수를 호출합니다. .NET을 사용하는 샘플 코드:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```
# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
```csharp
Workspace workspace = new Workspace
{
    Identity = new ManagedIdentity
    {
        Type = ResourceIdentityType.SystemAssigned
    },
    DefaultDataLakeStorage = new DataLakeStorageAccountDetails
    {
        AccountUrl = <defaultDataLakeStorageAccountUrl>,
        Filesystem = <DefaultDataLakeStorageFilesystem>
    },
    SqlAdministratorLogin = <SqlAdministratorLoginCredentialUserName>
    SqlAdministratorLoginPassword = <SqlAdministratorLoginCredentialPassword>,
    Location = <region>
};
client.Workspaces.CreateOrUpdate(resourceGroupName, workspaceName, workspace);
```
---

### <a name="retrieve-system-assigned-managed-identity"></a><a name="retrieve-managed-identity"></a> 시스템 할당 관리 ID 검색

Azure Portal에서 또는 프로그래밍 방식으로 관리 ID를 검색할 수 있습니다. 다음 섹션에서는 몇 가지 샘플을 보여 줍니다.

>[!TIP]
> 관리 ID가 표시되지 않으면 서비스 인스턴스를 업데이트하여 [관리 ID를 생성합니다.](#generate-managed-identity)

#### <a name="retrieve-system-assigned-managed-identity-using-azure-portal"></a>Azure Portal에서 시스템 할당 관리 ID 검색

데이터 팩터리 또는 Synapse 작업 영역 -> 속성 > Azure Portal 관리 ID 정보를 찾을 수 있습니다.

- 관리 ID 개체 ID
- 관리 ID 테넌트(Azure Data Factory만 해당)

관리 ID 정보는 Azure Blob, Azure Data Lake Storage, Azure Key Vault 등의 관리 ID 인증을 지원하는 연결된 서비스를 만들 때도 표시됩니다.

사용 권한을 부여할 때 Azure 리소스의 액세스 제어(IAM) 탭에서 역할 할당을 추가하고 액세스 권한을 할당하고 시스템 할당 관리 ID에서 Data Factory을 선택하고 팩터리 이름으로 선택합니다. 또는 일반적으로 개체 ID 또는 데이터 팩터리 이름을 관리 ID 이름으로 사용하여 이 ID를 찾을 수 있습니다. 관리 ID의 애플리케이션 ID를 알아야 하는 경우 PowerShell을 사용할 수 있습니다.

#### <a name="retrieve-system-assigned-managed-identity-using-powershell"></a>PowerShell을 사용하여 시스템 할당 관리 ID 검색

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
관리 ID 보안 주체 ID 및 테넌트 ID는 다음과 같이 특정 서비스 인스턴스를 얻을 때 반환됩니다. **Principalid** 를 사용하여 액세스 권한을 부여합니다.

```powershell
PS C:\> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

보안 주체 ID를 복사하여 애플리케이션 ID를 가져온 다음, 보안 주체 ID를 매개 변수로 사용하여 아래 Azure Active Directory 명령을 실행할 수 있습니다.

```powershell
PS C:\> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```
# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
관리 ID 보안 주체 ID 및 테넌트 ID는 다음과 같이 특정 서비스 인스턴스를 얻을 때 반환됩니다. **Principalid** 를 사용하여 액세스 권한을 부여합니다.

```powershell
PS C:\> (Get-AzSynapseWorkspace -ResourceGroupName <resourceGroupName> -Name <workspaceName>).Identity

IdentityType   PrincipalId                          TenantId                            
------------   -----------                          --------                            
SystemAssigned cadadb30-XXXX-XXXX-XXXX-ef3500e2ff05 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

보안 주체 ID를 복사하여 애플리케이션 ID를 가져온 다음, 보안 주체 ID를 매개 변수로 사용하여 아래 Azure Active Directory 명령을 실행할 수 있습니다.

```powershell
PS C:\> Get-AzADServicePrincipal -ObjectId cadadb30-XXXX-XXXX-XXXX-ef3500e2ff05

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : <workspaceName>
Id                    : cadadb30-XXXX-XXXX-XXXX-ef3500e2ff05
Type                  : ServicePrincipal
```
---

#### <a name="retrieve-managed-identity-using-rest-api"></a>REST API를 사용하여 관리 ID 검색

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
관리 ID 보안 주체 ID 및 테넌트 ID는 다음과 같이 특정 서비스 인스턴스를 얻을 때 반환됩니다.

요청에서 아래 API를 호출합니다.

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}?api-version=2018-06-01
```

**응답**: 아래 예제에서와 같이 응답을 받게 됩니다. "ID" 섹션이 그에 따라 채워집니다.

```json
{
    "name":"<dataFactoryName>",
    "identity":{
        "type":"SystemAssigned",
        "principalId":"554cff9e-XXXX-XXXX-XXXX-90c7d9ff2ead",
        "tenantId":"72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>",
    "type":"Microsoft.DataFactory/factories",
    "properties":{
        "provisioningState":"Succeeded",
        "createTime":"2020-02-12T02:22:50.2384387Z",
        "version":"2018-06-01",
        "factoryStatistics":{
            "totalResourceCount":0,
            "maxAllowedResourceCount":0,
            "factorySizeInGbUnits":0,
            "maxAllowedFactorySizeInGbUnits":0
        }
    },
    "eTag":"\"03006b40-XXXX-XXXX-XXXX-5e43617a0000\"",
    "location":"<region>",
    "tags":{

    }
}
```

> [!TIP] 
> ARM 템플릿에서 관리 ID를 검색하려면 ARM JSON에 **출력** 섹션을 추가합니다.

```json
{
    "outputs":{
        "managedIdentityObjectId":{
            "type":"string",
            "value":"[reference(resourceId('Microsoft.DataFactory/factories', parameters('<dataFactoryName>')), '2018-06-01', 'Full').identity.principalId]"
        }
    }
}
```
# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
관리 ID 보안 주체 ID 및 테넌트 ID는 다음과 같이 특정 서비스 인스턴스를 얻을 때 반환됩니다.

요청에서 아래 API를 호출합니다.

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Synapse/workspaces/{workspaceName}?api-version=2018-06-01
```

**응답**: 아래 예제에서와 같이 응답을 받게 됩니다. "ID" 섹션이 그에 따라 채워집니다.

```json
{
  "properties": {
    "defaultDataLakeStorage": {
      "accountUrl": "https://exampledatalakeaccount.dfs.core.windows.net",
      "filesystem": "examplefilesystem"
    },
    "encryption": {
      "doubleEncryptionEnabled": false
    },
    "provisioningState": "Succeeded",
    "connectivityEndpoints": {
      "web": "https://web.azuresynapse.net?workspace=%2fsubscriptions%2{subscriptionId}%2fresourceGroups%2f{resourceGroupName}%2fproviders%2fMicrosoft.Synapse%2fworkspaces%2f{workspaceName}",
      "dev": "https://{workspaceName}.dev.azuresynapse.net",
      "sqlOnDemand": "{workspaceName}-ondemand.sql.azuresynapse.net",
      "sql": "{workspaceName}.sql.azuresynapse.net"
    },
    "managedResourceGroupName": "synapseworkspace-managedrg-f77f7cf2-XXXX-XXXX-XXXX-c4cb7ac3cf4f",
    "sqlAdministratorLogin": "sqladminuser",
    "privateEndpointConnections": [],
    "workspaceUID": "e56f5773-XXXX-XXXX-XXXX-a0dc107af9ea",
    "extraProperties": {
      "WorkspaceType": "Normal",
      "IsScopeEnabled": false
    },
    "publicNetworkAccess": "Enabled",
    "cspWorkspaceAdminProperties": {
      "initialWorkspaceAdminObjectId": "3746a407-XXXX-XXXX-XXXX-842b6cf1fbcc"
    },
    "trustedServiceBypassEnabled": false
  },
  "type": "Microsoft.Synapse/workspaces",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Synapse/workspaces/{workspaceName}",
  "location": "eastus",
  "name": "{workspaceName}",
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47",
    "principalId": "cadadb30-XXXX-XXXX-XXXX-ef3500e2ff05"
  },
  "tags": {}
}
```

> [!TIP] 
> ARM 템플릿에서 관리 ID를 검색하려면 ARM JSON에 **출력** 섹션을 추가합니다.

```json
{
    "outputs":{
        "managedIdentityObjectId":{
            "type":"string",
            "value":"[reference(resourceId('Microsoft.Synapse/workspaces', parameters('<workspaceName>')), '2018-06-01', 'Full').identity.principalId]"
        }
    }
}
```
---

## <a name="user-assigned-managed-identity"></a>사용자 할당 관리 ID

Azure Active Directory 사용자 할당 관리 ID를 만들고, 삭제하고, 관리할 수 있습니다. 자세한 내용은 [Azure Portal를 사용 하 여 사용자 할당 관리 id에 역할 만들기, 나열, 삭제 또는 할당](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)을 참조 하세요. 

사용자 할당 관리 id를 사용 하려면 먼저 UAMI에 대 한 서비스 인스턴스에서 [자격 증명을 만들어야](credentials.md) 합니다.

## <a name="next-steps"></a>다음 단계

- [자격 증명을 만듭니다](credentials.md).

관리 id를 사용 하는 경우 및 방법을 소개 하는 다음 항목을 참조 하세요.

- [Azure Key Vault에 자격 증명을 저장](store-credentials-in-key-vault.md)합니다.
- [Azure 리소스 인증을 위해 관리 되는 id를 사용 하 여 Azure Data Lake Store 간에 데이터를 복사](connector-azure-data-lake-store.md)합니다.

Azure Data Factory에서 관리 되는 id 및 Azure Synapse의 기반이 되는 Azure 리소스에 대 한 관리 되는 id에 대 한 자세한 배경 정보는 [Azure 리소스에 대 한 관리 Id 개요](../active-directory/managed-identities-azure-resources/overview.md) 를 참조 하세요.
