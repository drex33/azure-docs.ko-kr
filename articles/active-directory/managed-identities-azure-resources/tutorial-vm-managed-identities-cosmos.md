---
title: 가상 머신에서 관리 ID를 사용하여 Cosmos DB에 액세스 | Microsoft Docs
description: Azure Portal, CLI, PowerShell, Azure Resource Manager 템플릿을 사용하여 Windows VM에서 관리 ID를 사용하는 방법을 알아봅니다.
author: barclayn
manager: karenh444
ms.service: active-directory
ms.subservice: msi
ms.workload: integration
ms.topic: tutorial
ms.date: 10/14/2021
ms.author: barclayn
ms.custom: ep-miar
ms.openlocfilehash: 7ff0ae1578a8b1d637f7baee50ed6d71a63e3996
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130070112"
---
# <a name="how-to-use-managed-identities-to-connect-to-cosmos-db-from-an-azure-virtual-machine"></a>관리 ID를 사용하여 Azure 가상 머신에서 Cosmos DB에 연결하는 방법

이 문서에서는 관리 ID를 사용하여 Cosmos에 연결하도록 가상 머신을 설정합니다. [Azure Cosmos DB](../../cosmos-db/introduction.md)는 최신 앱을 개발하기 위한 완전 관리형 NoSQL 데이터베이스입니다. [Azure 리소스에 대한 관리 ID](overview.md)를 사용하면 Azure에서 관리하는 ID를 사용하여 Azure AD 인증을 지원하는 서비스에 액세스할 때 애플리케이션을 인증할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 관리 ID에 대한 기본 이해. 계속하기 전에 Azure 리소스에 대한 관리 ID에 대해 자세히 알아보려면 관리 ID [개요](overview.md)를 검토하세요.
- 활성 구독이 포함된 Azure 계정이 있어야 합니다. [체험 계정을 만듭니다](https://azure.microsoft.com/free/).
- [PowerShell](/powershell/azure/new-azureps-module-az) 또는 [CLI](/cli/azure/install-azure-cli)가 필요할 수 있습니다.
- [Visual Studio Community Edition](https://visualstudio.microsoft.com/vs/community/) 또는 선택한 다른 개발 환경 

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

**mi-test** 라는 리소스 그룹을 만듭니다. 이 리소스 그룹은 이 자습서에서 사용되는 모든 리소스에 사용합니다.

- [Azure Portal을 사용하여 리소스 그룹 만들기](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)
- [CLI를 사용하여 리소스 그룹 만들기](../../azure-resource-manager/management/manage-resource-groups-cli.md#create-resource-groups)
- [PowerShell을 사용하여 리소스 그룹 만들기](../../azure-resource-manager/management/manage-resource-groups-powershell.md#create-resource-groups)

## <a name="create-an-azure-vm-with-a-managed-identity"></a>관리 ID를 사용하여 Azure VM 만들기

이 자습서에는 Azure VM(가상 머신)이 필요합니다. **mi-vm-01** 이라는 시스템이 할당한 관리 ID가 사용하도록 설정된 가상 머신을 만듭니다.  또한 이전에 만든 리소스 그룹(**mi-test**)에서 **mi-ua-01** 이라는 [사용자가 할당한 관리 ID를 만들](how-manage-user-assigned-managed-identities.md) 수도 있습니다. 사용자가 할당한 관리 ID를 사용하는 경우 만드는 동안 VM에 할당할 수 있습니다.

### <a name="create-a-vm-with-a-system-assigned-managed-identity"></a>시스템이 할당한 관리 ID를 사용하는 VM 만들기

시스템 할당 관리 ID를 사용하도록 설정된 Azure VM을 만들려면 계정에 [Virtual Machine 기여자](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) 역할 할당이 필요합니다.  다른 Azure AD 역할 할당은 필요하지 않습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

- **Azure Portal** 에서 **가상 머신** 을 검색합니다.
- **만들기** 를 선택합니다.
- [기본 사항] 탭에서 필요한 정보를 제공합니다.
- **다음: 디스크 >** 를 선택합니다.
- 필요에 따라 정보를 계속 입력하고, **관리** 탭에서 **ID** 섹션을 찾아서 **시스템이 할당한 관리 ID** 옆의 확인란을 선택합니다.

:::image type="content" source="media/how-to-manage-identities-vm-cosmos/create-vm-system-assigned-managed-identities.png" alt-text="VM을 만드는 동안 시스템이 할당한 관리 ID를 사용하도록 설정하는 방법을 보여 주는 이미지":::

자세한 내용은 다음 Azure 가상 머신 설명서를 검토하세요.

- [Linux](../../virtual-machines/linux/quick-create-portal.md)
- [Windows](../../virtual-machines/windows/quick-create-portal.md)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[New-AZVM](/powershell/module/az.compute/new-azvm)은 참조하는 리소스가 없는 경우 해당 리소스를 만듭니다. 시스템이 할당한 관리 ID가 사용하도록 설정된 VM을 만들려면 아래와 같이 **-SystemAssignedIdentity** 매개 변수를 전달합니다. 


```powershell

New-AzVm `
    -ResourceGroupName "My VM" `
    -Name "My resource group" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -SystemAssignedIdentity
    -OpenPorts 80,3389
```

- [빠른 시작: PowerShell을 사용하여 Azure에서 Windows 가상 머신 만들기](../../virtual-machines/windows/quick-create-powershell.md)
- [빠른 시작: PowerShell을 사용하여 Azure에서 Linux 가상 머신 만들기](../../virtual-machines/linux/quick-create-powershell.md)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az vm create](/cli/azure/vm/#az_vm_create)를 사용하여 VM을 만듭니다. 다음 예제에서는 `--assign-identity` 매개 변수의 요청에 따라 시스템 할당 관리 ID를 사용하여 *myVM* 이라는 VM을 만듭니다. `--admin-username` 및 `--admin-password` 매개 변수는 가상 머신 로그인을 위한 관리자 이름 및 암호 계정을 지정합니다. 이러한 값은 사용자 환경에 적절하게 업데이트합니다. 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

- [시스템이 할당한 관리 ID를 사용하는 Linux 가상 머신 만들기](../../virtual-machines/linux/quick-create-cli.md)
- [시스템이 할당한 관리 ID를 사용하는 Windows 가상 머신 만들기](../../virtual-machines/windows/quick-create-cli.md)

# <a name="resource-manager-template"></a>[Resource Manager 템플릿](#tab/azure-resource-manager)

시스템 할당 관리 ID를 사용하도록 설정하려면 편집기에 템플릿을 로드하고 `resources` 섹션 내에서 관심이 있는 `Microsoft.Compute/virtualMachines` 리소스를 찾아서 `"type": "Microsoft.Compute/virtualMachines"` 속성과 같은 수준으로 `"identity"` 속성을 추가합니다. 다음 구문을 사용합니다.

   ```json
   "identity": {
       "type": "SystemAssigned"
   },
   ```

완료되면 다음 섹션을 템플릿의 `resource` 섹션에 추가해야 합니다. 템플릿은 아래에 표시된 예제와 비슷합니다.

   ```json
    "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('myVM')]",
            "location": "[myResourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
                }                        
        }
    ]
   ```

---


### <a name="create-a-vm-with-a-user-assigned-managed-identity"></a>사용자가 할당한 관리 ID를 사용하는 VM 만들기

아래 단계에서는 사용자가 할당한 관리 ID가 구성된 가상 머신을 만드는 방법을 보여 줍니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

현재 Azure Portal은 VM을 만드는 동안 사용자가 할당한 관리 ID를 할당하도록 지원하지 않습니다. 가상 머신을 만든 다음, 사용자가 할당한 관리 ID를 할당해야 합니다.

[Azure Portal을 사용하여 VM에서 Azure 리소스에 대한 관리 ID 구성](qs-configure-portal-windows-vm.md#user-assigned-managed-identity)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

사용자가 할당한 관리 ID가 지정된 Windows 가상 머신을 만듭니다.

```powershell
New-AzVm `
    -ResourceGroupName "<Your resource group>" `
    -Name "<Your VM name>" `
    -Location "East US" `
    -VirtualNetworkName "<myVnet>" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -UserAssignedIdentity "/subscriptions/<Your subscription>/resourceGroups/<Your resource group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<Your user assigned managed identity>" `
    -OpenPorts 80,3389

```

사용자가 할당한 관리 ID가 지정된 Linux 가상 머신을 만듭니다.

```powershell
New-AzVm `
    -Name "<Linux VM name>" `
    -image CentOS
    -ResourceGroupName "<Your resource group>" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -Linux `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -UserAssignedIdentity "/subscriptions/<Your subscription>/resourceGroups/<Your resource group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<Your user assigned managed identity>" `
    -OpenPorts 22


```

사용자가 할당한 관리 ID는 [resourceID](how-manage-user-assigned-managed-identities.md
)를 사용하여 지정해야 합니다. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az vm create --resource-group <MyResourceGroup> --name <myVM> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
```

[Azure CLI를 사용하여 VM에서 Azure 리소스에 대한 관리 ID 구성](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)

# <a name="resource-manager-template"></a>[Resource Manager 템플릿](#tab/azure-resource-manager)

API 버전에 따라 [다른 단계](qs-configure-template-windows-vm.md#user-assigned-managed-identity)를 수행해야 합니다. apiVersion이 2018-06-01인 경우 사용자가 할당한 관리 ID는 userAssignedIdentities 사전 형식으로 저장되고, ```<identityName>``` 값은 템플릿의 변수 섹션에서 정의하는 변수의 이름입니다. 변수에서 할당하려는 사용자가 할당한 관리 ID를 가리킵니다.

```json
    "variables": {
     "identityName": "my-user-assigned" 
        
    },
```

resources 요소 아래에서 다음 항목을 추가하여 사용자가 할당한 관리 ID를 VM에 할당합니다. ```<identityName>```를 직접 만든 사용자 할당 관리 ID의 이름으로 바꿔야 합니다.

```json

"resources": [
     {
         //other resource provider properties...
         "apiVersion": "2018-06-01",
         "type": "Microsoft.Compute/virtualMachines",
         "name": "[variables('vmName')]",
         "location": "[resourceGroup().location]",
         "identity": {
             "type": "userAssigned",
             "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<identityName>'))]": {}
             }
         }
     }
 ]
```

---

## <a name="create-a-cosmos-db-account"></a>Cosmos DB 계정 만들기

이제 사용자가 할당한 관리 ID 또는 시스템이 할당한 관리 ID를 사용하는 VM이 ​​있으므로 관리 권한이 있는 Cosmos DB 계정이 필요합니다. 이 자습서에서 사용할 Cosmos DB 계정을 만들어야 하는 경우 [Cosmos DB 빠른 시작](../..//cosmos-db/sql/create-cosmosdb-resources-portal.md)에서 이 작업을 수행하는 방법에 대한 자세한 단계를 제공합니다.

>[!NOTE]
> 관리 ID는 Azure Active Directory 인증을 지원하는 모든 Azure 리소스에 액세스하는 데 사용할 수 있습니다. 이 자습서에서는 Cosmos DB 계정이 아래와 같이 구성되어 있다고 가정합니다.

 |설정|값|Description |
   |---|---|---|
   |Subscription|구독 이름|이 Azure Cosmos 계정에 사용하려는 Azure 구독을 선택합니다. |
   |리소스 그룹|리소스 그룹 이름|**mi-test** 를 선택하거나 **새로 만들기** 를 선택한 다음, 새 리소스 그룹에 대한 고유한 이름을 입력합니다. |
   |계정 이름|고유한 이름|Azure Cosmos 계정을 식별하는 이름을 입력합니다. URI를 만들기 위해 제공하는 이름에 *documents.azure.com* 이 추가되므로 고유한 이름을 사용합니다.<br><br>이름은 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있으며, 길이는 3~44자여야 합니다.|
   |API|만들 계정 형식|**Core(SQL)** 를 선택하여 문서 데이터베이스를 만들고 SQL 구문을 사용하여 쿼리합니다. <br><br>[SQL API에 대한 자세한 정보](../../cosmos-db/introduction.md)|
   |위치|사용자와 가장 가까운 지역|Azure Cosmos DB 계정을 호스트할 지리적 위치를 선택합니다. 데이터에 가장 빨리 액세스할 수 있도록 사용자와 가장 가까운 위치를 사용합니다.|

   > [!NOTE]
   > 테스트하는 경우 Azure Cosmos DB 무료 계층 할인을 적용할 수 있습니다. Azure Cosmos DB 체험 계층을 사용하는 경우 처음에는 1000RU/초 및 25GB의 스토리지가 계정에 무료로 제공됩니다. [체험 계층](https://azure.microsoft.com/pricing/details/cosmos-db/)에 대해 자세히 알아보세요. 이 자습서의 목적을 위해 이 선택은 아무런 차이가 없습니다.

## <a name="grant-access"></a>액세스 권한 부여

이 시점에서 관리 ID와 Cosmos DB 계정으로 구성된 두 개의 가상 머신이 모두 있어야 합니다. 계속하기 전에 몇 가지 다른 역할을 관리 ID에 부여해야 합니다.

- 먼저 [Azure RBAC](../../cosmos-db/role-based-access-control.md)를 사용하여 Cosmos 관리 평면에 대한 액세스 권한을 부여합니다. 데이터베이스 및 컨테이너를 만들려면 DocumentDB 계정 기여자 역할을 관리 ID에 할당해야 합니다.

- [Cosmos RBAC](../../cosmos-db/how-to-setup-rbac.md)도 사용하여 기여자 역할을 관리 ID에 부여해야 합니다. 구체적인 단계는 아래에서 확인할 수 있습니다. 

> [!NOTE] 
> 여기서는 **Cosmos DB 기본 제공 데이터 기여자** 역할을 사용합니다. 액세스 권한을 부여하려면 역할 정의를 ID와 연결해야 합니다. 이 경우 가상 머신과 연결된 관리 ID입니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

**Azure Portal에는 현재 사용할 수 있는 역할 할당 옵션이 없습니다.**


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>" 
$contributorRoleDefinitionId = "00000000-0000-0000-0000-000000000002" # This is the ID of the Cosmos DB Built-in Data contributor role definition
$principalId = "1111111-1111-11111-1111-11111111" # This is the object ID of the managed identity.
New-AzCosmosDBSqlRoleAssignment -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -RoleDefinitionId $contributorRoleDefinitionId `
    -Scope "/" `
    -PrincipalId $principalId
```

역할 할당 단계가 완료되면 아래와 비슷한 결과가 표시됩니다.

:::image type="content" source="media/how-to-manage-identities-vm-cosmos/results-role-assignment.png" alt-text="역할 할당의 결과를 보여 주는 스크린샷":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli

resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
readOnlyRoleDefinitionId = '00000000-0000-0000-0000-000000000002' # This is the ID of the Cosmos DB Built-in Data contributor role definition
principalId = "1111111-1111-11111-1111-11111111" # This is the object ID of the managed identity.
az cosmosdb sql role assignment create --account-name $accountName --resource-group $resourceGroupName --scope "/" --principal-id $principalId --role-definition-id $readOnlyRoleDefinitionId

```

# <a name="resource-manager-template"></a>[Resource Manager 템플릿](#tab/azure-resource-manager)

```JSON
{
  "id": "/subscriptions/mySubscriptionId/resourceGroups/myResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/myAccountName/sqlRoleAssignments/00000000-0000-0000-0000-000000000002",
  "name": "myRoleAssignmentId",
  "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments",
  "properties": {
    "roleDefinitionId": "/subscriptions/mySubscriptionId/resourceGroups/myResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/myAccountName/sqlRoleDefinitions/00000000-0000-0000-0000-000000000002",
    "scope": "/subscriptions/mySubscriptionId/resourceGroups/myResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/myAccountName/dbs/purchases/colls/redmond-purchases",
    "principalId": "myPrincipalId"
  }
}

```

---

## <a name="access-data"></a>데이터 액세스

관리 ID를 사용하여 Cosmos에 액세스하려면 Azure.identity 라이브러리를 사용하여 애플리케이션에서 인증을 사용하도록 설정할 수 있습니다. [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential)을 직접 호출하거나 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential)을 사용할 수 있습니다.

ManagedIdentityCredential 클래스는 배포 환경에 할당된 관리 ID를 사용하여 인증을 시도합니다. [DefaultAzureCredential](/dotnet/api/overview/azure/identity-readme) 클래스는 다양한 인증 옵션을 순서대로 시도합니다. DefaultAzureCredential에서 시도하는 두 번째 인증 옵션은 관리 ID입니다. 

아래 예제에서는 데이터베이스, 컨테이너, 컨테이너의 항목을 만들고, 가상 머신의 시스템이 할당한 관리 ID를 사용하여 새로 만든 항목을 다시 읽습니다. 사용자가 할당한 관리 ID를 사용하려면 관리 ID의 클라이언트 ID를 지정하여 사용자가 할당한 관리 ID를 지정해야 합니다. 

```csharp
string userAssignedClientId = "<your managed identity client Id>";
var tokenCredential = new DefaultAzureCredential(new DefaultAzureCredentialOptions { ManagedIdentityClientId = userAssignedClientId });
```

아래 샘플을 사용하려면 다음 NuGet 패키지가 필요합니다.

- Azure.Identity
- Microsoft.Azure.Cosmos
- Microsoft.Azure.Management.CosmosDB

위의 NuGet 패키지 외에도 **시험판 포함** 을 사용하도록 설정한 다음, **Azure.ResourceManager.CosmosDB** 를 추가해야 합니다. 

```csharp
using Azure.Identity;
using Azure.ResourceManager.CosmosDB;
using Azure.ResourceManager.CosmosDB.Models;
using Microsoft.Azure.Cosmos;
using System;
using System.Threading.Tasks;

namespace MITest
{
    class Program
    {
        static async Task Main(string[] args)
        {
            var subscriptionId = "Your subscription ID";
            var resourceGroupName = "You resource group";
            var accountName = "Cosmos DB Account name";
            var databaseName = "mi-test";
            var containerName = "container01";

            var tokenCredential = new DefaultAzureCredential();

            // create the management clientSS
            var managementClient = new CosmosDBManagementClient(subscriptionId, tokenCredential);

            // create the data client
            var dataClient = new CosmosClient("https://[Account].documents.azure.com:443/", tokenCredential);

            // create a new database 
            var createDatabaseOperation = await managementClient.SqlResources.StartCreateUpdateSqlDatabaseAsync(resourceGroupName, accountName, databaseName,
                new SqlDatabaseCreateUpdateParameters(new SqlDatabaseResource(databaseName), new CreateUpdateOptions()));
            await createDatabaseOperation.WaitForCompletionAsync();

            // create a new container
            var createContainerOperation = await managementClient.SqlResources.StartCreateUpdateSqlContainerAsync(resourceGroupName, accountName, databaseName, containerName,
                new SqlContainerCreateUpdateParameters(new SqlContainerResource(containerName), new CreateUpdateOptions()));
            await createContainerOperation.WaitForCompletionAsync();


            // create a new item 
            var partitionKey = "pkey";
            var id = Guid.NewGuid().ToString();
            await dataClient.GetContainer(databaseName, containerName)
                .CreateItemAsync(new { id = id, _partitionKey = partitionKey }, new PartitionKey(partitionKey));


            // read back the item
            var pointReadResult = await dataClient.GetContainer(databaseName, containerName)
                .ReadItemAsync<dynamic>(id, new PartitionKey(partitionKey));


            // run a query
            await dataClient.GetContainer(databaseName, containerName)
                .GetItemQueryIterator<dynamic>("SELECT * FROM c")
                .ReadNextAsync();
        }
    }
}

```

ManagedIdentityCredential을 사용하는 언어별 예제는 다음과 같습니다.

### <a name="net"></a>.NET

Cosmos DB 클라이언트를 초기화합니다.

```csharp
CosmosClient client = new CosmosClient("<account-endpoint>", new ManagedIdentityCredential());
```

그런 다음, [데이터를 읽고 씁니다](../../cosmos-db/sql/sql-api-dotnet-v3sdk-samples.md).

### <a name="java"></a>Java

Cosmos DB 클라이언트를 초기화합니다.

```java
CosmosAsyncClient Client = new CosmosClientBuilder().endpoint("<account-endpoint>") .credential(new ManagedIdentityCredential()) .build();
```

그런 다음, [이러한 샘플](../../cosmos-db/sql/sql-api-java-sdk-samples.md)에서 설명한 대로 데이터를 읽고 씁니다.

### <a name="javascript"></a>JavaScript

Cosmos DB 클라이언트를 초기화합니다.

```javascript
const client = new CosmosClient({ "<account-endpoint>", aadCredentials: new ManagedIdentityCredential() });
```

그런 다음, [이러한 샘플](../../cosmos-db/sql/sql-api-nodejs-samples.md)에서 설명한 대로 데이터를 읽고 씁니다.

## <a name="clean-up-steps"></a>정리 단계

# <a name="portal"></a>[포털](#tab/azure-portal)

1. [포털](https://portal.azure.com)에서 삭제하려는 리소스를 선택합니다.

1. **삭제** 를 선택합니다.

1. 메시지가 표시되면 삭제를 확인합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResource `
  -ResourceGroupName ExampleResourceGroup `
  -ResourceName ExampleVM `
  -ResourceType Microsoft.Compute/virtualMachines
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az resource delete \
  --resource-group ExampleResourceGroup \
  --name ExampleVM \
  --resource-type "Microsoft.Compute/virtualMachines"
```

# <a name="resource-manager-template"></a>[Resource Manager 템플릿](#tab/azure-resource-manager)

이 섹션은 의도적으로 비워 두었습니다.

---

## <a name="next-steps"></a>다음 단계

Azure 리소스의 관리 ID에 대해 자세히 알아보기

- [Azure 리소스에 대한 관리 ID란?](overview.md)
- [Azure 리소스 관리자 템플릿](https://github.com/Azure/azure-quickstart-templates)

Azure Cosmos에 대해 자세히 알아보기

- [Azure Cosmos DB 리소스 모델](../../cosmos-db/account-databases-containers-items.md)
- [자습서: .NET 콘솔 앱을 빌드하여 Azure Cosmos DB SQL API 계정에서 데이터 관리](../../cosmos-db/sql/sql-api-get-started.md)
