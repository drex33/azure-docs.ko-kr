---
title: REST API를 사용하여 Recovery Services 자격 증명 모음 구성 업데이트
description: 이 문서에서는 REST API를 사용하여 자격 증명 모음 구성을 업데이트하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 12/06/2019
ms.assetid: 9aafa5a0-1e57-4644-bf79-97124db27aa2
ms.openlocfilehash: 6dfa05a3bc26c21da95d60374582f10a1a0b84d2
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114458850"
---
# <a name="update-azure-recovery-services-vault-configurations-using-rest-api"></a>REST API를 사용하여 Azure Recovery Services 자격 증명 모음 구성 업데이트

이 문서에서는 REST API를 사용하여 Azure Recovery Services 자격 증명 모음에서 백업 관련 구성을 업데이트하는 방법을 설명합니다.

## <a name="soft-delete-state"></a>일시 삭제 상태

보호된 항목의 백업 삭제는 모니터링이 필요한 중요한 작업입니다. 실수로 인한 삭제를 방지하기 위해 Azure Recovery Services 자격 증명 모음에는 일시 삭제 기능이 있습니다. 이 기능을 사용하면 삭제 후 일정 기간 내에 삭제된 백업을 복원할 수 있습니다(필요한 경우).

그러나 이 기능이 필요하지 않은 시나리오가 있습니다. Azure Recovery Services 자격 증명 모음은 일시 삭제된 경우에도 해당 항목 내에 백업 항목이 있으면 삭제할 수 없습니다. 이는 자격 증명 모음을 즉시 삭제해야 하는 경우에 문제가 될 수 있습니다. 예를 들어 배포 작업은 동일한 워크플로에서 생성된 리소스를 정리하는 경우가 많습니다. 배포는 자격 증명 모음을 만들고, 항목에 대한 백업을 구성하고, 테스트 복원을 수행한 다음, 백업 항목 및 자격 증명 모음 삭제를 진행할 수 있습니다. 자격 증명 모음을 삭제하지 못하면 전체 배포가 실패할 수 있습니다. 즉시 삭제를 보장하는 유일한 방법은 일시 삭제를 사용하지 않는 것입니다.

따라서 시나리오에 따라 특정 자격 증명 모음에 대해 일시 삭제를 비활성화할지 여부를 신중하게 선택해야 합니다. 자세한 내용은 [일시 삭제 문서](backup-azure-security-feature-cloud.md)를 참조하세요.

### <a name="fetch-soft-delete-state-using-rest-api"></a>REST API를 사용하여 일시 삭제 상태 페치

기본적으로 새로 만든 Recovery Services 자격 증명 모음에는 일시 삭제 상태가 활성화됩니다. 자격 증명 모음에 대한 일시 삭제 상태를 페치/업데이트하려면 백업 자격 증명 모음의 구성 관련 [REST API 문서](/rest/api/backup/backup-resource-vault-configs)를 사용합니다.

자격 증명 모음에 대한 일시 삭제의 현재 상태를 페치하려면 다음 *GET* 작업을 사용합니다.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-06-15
```

GET URI에는 `{subscriptionId}`, `{vaultName}`, `{vaultresourceGroupName}` 매개 변수가 있습니다. 이 예제에서 `{vaultName}`은 "testVault"이고 `{vaultresourceGroupName}`은 "testVaultRG"입니다. 모든 필수 매개 변수가 URI에서 지정되므로 별도의 요청 본문이 필요 없습니다.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-06-15
```

#### <a name="responses"></a>응답

'GET' 작업에 대한 성공적인 응답은 다음과 같습니다.

|이름  |유형  |설명  |
|---------|---------|---------|
|200 정상     |   [BackupResourceVaultConfig](/rest/api/backup/backup-resource-vault-configs/get#backupresourcevaultconfigresource)      | 정상        |

##### <a name="example-response"></a>예제 응답

‘GET’ 요청이 제출되면 응답으로 200(성공)이 반환됩니다.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Enabled"
  }
}
```

### <a name="update-soft-delete-state-using-rest-api"></a>REST API를 사용하여 일시 삭제 상태 업데이트

REST API를 사용하여 Recovery Services 자격 증명 모음의 일시 삭제 상태를 업데이트하려면 다음 *PUT* 작업을 사용합니다.

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-06-15
```

POST URI에는 `{subscriptionId}`, `{vaultName}`, `{vaultresourceGroupName}` 매개 변수가 있습니다. 이 예제에서 `{vaultName}`은 "testVault"이고 `{vaultresourceGroupName}`은 "testVaultRG"입니다. URI를 위의 값으로 바꾸면 URI가 다음과 같이 표시됩니다.

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-06-15
```

#### <a name="create-the-request-body"></a>요청 본문 만들기

다음과 같은 일반적인 정의가 요청 본문을 만드는 데 사용됩니다.

자세한 내용은 [REST API 설명서](/rest/api/backup/backup-resource-vault-configs/update#request-body)를 참조하세요.

|이름  |필수  |Type  |설명  |
|---------|---------|---------|---------|
|eTag     |         |   String      |  선택적 eTag       |
|위치     |  true       |String         |   리소스 위치      |
|properties     |         | [VaultProperties](/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  자격 증명 모음의 속성       |
|tags     |         | Object        |     리소스 태그    |

#### <a name="example-request-body"></a>요청 본문 예제

다음 예제는 일시 삭제 상태를 '사용할 수 없는'으로 업데이트하는 데 사용됩니다.

```json
{
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

#### <a name="responses-for-the-patch-operation"></a>PATCH 작업에 대한 응답

'PATCH' 작업에 대한 성공적인 응답은 다음과 같습니다.

|이름  |유형  |설명  |
|---------|---------|---------|
|200 정상     |   [BackupResourceVaultConfig](/rest/api/backup/backup-resource-vault-configs/get#backupresourcevaultconfigresource)      | 정상        |

##### <a name="example-response-for-the-patch-operation"></a>PATCH 작업에 대한 응답의 예

‘PATCH’ 요청이 제출되면 응답으로 200(성공)이 반환됩니다.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

## <a name="next-steps"></a>다음 단계

[이 자격 증명 모음의 Azure VM을 백업하기 위한 백업 정책을 만듭니다](backup-azure-arm-userestapi-createorupdatepolicy.md).

Azure REST API에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure Recovery Services 공급자 REST API](/rest/api/recoveryservices/)
- [Azure REST API 시작하기](/rest/api/azure/)
