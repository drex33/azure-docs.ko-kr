---
title: 인덱서 IP 범위에 대한 액세스 허용
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 인덱서의 데이터 액세스를 허용하도록 IP 방화벽 규칙을 구성합니다.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: f0b73c1cc510156e0db7518c2d377a6a5cc9b921
ms.sourcegitcommit: 351279883100285f935d3ca9562e9a99d3744cbd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2021
ms.locfileid: "112377299"
---
# <a name="configure-ip-firewall-rules-to-allow-indexer-connections-in-azure-cognitive-search"></a>Azure Cognitive Search에서 인덱서 연결을 허용하도록 IP 방화벽 규칙 구성

스토리지 계정, Cosmos DB 계정 및 Azure SQL Server와 같은 Azure 리소스에 대한 IP 방화벽 규칙은 특정 IP 범위에서 시작된 트래픽만 데이터에 액세스하도록 허용합니다.

이 문서에서는 Azure Cognitive Search 인덱서가 데이터에 안전하게 액세스할 수 있도록 Azure Portal을 통해 스토리지 계정에 대해 IP 규칙을 구성하는 방법을 설명합니다. 이 접근 방법은 Azure Storage와 관련된 것이지만 데이터에 대한 액세스를 보호하기 위해 IP 방화벽 규칙을 사용하는 다른 Azure 리소스에도 작동합니다.

> [!NOTE]
> 스토리지 계정에 대한 IP 방화벽 규칙은 스토리지 계정과 검색 서비스가 서로 다른 지역에 있는 경우에만 적용됩니다. 설치가 이를 허용하지 않는 경우 [신뢰할 수 있는 서비스 예외 옵션](search-indexer-howto-access-trusted-service-exception.md)을 활용하는 것이 좋습니다.

## <a name="get-the-ip-address-of-the-search-service"></a>검색 서비스의 IP 주소 가져오기

검색 서비스의 FQDN(정규화된 도메인 이름)을 가져옵니다. 이는 `<search-service-name>.search.windows.net`과 같습니다. FQDN은 Azure Portal에서 검색 서비스를 조회하여 확인할 수 있습니다.

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

## <a name="get-the-ip-address-ranges-for-azurecognitivesearch-service-tag"></a>"AzureCognitiveSearch" 서비스 태그에 대한 IP 주소 범위 가져오기

추가 IP 주소는 인덱서의 [다중 테넌트 실행 환경](search-indexer-securing-resources.md#indexer-execution-environment)에서 시작하는 요청에 사용됩니다. 이 IP 주소 범위는 서비스 태그에서 가져올 수 있습니다.

`AzureCognitiveSearch` 서비스 태그의 IP 주소 범위는 [검색 API(미리 보기)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) 또는 [다운로드 가능한 JSON 파일](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)을 통해 가져올 수 있습니다.

이 연습에서는 검색 서비스가 Azure 퍼블릭 클라우드라고 가정하여 [Azure 퍼블릭 JSON 파일](https://www.microsoft.com/download/details.aspx?id=56519)을 다운로드해야 합니다.

   ![JSON 파일 다운로드](media\search-indexer-howto-secure-access\service-tag.png "JSON 파일 다운로드")

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

/32 IP 주소의 경우 "/32"(52.253.133.74/32 -> 52.253.133.74)를 삭제하고 다른 주소의 경우 축약 형태를 사용할 수 있습니다.

## <a name="add-the-ip-address-ranges-to-ip-firewall-rules"></a>IP 방화벽 규칙에 IP 주소 범위 추가

스토리지 계정의 방화벽 규칙에 IP 주소 범위를 추가하는 가장 쉬운 방법은 Azure Portal을 통하는 것입니다. Portal에서 스토리지 계정을 찾고 **방화벽 및 가상 네트워크** 탭으로 이동합니다.

   ![방화벽 및 가상 네트워크](media\search-indexer-howto-secure-access\storage-firewall.png "방화벽 및 가상 네트워크")

주소 범위에서 이전에 획득한 3개의 IP 주소(검색 서비스 IP에 대해 1개, `AzureCognitiveSearch` 서비스 태그에 대해 2개)를 추가하고 **저장** 을 선택합니다.

   ![방화벽 IP 규칙](media\search-indexer-howto-secure-access\storage-firewall-ip.png "방화벽 IP 규칙")

방화벽 규칙을 업데이트하는 데 5-10분이 걸리며, 그 이후에 인덱서에서 스토리지 계정의 데이터에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Storage 방화벽 구성](../storage/common/storage-network-security.md)
- [Cosmos DB에 대해 IP 방화벽 구성](../cosmos-db/how-to-configure-firewall.md)
- [Azure SQL Server에 대해 IP 방화벽 구성](../azure-sql/database/firewall-configure.md)