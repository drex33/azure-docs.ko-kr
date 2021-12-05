---
title: Azure AD를 사용하여 Azure Cosmos DB 계정에 대한 역할 기반 액세스 제어 구성
description: Azure Cosmos DB 계정에 대해 Azure Active Directory를 사용하여 역할 기반 액세스 제어를 구성하는 방법을 알아봅니다.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/21/2021
ms.author: thweiss
ms.openlocfilehash: 0b7dc975dcc75b66d1332cafd84d98c9faea943f
ms.sourcegitcommit: b69ce103ff31805cf2002b727670db9452ef8518
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2021
ms.locfileid: "133570596"
---
# <a name="configure-role-based-access-control-with-azure-active-directory-for-your-azure-cosmos-db-account"></a>Azure Cosmos DB 계정에 대해 Azure Active Directory를 사용하여 역할 기반 액세스 제어 구성
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!NOTE]
> 이 문서에서는 Azure Cosmos DB에서 데이터 평면 작업을 위한 역할 기반 액세스 제어에 대해 설명합니다. 관리 평면 작업을 사용하는 경우 해당 관리 평면 작업 문서에 적용되는 [역할 기반 액세스 제어](role-based-access-control.md)를 참조하세요.

Azure Cosmos DB에서 제공되는 기본 제공 RBAC(역할 기반 액세스 제어) 시스템을 통해 다음을 수행할 수 있습니다.

- Azure AD(Azure Active Directory) ID를 사용하여 데이터 요청을 인증합니다.
- 세분화된 역할 기반 권한 모델을 사용하여 데이터 요청에 권한을 부여합니다.

## <a name="concepts"></a>개념

Azure Cosmos DB 데이터 평면 RBAC는 [Azure RBAC](../role-based-access-control/overview.md)와 같은 다른 RBAC 시스템에서 일반적으로 발견되는 개념을 기반으로 합니다.

