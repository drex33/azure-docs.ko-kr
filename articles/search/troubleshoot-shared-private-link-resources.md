---
title: 공유 프라이빗 링크 리소스 문제 해결
titleSuffix: Azure Cognitive Search
description: 공유 프라이빗 링크 리소스를 관리할 때 발생하는 일반적인 문제 해결 가이드입니다.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/30/2021
ms.openlocfilehash: 81bae18cdc4a977ef03ddf807f9277037d939bd0
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122567770"
---
# <a name="troubleshooting-common-issues-with-shared-private-link-resources"></a>공유 프라이빗 링크 리소스 일반 문제 해결

공유 프라이빗 링크 리소스를 사용하면 Azure Cognitive Search에서 보안 아웃바운드 연결을 설정하여 고객 리소스에 액세스할 수 있습니다. 하지만 이러한 리소스를 관리(만들기, 삭제 또는 업데이트)하는 과정에서 몇 가지 유형의 오류가 발생할 수 있습니다.

## <a name="creating-a-shared-private-link-resource"></a>공유 프라이빗 링크 리소스 만들기

공유 프라이빗 링크 리소스를 만드는 네 가지 고유한 단계는 다음과 같습니다.

1. 고객이 검색 리소스 공급자(RP)에서 만들어야 하는 공유 프라이빗 링크 세부 정보를 사용하여 관리 평면 [CreateOrUpdate API](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/create-or-update)를 호출합니다.

2. 검색 RP는 요청의 유효성을 검사하고 유효한 경우 비동기 Azure Resource Manager 작업을 수행합니다(고객이 진행 상황을 쿼리할 수 있음).

3. 작업 완료에 대해 쿼리를 검색합니다(일반적으로 몇 분 정도 소요됨). 이 시점에서 공유 프라이빗 링크 리소스의 프로비전 상태는 “업데이트 중”입니다.

4. 작업이 완료되면 프라이빗 엔드포인트(DNS 영역 및 매핑과 함께)가 만들어집니다. 이 시점에서 고객이 공유 프라이빗 링크 리소스의 상태를 쿼리하면 프로비전 상태는 “성공”입니다.

![공유 프라이빗 링크 리소스 만들기의 단계 ](media\troubleshoot-shared-private-link-resources\shared-private-link-states.png)

만들기 단계 중에 발생하는 몇 가지 일반적인 오류는 다음과 같습니다.

### <a name="request-validation-failures"></a>요청 유효성 검사 실패

+ 지원되지 않는 SKU: 유료 SKU에 대해서만 공유 프라이빗 링크 리소스를 만들 수 있습니다. 무료 계층 서비스에서는 지원되지 않습니다.

+ 이름 유효성 검사: 공유 프라이빗 링크 리소스 이름은 특정 문자 집합으로만 제한됩니다. 리소스 이름에 잘못된 문자가 포함된 경우 리소스 만들기 요청은 허용되지 않습니다.
공유 프라이빗 링크 리소스의 명명 규칙은 다음과 같습니다.
  
  + 길이는 1~60자 사이여야 합니다.
  + 영숫자 문자 또는 밑줄(_), 마침표(.) 또는 하이픈(-)만 포함해야 합니다.

+ `groupId` 유효성 검사: 공유 프라이빗 링크 리소스 만들기 요청의 일부로 지정되는 `groupId`가 아래 표에 나와 있는 것과 일치 해야 합니다(철자 및 대/소문자 모두).

| Azure 리소스 | 그룹 ID | 사용 가능한 첫 번째 API 버전 |
| --- | --- | --- |
| Azure Storage - Blob (또는) ADLS Gen 2 | `blob`| `2020-08-01` |
| Azure Storage - 테이블 | `table`| `2020-08-01` |
| Azure Cosmos DB - SQL API | `Sql`| `2020-08-01` |
| Azure SQL Database | `sqlServer`| `2020-08-01` |
| Azure Database for MySQL(미리 보기) | `mysqlServer`| `2020-08-01-Preview` |
| Azure Key Vault | `vault` | `2020-08-01` |
| Azure Functions(미리 보기) | `sites` | `2020-08-01-Preview` |

“(미리 보기)”로 표시된 리소스는 미리 보기 관리 평면 API 버전에서만 사용할 수 있으며 일반적으로는 아직 사용할 수 없습니다. 기타 `groupId`(또는 이를 지원하지 않는 API 버전에서 사용되는 `groupId`)는 유효성 검사에 실패합니다.

