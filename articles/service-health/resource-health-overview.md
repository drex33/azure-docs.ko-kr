---
title: Azure Resource Health 개요
description: Azure Resource Health를 통해 Azure 리소스에 영향을 주는 서비스 문제를 진단하고 지원을 받는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: 532c7001b12efb2917c906f7607fef33bac194ff
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566426"
---
# <a name="resource-health-overview"></a>Resource Health 개요
 
Azure Resource Health를 통해 Azure 리소스에 영향을 주는 서비스 문제를 진단하고 지원을 받을 수 있습니다. Azure Resource Health는 리소스의 현재 및 과거 상태를 보고합니다.

[Azure 상태](https://status.azure.com)는 광범위한 Azure 고객 집합에 영향을 주는 서비스 문제를 보고합니다. Resource Health는 맞춤형 리소스 상태 대시보드를 제공합니다. Resource Health는 Azure 서비스 문제로 인해 리소스를 사용할 수 없었던 모든 시간을 보여 줍니다. 이 데이터를 통해 SLA 위반 여부를 쉽게 확인할 수 있습니다.

## <a name="resource-definition-and-health-assessment"></a>리소스 정의 및 상태 평가

‘리소스’는 가상 머신, 웹앱 또는 SQL Database와 같은 Azure 서비스의 특정 인스턴스입니다. Resource Health는 다양한 Azure 서비스의 신호를 사용하여 리소스의 정상 여부를 평가합니다. 리소스가 정상이 아니면 Resource Health에서 추가 정보를 분석하여 문제 원인을 파악합니다. 또한 Microsoft에서 문제 해결을 위해 수행하는 작업을 보고하고 사용자가 문제 해결을 위해 수행할 수 있는 작업을 식별합니다.

상태 평가 방법에 대한 자세한 내용은 [Azure Resource Health](resource-health-checks-resource-types.md)에서 리소스 종류 및 상태 검사 목록을 참조하세요.

## <a name="health-status"></a>상태

리소스 상태는 다음 상태 중 하나로 표시됩니다.

### <a name="available"></a>사용 가능

‘사용 가능’은 리소스의 상태에 영향을 주는 이벤트가 검색되지 않았음을 의미합니다. 리소스가 지난 24시간 동안 계획되지 않은 가동 중지 시간에서 복구된 경우 “최근에 해결됨” 알림이 표시됩니다.

![“최근에 해결됨” 알림이 있는 가상 머신의 *사용 가능* 상태](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Unavailable

‘사용할 수 없음’은 리소스의 상태에 영향을 주는 플랫폼 또는 비플랫폼 이벤트가 진행되고 있는 것이 서비스에서 검색되었음을 의미합니다.

#### <a name="platform-events"></a>플랫폼 이벤트

플랫폼 이벤트는 Azure 인프라의 여러 구성 요소에 의해 트리거됩니다. 예약된 작업(예: 계획된 유지 관리)과 예기치 않은 인시던트(예: 계획되지 않은 호스트 다시 부팅 또는 지정한 기간 후에 실패할 것으로 예상되는 저하된 호스트 하드웨어)가 모두 포함됩니다.

Resource Health는 이벤트 및 복구 프로세스에 대한 추가 세부 정보를 제공합니다. 활성 지원 계약이 없는 경우에도 Microsoft 지원에 문의할 수 있습니다.

![플랫폼 이벤트로 인한 가상 머신의 *사용할 수 없음* 상태](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>비-플랫폼 이벤트

비플랫폼 이벤트는 사용자 작업을 통해 트리거됩니다. 예를 들어 가상 머신 중지, Azure Cache for Redis에 대한 최대 연결 수 도달 등이 포함됩니다.

![비플랫폼 이벤트로 인한 가상 머신의 “사용할 수 없음” 상태](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>알 수 없음

‘알 수 없음’은 Resource Health가 리소스 관련 정보를 10분 넘게 받지 못했음을 의미합니다. 이는 일반적으로 가상 머신이 할당 해제된 경우에 발생합니다. 이 상태는 리소스 상태를 명확하게 표시하지는 않지만 문제 해결 시 중요한 데이터 요소입니다.

리소스가 예상한 대로 실행 중이면 몇 분 후 리소스 상태가 *사용 가능* 으로 변경됩니다.

리소스 문제가 발생하는 경우 성능 상태가 ‘알 수 없음’이면, 플랫폼의 이벤트가 리소스에 영향을 미치고 있음을 의미할 수 있습니다.

![가상 머신의 *알 수 없음* 상태](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>성능 저하됨

‘저하됨’은 리소스를 계속 사용할 수는 있지만 리소스에서 성능 저하가 검색되었음을 의미합니다.

리소스마다 성능 저하를 보고하는 시기에 대한 고유한 기준이 있습니다.

![가상 머신의 *저하됨* 상태](./media/resource-health-overview/degraded.png)

## <a name="history-information"></a>기록 정보

Resource Health의 **상태 기록** 섹션에서 최대 30일 동안의 기록에 액세스할 수 있습니다.

![지난 2주 동안의 Resource Health 이벤트 목록](./media/resource-health-overview/history-blade.png)

## <a name="root-cause-information"></a>근본 원인 정보

Azure에 플랫폼에서 시작된 사용 불가의 근본 원인에 대한 추가 정보가 있는 경우 해당 정보는 최초 사용 불가 후 최대 72시간까지 리소스 상태에 게시될 수 있습니다. 이 정보는 현재 가상 머신에만 사용할 수 있습니다. 

## <a name="get-started"></a>시작

하나의 리소스에 대한 Resource Health를 열려면:

1. Azure Portal에 로그인합니다.
2. 리소스를 찾습니다.
3. 왼쪽 창의 리소스 메뉴에서 **Resource Health** 를 선택합니다.

![리소스 뷰에서 Resource Health 열기](./media/resource-health-overview/from-resource-blade.png)

**모든 서비스** 를 선택하고 필터 텍스트 상자에 **Resource Health** 를 입력하여 Resource Health에 액세스할 수도 있습니다. **도움말 + 지원** 창에서 [Resource Health](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth)를 선택합니다.

!["모든 서비스"에서 Resource Health 열기](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>다음 단계

Resource Health에 대한 자세한 내용은 다음 참조를 확인하세요.
-  [Azure Resource Health에서 리소스 유형 및 상태 검사](resource-health-checks-resource-types.md)
-  [Azure Resource Health에 대한 질문과 대답](resource-health-faq.yml)
