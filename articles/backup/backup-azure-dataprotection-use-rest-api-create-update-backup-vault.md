---
title: REST API를 사용하여 Blob에 대한 Azure Backup 정책 만들기
description: 이 문서에서는 REST API를 사용하여 스토리지 계정에서 Blob을 백업하는 정책을 만드는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 07/09/2021
ms.assetid: 93861379-5bec-4ed5-95d2-46f534a115fd
ms.openlocfilehash: 9b32e69bcd96d48dcd1321a69132790a93b1220b
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598755"
---
# <a name="create-azure-backup-vault-using-rest-api"></a>REST API를 사용하여 Azure Backup 자격 증명 모음 만들기

Azure Backup의 새로운 데이터 보호 플랫폼은 스토리지 계정, 관리 디스크, PostGre SQL 서버 PaaS 플랫폼의 Blob과 같은 최신 워크로드를 백업 및 복원하는 향상된 기능을 제공합니다. 이 기능의 목표는 관리 오버헤드를 최소화하면서 백업을 쉽게 구성하는 것입니다. ‘백업 자격 증명 모음’은 데이터 보호 플랫폼의 토대이며 이는 ‘Recovery Services’ 자격 증명 모음과 다릅니다.

REST API를 사용하여 Azure Backup 자격 증명 모음을 만드는 단계는 [자격 증명 모음 REST API 만들기](/rest/api/dataprotection/backup-vaults/create-or-update) 설명서에 설명되어 있습니다. 이 문서를 참조하여 “미국 서부” 및 ‘TestBkpVaultRG’ 리소스 그룹에서 “testBkpVault”라는 자격 증명 모음을 만들어 보겠습니다.

Azure Backup 자격 증명 모음을 만들거나 업데이트하려면 다음 *PUT* 작업을 사용합니다.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/testBkpVault?api-version=2021-01-01
```

## <a name="create-a-request"></a>요청 만들기

*PUT* 요청을 만들려면 `{subscription-id}` 매개 변수는 필수입니다. 구독이 여러 개인 경우 [여러 구독으로 작업](/cli/azure/manage-azure-subscriptions-azure-cli)을 참조합니다. 리소스에 대해 `{resourceGroupName}` 및 `{vaultName}`과 함께 `api-version` 매개 변수를 정의합니다. 이 문서에서는 `api-version=2021-01-01`을 사용합니다.

다음과 같은 헤더가 필요합니다.

| 요청 헤더   | Description |
|------------------|-----------------|
| *Content-Type:*  | 필수 사항입니다. `application/json`로 설정합니다. |
| *권한 부여* | 필수 사항입니다. 유효한 `Bearer` [액세스 토큰](/rest/api/azure/#authorization-code-grant-interactive-clients)으로 설정합니다. |

요청을 만드는 방법에 대한 자세한 내용은 [REST API 요청/응답의 구성 요소](/rest/api/azure/#components-of-a-rest-api-requestresponse)를 참조하세요.

## <a name="create-the-request-body"></a>요청 본문 만들기

다음과 같은 일반적인 정의가 요청 본문을 빌드하는 데 사용됩니다.

|이름  |필수  |Type  |설명  |
|---------|---------|---------|---------|
|eTag     |         |   String      |  선택적 eTag       |
|위치     |  true       |String         |   리소스 위치      |
|properties     |   true      | [BackupVault](/rest/api/dataprotection/backup-vaults/create-or-update#backupvault)        |  자격 증명 모음의 속성       |
|ID     |         |  [DPPIdentityDetails](/rest/api/dataprotection/backup-vaults/create-or-update#dppidentitydetails)       |    각 Azure 리소스에 대한 고유한 시스템 식별자를 식별합니다.     |
|tags     |         | Object        |     리소스 태그    |

자격 증명 모음 이름 및 리소스 그룹 이름은 PUT URI에 제공되어 있습니다. 요청 본문은 위치를 정의합니다.

## <a name="example-request-body"></a>요청 본문 예제

다음 예제 본문은 “미국 서부”에 자격 증명 모음을 만드는 데 사용됩니다. 위치를 지정하세요.

```json
{
  "location": "WestUS",
  "tags": {
    "key1": "val1"
  },
  "identity": {
    "type": "None"
  },
  "properties": {
    "storageSettings": [
      {
        "datastoreType": "VaultStore",
        "type": "LocallyRedundant"
      }
    ]
  }
}
```

백업 자격 증명 모음을 만들고 시스템이 할당한 ID도 생성하려면 다음 요청 본문을 제공해야 합니다.

```json
{
  "location": "WestUS",
  "tags": {
    "key1": "val1"
  },
  "identity": {
    "type": "systemAssigned"
  },
  "properties": {
    "storageSettings": [
      {
        "datastoreType": "VaultStore",
        "type": "LocallyRedundant"
      }
    ]
  }
}
```

## <a name="responses"></a>응답

백업 자격 증명 모음 만들기는 [비동기 작업](../azure-resource-manager/management/async-operations.md)입니다. 즉, 이 작업은 별도로 추적해야 하는 다른 작업을 만듭니다.
백업 자격 증명 모음을 만들거나 업데이트하는 작업에 대한 성공적인 응답에는 두 가지가 있습니다.

|Name  |유형  |설명  |
|---------|---------|---------|
|200 정상     |   [BackupVaultResource](/rest/api/dataprotection/backup-vaults/create-or-update#backupvaultresource)      | 정상        |
|201 생성됨     | [BackupVaultResource](/rest/api/dataprotection/backup-vaults/create-or-update#backupvaultresource)        |   생성일      |
| 기타 상태 코드  |  [CloudError](/rest/api/dataprotection/backup-vaults/create-or-update#clouderror)

REST API 응답에 대한 자세한 내용은 [응답 메시지 처리](/rest/api/azure/#process-the-response-message)를 참조하세요.

### <a name="example-response"></a>예제 응답

이전 요청 본문 예제에서 압축된 ‘201 생성됨’ 응답은 *id* 가 할당되었으며 *provisioningState* 가 ‘성공’임을 보여 줍니다.

```json
{
    "eTag": null,
    "id": "/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/TestBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/testBkpVault",
    "identity": {
      "principalId": "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "tenantId": "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "SystemAssigned"
    },
    "location": "westUS",
    "name": "testBkpVault",
    "properties": {
      "provisioningState": "Succeeded",
      "storageSettings": [
        {
          "datastoreType": "VaultStore",
          "type": "GeoRedundant"
        }
      ]
    },
    "resourceGroup": "TestBkpVaultRG",
    "systemData": null,
    "tags": {},
    "type": "Microsoft.DataProtection/backupVaults"
  }
```

## <a name="next-steps"></a>다음 단계

[이 자격 증명 모음에서 Blob을 백업하기 위한 백업 정책을 만듭니다](backup-azure-dataprotection-use-rest-api-create-update-blob-policy.md).

Azure REST API에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure Data Protection 공급자 REST API](/rest/api/dataprotection/)
- [Azure REST API 시작하기](/rest/api/azure/)
