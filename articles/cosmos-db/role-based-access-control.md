---
title: Azure Cosmos DB의 Azure 역할 기반 액세스 제어
description: Active Directory 통합(Azure RBAC)을 사용하여 Azure Cosmos DB에서 데이터베이스 보호를 제공하는 방법을 알아봅니다.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/17/2021
author: ThomasWeiss
ms.author: thweiss
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4925120adc86eb193e4b031ee7cebb23e7a71f45
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130247572"
---
# <a name="azure-role-based-access-control-in-azure-cosmos-db"></a>Azure Cosmos DB의 Azure 역할 기반 액세스 제어
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

> [!NOTE]
> Azure Cosmos DB의 Azure RBAC 지원은 관리 평면 작업에만 적용됩니다. 이 문서에서는 Azure Cosmos DB의 관리 평면 작업을 위한 역할 기반 액세스 제어에 대해 설명합니다. 데이터 평면 작업을 사용하는 경우 데이터는 기본 키, 리소스 토큰 또는 Azure Cosmos DB RBAC를 사용하여 보호됩니다. 데이터 평면 작업에 적용되는 역할 기반 액세스 제어에 대해 자세히 알아보려면 [데이터에 대한 보안 액세스](secure-access-to-data.md) 및 [Azure Cosmos DB RBAC](how-to-setup-rbac.md) 문서를 참조하세요.

Azure Cosmos DB는 Azure Cosmos DB의 일반적인 관리 시나리오에 대한 Azure RBAC(Azure 역할 기반 액세스 제어)를 기본적으로 제공합니다. Azure Active Directory에 프로필이 있는 개인은 이러한 Azure 역할을 사용자, 그룹, 서비스 주체 또는 관리 ID에 할당하여 리소스 및 Azure Cosmos DB 리소스 작업에 대한 액세스를 부여하거나 거부할 수 있습니다. 역할 할당은 컨트롤 플레인 액세스 전용으로 범위가 지정됩니다. 여기에는 Azure Cosmos 계정, 데이터베이스, 컨테이너 및 제품(처리량)에 대한 액세스가 포함됩니다.

## <a name="built-in-roles"></a>기본 제공 역할

Azure Cosmos DB에서 지원하는 기본 제공 역할은 다음과 같습니다.