+ `privateLinkResourceId` 형식 유효성 검사: `groupId`와 마찬가지로 Azure Cognitive Search는 “올바른” 리소스 종류가 `privateLinkResourceId`에 지정되어 있는지 확인합니다. 유효한 리소스 종류는 다음과 같습니다.

| Azure 리소스 | 리소스 유형 | 사용 가능한 첫 번째 API 버전 |
| --- | --- | --- |
| Azure Storage | `Microsoft.Storage/storageAccounts`| `2020-08-01` |
| Azure Cosmos DB | `Microsoft.DocumentDb/databaseAccounts`| `2020-08-01` |
| Azure SQL Database | `Microsoft.Sql/servers`| `2020-08-01` |
| Azure Database for MySQL(미리 보기) | `Microsoft.DBforMySQL/servers`| `2020-08-01-Preview` |
| Azure Key Vault | `Microsoft.KeyVault/vaults` | `2020-08-01` |
| Azure Functions(미리 보기) | `Microsoft.Web/sites` | `2020-08-01-Preview` |

추가로 지정된 리소스 종류에 대해 지정된 `groupId`가 유효해야 합니다. 예를 들어 `groupId` “BLOB”은 “Microsoft.Storage/storageAccounts” 종류에 유효하며, 다른 리소스 종류에는 사용할 수 없습니다. 지정된 검색 관리 API 버전의 경우 고객은 [List supported API](/rest/api/searchmanagement/2021-04-01-preview/private-link-resources/list-supported)를 활용하여 지원되는 `groupId` 및 리소스 종류 세부 정보를 확인할 수 있습니다.

