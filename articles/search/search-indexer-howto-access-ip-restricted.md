---
title: 인덱서 IP 범위에 대한 액세스 허용
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 인덱서의 데이터 액세스를 허용하도록 IP 방화벽 규칙을 구성합니다.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: how-to
ms.date: 11/11/2021
ms.openlocfilehash: 50ee624a3795015d35637e0e984a48025c802698
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132492723"
---
# <a name="configure-ip-firewall-rules-to-allow-indexer-connections-in-azure-cognitive-search"></a>Azure Cognitive Search에서 인덱서 연결을 허용하도록 IP 방화벽 규칙 구성

인덱서를 대신 하 여 검색 서비스는 인덱싱 중에 데이터를 가져오기 위해 외부 Azure 리소스에 대 한 아웃 바운드 호출을 실행 합니다. Azure 리소스가 IP 방화벽 규칙을 사용 하 여 들어오는 호출을 필터링 하는 경우 방화벽에서 인덱서 요청을 인정 하는 인바운드 규칙을 만들어야 합니다.

이 문서에서는 검색 서비스의 IP 주소를 확인 한 다음 Azure Portal를 사용 하 여 Azure Storage 계정에서 인바운드 IP 규칙을 구성 하는 방법을 설명 합니다. Azure Storage에만 해당 되는 경우이 방법은 Cosmos DB 및 azure SQL와 같은 데이터 액세스에 IP 방화벽 규칙을 사용 하는 다른 Azure 리소스에도 적용 됩니다.

> [!NOTE]
> 저장소 계정에 대 한 IP 방화벽 규칙은 저장소 계정과 검색 서비스가 서로 다른 지역에 있는 경우에만 적용 됩니다. 설치 프로그램에서이를 허용 하지 않는 경우에는 [신뢰할 수 있는 서비스 예외 옵션](search-indexer-howto-access-trusted-service-exception.md) 을 대신 활용 하는 것이 좋습니다.

## <a name="get-a-search-service-ip-address"></a>검색 서비스 IP 주소 가져오기

1. 검색 서비스의 FQDN (정규화 된 도메인 이름)을 확인 합니다. 이는 `<search-service-name>.search.windows.net`과 같습니다. FQDN은 Azure Portal에서 검색 서비스를 조회하여 확인할 수 있습니다.

   ![서비스 FQDN 가져오기](media\search-indexer-howto-secure-access\search-service-portal.png "서비스 FQDN 가져오기")

1. `nslookup`명령 프롬프트에서 FQDN의 (또는)를 수행 하 여 검색 서비스의 IP 주소를 조회 `ping` 합니다.

1. 다음 단계에서 인바운드 규칙에 대해 지정할 수 있도록 IP 주소를 복사 합니다. 아래 예제에서 복사 해야 하는 IP 주소는 "150.0.0.1"입니다.

   ```azurepowershell
   nslookup contoso.search.windows.net
   Server:  server.example.org
   Address:  10.50.10.50
    
   Non-authoritative answer:
   Name:    <name>
   Address:  150.0.0.1
   aliases:  contoso.search.windows.net
   ```

## <a name="get-ip-addresses-for-azurecognitivesearch-service-tag"></a>"AzureCognitiveSearch" 서비스 태그에 대 한 IP 주소 가져오기

검색 서비스 구성에 따라 IP 주소 범위에서 요청을 인정 하는 인바운드 규칙도 만들어야 할 수도 있습니다. 특히 인덱서의 [다중 테 넌 트 실행 환경](search-indexer-securing-resources.md#indexer-execution-environment)에서 발생 하는 요청에 대 한 추가 IP 주소가 사용 됩니다. 

서비스 태그에서이 IP 주소 범위를 가져올 수 있습니다 `AzureCognitiveSearch` .

1. `AzureCognitiveSearch` [검색 API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api) 또는 다운로드할 수도 있는 [JSON 파일](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)을 사용 하 여 서비스 태그의 IP 주소 범위를 가져옵니다.

1. 검색 서비스가 Azure 공용 클라우드 인 경우 [Azure 공용 JSON 파일](https://www.microsoft.com/download/details.aspx?id=56519) 을 다운로드 해야 합니다.

   ![JSON 파일 다운로드](media\search-indexer-howto-secure-access\service-tag.png "JSON 파일 다운로드")

1. 검색 서비스가 미국 중서부에 있다고 가정하면 JSON 파일에서 다중 테넌트 인덱서 실행 환경에 대한 IP 주소 목록은 아래와 같습니다.

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

`/32`IP 주소의 경우 "/32" (52.253.133.74/32는 규칙 정의에서 52.253.133.74이 됨)을 삭제 합니다. 다른 모든 IP 주소는 약어를 사용 하 여 사용할 수 있습니다.

## <a name="add-ip-addresses-to-ip-firewall-rules"></a>Ip 방화벽 규칙에 IP 주소 추가

IP 주소가 있으면 규칙을 설정할 준비가 된 것입니다. 스토리지 계정의 방화벽 규칙에 IP 주소 범위를 추가하는 가장 쉬운 방법은 Azure Portal을 통하는 것입니다. 

1. Portal에서 스토리지 계정을 찾고 **방화벽 및 가상 네트워크** 탭으로 이동합니다.

   ![방화벽 및 가상 네트워크](media\search-indexer-howto-secure-access\storage-firewall.png "방화벽 및 가상 네트워크")

1. 주소 범위에서 이전에 얻은 3 개의 IP 주소 (검색 서비스 IP의 경우 하나, 서비스 태그의 경우 2 개)를 추가 `AzureCognitiveSearch` 하 고 **저장** 을 선택 합니다.

   ![방화벽 IP 규칙](media\search-indexer-howto-secure-access\storage-firewall-ip.png "방화벽 IP 규칙")

방화벽 규칙을 업데이트 하는 데는 5 ~ 10 분 정도 걸릴 수 있으며, 그 후에는 인덱서가 저장소 계정의 데이터에 액세스할 수 있어야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Storage 방화벽 구성](../storage/common/storage-network-security.md)
- [Cosmos DB에 대해 IP 방화벽 구성](../cosmos-db/how-to-configure-firewall.md)
- [Azure SQL Server에 대해 IP 방화벽 구성](../azure-sql/database/firewall-configure.md)
