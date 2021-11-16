---
title: 보호되는 리소스에 대한 인덱서 액세스
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 인덱서로 Azure 데이터 액세스를 위한 네트워크 수준 보안 옵션에 대한 개념적 개요입니다.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/12/2021
ms.openlocfilehash: a541eb900648fe33beb76207da956c1489f89cb5
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485109"
---
# <a name="indexer-access-to-content-protected-by-azure-network-security-features"></a>Azure 네트워크 보안 기능으로 보호되는 콘텐츠에 대한 인덱서 액세스

Azure Cognitive Search 인덱서는 실행하는 동안 다양한 Azure 리소스에 대한 아웃바운드 호출을 수행할 수 있습니다. 이 문서에서는 IP 방화벽, 프라이빗 엔드포인트 또는 기타 Azure 네트워크 수준 보안 메커니즘으로 보호되는 콘텐츠에 대한 인덱서 액세스의 개념에 대해 설명합니다. 

인덱서는 다음 두 가지 상황에서 아웃바운드 호출을 수행합니다.

- 인덱싱 중에 외부 데이터 원본에 연결
- 기술 세트를 통해 캡슐화된 외부 코드에 연결

인덱서가 일반적인 실행에서 액세스할 수 있는 모든 가능한 리소스 종류 목록은 아래 표에 나와 있습니다.

| 리소스 | 인덱서 실행 내의 목적 |
| --- | --- |
| Azure Storage(BLOB, 테이블, ADLS Gen 2) | 데이터 원본 |
| Azure Storage(BLOB, 테이블) | 기술 세트(보강된 문서 캐싱 및 지식 저장소 프로젝션 저장) |
| Azure Cosmos DB(다양한 API) | 데이터 원본 |
| Azure SQL Database | 데이터 원본 |
| Azure 가상 머신의 SQL Server | 데이터 원본 |
| SQL Managed Instance | 데이터 원본 |
| Azure Functions | 사용자 지정 웹 API 기술을 위한 호스트 |
| Cognitive Services | 무료 문서 제한 20개를 초과하는 보강을 청구하는 데 사용되는 기술 세트에 연결됨 |

> [!NOTE]
> 기술 세트에 연결된 Cognitive Service 리소스는 검색 인덱스에 대해 수행되고 작성된 보강을 기준으로 청구에 사용됩니다. Cognitive Services API에 액세스하는 데 사용되지 않습니다. 안전한 내부 통신 채널을 통해 인덱서의 보강 파이프라인에서 Cognitive Services API에 액세스하는데, 이때 데이터는 전송 중 강력하게 암호화되고 미사용 시 절대 저장되지 않습니다.

고객은 Azure에서 제공하는 여러 네트워크 격리 메커니즘을 통해 리소스를 보호할 수 있습니다. Cognitive Service 리소스를 제외하고, 인덱서는 네트워크에서 격리된 경우에도 다른 모든 리소스에 액세스할 수 있는 제한된 기능이 있으며, 이는 아래 표에 간략하게 설명되어 있습니다.

| 리소스 | IP 제한 | 프라이빗 엔드포인트 |
| --- | --- | ---- |
| Azure Storage(BLOB, 테이블, ADLS Gen 2) | 스토리지 계정과 검색 서비스가 서로 다른 지역에 있는 경우에만 지원됨 | 지원됨 |
| Azure Cosmos DB - SQL API | 지원됨 | 지원 여부 |
| Azure Cosmos DB - MongoDB 및 Gremlin API | 지원됨 | 지원되지 않음 |
| Azure SQL Database | 지원됨 | 지원됨 |
| Azure 가상 머신의 SQL Server | 지원됨 | 해당 없음 |
| SQL Managed Instance | 지원됨 | 해당 없음 |
| Azure Functions | 지원됨 | Azure Functions의 특정 계층에 대해서만 지원됨 |

