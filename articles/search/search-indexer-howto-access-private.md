---
title: 프라이빗 엔드포인트를 통한 인덱서 연결
titleSuffix: Azure Cognitive Search
description: 프라이빗 엔드포인트를 통해 보호되는 다른 Azure 리소스의 콘텐츠에 액세스하도록 인덱서 연결을 구성합니다.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/13/2021
ms.openlocfilehash: 79bb517faffdda7e9d7ddef45e7b52f5e81dc201
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128589685"
---
# <a name="make-indexer-connections-through-a-private-endpoint"></a>프라이빗 엔드포인트를 통한 인덱서 연결 만들기

> [!NOTE]
> [신뢰할 수 있는 Microsoft 서비스 접근 방식](../storage/common/storage-network-security.md#trusted-microsoft-services)을 사용하여 스토리지 계정에 대한 가상 네트워크 또는 IP 제한을 우회할 수 있습니다. 검색 서비스를 사용하여 스토리지 계정의 데이터에 액세스할 수도 있습니다. 이렇게 하려면 [신뢰할 수 있는 서비스 예외를 사용하여 Azure Storage에 대한 인덱서 액세스](search-indexer-howto-access-trusted-service-exception.md)를 참조하세요.
> 
> 그러나 이 접근 방식을 사용하면 보안 Microsoft 백본 네트워크와 스토리지 계정의 공용 IP 주소를 통해 Azure Cognitive Search와 스토리지 계정 간 통신이 수행됩니다.

가상 네트워크 목록에서 연결을 허용하고 공용 네트워크에서 시작되는 외부 연결을 거부하도록 Azure 스토리지 계정 등의 많은 Azure 리소스를 구성할 수 있습니다. 인덱서를 사용하여 Azure Cognitive Search에서 데이터를 인덱싱하고 데이터 원본이 프라이빗 네트워크에 있는 경우 아웃바운드 [프라이빗 엔드포인트 연결](../private-link/private-endpoint-overview.md)을 만들어 데이터에 연결할 수 있습니다.

이 인덱서 연결 방법에는 다음 두 가지 요구 사항이 적용됩니다.

+ 콘텐츠 또는 코드를 제공하는 Azure 리소스는 [Azure Private Link 서비스](https://azure.microsoft.com/services/private-link/)에 등록되어 있어야 합니다.

+ Azure Cognitive Search 서비스는 기본 계층 이상에 있어야 합니다. 무료 계층에서는 이 기능을 사용할 수 없습니다. 또한 인덱서에 기술 세트가 있는 경우 계층은 표준 2(S2) 이상이어야 합니다. 자세한 내용은 [서비스 제한](search-limits-quotas-capacity.md#shared-private-link-resource-limits)을 참조하세요.

## <a name="shared-private-link-resources-management-apis"></a>공유 프라이빗 링크 리소스 관리 API

Azure Cognitive Search API를 통해 생성된 보안 리소스의 프라이빗 엔드포인트를 *공유 프라이빗 링크 리소스* 라고 합니다. 이는 [Azure Private Link 서비스](https://azure.microsoft.com/services/private-link/)와 통합된 스토리지 계정과 같은 리소스에 대한 액세스 권한을 "공유"하기 때문입니다.

Azure Cognitive Search는 관리 REST API를 통해 Azure Cognitive Search 인덱서에서 액세스를 구성하는 데 사용할 수 있는 [CreateOrUpdate](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/create-or-update) 작업을 제공합니다.

다음 표에 *미리 보기* 로 지정된 Search Management API의 미리 보기 버전(버전 *2020-08-01-preview* 이상)을 사용해야 일부 리소스에 대한 프라이빗 엔드포인트 연결을 만들 수 있습니다. *미리 보기* 가 지정되지 않은 리소스는 미리 보기 또는 일반적으로 사용 가능한 API 버전(*2020-08-01* 이상)을 사용하여 만들 수 있습니다.

다음 표에는 Azure Cognitive Search에서 아웃바운드 프라이빗 엔드포인트를 만들 수 있는 Azure 리소스가 나열됩니다. 공유 프라이빗 링크 리소스를 만들려면 API에 작성된 대로 정확하게 **그룹 ID** 값을 입력합니다. 이 값은 대/소문자를 구분합니다.

| Azure 리소스 | 그룹 ID |
| --- | --- |
| Azure Storage - Blob | `blob`|
| Azure Storage - Data Lake Storage Gen2 | `dfs` 및 `blob` |
| Azure Storage - 테이블 | `table`|
| Azure Cosmos DB - SQL API | `Sql`|
| Azure SQL Database | `sqlServer`|
| Azure Database for MySQL(미리 보기) | `mysqlServer`|
| Azure Key Vault | `vault` |
| Azure Functions(미리 보기) | `sites` |

[지원되는 API 목록](/rest/api/searchmanagement/2021-04-01-preview/private-link-resources/list-supported)을 사용하여 아웃바운드 프라이빗 엔드포인트 연결이 지원되는 Azure 리소스를 쿼리할 수도 있습니다.

이 문서의 나머지 부분에서는 REST API 호출을 설명하기 위해 Azure Portal(또는 원하는 경우 [Azure CLI](/cli/azure/)) 및 [Postman](https://www.postman.com/)(또는 원하는 경우 [curl](https://curl.se/) 등의 다른 HTTP 클라이언트)을 혼합하여 사용합니다.

> [!NOTE]
> Azure Data Lake Storage Gen2에 대한 프라이빗 엔드포인트 연결을 만들려면 두 개의 프라이빗 엔드포인트를 만들어야 합니다. groupID 'dfs'가 있는 프라이빗 엔드포인트 하나와 groupID 'blob'이 있는 다른 프라이빗 엔드포인트.

## <a name="set-up-indexer-connection-through-private-endpoint"></a>프라이빗 엔드포인트를 통해 인덱서 연결 설정

프라이빗 엔드포인트를 통해 보안 Azure 리소스에 대한 인덱서 연결을 설정하려면 다음 지침을 사용합니다.

이 문서의 예는 다음과 같은 가정을 기반으로 합니다.
* 검색 서비스의 이름은 _contoso-search_ 이며 구독 ID가 _00000000-0000-0000-0000-000000000000_ 인 구독의 _contoso_ 리소스 그룹에 있습니다. 
* 이 검색 서비스의 리소스 ID는 _/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search_ 입니다.

### <a name="step-1-secure-your-azure-resource"></a>1단계: Azure 리소스 보호

액세스를 제한하는 단계는 리소스에 따라 다릅니다. 다음 시나리오에서는 일반적인 세 가지 리소스 종류를 보여줍니다.

- 시나리오 1: 데이터 원본

    다음은 Azure Storage 계정을 구성하는 방법의 예입니다. 이 옵션을 선택하고 페이지를 비워 두면 가상 네트워크의 트래픽이 허용되지 않습니다.

    ![선택한 네트워크에 대한 액세스를 허용하는 옵션을 보여주는 Azure Storage에 대한 "방화벽 및 가상 네트워크" 창의 스크린샷](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png)

- 시나리오 2: Azure Key Vault

    다음은 Azure Key Vault를 구성하는 방법의 예입니다.
 
    ![선택한 네트워크에 대한 액세스를 허용하는 옵션을 보여주는 Azure Key Vault에 대한 "방화벽 및 가상 네트워크" 창의 스크린샷](media\search-indexer-howto-secure-access\key-vault-firewall-noaccess.png)
    
- 시나리오 3: Azure Functions

    Azure Functions에 대한 네트워크 설정 변경은 필요하지 않습니다. 다음 단계의 뒷부분에서 공유 프라이빗 엔드포인트를 만들면 함수에 대한 공유 프라이빗 엔드포인트를 만든 후 개인 링크를 통한 액세스만 함수가 자동으로 허용합니다.

### <a name="step-2-create-a-shared-private-link-resource-to-the-azure-resource"></a>2단계: Azure 리소스에 대한 공유 개인 링크 리소스 만들기

다음 섹션에서는 Azure Portal 또는 Azure CLI를 사용하여 공유 개인 링크 리소스를 만드는 방법을 설명합니다.

#### <a name="option-1-portal"></a>옵션 1: 포털

> [!NOTE]
> 포털에서는 GA인 그룹 ID 값을 사용하여 공유 프라이잇 엔드포인트를 만드는 작업만 지원합니다. MySQL 및 Azure Functions의 경우 다음과 같이 옵션 2에 설명된 Azure CLI 단계를 사용합니다.

아웃바운드 프라이빗 엔드포인트 연결을 만들기 위한 Azure Cognitive Search를 요청하려면 공유 프라이빗 액세스 블레이드를 통해 “공유 프라이빗 액세스 추가”를 클릭합니다. 오른쪽에 열리는 블레이드에서 “내 디렉터리에서 Azure 리소스에 연결” 또는 “리소스 ID 또는 별칭으로 Azure 리소스에 연결”을 선택할 수 있습니다.

첫 번째 옵션(권장)을 사용하는 경우 블레이드는 적절한 Azure 리소스를 선택하는 데 도움이 되며, 리소스의 그룹 ID 및 리소스 종류와 같은 다른 속성을 자동으로 채웁니다.

   ![공유 프라이빗 링크 리소스를 만들기 위한 안내 환경을 보여 주는 “공유 프라이빗 액세스 추가” 창의 스크린샷. ](media\search-indexer-howto-secure-access\new-shared-private-link-resource.png)

두 번째 옵션을 사용하는 경우 Azure 리소스 ID를 수동으로 입력하고 적절한 그룹 ID를 선택할 수 있습니다. 그룹 ID는 이 문서의 시작 부분에 나와 있습니다.

![공유 프라이빗 링크 리소스를 만들기 위한 수동 환경을 보여 주는 “공유 프라이빗 액세스 추가” 창의 스크린샷. ](media\search-indexer-howto-secure-access\new-shared-private-link-resource-manual.png)

#### <a name="option-2-azure-cli"></a>옵션 2: Azure CLI

또는 [Azure CLI](/cli/azure/)를 통해 다음 API 호출을 수행할 수 있습니다. 미리 보기에 있는 그룹 ID를 사용하는 경우 2020-08-01-preview API 버전을 사용합니다. 예를 들어 그룹 ID *사이트* 및 *mysqlServer* 는 미리 보기 상태이며 미리 보기 API를 사용해야 합니다.

```dotnetcli
az rest --method put --uri https://management.azure.com/subscriptions/<search service subscription ID>/resourceGroups/<search service resource group name>/providers/Microsoft.Search/searchServices/<search service name>/sharedPrivateLinkResources/<shared private endpoint name>?api-version=2020-08-01 --body @create-pe.json
```

다음은 *create-pe.json* 파일의 콘텐츠 예입니다.

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve"
      }
}
```

성공 시 `202 Accepted` 응답이 반환됩니다. 아웃바운드 프라이빗 엔드포인트를 만드는 프로세스는 장기 실행(비동기) 작업입니다. 여기에는 다음 리소스 배포가 포함됩니다.

+ `"Pending"` 상태의 개인 IP 주소로 할당된 프라이빗 엔드포인트입니다. 개인 IP 주소는 검색 서비스별 프라이빗 인덱서에 대한 실행 환경의 가상 네트워크에 할당된 주소 공간에서 가져옵니다. 프라이빗 엔드포인트 승인 시 Azure Cognitive Search에서 Azure 리소스로의 통신은 개인 IP 주소와 보안 프라이빗 링크 채널에서 시작됩니다.

+ `groupId`를 기반으로 하는 리소스 유형에 대한 프라이빗 DNS 영역입니다. 이 리소스를 배포하여 프라이빗 리소스에 대한 DNS 조회가 프라이빗 엔드포인트와 연결된 IP 주소를 활용하는지 확인합니다.

프라이빗 엔드포인트를 만들 리소스의 유형에 대해 올바른 `groupId`를 지정해야 합니다. 불일치가 발생하면 성공하지 못한 응답 메시지가 반환됩니다.

### <a name="step-3-check-the-status-of-the-private-endpoint-creation"></a>3단계: 프라이빗 엔드포인트 만들기 상태 확인

이 단계에서는 리소스의 프로비전 상태가 "성공"으로 변경되었는지 확인합니다.

#### <a name="option-1-portal"></a>옵션 1: 포털

> [!NOTE]
> 프로비전 상태는 미리 보기로 제공되는 GA 및 그룹 ID 모두에 대해 포털에 표시됩니다.

포털에 공유 프라이빗 엔드포인트의 상태가 표시됩니다. 다음 예에서 상태는 "업데이트 중"입니다.

![진행 중인 리소스 생성을 보여 주는 “공유 프라이빗 액세스 추가” 창의 스크린샷. ](media\search-indexer-howto-secure-access\new-shared-private-link-resource-progress.png)

리소스가 성공적으로 만들어지면 포털 알림을 받게 되며 리소스의 프로비저닝 상태가 “Succeeded”로 변경됩니다.

![리소스 생성이 완료되었음을 보여 주는 “공유 프라이빗 액세스 추가” 창의 스크린샷. ](media\search-indexer-howto-secure-access\new-shared-private-link-resource-success.png)

#### <a name="option-2-azure-cli"></a>옵션 2: Azure CLI

공유 프라이빗 엔드포인트를 만들기 위한 `PUT` 호출은 다음과 같은 `Azure-AsyncOperation` 헤더 값을 반환합니다.

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

`Azure-AsyncOperationHeader` 값을 수동으로 쿼리하면 상태를 폴링할 수 있습니다.

```dotnetcli
az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01
```

### <a name="step-4-approve-the-private-endpoint-connection"></a>4단계: 프라이빗 엔드포인트 연결 승인

> [!NOTE]
> 이 섹션에서는 Azure Portal을 사용하여 연결할 Azure 리소스에 대한 프라이빗 엔드포인트의 승인 흐름을 안내합니다. 또는 스토리지 리소스 공급자를 통해 사용할 수 있는 [REST API](/rest/api/storagerp/privateendpointconnections)를 사용할 수 있습니다.
>
> Azure Cosmos DB 또는 Azure SQL Server 등의 다른 공급자는 프라이빗 엔드포인트 연결 관리를 위한 유사한 스토리지 리소스 공급자 API를 제공합니다.

1. Azure Portal에서 연결 중인 Azure 리소스로 이동하여 **네트워킹** 탭을 선택합니다. 그런 다음, 프라이빗 엔드포인트 연결을 나열하는 섹션으로 이동합니다. 다음은 스토리지 계정에 대한 예입니다. 비동기 작업이 성공한 후에는 이전 API 호출의 요청 메시지와 함께 프라이빗 엔드포인트 연결에 대한 요청이 있어야 합니다.

   !["프라이빗 엔드포인트 연결" 창이 표시된 Azure Portal의 스크린샷입니다.](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png)

1. Azure Cognitive Search에서 만든 프라이빗 엔드포인트를 선택합니다. **프라이빗 엔드포인트** 열에서 이전 API에 지정된 이름으로 프라이빗 엔드포인트 연결을 식별하고 **승인** 을 선택한 다음, 적절한 메시지를 입력합니다. 메시지 내용은 중요하지 않습니다.

   다음 스크린샷과 같이 프라이빗 엔드포인트 연결이 표시되는지 확인합니다. 포털에서 상태를 업데이트하는 데 1~2분 정도 걸릴 수 있습니다.

   !["프라이빗 엔드포인트 연결" 창에 “승인됨” 상태가 표시된 Azure Portal의 스크린샷입니다.](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png)

프라이빗 엔드포인트 연결 요청이 승인되면 트래픽이 프라이빗 엔드포인트를 통과 *할 수 있습니다*. 프라이빗 엔드포인트가 승인되면 Azure Cognitive Search는 생성된 DNS 영역에 필요한 DNS 영역 매핑을 만듭니다.

### <a name="step-5-query-the-status-of-the-shared-private-link-resource"></a>5단계: 공유 프라이빗 링크 리소스의 상태 쿼리

승인 후 공유 프라이빗 링크 리소스가 업데이트되었는지 확인하려면 Azure Portal에서 검색 서비스의 “공유 프라이빗 액세스” 블레이드를 다시 방문하고 “연결 상태”를 확인합니다.

   ![“Approved” 공유 프라이빗 링크 리소스를 보여 주는 Azure Portal 스크린샷.](media\search-indexer-howto-secure-access\new-shared-private-link-resource-approved.png)

또는 [GET API](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/get)를 사용하여 “연결 상태”를 가져올 수도 있습니다.

```dotnetcli
az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01
```

그러면 연결 상태가 “속성” 섹션 아래에 “상태”로 표시되는 JSON이 반환됩니다. 다음은 스토리지 계정에 대한 예입니다.

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve",
        "status": "Approved",
        "resourceRegion": null,
        "provisioningState": "Succeeded"
      }
}

```

리소스의 “프로비저닝 상태”(`properties.provisioningState`)가 `Succeeded`이고 “연결 상태”(`properties.status`)가 `Approved`이면 공유 프라이빗 링크 리소스가 작동하고 프라이빗 엔드포인트를 통해 통신하도록 인덱서를 구성할 수 있음을 의미합니다.

### <a name="step-6-configure-the-indexer-to-run-in-the-private-environment"></a>6단계: 프라이빗 환경에서 실행되도록 인덱서 구성

> [!NOTE]
> 프라이빗 엔드포인트 연결이 승인되기 전에 이 단계를 수행할 수 있습니다. 프라이빗 엔드포인트 연결이 승인될 때까지는 스토리지 계정과 같은 보안 리소스와 통신하려고 하는 모든 인덱서가 일시적 실패 상태가 됩니다. 새 인덱서가 생성되지 않습니다. 프라이빗 엔드포인트 연결이 승인되는 즉시 인덱서는 프라이빗 스토리지 계정에 액세스할 수 있습니다.

다음 단계에서는 REST API를 사용하여 프라이빗 환경에서 실행되도록 인덱서를 구성하는 방법을 보여줍니다. 포털에서 JSON 편집기를 사용하여 실행 환경을 설정할 수도 있습니다.

1. 평소와 같이 데이터 원본 정의, 인덱스 및 기술 세트(사용하는 경우)를 만듭니다. 공유 프라이빗 엔드포인트를 사용하는 경우에 따라 달라지는 이러한 정의에는 속성이 없습니다.

1. 이전 단계에서 만든 데이터 원본, 인덱스 및 기술 세트를 가리키는 [인덱서를 만듭니다](/rest/api/searchservice/create-indexer). 또한 인덱서 `executionEnvironment` 구성 속성을 `private`으로 설정하여 인덱서가 프라이빗 실행 환경에서 실행되도록 합니다.

    ```json
    {
        "name": "indexer",
        "dataSourceName": "blob-datasource",
        "targetIndexName": "index",
        "parameters": {
            "configuration": {
                "executionEnvironment": "private"
            }
        },
        "fieldMappings": []
    }
    ```

    다음은 Postman의 요청 예입니다.
    
    ![Postman 사용자 인터페이스에서 인덱서 생성을 보여주는 스크린샷입니다.](media\search-indexer-howto-secure-access\create-indexer.png)    

인덱서가 성공적으로 만들어지면 프라이빗 엔드포인트 연결을 통해 Azure 리소스에 연결해야 합니다. [인덱서 상태 API](/rest/api/searchservice/get-indexer-status)를 사용하여 인덱서의 상태를 모니터링할 수 있습니다.

> [!NOTE]
> 기존 인덱서가 이미 있는 경우 `executionEnvironment`를 `private`으로 설정하거나 포털에서 JSON 편집기를 사용하여 [PUT API](/rest/api/searchservice/create-indexer)를 통해 업데이트할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

+ "데이터 원본 자격 증명이 잘못되었습니다."와 같은 오류 메시지와 함께 인덱서 작성에 실패하면 프라이빗 엔드포인트 연결의 상태가 아직 *승인됨* 이 아니거나 연결이 작동하지 않는 것입니다. 문제를 해결하려면 다음을 수행합니다. 
  + [GET API](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/get)를 사용하여 공유 프라이빗 링크 리소스의 상태를 가져옵니다. 상태가 *승인됨* 인 경우 리소스의 `properties.provisioningState`를 확인합니다. 여기에서 상태가 `Incomplete`이면 리소스에 대한 기본 종속성 중 일부를 설정하지 못한 것입니다. 공유 프라이빗 링크 리소스를 다시 생성하기 위한 `PUT` 요청을 다시 실행하려면 문제를 해결해야 합니다. 재승인이 필요할 수 있습니다. 리소스 상태를 다시 점검하여 문제가 해결되었는지 확인합니다.

+ `executionEnvironment` 속성을 설정하지 않고 인덱서를 생성하는 경우 생성에 성공할 수 있지만 실행 기록에 인덱서 실행이 실패한 것으로 표시됩니다. 문제를 해결하려면 다음을 수행합니다.
  + [인덱서를 업데이트](/rest/api/searchservice/update-indexer)하여 실행 환경을 지정합니다.

+ `executionEnvironment` 속성을 설정하지 않고 인덱서를 생성한 경우 성공적으로 실행되면 Azure Cognitive Search에서 해당 실행 환경이 검색 서비스별 *프라이빗* 환경이라고 결정한 것입니다. 이는 인덱서에서 사용하는 리소스, 검색 서비스의 로드 및 기타 요인에 따라 바꿀 수 있으며 나중에 실패할 수 있습니다. 문제를 해결하려면 다음을 수행합니다.
  + 나중에 실패하지 않도록 `executionEnvironment` 속성을 `private`으로 설정하는 것이 좋습니다.

+ Azure Portal의 데이터 원본의 네트워킹 페이지를 보고 Azure Cognitive Search 서비스에서 이 데이터 원본에 액세스하기 위해 만든 프라이빗 엔드포인트를 선택하면 *액세스 권한 없음* 오류가 발생할 수 있습니다. 예상된 동작입니다. 대상 서비스의 포털 페이지를 통해 연결 요청의 상태를 변경할 수 있지만 공유 프라이빗 링크 리소스를 추가로 관리하려면 Azure Portal에서 검색 서비스의 네트워크 페이지에서 공유 프라이빗 링크 리소스를 확인해야 합니다.

[할당량 및 한도](search-limits-quotas-capacity.md)는 생성할 수 있는 공유 프라이빗 링크 리소스 수를 결정하며 검색 서비스의 SKU에 따라 달라집니다.

## <a name="next-steps"></a>다음 단계

프라이빗 엔드포인트에 대해 자세히 알아보세요.

+ [공유 프라이빗 링크 리소스와 관련된 문제 해결](troubleshoot-shared-private-link-resources.md)
+ [프라이빗 엔드포인트란 무엇인가요?](../private-link/private-endpoint-overview.md)
+ [프라이빗 엔드포인트에 필요한 DNS 구성](../private-link/private-endpoint-dns.md)