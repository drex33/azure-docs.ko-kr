---
title: Azure API Management 진단 및 문제 해결
description: Azure Portal의 진단 및 해결 도구를 사용해 Azure API Management에서 API 문제를 해결하는 방법을 알아봅니다.
author: rzhang628
ms.service: api-management
ms.topic: article
ms.date: 02/05/2021
ms.author: rongzhang
ms.openlocfilehash: 83fbb17baf63d51b9f7ef29d369924294aa7c262
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131447477"
---
# <a name="azure-api-management-diagnostics-overview"></a>Azure API Management 진단 개요

Azure API Management에서 API를 빌드 및 관리 하는 경우 404 찾을 수 없음 오류에서 502 잘못 된 게이트웨이 오류까지 발생할 수 있는 모든 문제에 대해 준비 하려고 합니다. API Management 진단은 구성할 필요 없이 APIM에 게시된 API 문제를 해결하는 데 도움이 되는 대화형 인텔리전트 환경입니다. 게시된 API 관련 문제가 발생한 경우, API Management 진단은 문제점을 밝혀 내고 신속히 해당 문제를 해결하는 데 적합한 정보를 안내합니다.

이 환경은 지난 24시간 동안 API에 문제가 있을 때 가장 유용하지만, 언제든지 분석할 수 있도록 모든 진단 그래프가 제공됩니다.

[!NOTE] 문제 진단 및 해결은 현재 소비 계층에서 지원되지 않습니다.

## <a name="open-api-management-diagnostics"></a>API Management 진단 열기

API Management 진단에 액세스하려면 [Azure Portal](https://portal.azure.com)의 API Management 서비스 인스턴스로 이동합니다. 왼쪽 탐색 메뉴에서 **문제 진단 및 해결** 을 선택합니다.

:::image type="content" source="media/diagnose-solve-problems/apim-diagnostic-home.png" alt-text="진단으로 이동하는 방법을 보여 주는 스크린샷":::



## <a name="intelligent-search"></a>지능형 검색

페이지 맨 위의 검색 표시줄에서 문제를 검색할 수 있습니다. 또한, 검색을 통해 문제 해결에 도움이 될 만한 도구를 찾을 수도 있습니다. 

:::image type="content" source="media/diagnose-solve-problems/intelligent-search.png" alt-text="인텔리전트 검색의 스크린샷":::


## <a name="troubleshooting-categories"></a>문제 해결 범주

범주에서 문제를 해결할 수 있습니다. API 성능, 게이트웨이, API 정책, 서비스 계층과 관련된 일반적인 문제는 각 범주 내에서 모두 분석할 수 있습니다. 또한, 각각의 범주는 보다 구체적인 진단 검사를 제공합니다. 

:::image type="content" source="media/diagnose-solve-problems/troubleshooting-category.png" alt-text="범주 개요의 스크린샷":::


### <a name="availability-and-performance"></a>가용성 및 성능

해당 범주에서 API 가용성 및 성능 문제를 확인합니다. 본 범주 타일을 선택하면 대화형 인터페이스에서 일반적인 검사 몇 가지를 권장하는 것을 확인할 수 있습니다. 각 검사를 클릭하여 문제마다 세부 정보를 자세히 확인합니다. 이 검사에서는 API 성능 및 성능 문제와 관련해 요약된 내용을 보여 주는 그래프도 제공합니다. 예를 들면, API 서비스에는 백 엔드 최종 시간에 5xx error 및 시간 제한이 발생했을 수 있습니다. 

:::image type="content" source="media/diagnose-solve-problems/category-interactive-search-1.png" alt-text="대화형 인터페이스 검사에 대한 스크린샷 1":::



:::image type="content" source="media/diagnose-solve-problems/category-interactive-search-2.png" alt-text="대화형 인터페이스 검사에 대한 스크린샷 2":::

### <a name="api-policies"></a>API 정책

본 범주는 오류를 탐지하고 정책 문제를 알려 줍니다. 

유사한 대화형 인터페이스는 API 정책 구성 문제 해결을 도울 데이터 메트릭을 안내합니다.

:::image type="content" source="media/diagnose-solve-problems/proxy-policies.png" alt-text="API 정책 범주 타일 스크린샷":::

### <a name="gateway-performance"></a>게이트웨이 성능 

게이트웨이 요청이나 응답 또는 게이트웨이 상의 4xx 또는 5xx 오류의 경우, 모니터링과 문제 해결에 이 범주를 활용합니다. 마찬가지로, 대화형 인터페이스를 활용해 API 게이트웨이 성능을 위해 확인하려는 특정 영역을 자세히 살펴볼 수 있습니다. 

:::image type="content" source="media/diagnose-solve-problems/gateway-performance-tile.png" alt-text="게이트웨이 성능 범주 타일 스크린샷":::

### <a name="service-upgrade"></a>서비스 업그레이드

해당 범주는 현재 사용 중인 서비스 계층(SKU)이 무엇인지 확인하고 해당 계층과 관련성이 있을 수 있는 문제를 피하기 위한 업그레이드를 알려 줍니다. 동일한 대화형 인터페이스를 통해 더 많은 그래픽 및 요약 확인 결과를 심층적으로 확인할 수 있습니다. 

:::image type="content" source="media/diagnose-solve-problems/service-sku.png" alt-text="서비스 업그레이드 범주 타일 스크린샷":::

## <a name="search-documentation"></a>설명서 검색

문제 진단 및 해결 도구 이외에 문제와 관련된 문제 해결 설명서도 검색할 수 있습니다. 서비스에서 진단을 실행한 다음, 대화형 인터페이스에서  **검색** 을 선택합니다. 

 :::image type="content" source="media/diagnose-solve-problems/search-documentation.png" alt-text="설명서 검색 기능 사용 방법 스크린샷 1":::


 :::image type="content" source="media/diagnose-solve-problems/search-documentation-2.png" alt-text="설명서 검색 기능 사용 방법 스크린샷 2":::


## <a name="next-steps"></a>다음 단계

* 또한, 해당 API의 사용량 및 성능 분석을 위해 [API analytics](howto-use-analytics.md)를 사용합니다. 
* 진단을 통해 Web Apps 문제를 해결하실 건가요? 해당 문서는 [여기](../app-service/overview-diagnostics.md)에서 확인할 수 있습니다.
* 진단을 활용해 Azure Kubernetes Services 문제를 확인합니다. [AKS 진단](../aks/concepts-diagnostics.md)을 참조
* 제목에 "[Diag]"을 추가하여 [UserVoice](https://feedback.azure.com/d365community/forum/e808a70c-ff24-ec11-b6e6-000d3a4f0858)에 질문이나 피드백을 게시합니다.