> [!NOTE]
> 네트워크 보안 Azure Storage 계정에 대해 위에 나열된 옵션 외에도, 고객은 Azure Cognitive Search가 [신뢰할 수 있는 Microsoft 서비스](../storage/common/storage-network-security.md#trusted-microsoft-services)라는 사실을 활용할 수 있습니다. 즉, 스토리지 계정에서 적절한 역할 기반 액세스 제어를 사용하도록 설정한 경우 특정 검색 서비스는 스토리지 계정에 대한 가상 네트워크 또는 IP 제한을 무시하고 스토리지 계정의 데이터에 액세스할 수 있습니다. 자세한 내용은 [신뢰할 수 있는 서비스 예외를 사용한 인덱서 연결](search-indexer-howto-access-trusted-service-exception.md)을 참조 하세요. 이 옵션은 스토리지 계정 또는 검색 서비스를 다른 지역으로 이동할 수 없는 경우 IP 제한 경로 대신 사용할 수 있습니다.

보안 액세스 메커니즘을 선택하는 경우 다음 제약 조건을 고려합니다.

- 인덱서는 [가상 네트워크 서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)에 연결할 수 없습니다. 자격 증명, 프라이빗 엔드포인트, 신뢰할 수 있는 서비스, IP 주소 지정을 사용하는 퍼블릭 엔드포인트는 인덱서 연결을 위해 지원되는 유일한 방법입니다.
- 검색 서비스는 항상 클라우드에서 실행되며, 가상 머신에서 기본적으로 실행되는 특정 가상 네트워크에 프로비저닝할 수 없습니다. 이 기능은 Azure Cognitive Search에서 제공하지 않습니다.
- 인덱서가 (아웃바운드) 프라이빗 엔드포인트를 사용하여 리소스에 액세스하는 경우 추가 [프라이빗 링크 요금](https://azure.microsoft.com/pricing/details/search/)이 적용될 수 있습니다.

## <a name="indexer-execution-environment"></a>인덱서 실행 환경

Azure Cognitive Search 인덱서는 데이터 원본에서 콘텐츠를 효율적으로 추출하고, 추출된 콘텐츠에 강화를 추가하고, 검색 인덱스에 결과를 쓰기 전에 프로젝션을 선택적으로 생성할 수 있습니다.

최적의 처리를 위해 검색 서비스는 작업을 설정할 내부 실행 환경을 결정합니다. 환경을 제어하거나 구성할 수는 없지만 IP 방화벽 규칙을 설정할 때 고려할 수 있도록 환경을 알고 있는 것이 중요합니다.

할당된 태스크의 수와 유형에 따라 인덱서가 다음 두 환경 중 하나에서 실행됩니다.

- 특정 검색 서비스에 대한 프라이빗 환경. 이러한 환경에서 실행되는 인덱서는 다른 워크로드(예: 다른 고객이 시작한 인덱싱 또는 쿼리 워크로드)와 리소스를 공유합니다. 일반적으로 텍스트 기반 인덱싱(예: 기술 세트를 사용하지 않음)을 수행하는 인덱서만 이 환경에서 실행됩니다.

- 기술 세트를 사용하는 인덱서와 같이 리소스를 많이 사용하는 인덱서를 호스트하는 다중 테넌트 환경. 이 환경은 계산 집약적 처리를 없애는 데 사용되며 서비스별 리소스는 일상적인 작업에 사용할 수 있게 남아 있습니다. 이 다중 테넌트 환경은 고객의 비용 추가 없이 Microsoft에서 관리하고 보호합니다.

지정된 인덱서 실행에 대해 Azure Cognitive Search는 인덱서를 실행할 최상의 환경을 결정합니다. IP 방화벽을 사용하여 Azure 리소스에 대한 액세스를 제어하는 경우 실행 환경에 대해 알고 있으면 다음 섹션에서 설명한 대로 둘 모두를 포함하는 IP 범위를 설정하는 데 도움이 됩니다.

## <a name="granting-access-to-indexer-ip-ranges"></a>인덱서 IP 범위에 대한 액세스 권한 부여

인덱서에서 데이터를 끌어온 리소스가 방화벽 뒤에 있는 경우 인바운드 규칙의 IP 범위에 인덱서 요청이 시작되는 모든 IP가 포함되어 있는지 확인합니다. 위에서 설명한 것처럼 인덱서를 실행하고 액세스 요청을 시작할 수 있는 가능한 두 가지 환경이 있습니다. 인덱서 액세스가 작동하려면 **두** 환경의 IP 주소를 추가해야 합니다.

- 검색 서비스 특정 프라이빗 환경의 IP 주소를 가져오려면 검색 `nslookup` `ping` 서비스의 FQDN(정규화된 도메인 이름)을 사용하거나 사용합니다. 예를 들어 퍼블릭 클라우드에서 검색 서비스의 FQDN은 `<service-name>.search.windows.net`일 수 있습니다. 이 정보는 Azure Portal에서 사용할 수 있습니다.

- 인덱서가 실행될 수 있는 다중 테넌트 환경의 IP 주소를 가져오려면 `AzureCognitiveSearch` 서비스 태그를 사용합니다. [Azure 서비스 태그에는](../virtual-network/service-tags-overview.md) 각 서비스에 대해 게시된 IP 주소 범위가 있습니다. 검색 [API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api) 또는 [다운로드 가능한 JSON 파일](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)를 사용하여 이러한 IP를 찾을 수 있습니다. 두 경우 모두 IP 범위는 지역별로 세분화됩니다. 검색 서비스가 프로비전되는 지역에 할당된 IP 범위만 지정해야 합니다.

특정 데이터 원본의 경우에는 IP 범위 목록을 열거하는 대신 서비스 태그 자체를 직접 사용할 수 있습니다. 검색 서비스의 IP 주소는 계속해서 명시적으로 사용해야 합니다. 해당 데이터 원본은 Azure Storage, Cosmos DB, Azure SQL 등에서 제공하는 것과 같은 IP 규칙과 달리 기본적으로 서비스 태그 추가를 지원하는 [네트워크 보안 그룹 규칙](../virtual-network/network-security-groups-overview.md)을 설정하여 액세스를 제한합니다. 검색 서비스 IP 주소 외에도 `AzureCognitiveSearch` 서비스 태그를 직접 활용하는 기능을 지원하는 데이터 원본은 다음과 같습니다.

- [Azure Virtual Machines의 SQL Server](./search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md#restrict-access-to-the-azure-cognitive-search)

- [SQL Managed Instances](./search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md#verify-nsg-rules)

이 연결 옵션에 대한 자세한 내용은 [IP 방화벽을 통한 인덱서 연결](search-indexer-howto-access-ip-restricted.md)을 참조하세요.

## <a name="granting-access-via-private-endpoints"></a>프라이빗 엔드포인트를 통한 액세스 권한 부여

인덱서가 잠긴 리소스에 대한 연결에서 [프라이빗 엔드포인트를](../private-link/private-endpoint-overview.md) 사용할 수 있습니다(보호된 가상 네트워크에서 실행 중이거나 공용 연결을 통해 사용할 수 없는 경우).

이 기능은 텍스트 기반 및 기술 기반 인덱싱을 위해 만들 수 있는 프라이빗 엔드포인트 수에 대한 계층 제한에 따라 청구 가능한 검색 서비스(기본 이상)에서만 사용할 수 있습니다. 자세한 내용은 서비스 제한 설명서의["공유 프라이빗 링크 리소스 제한" 섹션을](search-limits-quotas-capacity.md#shared-private-link-resource-limits)참조하세요.

### <a name="step-1-create-a-private-endpoint-to-the-secure-resource"></a>1단계: 보안 리소스에 대한 프라이빗 엔드포인트 만들기

고객은 보안 리소스(예: 스토리지 계정)에 대한 프라이빗 엔드포인트 연결을 만들기 위해 **공유 프라이빗 링크 리소스** 에서 검색 관리 작업인 [CreateOrUpdate API](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/create-or-update)를 호출해야 합니다. 이 (아웃 바운드) 프라이빗 엔드포인트 연결을 통해 전송되는 트래픽은 검색 서비스별 “프라이빗” 인덱서 실행 환경에 있는 가상 네트워크에서만 시작됩니다.

Azure Cognitive Search는 이 API의 호출자에게 보안 리소스에 대한 프라이빗 엔드포인트 연결 요청을 승인하는 Azure RBAC 권한이 있는지 확인합니다. 예를 들어 읽기 전용 권한을 사용하여 스토리지 계정에 대한 프라이빗 엔드포인트 연결을 요청하면 이 호출이 거부됩니다.

### <a name="step-2-approve-the-private-endpoint-connection"></a>2단계: 프라이빗 엔드포인트 연결 승인

공유 프라이빗 링크 리소스를 만드는 (비동기) 작업이 완료되면 프라이빗 엔드포인트 연결이 “보류 중” 상태로 생성됩니다. 아직 이 연결을 통해 트래픽이 흐르지 않습니다.

그러면 고객은 보안 리소스에서 이 요청을 찾아 “승인”해야 합니다. 일반적으로 이 작업은 Azure Portal 또는 [REST API](/rest/api/virtualnetwork/privatelinkservices/updateprivateendpointconnection)를 통해 수행할 수 있습니다.

### <a name="step-3-force-indexers-to-run-in-the-private-environment"></a>3단계: “프라이빗” 환경에서 강제로 인덱서 실행

승인된 프라이빗 엔드포인트는 특정 형식의 네트워크 수준 액세스 제한이 있는 리소스(예: 특정 가상 네트워크에서만 액세스하도록 구성된 스토리지 계정 데이터 원본)로 검색 서비스에서 나가는 호출을 수행할 수 있습니다.

즉, 프라이빗 엔드포인트를 통해 이러한 데이터 원본에 연결할 수 있는 인덱서가 성공합니다.
프라이빗 엔드포인트가 승인되지 않은 경우 또는 인덱서가 프라이빗 엔드포인트 연결을 사용하지 않는 경우 인덱서 실행이 `transientFailure`에서 종료됩니다.

인덱서가 프라이빗 엔드포인트 연결을 통해 리소스에 액세스하도록 하려면 인덱서의 `executionEnvironment`를 `"Private"`으로 설정하여 모든 인덱서 실행이 프라이빗 엔드포인트를 활용할 수 있도록 해야 합니다. 프라이빗 엔드포인트는 프라이빗 검색 서비스 관련 환경 내에서 프로비저닝되기 때문입니다.

```json
    {
      "name" : "myindexer",
      ... other indexer properties
      "parameters" : {
          ... other parameters
          "configuration" : {
            ... other configuration properties
            "executionEnvironment": "Private"
          }
        }
    }
```

이러한 단계는 [프라이빗 엔드포인트를 통한 인덱서 연결](search-indexer-howto-access-private.md)에서 자세히 설명합니다.
리소스에 대한 프라이빗 엔드포인트를 승인하면 *프라이빗* 으로 설정된 인덱서가 프라이빗 엔드포인트 연결을 통해 액세스하려고 시도합니다.

## <a name="next-steps"></a>다음 단계

- [IP 방화벽을 통한 인덱서 연결](search-indexer-howto-access-ip-restricted.md)
- [신뢰할 수 있는 서비스 예외를 사용한 인덱서 연결](search-indexer-howto-access-trusted-service-exception.md)
- [프라이빗 엔드포인트에 인덱서 연결](search-indexer-howto-access-private.md)