- [권한 모델](#permission-model)은 **작업** 집합으로 구성됩니다. 이러한 각 작업은 하나 이상의 데이터베이스 작업에 매핑됩니다. 일부 작업 예시에는 항목 읽기, 항목 쓰기, 쿼리 실행이 포함됩니다.
- Azure Cosmos DB 사용자는 허용된 작업 목록을 포함하여 **[역할 정의](#role-definitions)** 를 만듭니다.
- 역할 정의는 **[역할 할당](#role-assignments)** 을 통해 특정 Azure AD에 할당됩니다. 역할 할당은 또한 역할 정의가 적용되는 범위를 정의합니다. 현재까지는 세 가지 범위가 지원됩니다.
    - Azure Cosmos DB 계정,
    - Azure Cosmos DB 데이터베이스,
    - Azure Cosmos DB 컨테이너.

  :::image type="content" source="./media/how-to-setup-rbac/concepts.png" alt-text="RBAC 개념":::

## <a name="permission-model"></a><a id="permission-model"></a> 권한 모델

> [!IMPORTANT]
> 이 사용 권한 모델에는 데이터 읽기 및 쓰기와 관련 된 데이터베이스 작업만 포함 됩니다. 관리 리소스에 대 한 모든 종류의 관리 작업 *은 다루지 않습니다* . 예를 들면 다음과 같습니다.
> - 데이터베이스 만들기/바꾸기/삭제
> - 컨테이너 만들기/바꾸기/삭제
> - 컨테이너 처리량 바꾸기
> - 저장 프로시저 만들기/바꾸기/삭제/읽기
> - 트리거 만들기/바꾸기/삭제/읽기
> - 사용자 정의 함수 만들기/바꾸기/삭제/읽기
>
> *Azure Cosmos DB 데이터 평면 SDK를 사용* 하 여 Azure AD id로 관리 작업을 인증할 수 없습니다. 대신 다음 옵션 중 하나를 통해 [AZURE RBAC](role-based-access-control.md) 를 사용 해야 합니다.
> - [ARM 템플릿(Azure Resource Manager 템플릿)](./sql/manage-with-templates.md)
> - [Azure PowerShell 스크립트](./sql/manage-with-powershell.md)
> - [Azure CLI 스크립트](./sql/manage-with-cli.md)
> - 다음에서 사용할 수 있는 Azure 관리 라이브러리:
>   - [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.CosmosDB/)
>   - [Java](https://search.maven.org/artifact/com.azure.resourcemanager/azure-resourcemanager-cosmos)
>   - [Python](https://pypi.org/project/azure-mgmt-cosmosdb/)
>   
> 읽기 데이터베이스 및 읽기 컨테이너는 [메타 데이터 요청](#metadata-requests)으로 간주 됩니다. 다음 섹션에 설명 된 대로 이러한 작업에 대 한 액세스 권한을 부여할 수 있습니다.

다음 표에서는 권한 모델에서 제공되는 모든 작업을 보여줍니다.

| 이름 | 해당 데이터베이스 작업 |
|---|---|
| `Microsoft.DocumentDB/databaseAccounts/readMetadata` | 계정 메타데이터를 읽습니다. 자세한 내용은 [메타데이터 요청](#metadata-requests)을 참조하세요. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/create` | 새 항목을 만듭니다. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read` | 해당 ID 및 파티션 키로 개별 항목을 읽습니다(지점 읽기). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/replace` | 기존 항목을 바꿉니다. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/upsert` | 항목을 “upsert”합니다. 즉, 기존 항목이 없으면 만들고, 기존 항목이 있으면 바꿉니다. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/delete` | 항목을 삭제합니다. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery` | [SQL 쿼리](sql-query-getting-started.md)를 실행합니다. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed` | 컨테이너의 [변경 피드](read-change-feed.md)에서 읽습니다. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeStoredProcedure` | [저장 프로시저](stored-procedures-triggers-udfs.md)를 실행합니다. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/manageConflicts` | 다중 쓰기 지역 계정에 대한 [충돌](conflict-resolution-policies.md)을 관리합니다. 즉, 항목을 나열하고 충돌 피드에서 삭제합니다. |

와일드카드는 *컨테이너* 및 *항목* 수준 모두에서 지원됩니다.

- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*`
- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*`

### <a name="metadata-requests"></a><a id="metadata-requests"></a> 메타데이터 요청

Azure Cosmos DB SDK를 사용할 때 이러한 SDK는 초기화 중 읽기 전용 메타데이터 요청을 수행하고 특정 데이터 요청을 처리합니다. 이러한 메타데이터 요청은 다음과 같은 여러 구성 세부 정보를 페치합니다. 

- 계정을 사용할 수 있는 Azure 지역이 포함된 계정의 전역 구성.
- 컨테이너의 파티션 키 또는 해당 인덱싱 정책.
- 컨테이너 및 해당 주소를 구성하는 물리적 파티션 목록.

이것들은 계정에 저장된 데이터를 페치하지 *않습니다*.

권한 모델에 대해 최상의 투명성을 보장하기 위해 이러한 메타데이터 요청은 `Microsoft.DocumentDB/databaseAccounts/readMetadata` 작업으로 명시적으로 처리됩니다. 이 작업은 Azure Cosmos DB 계정이 Azure Cosmos DB SDK 중 하나를 통해 액세스되는 모든 경우에 허용됩니다. 계정, 데이터베이스 또는 컨테이너 등 Azure Cosmos DB 계층의 어느 수준에서든 역할 할당을 통해 할당될 수 있습니다.

`Microsoft.DocumentDB/databaseAccounts/readMetadata` 작업으로 허용되는 실제 메타데이터 요청은 작업이 할당된 범위에 따라 달라집니다.

| 범위 | 계정에서 허용되는 요청 |
|---|---|
| 계정 | - 계정의 데이터베이스 나열<br>- 계정의 각 데이터베이스에 대한 데이터베이스 범위에서 허용되는 작업 |
| 데이터베이스 | - 데이터베이스 메타데이터 읽기<br>- 데이터베이스 아래의 컨테이너 나열<br>- 데이터베이스 아래의 각 컨테이너에 대한 컨테이너 범위에서 허용되는 작업 |
| 컨테이너 | - 컨테이너 메타데이터 읽기<br>- 컨테이너 아래의 실제 파티션 나열<br>- 각 물리적 파티션의 주소 확인 |

## <a name="built-in-role-definitions"></a>기본 제공 역할 정의

Azure Cosmos DB에는 다음 두 개의 기본 제공 역할 정의가 표시됩니다.

| ID | 속성 | 포함된 작업 |
|---|---|---|
| 00000000-0000-0000-0000-000000000001 | Cosmos DB 기본 제공 데이터 판독기 | `Microsoft.DocumentDB/databaseAccounts/readMetadata`<br>`Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read`<br>`Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery`<br>`Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed` |
| 00000000-0000-0000-0000-000000000002 | Cosmos DB 기본 제공 데이터 기여자 | `Microsoft.DocumentDB/databaseAccounts/readMetadata`<br>`Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*`<br>`Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*` |

## <a name="create-custom-role-definitions"></a><a id="role-definitions"></a> 사용자 지정 역할 정의 만들기

사용자 지정 역할 정의를 만들 때는 다음을 제공해야 합니다.

- Azure Cosmos DB 계정의 이름.
- 계정이 포함된 리소스 그룹.
- 역할 정의의 유형: `CustomRole`.
- 역할 정의의 이름.
- 역할에 허용할 [작업](#permission-model) 목록.
- 역할 정의를 할당할 수 있는 하나 이상의 범위. 지원되는 범위:
    - `/`(계정 수준),
    - `/dbs/<database-name>`(데이터베이스 수준),
    - `/dbs/<database-name>/colls/<container-name>`(컨테이너 수준).

> [!NOTE]
> 아래 설명된 작업은 현재 다음 위치에서 제공됩니다.
> - Azure PowerShell: [Az.CosmosDB 버전 1.2.0](https://www.powershellgallery.com/packages/Az.CosmosDB/1.2.0) 이상
> - [Azure CLI](/cli/azure/install-azure-cli): 버전 2.24.0 이상

### <a name="using-azure-powershell"></a>Azure PowerShell 사용

읽기 작업만 포함하는 *MyReadOnlyRole* 라는 역할 만들기:

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>"
New-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -Type CustomRole -RoleName MyReadOnlyRole `
    -DataAction @( `
        'Microsoft.DocumentDB/databaseAccounts/readMetadata',
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed') `
    -AssignableScope "/"
```

모든 작업을 포함하는 *MyReadWriteRole* 라는 역할 만들기:

```powershell
New-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -Type CustomRole -RoleName MyReadWriteRole `
    -DataAction @( `
        'Microsoft.DocumentDB/databaseAccounts/readMetadata',
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*') `
    -AssignableScope "/"
```

ID를 페치하기 위해 만든 역할 정의 나열:

```powershell
Get-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName
```

```
RoleName         : MyReadWriteRole
Id               : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAcc
                   ounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>
Type             : CustomRole
Permissions      : {Microsoft.Azure.Management.CosmosDB.Models.Permission}
AssignableScopes : {/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAc
                   counts/<myCosmosAccount>}

RoleName         : MyReadOnlyRole
Id               : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAcc
                   ounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>
Type             : CustomRole
Permissions      : {Microsoft.Azure.Management.CosmosDB.Models.Permission}
AssignableScopes : {/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAc
                   counts/<myCosmosAccount>}
```

### <a name="using-the-azure-cli"></a>Azure CLI 사용

읽기 작업만 포함하는 *MyReadOnlyRole* 라는 역할 만들기:

```json
// role-definition-ro.json
{
    "RoleName": "MyReadOnlyRole",
    "Type": "CustomRole",
    "AssignableScopes": ["/"],
    "Permissions": [{
        "DataActions": [
            "Microsoft.DocumentDB/databaseAccounts/readMetadata",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed"
        ]
    }]
}
```

```azurecli
resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
az cosmosdb sql role definition create --account-name $accountName --resource-group $resourceGroupName --body @role-definition-ro.json
```

모든 작업을 포함하는 *MyReadWriteRole* 라는 역할 만들기:

```json
// role-definition-rw.json
{
    "RoleName": "MyReadWriteRole",
    "Type": "CustomRole",
    "AssignableScopes": ["/"],
    "Permissions": [{
        "DataActions": [
            "Microsoft.DocumentDB/databaseAccounts/readMetadata",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*"
        ]
    }]
}
```

```azurecli
az cosmosdb sql role definition create --account-name $accountName --resource-group $resourceGroupName --body @role-definition-rw.json
```

ID를 페치하기 위해 만든 역할 정의 나열:

```azurecli
az cosmosdb sql role definition list --account-name $accountName --resource-group $resourceGroupName
```

```
[
  {
    "assignableScopes": [
      "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>"
    ],
    "id": "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>",
    "name": "<roleDefinitionId>",
    "permissions": [
      {
        "dataActions": [
          "Microsoft.DocumentDB/databaseAccounts/readMetadata",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*"
        ],
        "notDataActions": []
      }
    ],
    "resourceGroup": "<myResourceGroup>",
    "roleName": "MyReadWriteRole",
    "sqlRoleDefinitionGetResultsType": "CustomRole",
    "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions"
  },
  {
    "assignableScopes": [
      "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>"
    ],
    "id": "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>",
    "name": "<roleDefinitionId>",
    "permissions": [
      {
        "dataActions": [
          "Microsoft.DocumentDB/databaseAccounts/readMetadata",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed"
        ],
        "notDataActions": []
      }
    ],
    "resourceGroup": "<myResourceGroup>",
    "roleName": "MyReadOnlyRole",
    "sqlRoleDefinitionGetResultsType": "CustomRole",
    "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions"
  }
]
```

### <a name="using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿 사용

[이 페이지](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/sql-resources/create-update-sql-role-definition)를 참조해 Azure Resource Manager를 사용해 역할 정의를 만드는 예제를 확인하세요.

## <a name="create-role-assignments"></a><a id="role-assignments"></a> 역할 할당 만들기

기본 제공 또는 사용자 지정 역할 정의를 Azure AD ID와 연결할 수 있습니다. 역할 할당을 만들 때 다음을 제공해야 합니다.

- Azure Cosmos DB 계정의 이름.
- 계정이 포함된 리소스 그룹.
- 할당할 역할 정의의 ID입니다.
- 역할 정의를 할당할 ID의 보안 주체 ID입니다.
- 역할 할당의 범위입니다. 지원되는 범위:
    - `/`(계정 수준)
    - `/dbs/<database-name>`(데이터베이스 수준)
    - `/dbs/<database-name>/colls/<container-name>`(컨테이너 수준)

  범위는 역할 정의의 할당 가능한 범위 중 하나와 일치하거나 이 범위에 포함되어야 합니다.

> [!NOTE]
> 서비스 보안 주체에 대해 역할 할당을 만들려면 **Azure Active Directory** 포털 블레이드의 **엔터프라이즈 애플리케이션** 섹션에서 확인된 대로 해당 **개체 ID** 를 사용해야 합니다.

> [!NOTE]
> 아래 설명된 작업은 현재 다음 위치에서 제공됩니다.
> - Azure PowerShell: [Az.CosmosDB 버전 1.2.0](https://www.powershellgallery.com/packages/Az.CosmosDB/1.2.0) 이상
> - [Azure CLI](/cli/azure/install-azure-cli): 버전 2.24.0 이상

### <a name="using-azure-powershell"></a>Azure PowerShell 사용

ID에 역할 할당:

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>"
$readOnlyRoleDefinitionId = "<roleDefinitionId>" # as fetched above
$principalId = "<aadPrincipalId>"
New-AzCosmosDBSqlRoleAssignment -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -RoleDefinitionId $readOnlyRoleDefinitionId `
    -Scope "/" `
    -PrincipalId $principalId
```

### <a name="using-the-azure-cli"></a>Azure CLI 사용

ID에 역할 할당:

```azurecli
resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
readOnlyRoleDefinitionId = '<roleDefinitionId>' # as fetched above
principalId = '<aadPrincipalId>'
az cosmosdb sql role assignment create --account-name $accountName --resource-group $resourceGroupName --scope "/" --principal-id $principalId --role-definition-id $readOnlyRoleDefinitionId
```

### <a name="using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿 사용

[이 페이지](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/sql-resources/create-update-sql-role-assignment)를 참조해 Azure Resource Manager 템플릿을 사용하여 역할 할당을 만드는 예제를 확인하세요.

## <a name="initialize-the-sdk-with-azure-ad"></a>Azure AD로 SDK 초기화

애플리케이션에서 Azure Cosmos DB RBAC를 사용하려면 Azure Cosmos DB SDK를 초기화하는 방법을 업데이트해야 합니다. 계정의 기본 키를 전달하는 대신 `TokenCredential` 클래스의 인스턴스를 전달해야 합니다. 이 인스턴스는 사용하려는 ID 대신 AAD 토큰을 페치하기 위해 필요한 컨텍스트를 Azure Cosmos DB SDK에 제공합니다.

`TokenCredential` 인스턴스를 만드는 방법은 이 문서의 범위를 벗어납니다. 사용자 보안 주체, 서비스 보안 제출, 그룹 등 사용하려는 AAD ID의 유형에 따라 이러한 인스턴스를 만드는 여러 방법이 있습니다. 가장 중요한 것은 `TokenCredential` 인스턴스가 역할이 할당된 ID(보안 주체 ID)로 확인되어야 한다는 것입니다. 다음 위치에서 `TokenCredential` 클래스 만들기 예시를 찾아볼 수 있습니다.

- [.NET](/dotnet/api/overview/azure/identity-readme#credential-classes)
- [Java에서](/java/api/overview/azure/identity-readme#credential-classes)
- [JavaScript에서](/javascript/api/overview/azure/identity-readme#credential-classes)

아래 예시에서는 `ClientSecretCredential` 인스턴스에 서비스 보안 주체를 사용합니다.

### <a name="in-net"></a>.NET

Azure Cosmos DB RBAC는 현재 [.NET SDK V3](sql-api-sdk-dotnet-standard.md)에서 지원됩니다.

```csharp
TokenCredential servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
CosmosClient client = new CosmosClient("<account-endpoint>", servicePrincipal);
```

### <a name="in-java"></a>Java에서

Azure Cosmos DB RBAC는 현재 [Java SDK V4](sql-api-sdk-java-v4.md)에서 지원됩니다.

```java
TokenCredential ServicePrincipal = new ClientSecretCredentialBuilder()
    .authorityHost("https://login.microsoftonline.com")
    .tenantId("<azure-ad-tenant-id>")
    .clientId("<client-application-id>")
    .clientSecret("<client-application-secret>")
    .build();
CosmosAsyncClient Client = new CosmosClientBuilder()
    .endpoint("<account-endpoint>")
    .credential(ServicePrincipal)
    .build();
```

### <a name="in-javascript"></a>JavaScript

Azure Cosmos DB RBAC는 현재 [JavaScript SDK V3](sql-api-sdk-node.md)에서 지원됩니다.

```javascript
const servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
const client = new CosmosClient({
    "<account-endpoint>",
    aadCredentials: servicePrincipal
});
```

## <a name="authenticate-requests-on-the-rest-api"></a>REST API에서 요청 인증

[REST API 인증 헤더](/rest/api/cosmos-db/access-control-on-cosmosdb-resources)를 생성할 때 다음 예제와 같이 **유형** 매개 변수를 **aad** 로 설정하고, 해시 서명 **(sig)** 을 **oauth 토큰** 으로 설정합니다.

`type=aad&ver=1.0&sig=<token-from-oauth>`

## <a name="use-data-explorer"></a>데이터 탐색기 사용

> [!NOTE]
> Azure Portal에서 표시되는 데이터 탐색기는 아직 Azure Cosmos DB RBAC를 지원하지 않습니다. 데이터를 탐색할 때 Azure AD ID를 사용하려면 [Azure Cosmos DB Explorer](https://cosmos.azure.com/?feature.enableAadDataPlane=true)를 사용해야 합니다.

특정 `?feature.enableAadDataPlane=true` 쿼리 매개 변수를 사용하여 [Azure Cosmos DB 탐색기](https://cosmos.azure.com/?feature.enableAadDataPlane=true)에 액세스하고 로그인하는 경우 데이터에 액세스하는 데 다음 논리가 사용됩니다.

1. 계정 기본 키를 가져오는 요청은 로그인한 ID 대신 시도됩니다. 이 요청이 성공하면 기본 키가 계정 데이터에 액세스하는 데 사용됩니다.
1. 로그인한 ID가 계정의 기본 키를 가져올 수 없는 경우 이 ID는 데이터 액세스를 인증하는 데 직접 사용됩니다. 이 모드에서 데이터 액세스를 보장하려면 ID에 [적절한 역할 정의를 할당](#role-assignments)해야 합니다.

## <a name="audit-data-requests"></a>데이터 요청 감사

Azure Cosmos DB RBAC를 사용하면, [진단 로그](cosmosdb-monitor-resource-logs.md)가 각 데이터 작업의 ID 및 권한 부여 정보로 강화됩니다. 이를 통해 세부 감사를 수행하고 Azure Cosmos DB 계정에 전송되는 모든 데이터 요청에 사용되는 AAD ID를 검색할 수 있습니다.

이 추가 정보는 **DataPlaneRequests** 로그 범주에 들어가며, 두 가지 추가 열로 구성됩니다.

- `aadPrincipalId_g`는 요청을 인증하기 위해 사용된 AAD ID의 보안 주체 ID를 보여줍니다.
- `aadAppliedRoleAssignmentId_g`는 요청을 인증할 때 적용된 [역할 할당](#role-assignments)을 보여줍니다.

## <a name="enforcing-rbac-as-the-only-authentication-method"></a><a id="disable-local-auth"></a> 유일한 인증 방법으로 RBAC 적용

클라이언트가 RBAC를 통해서만 Azure Cosmos DB에 연결하도록 하려는 상황에서는 계정의 기본/보조 키를 사용하지 않도록 설정하는 옵션이 있습니다. 이렇게 하면 기본/보조 키 또는 리소스 토큰을 사용하는 모든 수신 요청이 적극적으로 거부됩니다.

### <a name="using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿 사용

Azure Resource Manager 템플릿을 사용하여 Azure Cosmos DB 계정을 만들거나 업데이트할 때 `disableLocalAuth` 속성을 `true`로 설정합니다.

```json
"resources": [
    {
        "type": " Microsoft.DocumentDB/databaseAccounts",
        "properties": {
            "disableLocalAuth": true,
            // ...
        },
        // ...
    },
    // ...
 ]
```

## <a name="limits"></a>제한

- Azure Cosmos DB 계정당 최대 100개의 역할 정의와 2,000개의 역할 할당을 만들 수 있습니다.
- 또한 Azure Cosmos DB 계정과 동일한 Azure AD 테넌트에 속하는 Azure AD ID에만 역할 정의를 할당할 수 있습니다.
- 200개를 초과하는 그룹에 속하는 ID에 대해서는 현재까지 Azure AD 그룹 확인이 지원되지 않습니다.
- Azure AD 토큰은 현재까지 Azure Cosmos DB 서비스로 전송되는 개별 요청의 헤더로 전달되어, 전체 페이로드 크기를 늘립니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="which-azure-cosmos-db-apis-are-supported-by-rbac"></a>RBAC에서 지원되는 Azure Cosmos DB API는 무엇인가요?

현재는 SQL API만 지원됩니다.

### <a name="is-it-possible-to-manage-role-definitions-and-role-assignments-from-the-azure-portal"></a>Azure Portal에서 역할 정의 및 역할 할당을 관리할 수 있나요?

역할 관리에 대한 Azure Portal 지원은 아직 사용할 수 없습니다.

### <a name="which-sdks-in-azure-cosmos-db-sql-api-support-rbac"></a>Azure Cosmos DB SQL API에서 RBAC를 지원하는 SDK는 무엇인가요?

현재 [.NET V3](sql-api-sdk-dotnet-standard.md), [Java V4](sql-api-sdk-java-v4.md) 및 [JavaScript V3](sql-api-sdk-node.md) SDK가 지원됩니다.

### <a name="is-the-azure-ad-token-automatically-refreshed-by-the-azure-cosmos-db-sdks-when-it-expires"></a>Azure AD 토큰이 만료될 때 Azure Cosmos DB SDK에 의해 자동으로 새로 고쳐지나요?

예.

### <a name="is-it-possible-to-disable-the-usage-of-the-account-primarysecondary-keys-when-using-rbac"></a>RBAC를 사용할 때 계정의 기본/보조 키를 사용하지 않도록 설정할 수 있나요?

예, [유일한 인증 방법으로 RBAC 적용](#disable-local-auth)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Cosmos DB의 데이터에 대한 보안 액세스](secure-access-to-data.md) 개요를 살펴봅니다.
- [Azure Cosmos DB 관리를 위한 RBAC](role-based-access-control.md)에 대해 자세히 알아보세요.