+ 할당량 한도 적용: 검색 서비스에는 만들 수 있는 공유 프라이빗 링크 리소스의 고유한 수와 사용되는 다양한 대상 리소스 종류(`groupId` 기반)의 수에 대한 할당량이 적용됩니다. 이는 Azure Cognitive Search 서비스 제한 페이지의 [공유 프라이빗 링크 리소스 제한 섹션](search-limits-quotas-capacity.md#shared-private-link-resource-limits)에서 설명되어 있습니다.

### <a name="azure-resource-manager-deployment-failures"></a>Azure Resource Manager 배포 모델

검색 서비스는 공유 프라이빗 링크를 만드는 요청을 시작하지만 Azure Resource Manager가 실제 작업을 수행합니다. 포털 또는 쿼리를 통해 [배포 상태를 확인](search-indexer-howto-access-private.md#step-3-check-the-status-of-the-private-endpoint-creation)하고 발생할 수 있는 오류를 해결할 수 있습니다.

Azure Resource Manager 배포가 실패한 공유 프라이빗 링크 리소스는 [List](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/list-by-service) 및 [Get](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/get) API 호출에 표시되지만 “프로비전 상태”는 `Failed`입니다. Azure Resource Manager 배포 실패 사유가 확인되면 `Failed` 리소스를 삭제하고 다음 표에서 적절한 해결 방법을 적용한 후 리소스를 다시 만듭니다.

| 배포 실패 사유 | Description | 해결 방법 |
| --- | --- | --- |
| 네트워크 리소스 공급자가 대상 리소스의 구독에 등록되지 않았음 | `Microsoft.Network` 리소스 공급자(RP)를 통해 대상 리소스(스토리지 계정, CosmosDB, SQL 서버 등)에 대해 프라이빗 엔드포인트(및 연결된 DNS 매핑)가 생성됩니다. 대상 리소스를 호스트하는 구독(“대상 구독”)이 `Microsoft.Network` RP에 등록되지 않은 경우 Azure Resource Manager 배포가 실패할 수 있습니다. | 고객은 대상 구독에서 이 RP를 등록해야 합니다. 일반적으로 이 작업은 [이 가이드](../azure-resource-manager/management/resource-providers-and-types.md)에 설명한 것과 같이 Azure Portal, PowerShell 또는 CLI를 통해 수행할 수 있습니다. |
| 대상 리소스에 대해 `groupId`가 유효하지 않음 | CosmosDB 계정을 만들면 고객이 데이터베이스 계정에 대한 API 유형을 지정할 수 있습니다. CosmosDB는 다양한 API 유형을 제공하지만, Azure Cognitive Search는 공유 프라이빗 링크 리소스에 대한 `groupId`로 “Sql”만 지원합니다. “Sql” 공유 프라이빗 링크 리소스가 비 Sql 데이터베이스 계정을 가리키는 `privateLinkResourceId`에 대해 만들어진 경우 `groupId` 불일치로 인해 Azure Resource Manager 배포가 실패합니다. CosmosDB 계정의 Azure 리소스 ID가 사용 중인 API 유형을 확인하기에 충분하지 않습니다. Azure Cognitive Search 프라이빗 엔드포인트를 만들려고 시도하며, 이는 CosmosDB에 의해 거부됩니다. | 고객은 지정된 CosmosDB 리소스의 `privateLinkResourceId`가 “Sql” API 유형의 데이터베이스 계정에 대한 것인지 확인해야 합니다. |
| 대상 리소스를 찾을 수 없음 | `privateLinkResourceId`에 지정된 대상 리소스의 존재는 Azure Resource Manager 배포 실행 도중에만 확인됩니다. 대상 리소스를 더 이상 사용할 수 없는 경우 배포에 실패합니다. | 고객은 대상 리소스가 지정된 구독 및 리소스 그룹에 있고 이동/삭제되지 않았는지 확인해야 합니다. |
| 임시/기타 오류 | 인프라 중단이 발생하거나 예기치 않은 다른 이유로 인해 Azure Resource Manager 배포가 실패할 수 있습니다. 이는 드물게 발생하며 일반적으로 임시 상태를 나타냅니다. | 나중에 이 리소스 만들기를 다시 시도합니다. 문제가 계속되면 Azure 지원에 문의하세요. |

### <a name="resource-stuck-in-updating-or-incomplete-state"></a>리소스가 “업데이트 중” 또는 “불완전” 상태에서 변경되지 않음

일반적으로 검색 RP에서 요청을 수락한 이후 몇 분 내에 공유 프라이빗 링크 리소스가 종료 상태(`Succeeded` 또는 `Failed`)가 되어야 합니다.

드물게 Azure Cognitive Search에서 공유 프라이빗 링크 리소스의 상태를 종료 상태(`Succeeded` 또는 `Failed`)가 올바르게 표시되지 않을 수 있습니다. 이 오류는 일반적으로 검색 RP의 예기치 않은 오류 또는 치명적인 오류로 인해 발생합니다. 공유 프라이빗 링크 리소스는 종료가 아닌 상태로 8시간 넘게 지속된 경우 자동으로 `Failed` 상태로 전환됩니다.

공유 프라이빗 링크 리소스가 종료 상태로 전환되지 않은 것이 확인되면 `Failed` 상태가 될 때까지 8시간 동안 기다린 다음, 삭제하고 다시 만들 수 있습니다. 또는 기다리는 대신 다른 이름(다른 매개 변수는 동일하게 유지)으로 된 공유 프라이빗 링크 리소스를 만들 수 있습니다.

## <a name="updating-a-shared-private-link-resource"></a>공유 프라이빗 링크 리소스 업데이트하기

기존 공유 프라이빗 링크 리소스는 [Create 또는 Update API](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/create-or-update)를 사용하여 업데이트할 수 있습니다. 검색 RP는 공유 프라이빗 링크 리소스에 대한 좁은 업데이트만을 허용하므로 요청 메시지는 이 API를 통해서만 수정할 수 있습니다.

+ 기존 공유 프라이빗 링크 리소스(예: `privateLinkResourceId` 또는 `groupId`)의 “핵심” 속성을 업데이트할 수 없으며, 이는 항상 지원되지 않습니다. 요청 메시지 외에 다른 속성을 변경해야 하는 경우 공유 프라이빗 링크 리소스를 삭제하고 다시 만드는 것이 좋습니다.

+ 공유 프라이빗 링크 리소스의 요청 메시지를 업데이트하려는 시도는 프로비전 상태가 `Succeeded`인 경우에만 가능합니다.

## <a name="deleting-a-shared-private-link-resource"></a>공유 프라이빗 링크 리소스 삭제하기

고객은 [Delete API](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/delete)를 통해 기존 공유 프라이빗 링크 리소스를 삭제할 수 있습니다. 만들기(또는 업데이트) 프로세스와 마찬가지로, 이 작업은 네 단계로 이루어진 비동기 작업이기도 합니다.

1. 고객은 검색 RP에 공유 프라이빗 링크 리소스 삭제를 요청합니다.

2. 검색 RP는 리소스가 존재하고 삭제에 대해 유효한 상태인지 확인합니다. 유효한 상태인 경우 리소스를 제거할 Azure Resource Manager 삭제 작업이 시작됩니다.

3. 작업 완료에 대해 쿼리를 검색합니다(일반적으로 몇 분 정도 소요됨). 이 시점에서 공유 프라이빗 링크 리소스의 프로비전 상태는 “삭제 중”입니다.

4. 작업이 성공적으로 완료되면 지원되는 프라이빗 엔드포인트와 연결된 DNS 매핑이 제거됩니다. 리소스는 [List](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/list-by-service) 작업의 일부로 표시되지 않으며 이 리소스에 [Get](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/get) 작업을 시도하면 404 찾을 수 없음 오류가 발생합니다.

![공유 프라이빗 링크 리소스 삭제하기의 단계 ](media\troubleshoot-shared-private-link-resources\shared-private-link-delete-states.png)

삭제하기 단계 중에 발생하는 몇 가지 일반적인 오류는 다음과 같습니다.

| 실패 유형 | Description | 해결 방법 |
| --- | --- | --- |
| 리소스가 종료 상태가 아님 | 종료 상태(`Succeeded` 또는`Failed`)가 아닌 공유 프라이빗 링크 리소스는 삭제할 수 없습니다. (드물지만) 공유 프라이빗 링크 리소스가 최대 8시간 동안 종료 상태가 아닌 상태로 고정될 수 있습니다. | 리소스가 종료 상태가 될 때까지 기다린 다음, 삭제 요청을 다시 시도합니다. |
| “충돌” 오류로 인해 삭제 작업 실패 | 공유 프라이빗 링크 리소스를 삭제하는 Azure Resource Manager 작업은 `privateLinkResourceId`(“대상 RP”)에 지정된 대상 리소스의 리소스 공급자에 도달하여 프라이빗 엔드포인트와 DNS 매핑을 제거할 수 있습니다. 고객은 [Azure 리소스 잠금](../azure-resource-manager/management/lock-resources.md)을 사용하여 리소스에 대한 변경을 방지할 수 있습니다. Azure Resource Manager가 대상 RP에 도달하면 대상 RP에서 대상 리소스의 상태를 수정해야 합니다(메타데이터로부터 프라이빗 엔드포인트에 관한 세부 정보를 제거해야 함). 대상 리소스에 구성된 잠금(또는 해당 리소스 그룹/구독)이 있는 경우 Azure Resource Manager 작업이 실패하고 “충돌”(및 적절한 세부 정보)이 발생합니다. 공유 프라이빗 링크 리소스는 삭제되지 않습니다. | 고객은 삭제 작업을 다시 시도하기 전에 대상 리소스에 대한 잠금을 제거해야 합니다. **참고**: 이 문제는 고객이 “잠긴” 대상 리소스를 가리키는 공유 프라이빗 링크 리소스를 사용하여 검색 서비스를 삭제하려는 경우에도 발생할 수 있습니다. |
| 삭제 작업 실패 | 드문 경우이지만 비동기 Azure Resource Manager 삭제 작업이 실패할 수 있습니다. 이 작업이 실패하면 비동기 작업의 상태를 쿼리하면 오류 메시지와 해당 세부 정보가 고객에게 표시됩니다. | 나중에 작업을 다시 시도하거나 문제가 지속되면 Azure 지원에 문의하세요.
| 리소스가 “삭제 중” 상태에서 변경되지 않음 | 드물게 공유 프라이빗 링크 리소스가 최대 8시간 동안 “삭제 중” 상태에서 변경되지 않을 수 있으며, 이는 검색 RP에서 치명적인 오류가 발생했기 때문일 수 있습니다. | 8시간 동안 기다린 후 리소스가 `Failed` 상태로 전환되면 요청을 다시 실행합니다.|

## <a name="next-steps"></a>다음 단계

공유 프라이빗 링크 리소스 및 이를 사용하여 보호되는 콘텐츠에 대해 보안 액세스를 하는 방법을 자세히 알아보세요.

+ [인덱서를 통해 보호된 콘텐츠에 액세스](search-indexer-howto-access-private.md)
+ [REST API 참조](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources)