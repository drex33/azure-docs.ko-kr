---
title: 포털의 서비스 관리
titleSuffix: Azure Cognitive Search
description: Azure Portal을 사용하여 Microsoft Azure에 호스트된 클라우드 검색 서비스인 Azure Cognitive Search 서비스를 관리합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: 42d3a531bc0b91e824912991d41c7e8dccb4e535
ms.sourcegitcommit: fc912bf0540585f44c09c6d63728c05c5dda558b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2021
ms.locfileid: "133128496"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Azure 포털에서 Azure Cognitive Search에 대한 서비스 관리

> [!div class="op_single_selector"]
>
> * [PowerShell](search-manage-powershell.md)
> * [Azure CLI](search-manage-azure-cli.md)
> * [REST API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [포털](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure Cognitive Search는 사용자 지정 앱에 풍부한 검색 환경을 구축하기 위해 사용되는 완전히 관리되는 클라우드 기반 검색 서비스입니다. 이 문서에서는 이미 만든 검색 서비스에 대해 [Azure Portal](https://portal.azure.com)에서 수행할 수 있는 관리 작업에 대해 설명합니다. 포털에서는 콘텐츠 관리 및 검색뿐만 아니라 서비스와 관련된 모든 [관리 작업](#management-tasks)도 수행할 수 있습니다. 따라서 포털은 검색 서비스 작업의 모든 측면에 대한 광범위한 액세스를 제공합니다.

각 검색 서비스는 독립 실행형 리소스로 관리됩니다. 다음 이미지에서는 "demo-search-svc"라는 단일 무료 검색 서비스에 대한 포털 페이지를 보여 줍니다. 서비스 관리를 위해 Azure PowerShell 또는 Azure CLI를 사용하는 데 익숙할 수도 있지만, 포털 페이지에서 제공하는 도구와 기능에 익숙해지는 것이 좋습니다. 일부 작업은 코드를 통해 수행하는 것보다 포털에서 수행하는 것이 더 쉽고 빠릅니다. 

## <a name="overview-home-page"></a>개요(홈) 페이지

개요 페이지는 각 서비스의 "홈" 페이지입니다. 아래에서 빨간색 상자로 묶은 화면 영역은 특히 서비스를 처음 접하는 경우 자주 사용할 수 있는 작업, 도구 및 타일을 나타냅니다.

:::image type="content" source="media/search-manage/search-portal-overview-page.png" alt-text="검색 서비스에 대한 포털 페이지" border="true":::

| 영역 | Description |
|------|-------------|
| 1  | **기본 정보** 섹션에는 연결을 설정할 때 사용되는 엔드포인트를 포함한 서비스 속성이 표시됩니다. 계층, 복제본 및 파티션 수도 한눈에 보여 줍니다. |
| 2 | 페이지 위쪽에는 대화형 도구를 호출하거나 서비스를 관리하기 위한 일련의 명령이 있습니다. [데이터 가져오기](search-get-started-portal.md) 및 [검색 탐색기](search-explorer.md)는 모두 프로토타입 및 검색에 일반적으로 사용됩니다. |
| 3 | **기본 정보** 섹션 아래에는 사용량 통계, 서비스 상태 메트릭 및 서비스의 모든 기존 인덱스, 인덱서, 데이터 원본 및 기술 세트에 대한 액세스에 빠르게 액세스할 수 있는 일련의 하위 탭 페이지가 있습니다. 인덱스 또는 다른 개체를 선택하면 개체 구성, 설정 및 상태(해당하는 경우)를 표시하는 추가 페이지를 사용할 수 있습니다. |
| 4 | 왼쪽에서는 연결에 사용되는 API 키에 액세스하고, 서비스를 구성하며, 보안을 구성하고, 작업을 모니터링하며, 작업을 자동화하고, 지원을 받기 위한 추가 페이지를 여는 링크에 액세스할 수 있습니다. |

### <a name="read-only-service-properties"></a>읽기 전용 서비스 속성

검색 서비스의 몇 가지 측면은 서비스가 프로비전될 때 결정되며 변경할 수 없습니다.

* 서비스 이름 - 검색 서비스의 이름은 바꿀 수 없습니다.
* 서비스 위치 - 원래 그대로의 서비스는 다른 지역으로 쉽게 이동할 수 없습니다. 템플릿이 있지만 콘텐츠 이동은 수동 프로세스입니다.
* 서비스 계층 - 예를 들어 새 서비스를 만들지 않고는 S1에서 S2로 전환할 수 없습니다.

## <a name="management-tasks"></a>관리 작업

서비스 관리는 의도적으로 간단하며, 서비스 자체와 관련하여 수행할 수 있는 작업을 통해 정의되는 경우가 많습니다.

* 복제본 및 파티션을 추가하거나 제거하여 [용량 조정](search-capacity-planning.md)
* 관리 및 쿼리 작업에 사용되는 [API 키 관리](search-security-api-keys.md)
* 역할 기반 보안을 통해 [관리 작업에 대한 액세스 제어](search-security-rbac.md)
* IP 주소로 액세스를 제한하도록 [IP 방화벽 규칙 구성](service-configure-firewall.md)
* Azure Private Link 및 프라이빗 가상 네트워크를 사용하여 [프라이빗 엔드포인트 구성](service-create-private-endpoint.md)
* [서비스 상태 및 작업 모니터링:](monitor-azure-cognitive-search.md)스토리지, 쿼리 볼륨 및 대기 시간

또한 서비스에서 만든 모든 개체(인덱스, 인덱서, 데이터 원본, 기술 세트 등)를 열거할 수 있습니다. 포털의 개요 페이지에는 서비스에 있는 모든 콘텐츠가 표시됩니다. 검색 서비스에 대한 대부분의 작업은 콘텐츠와 관련이 있습니다.

포털에서 수행되는 동일한 관리 작업은 [관리 REST API](/rest/api/searchmanagement/), [Az.Search PowerShell 모듈](search-manage-powershell.md), [az search Azure CLI 모듈](search-manage-azure-cli.md) 및 .NET, Python, Java 및 JavaScript용 Azure SDK를 통해 프로그래밍 방식으로 처리할 수도 있습니다. 관리 작업은 포털 및 모든 프로그래밍 인터페이스에서 완전히 표시됩니다. 한 가지 형식에만 사용할 수 있는 특정 관리 작업은 없습니다.

Cognitive Search는 더 심층적인 모니터링 및 관리를 위해 다른 Azure 서비스를 활용합니다. 자체적으로 검색 서비스 내에 저장되는 유일한 데이터는 개체 콘텐츠(인덱스, 인덱서, 데이터 원본 정의 및 기타 개체)입니다. 포털 페이지에 보고된 메트릭은 30일 롤링 주기로 내부 로그에서 가져옵니다. 사용자가 제어하는 로그 보존 및 추가 이벤트의 경우 [Azure Monitor](../azure-monitor/index.yml)가 필요합니다. 검색 서비스의 진단 로깅 설정에 대한 자세한 내용은 [로그 데이터 수집 및 분석](monitor-azure-cognitive-search.md)을 참조하세요.

## <a name="administrator-permissions"></a>관리자 권한

검색 서비스 개요 페이지를 열면 계정에 할당된 권한에 따라 사용 가능한 페이지가 결정됩니다. 문서의 시작 부분에 나와 있는 개요 페이지에서는 관리자 또는 기여자에게 표시될 포털 페이지를 보여 줍니다.

Azure 리소스에서 관리 권한은 역할 할당을 통해 부여됩니다. Azure Cognitive Search의 컨텍스트에서 [역할 할당](search-security-rbac.md)은 포털, [PowerShell](search-manage-powershell.md), [Azure CLI](search-manage-azure-cli.md) 또는 [관리 REST API](/rest/api/searchmanagement)를 사용하는지에 관계없이 복제본과 파티션을 할당하거나 API 키를 관리할 수 있는 사용자를 결정합니다.

> [!TIP]
> Azure 구독 관리자 또는 공동 관리자는 서비스 자체를 프로비전 또는 프로비전 해제할 수 있습니다. Azure 전체 메커니즘을 사용하여 구독 또는 리소스를 잠가서 관리자 권한이 있는 사용자가 검색 서비스를 실수로 삭제하거나 무단으로 삭제하는 것을 방지할 수 있습니다. 자세한 내용은 [예기치 않은 삭제를 방지하기 위해 리소스 잠그기](../azure-resource-manager/management/lock-resources.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 포털에서 사용할 수 있는 [모니터링 기능](monitor-azure-cognitive-search.md) 검토
* [PowerShell](search-manage-powershell.md) 또는 [Azure CLI](search-manage-azure-cli.md)를 사용하여 자동화
* [보안 기능](search-security-overview.md)을 검토하여 콘텐츠 및 작업 보호
* [진단 로깅](monitor-azure-cognitive-search.md)을 사용하여 쿼리 및 인덱싱 워크로드 모니터링