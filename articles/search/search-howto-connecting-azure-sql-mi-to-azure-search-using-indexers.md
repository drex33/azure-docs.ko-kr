---
title: SQL Managed Instance에 대한 인덱서 연결
titleSuffix: Azure Cognitive Search
description: 퍼블릭 엔드포인트를 통해 Azure Cognitive Search 인덱서에서 SQL Managed Instance로 연결할 수 있도록 합니다.
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/26/2021
ms.openlocfilehash: 12ee369bfd69e82a73ccaa766190cedf7910a7a0
ms.sourcegitcommit: 3b371af4c30fce82854b3d45fd8b8e674bbe7517
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2021
ms.locfileid: "112984897"
---
# <a name="indexer-connections-to-azure-sql-managed-instance-through-a-public-endpoint"></a>퍼블릭 엔드포인트를 통한 Azure SQL Managed Instance에 대한 인덱서 연결

Azure SQL Managed Instance에 연결하는 Azure Cognitive Search 인덱서를 설정하는 경우 관리형 인스턴스에서 필수 조건으로 퍼블릭 엔드포인트를 사용하도록 설정해야 합니다. 인덱서는 퍼블릭 엔드포인트를 통해 관리형 인스턴스에 연결됩니다.

이 문서에서는 데이터 원본 구성에 필요한 정보 수집을 포함하는 기본 단계를 제공합니다. 자세한 내용과 방법은 [Azure SQL Managed Instance에서 퍼블릭 엔드포인트 구성](../azure-sql/managed-instance/public-endpoint-configure.md)을 참조하세요.

## <a name="enable-a-public-endpoint"></a>퍼블릭 엔드포인트 사용

새 SQL Managed Instance의 경우 **퍼블릭 엔드포인트 사용** 옵션을 선택하여 리소스를 만듭니다.

   ![공용 엔드포인트 사용](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "공용 엔드포인트 사용")

또는 인스턴스가 이미 있는 경우 **보안** > **가상 네트워크** > **퍼블릭 엔드포인트** > **사용** 에서 기존 SQL Managed Instance의 퍼블릭 엔드포인트를 사용하도록 설정할 수 있습니다.

   ![관리형 인스턴스 VNET을 통해 퍼블릭 엔드포인트 사용](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "공용 엔드포인트 사용")

## <a name="verify-nsg-rules"></a>NSG 규칙 확인

네트워크 보안 그룹에 Azure 서비스의 연결을 허용하는 올바른 **인바운드 보안 규칙** 이 있는지 확인합니다.

   ![NSG 인바운드 보안 규칙](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "NSG 인바운드 보안 규칙")

## <a name="restrict-inbound-access-to-the-endpoint"></a>엔드포인트에 대한 인바운드 액세스 제한

현재 규칙(`public_endpoint_inbound`)을 다음 두 개의 규칙으로 바꿔서 퍼블릭 엔드포인트에 대한 인바운드 액세스를 제한할 수 있습니다.

* `AzureCognitiveSearch` [서비스 태그](../virtual-network/service-tags-overview.md#available-service-tags)에서 인바운드 액세스 허용(“SOURCE” = `AzureCognitiveSearch`, “NAME” = `cognitive_search_inbound`)

* 정규화된 도메인 이름(예: `<your-search-service-name>.search.windows.net`)을 ping하여 가져올 수 있는 Search Service의 IP 주소에서 인바운드 액세스 허용 (“SOURCE” = `IP address`, “NAME” = `search_service_inbound`)

두 규칙에 대해 “PORT” = `3342`, “PROTOCOL” = `TCP`, “DESTINATION” = `Any`, “ACTION” = `Allow`를 설정합니다.

## <a name="get-public-endpoint-connection-string"></a>퍼블릭 엔드포인트 연결 문자열 가져오기

검색 인덱서의 데이터 원본 연결에 사용할 연결 문자열을 복사합니다. **퍼블릭 엔드포인트**(포트 1433이 아닌 포트 3342)의 연결 문자열을 복사해야 합니다.

   ![퍼블릭 엔드포인트 연결 문자열](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "퍼블릭 엔드포인트 연결 문자열")

## <a name="next-steps"></a>다음 단계

구성이 완료되면 이제 [SQL Managed Instance를 인덱서 데이터 원본으로](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) 지정할 수 있습니다.