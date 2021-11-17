---
title: 인덱서 문제 해결 지침
titleSuffix: Azure Cognitive Search
description: 이 문서에서는 서비스 검색에서 오류 메시지가 반환되지 않는 경우에 대한 인덱서 문제 및 해결 지침을 제공합니다.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2021
ms.openlocfilehash: d54aec5bae8fb86b9c0ed0356cd6af713500527f
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132492060"
---
# <a name="indexer-troubleshooting-guidance-for-azure-cognitive-search"></a>Azure Cognitive Search에 대한 인덱서 문제 해결 지침

간혹 인덱서에 문제가 발생해도 진단에 도움이 되는 오류가 없습니다. 이 문서에서는 인덱서 결과가 예기치 않고 정보가 제한되어 있을 때 발생할 수 있는 문제와 잠재적인 해결 방법을 다룹니다. 조사할 오류가 있는 경우 [일반적인 인덱서 오류 및 경고 문제 해결](cognitive-search-common-errors-warnings.md)을 참조하세요.

## <a name="connection-errors"></a>연결 오류

> [!NOTE]
> 인덱서는 Azure 네트워크 보안 메커니즘에 의해 보호되는 데이터 원본 및 기타 리소스에 액세스하는 데 제한된 지원을 제공합니다. 현재 인덱서는 해당하는 IP 주소 범위 제한 메커니즘 또는 NSG 규칙을 통해서만 데이터 원본에 액세스할 수 있습니다(해당하는 경우). 지원되는 각 데이터 원본에 액세스하는 방법에 대한 자세한 내용은 아래를 참조하세요.
>
> 정규화된 도메인 이름(예: `<your-search-service-name>.search.windows.net`)을 ping하여 검색 서비스의 IP 주소를 확인할 수 있습니다.
>
> [다운로드 가능한 JSON 파일](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)을 사용하거나 [서비스 태그 검색 API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api)를 통해 `AzureCognitiveSearch` [서비스 태그](../virtual-network/service-tags-overview.md#available-service-tags)의 IP 주소 범위를 확인할 수 있습니다. IP 주소 범위는 매주 업데이트됩니다.

### <a name="firewall-rules"></a>방화벽 규칙

Azure Storage, Cosmos DB 및 Azure SQL은 구성 가능한 방화벽을 제공합니다. 방화벽을 사용하는 경우 특정 오류 메시지가 발생하지 않습니다. 일반적으로 방화벽 오류는 일반적이며 `The remote server returned an error: (403) Forbidden` 또는 `Credentials provided in the connection string are invalid or have expired`와 같습니다.

이러한 인스턴스에서 인덱서가 이러한 리소스에 액세스할 수 있도록 허용하는 두 가지 옵션은 다음과 같습니다.

* **모든 네트워크** 에서 액세스할 수 있도록 허용하여 방화벽을 사용하지 않도록 설정합니다(가능한 경우).

* 또는 리소스의 방화벽 규칙(IP 주소 범위 제한)에서 검색 서비스의 IP 주소와 `AzureCognitiveSearch` [서비스 태그](../virtual-network/service-tags-overview.md#available-service-tags)의 IP 주소 범위에 대한 액세스를 허용할 수 있습니다.

각 데이터 원본 유형에 대한 IP 주소 범위 제한 구성에 대한 자세한 내용은 다음 링크에서 확인할 수 있습니다.

* [Azure Storage](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Cosmos DB](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Azure SQL](../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules)

**제한**: Azure Storage에 대해 위의 설명서에서 설명한 것처럼 IP 주소 범위 제한은 검색 서비스와 스토리지 계정이 서로 다른 지역에 있는 경우에만 적용됩니다.

[사용자 지정 Web API 기술](cognitive-search-custom-skill-web-api.md)로 사용할 수 있는 Azure Functions는 [IP 주소 제한](../azure-functions/ip-addresses.md#ip-address-restrictions)도 지원합니다. 구성할 IP 주소 목록은 검색 서비스의 IP 주소 및 `AzureCognitiveSearch` 서비스 태그의 IP 주소 범위입니다.

가상 머신 연결에 대한 자세한 내용은 [Azure VM에서 SQL Server 연결 구성](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)을 참조하세요.

### <a name="configure-network-security-group-nsg-rules"></a>NSG(네트워크 보안 그룹) 규칙 구성

SQL Managed Instance에서 데이터에 액세스하거나 Azure VM이 [사용자 지정 웹 API 기술](cognitive-search-custom-skill-web-api.md)의 웹 서비스 URI로 사용되는 경우 고객은 특정 IP 주소에 대해 염려할 필요가 없습니다.

이 경우 Azure VM 또는 SQL Managed Instance를 가상 네트워크 내에 포함하도록 구성할 수 있습니다. 그런 다음 가상 네트워크 서브넷 및 네트워크 인터페이스로 들어오고 나갈 수 있는 네트워크 트래픽 유형을 필터링하도록 네트워크 보안 그룹을 구성할 수 있습니다.

`AzureCognitiveSearch` 서비스 태그는 IP 주소 범위를 조회할 필요 없이 인바운드 [NSG 규칙](../virtual-network/manage-network-security-group.md#work-with-security-rules)에서 직접 사용할 수 있습니다.

SQL Managed Instance의 데이터에 액세스하는 방법에 대한 자세한 내용은 [여기](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)를 참조하세요.

## <a name="azure-sql-database-serverless-indexing-error-code-40613"></a>서버를 사용 하지 않는 인덱싱 Azure SQL Database (오류 코드 40613)

SQL 데이터베이스가 서버를 사용 하지 않는 [계산 계층](../azure-sql/database/serverless-tier-overview.md)에 있으면 인덱서가 연결 될 때 데이터베이스가 실행 중이 고 일시 중지 되지 않았는지 확인 합니다.

데이터베이스가 일시 중지 된 경우 검색 서비스의 첫 번째 로그인에서 데이터베이스를 자동으로 다시 시작 하지만 오류 코드 40613로 데이터베이스를 사용할 수 없다는 오류도 반환 합니다. 데이터베이스를 실행 한 후에는 로그인을 다시 시도 하 여 연결을 설정 합니다.

## <a name="sharepoint-online-conditional-access-policies"></a>SharePoint Online 조건부 액세스 정책

SharePoint Online 인덱서를 만들 때 디바이스 코드를 제공한 후 Azure AD 앱에 로그인해야 하는 단계를 진행하게 됩니다. `"Your sign-in was successful but your admin requires the device requesting access to be managed"` 메시지가 표시되면 [조건부 액세스](../active-directory/conditional-access/overview.md) 정책으로 인해 인덱서가 SharePoint Online 문서 라이브러리에 액세스하지 못하도록 차단되었을 수 있습니다.

인덱서에서 문서 라이브러리에 액세스할 수 있도록 정책을 업데이트하려면 다음 단계를 수행합니다.

1. Azure Portal을 열고, **Azure AD 조건부 액세스를** 를 검색한 다음, 왼쪽 메뉴에서 **정책** 을 선택합니다. 이 페이지를 볼 수 있는 액세스 권한이 없는 경우 액세스 권한이 있는 사용자를 찾거나 액세스 권한을 얻어야 합니다.

1. SharePoint Online 인덱서에서 문서 라이브러리에 액세스하지 못하도록 차단하는 정책을 확인합니다. 인덱서를 차단할 수 있는 정책에는 **사용자 및 그룹** 섹션의 인덱서 만들기 단계에서 인증하는 데 사용한 사용자 계정이 포함됩니다. 정책에는 다음과 같은 **조건** 도 있을 수 있습니다.
    * **Windows** 플랫폼을 제한합니다.
    * **모바일 앱 및 데스크톱 클라이언트** 를 제한합니다.
    * **디바이스 상태** 를 **예** 로 구성합니다.

1. 인덱서를 차단하는 정책이 있다고 확인되면 인덱서에 대한 제외를 만들어야 합니다. 검색 서비스 IP 주소를 검색합니다.

    1. 검색 서비스의 FQDN(정규화된 도메인 이름)을 가져옵니다. 이는 `<search-service-name>.search.windows.net`과 같습니다. FQDN은 Azure Portal에서 검색 서비스를 조회하여 확인할 수 있습니다.

   ![서비스 FQDN 가져오기](media\search-indexer-howto-secure-access\search-service-portal.png "서비스 FQDN 가져오기")

    검색 서비스의 IP 주소는 FQDN에 대해 `nslookup`(또는 `ping`)을 수행하여 가져올 수 있습니다. 아래 예제에서는 "150.0.0.1"을 Azure Storage 방화벽의 인바운드 규칙에 추가합니다. 검색 서비스 인덱서에서 Azure Storage 계정에 액세스할 수 있도록 방화벽 설정이 업데이트될 때까지 최대 15분이 걸릴 수 있습니다.

    ```azurepowershell

    nslookup contoso.search.windows.net
    Server:  server.example.org
    Address:  10.50.10.50
    
    Non-authoritative answer:
    Name:    <name>
    Address:  150.0.0.1
    Aliases:  contoso.search.windows.net
    ```

1. 해당 지역의 인덱서 실행 환경에 대한 IP 주소 범위를 가져옵니다.

    추가 IP 주소는 인덱서의 [다중 테넌트 실행 환경](search-indexer-securing-resources.md#indexer-execution-environment)에서 시작하는 요청에 사용됩니다. 이 IP 주소 범위는 서비스 태그에서 가져올 수 있습니다.

    서비스 태그의 IP 주소 범위는 `AzureCognitiveSearch` [검색 API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api) 또는 [다운로드 가능한 JSON 파일](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)을 통해 가져올 수 있습니다.

    이 연습에서는 검색 서비스가 Azure 퍼블릭 클라우드라고 가정하여 [Azure 퍼블릭 JSON 파일](https://www.microsoft.com/download/details.aspx?id=56519)을 다운로드해야 합니다.

   ![JSON 파일 다운로드](media\search-indexer-troubleshooting\service-tag.png "JSON 파일 다운로드")

    검색 서비스가 미국 중서부에 있다고 가정하면 JSON 파일에서 다중 테넌트 인덱서 실행 환경에 대한 IP 주소 목록은 아래와 같습니다.

    ```json
        {
          "name": "AzureCognitiveSearch.WestCentralUS",
          "id": "AzureCognitiveSearch.WestCentralUS",
          "properties": {
            "changeNumber": 1,
            "region": "westcentralus",
            "platform": "Azure",
            "systemService": "AzureCognitiveSearch",
            "addressPrefixes": [
              "52.150.139.0/26",
              "52.253.133.74/32"
            ]
          }
        }
    ```

1. Azure Portal의 조건부 액세스 페이지로 돌아가서 왼쪽 메뉴에서 **명명된 위치** 를 선택한 다음, **+ IP 범위 위치** 를 선택합니다. 새로 명명된 위치의 이름을 지정하고, 마지막 두 단계에서 수집한 검색 서비스 및 인덱서 실행 환경에 대한 IP 범위를 추가합니다.
    * 검색 서비스 IP 주소의 경우 유효한 IP 범위만 허용하므로 "/32"를 IP 주소 끝에 추가해야 할 수 있습니다.
    * 인덱서 실행 환경 IP 범위의 경우 검색 서비스가 있는 지역의 IP 범위만 추가하면 됩니다.

1. 정책에서 새 명명된 위치를 제외합니다. 
    1. 왼쪽 메뉴에서 **정책** 을 선택합니다. 
    1. 인덱서를 차단하는 정책을 선택합니다.
    1. **조건** 을 선택합니다.
    1. **위치** 를 선택합니다.
    1. **제외** 를 선택한 다음, 새 명명된 위치를 추가합니다.
    1. 변경 내용을 **저장** 합니다.

1. 정책에서 새 정책 규칙을 업데이트하고 적용할 때까지 몇 분 정도 기다립니다.

1. 인덱서 만들기를 다시 시도합니다.
    1. 만든 데이터 원본 개체에 대한 업데이트 요청을 보냅니다.
    1. 인덱서 만들기 요청을 다시 보냅니다. 새 코드를 사용하여 로그인한 다음, 다른 인덱서 만들기 요청을 보냅니다.

## <a name="indexing-unsupported-document-types"></a>지원되지 않는 문서 형식 인덱싱

Azure Blob Storage의 콘텐츠를 인덱싱하고 컨테이너에 [지원되지 않는 콘텐츠 형식](search-howto-indexing-azure-blob-storage.md#SupportedFormats)의 Blob이 포함된 경우 인덱서가 해당 문서를 건너뜁니다. 다른 경우에는 개별 문서에 문제가 있을 수 있습니다. 

개별 문서에 문제가 있는 경우 인덱서 처리를 계속할 수 있도록 [구성 옵션을 설정](search-howto-indexing-azure-blob-storage.md#DealingWithErrors)할 수 있습니다.

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

## <a name="missing-documents"></a>누락된 문서

인덱서가 외부 [데이터 원본](/rest/api/searchservice/create-data-source)에서 문서 또는 행을 추출하고 검색 서비스를 통해 인덱싱되는 *검색 문서* 를 만듭니다. 경우에 따라 데이터 원본에 있는 문서가 검색 인덱스에 나타나지 않습니다. 이 예기치 않은 결과는 다음과 같은 이유로 인해 발생할 수 있습니다.

* 문서는 인덱서 실행 후에 업데이트되었습니다. 인덱서가 [일정](/rest/api/searchservice/create-indexer#indexer-schedule)대로 진행되는 경우 결국 문서를 다시 실행하고 선택합니다.
* 문서를 수집하기 전에 인덱서의 시간이 초과되었습니다. 문서가 처리되지 않는 [최대 처리 시간 제한](search-limits-quotas-capacity.md#indexer-limits)이 있습니다. 포털에서 또는 [인덱서 상태 가져오기(REST API)](/rest/api/searchservice/get-indexer-status)를 호출하여 인덱서 상태를 확인할 수 있습니다.
* [필드 매핑](/rest/api/searchservice/create-indexer#fieldmappings) 또는 [AI 보강](./cognitive-search-concept-intro.md)으로 문서가 변경되었으며 검색 인덱스의 설명이 예상과 다릅니다.
* [변경 추적](/rest/api/searchservice/create-data-source#data-change-detection-policies) 값이 잘못되거나 필수 구성 조건이 누락되었습니다. 높은 워터마크 값이 이후 시간으로 설정된 날짜인 경우, 이 값보다 앞선 날짜를 가진 문서는 인덱서에서 건너뜁니다. [인덱서 상태](/rest/api/searchservice/get-indexer-status#indexer-execution-result)에서 'initialTrackingState' 및 'finalTrackingState' 필드를 사용하여 인덱서의 변경 내용 추적 상태를 이해할 수 있습니다. Azure SQL 및 MySQL용 인덱서에는 원본 테이블의 높은 워터 마크 열에 인덱스가 있어야 합니다. 그렇지 않은 경우 인덱서에서 사용하는 쿼리의 시간이 부족할 수 있습니다. 

> [!TIP]
> 문서가 누락된 경우 사용 중인 [쿼리](/rest/api/searchservice/search-documents)를 확인하여 해당 문서를 제외하지 않는지 확인합니다. 특정 문서를 쿼리하려면 [문서 조회 REST API](/rest/api/searchservice/lookup-document)를 사용합니다.

## <a name="missing-content-from-blob-storage"></a>Blob Storage에서 누락된 콘텐츠

Blob 인덱서는 [컨테이너의 blob에서 텍스트를 찾아서 추출합니다](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). 텍스트 추출에 대한 몇 가지 문제는 다음과 같습니다.

* 문서에는 스캔된 이미지만 포함됩니다. 스캔된 이미지(JPG)와 같은 텍스트가 아닌 콘텐츠를 포함한 PDF Blob은 표준 Blob 인덱싱 파이프라인에서 결과를 생성하지 않습니다. 텍스트 요소가 포함된 이미지 콘텐츠가 있는 경우 [Cognitive Search](cognitive-search-concept-image-scenarios.md)를 사용하여 텍스트를 찾고 추출할 수 있습니다.

* Blob 인덱서는 메타데이터를 인덱스하도록 구성됩니다. 콘텐츠를 추출하려면 [콘텐츠 및 메타데이터를 모두 추출](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex)하도록 Blob 인덱서를 구성해야 합니다.

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="missing-content-from-cosmos-db"></a>Cosmos DB에서 누락된 콘텐츠

Azure Cognitive Search는 Cosmos DB 인덱싱에 대해 암시적으로 의존합니다. Cosmos DB에서 자동 인덱싱을 해제하면 Azure Cognitive Search는 성공적인 상태를 반환하지만 컨테이너 콘텐츠를 인덱싱하지 못합니다. 설정을 확인하고 인덱싱을 설정하는 방법에 대한 지침은 [Azure Cosmos DB에서 인덱싱 관리](../cosmos-db/how-to-manage-indexing-policy.md#use-the-azure-portal)를 참조하세요.

## <a name="documents-processed-multiple-times"></a>여러 번 처리된 문서

인덱서에서는 데이터를 인덱싱하는 동안 데이터 원본의 모든 새 문서와 변경된 문서가 선택되도록 하는 버퍼링 전략을 활용합니다. 특정 상황에서는 이러한 버퍼가 겹쳐 인덱서가 문서를 두 번 이상 인덱싱하여 처리된 문서 수가 데이터 원본의 실제 문서 수보다 많을 수 있습니다. 이 동작은 문서 복제와 같이 인덱스 에 저장된 데이터에는 영향을 **주지** 않으며 최종 일관성에 도달하는 데 시간이 더 오래 걸릴 수 있습니다. 이는 다음 조건 중 충족되는 경우 특히 우선적으로 수행될 수 있습니다.

- 주문형 인덱서 요청이 연속해서 빠르게 발급됩니다.
- 데이터 원본의 토폴로지는 여러 복제본 및 파티션을 포함합니다(이러한 예제 중 하나는 [여기에서](https://docs.microsoft.com/azure/cosmos-db/consistency-levels)설명).

인덱서가 연속적으로 여러 번 호출될 수 없습니다. 업데이트가 신속하게 필요한 경우 지원되는 방법은 데이터 원본을 동시에 업데이트하는 동시에 인덱스에 업데이트를 푸시하는 것입니다. 주문형 처리의 경우 요청 속도를 5분 이상 간격으로 실행하고 일정에 따라 인덱서를 실행하는 것이 좋습니다.

### <a name="example-of-duplicate-document-processing-with-30-second-buffer"></a>30초 버퍼가 있는 중복 문서 처리의 예
문서가 두 번 처리되는 조건은 각 작업 및 카운터 작업을 기록하는 타임라인에 설명되어 있습니다. 다음 타임라인에서는 문제를 보여 줍니다.

| 타임라인(hh:mm:ss) | 이벤트 | 인덱서 높은 워터 마크 | 의견 |
|---------------------|-------|-------------------------|---------|
| 00:01:00 | `doc1`최종 일관성을 사용하여 데이터 원본에 쓰기 | `null` | 문서 타임스탬프는 00:01:00입니다. |
| 00:01:05 | `doc2`최종 일관성을 사용하여 데이터 원본에 쓰기 | `null` | 문서 타임스탬프는 00:01:05입니다. |
| 00:01:10 | 인덱서 시작 | `null` | |
| 00:01:11 | 00:01:10 이전의 모든 변경 내용에 대한 인덱서 쿼리 인덱서 쿼리에서 인식만 하는 `doc2` `doc2` 복제본입니다. 만 검색됩니다. | `null` | 인덱서가 타임스탬프를 시작하기 전에 모든 변경 내용을 요청하지만 실제로 하위 집합을 받습니다. 이 동작을 위해서는 버퍼 기간을 다시 조회해야 합니다. |
| 00:01:12 | 처음으로 인덱서 프로세스 `doc2` | `null` | |
| 00:01:13 | 인덱서 종료 | 00:01:10 | 높은 워터 마크가 현재 인덱서 실행의 시작 타임스탬프로 업데이트됩니다. |
| 00:01:20 | 인덱서 시작 | 00:01:10 | |
| 00:01:21 | 인덱서가 00:00:40에서 00:01:20 사이의 모든 변경 내용을 쿼리합니다. 인덱서 쿼리에서 및 를 모두 인식하는 복제본입니다. 는 `doc1` `doc2` 및 를 검색합니다. `doc1``doc2` | 00:01:10 | 인덱서가 현재 높은 워터마크에서 30초 버퍼를 뺀 값과 현재 인덱서 실행의 시작 타임스탬프 사이의 모든 변경 내용을 요청합니다. |
| 00:01:22 | 처음으로 인덱서 프로세스 `doc1` | 00:01:10 | |
| 00:01:23 | 두 번째로 인덱서 프로세스 `doc2` | 00:01:10 | |
| 00:01:24 | 인덱서 종료 | 00:01:20 | 높은 워터 마크가 현재 인덱서 실행의 시작 타임스탬프로 업데이트됩니다. |
| 00:01:32 | 인덱서 시작 | 00:01:20 | |
| 00:01:33 | 인덱서가 00:00:50에서 00:01:32 사이의 모든 변경 내용을 쿼리합니다. 는 및 를 검색합니다. `doc1``doc2` | 00:01:20 | 인덱서가 현재 높은 워터마크에서 30초 버퍼를 뺀 값과 현재 인덱서 실행의 시작 타임스탬프 사이의 모든 변경 내용을 요청합니다. |
| 00:01:34 | 두 번째로 인덱서 프로세스 `doc1` | 00:01:20 | |
| 00:01:35 | 세 번째로 인덱서 프로세스 `doc2` | 00:01:20 | |
| 00:01:36 | 인덱서 종료 | 00:01:32 | 높은 워터 마크가 현재 인덱서 실행의 시작 타임스탬프로 업데이트됩니다. |
| 00:01:40 | 인덱서 시작 | 00:01:32 | |
| 00:01:41 | 00:01:02와 00:01:40 사이의 모든 변경 내용에 대한 인덱서 쿼리 검색 `doc2` | 00:01:32 | 인덱서가 현재 높은 워터마크에서 30초 버퍼를 뺀 값과 현재 인덱서 실행의 시작 타임스탬프 사이의 모든 변경 내용을 요청합니다. |
| 00:01:42 | 네 번째로 인덱서 프로세스 `doc2` | 00:01:32 | |
| 00:01:43 | 인덱서 종료 | 00:01:40 | 이 인덱서 실행은 데이터 원본에 마지막으로 쓴 후 30초 이상 시작되고 도 `doc2` 처리되었습니다. 00:01:35 이전의 모든 인덱서 실행이 제거되면 및 을 처리하는 첫 번째이자 유일한 실행이 되기 때문에 예상되는 `doc1` `doc2` 동작입니다. |

실제로 이 시나리오는 특정 데이터 원본에 대해 주문형 인덱서가 몇 분 이내에 수동으로 호출되는 경우에만 발생합니다. 이로 인해 숫자가 일치하지 않거나(예: 인덱서가 인덱서 실행 통계에 따라 총 345개의 문서를 처리했지만 데이터 원본 및 인덱스에 340개의 문서가 있음) 동일한 문서에 대해 동일한 기술을 여러 번 실행하는 경우 청구가 증가할 수 있습니다. 일정을 사용 하 여 인덱서를 실행 하는 것이 선호 되는 권장 사항입니다.

## <a name="see-also"></a>참고 항목

* [일반적인 인덱서 오류 및 경고 문제 해결](cognitive-search-common-errors-warnings.md)
* [인덱서 기반 인덱싱 모니터링](search-howto-monitor-indexers.md)