|**기본 제공 역할**  |**설명**  |
|---------|---------|
|[DocumentDB 계정 기여자](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Azure Cosmos DB 계정을 관리할 수 있습니다.|
|[Cosmos DB 계정 읽기 권한자](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Azure Cosmos DB 계정 데이터를 읽을 수 있음.|
|[Cosmos Backup 운영자](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)| 정기적 백업 사용 데이터베이스 또는 컨테이너의 Azure Portal에 대한 복원 요청을 제출할 수 있습니다. Azure Portal의 백업 간격 및 보존을 수정할 수 있습니다. 데이터에 액세스하거나 데이터 탐색기를 사용할 수 없습니다.  |
| [CosmosRestoreOperator](../role-based-access-control/built-in-roles.md) | 연속 백업 모드를 사용하여 Azure Cosmos DB 계정에 대해 복원 작업을 수행할 수 있습니다.|
|[Cosmos DB 운영자](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|Azure Cosmos 계정, 데이터베이스 및 컨테이너를 프로비저닝할 수 있습니다. 데이터에 액세스하거나 데이터 탐색기를 사용할 수 없습니다.|

## <a name="identity-and-access-management-iam"></a>IAM(ID 및 액세스 관리)

Azure Portal의 **액세스 제어(IAM)** 창은 Azure Cosmos 리소스에 대한 Azure 역할 기반 액세스 제어를 구성하는 데 사용됩니다. 이러한 역할은 Active Directory의 사용자, 그룹, 서비스 주체 및 관리 ID에 적용됩니다. 개인 및 그룹에 대해 기본 제공 역할 또는 사용자 지정 역할을 사용할 수 있습니다. 다음 스크린샷은 Azure Portal에서 액세스 제어(IAM)를 사용하는 Active Directory 통합(Azure RBAC)을 보여 줍니다.

:::image type="content" source="./media/role-based-access-control/database-security-identity-access-management-rbac.png" alt-text="Azure Portal에서 액세스 제어(IAM) - 데이터베이스 보안 설명":::

## <a name="custom-roles"></a>사용자 지정 역할

사용자는 기본 제공 역할 외에도 Azure에서 [사용자 지정 역할](../role-based-access-control/custom-roles.md)을 만들고 해당 Active Directory 테넌트 내의 모든 구독에서 서비스 주체에 이러한 역할을 적용할 수 있습니다. 사용자 지정 역할은 사용자가 리소스 공급자 작업의 사용자 지정 집합을 사용하여 Azure 역할 정의를 만드는 방법을 제공합니다. Azure Cosmos DB에 대한 사용자 지정 역할을 빌드하는 데 사용할 수 있는 작업에 대한 자세한 내용은 [Azure Cosmos DB 리소스 공급자 작업](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)을 참조하세요.

> [!TIP]
> Cosmos DB 내에 저장된 데이터에 액세스하거나 Azure Portal에서 데이터 탐색기를 사용해야 하는 사용자 지정 역할은 `Microsoft.DocumentDB/databaseAccounts/listKeys/*` 작업을 수행해야 합니다.

## <a name="preventing-changes-from-the-azure-cosmos-db-sdks"></a><a id="prevent-sdk-changes"></a>Azure Cosmos DB SDK에서 변경 방지

계정 키를 사용하여 연결하는 클라이언트(즉, Azure Cosmos SDK를 통해 연결하는 애플리케이션)에서 리소스를 변경하는 것을 방지하기 위해 Azure Cosmos DB 리소스 공급자를 잠글 수 있습니다. 이 기능은 프로덕션 환경에 대한 더 많은 제어 및 관리를 원하는 사용자에게 적합할 수 있습니다. 또한 SDK를 변경하지 못하게 하면 컨트롤 플레인 작업에 대한 리소스 잠금 및 진단 로그와 같은 기능을 사용할 수 있습니다. Azure Cosmos DB SDK에서 연결하는 클라이언트는 Azure Cosmos 계정, 데이터베이스, 컨테이너 및 처리량에 대한 속성을 변경할 수 없습니다. Cosmos 컨테이너 자체에 대한 데이터 읽기 및 쓰기와 관련된 작업은 영향을 받지 않습니다.

이 기능을 사용하도록 설정하면 관리되는 서비스 ID를 비롯한 Azure Active Directory 자격 증명 및 올바른 Azure 역할이 있는 사용자만 리소스를 변경할 수 있습니다.

> [!WARNING]
> 이 기능을 사용하도록 설정하면 애플리케이션에 영향을 줄 수 있습니다. 사용하도록 설정하기 전에 영향을 이해해야 합니다.

### <a name="check-list-before-enabling"></a>활성화하기 전에 목록 확인

이 설정은 Cosmos DB SDK, 계정 키를 통해 연결된 모든 도구 또는 Azure Portal을 포함하여 계정 키를 사용하여 연결하는 모든 클라이언트에서 Cosmos 리소스를 변경하는 것을 방지합니다. 이 기능을 사용하도록 설정한 후 애플리케이션의 문제 또는 오류를 방지하려면 이 기능을 사용하도록 설정하기 전에 애플리케이션 또는 Azure Portal 사용자가 다음 작업 중 하나를 수행하는지 확인합니다.

- 모든 속성을 포함하거나 지역을 추가하거나 제거하는 Cosmos 계정에 대한 변경

- 데이터베이스 및 컨테이너와 같은 자식 리소스를 만들고 삭제. 여기에는 Cassandra, MongoDB, Gremlin 및 테이블 리소스와 같은 다른 API에 대한 리소스가 포함됩니다.

- 데이터베이스 또는 컨테이너 수준 리소스에 대한 처리량 업데이트

- 인덱스 정책, TTL 및 고유 키를 비롯한 컨테이너 속성 수정

- 저장 프로시저, 트리거 또는 사용자 정의 함수 수정

애플리케이션(또는 Azure Portal을 통한 사용자)이 이러한 작업을 수행하는 경우 [ARM 템플릿](sql/manage-with-templates.md), [PowerShell](sql/manage-with-powershell.md), [Azure CLI](sql/manage-with-cli.md), REST 또는 [Azure Management Library](https://github.com/Azure-Samples/cosmos-management-net)를 통해 실행하도록 마이그레이션해야 합니다. Azure Management는 [여러 언어](../index.yml?product=featured)로 제공됩니다.

### <a name="set-via-arm-template"></a>ARM 템플릿을 통해 설정

ARM 템플릿을 사용하여 이 속성을 설정하려면 기존 템플릿을 업데이트하거나 현재 배포에 대한 새 템플릿을 내보낸 다음 `databaseAccounts` 리소스의 속성에 `"disableKeyBasedMetadataWriteAccess": true`를 포함합니다. 다음은 이 속성 설정을 사용하는 Azure Resource Manager 템플릿의 기본 예입니다.

```json
{
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2020-04-01",
      "location": "[parameters('location')]",
      "kind": "GlobalDocumentDB",
      "properties": {
        "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
        "locations": "[variables('locations')]",
        "databaseAccountOfferType": "Standard",
        "disableKeyBasedMetadataWriteAccess": true
        }
    }
}
```

> [!IMPORTANT]
> 이 속성을 사용하여 재배포할 때 계정 및 자식 리소스에 대한 다른 속성을 포함해야 합니다. 이 템플릿을 그대로 배포하면 모든 계정 속성이 초기화됩니다.

### <a name="set-via-azure-cli"></a>Azure CLI를 통해 설정

Azure CLI를 사용하도록 설정하려면 아래 명령을 사용합니다.

```azurecli-interactive
az cosmosdb update  --name [CosmosDBAccountName] --resource-group [ResourceGroupName]  --disable-key-based-metadata-write-access true

```

### <a name="set-via-powershell"></a>PowerShell을 통해 설정

Azure PowerShell을 사용하도록 설정하려면 아래 명령을 사용합니다.

```azurepowershell-interactive
Update-AzCosmosDBAccount -ResourceGroupName [ResourceGroupName] -Name [CosmosDBAccountName] -DisableKeyBasedMetadataWriteAccess true
```

## <a name="next-steps"></a>다음 단계

- [Azure RBAC(Azure 역할 기반 액세스 제어)](../role-based-access-control/overview.md)
- [Azure 사용자 지정 역할](../role-based-access-control/custom-roles.md)
- [Azure Cosmos DB 리소스 공급자 작업](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
