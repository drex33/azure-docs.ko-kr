---
title: 자습서 - Azure 리소스에 대한 로그 쿼리 경고 만들기
description: Azure 리소스에 대한 로그 쿼리 경고를 만드는 자습서입니다.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 09/16/2021
ms.openlocfilehash: 526abdb9a5e3c16fe58551c2aa8784caf329d1c2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350926"
---
# <a name="tutorial-create-a-log-query-alert-for-an-azure-resource"></a>자습서: Azure 리소스에 대한 로그 쿼리 경고 만들기
Azure Monitor 경고는 모니터링 데이터에서 중요한 조건이 발견될 때 사용자에게 사전에 알립니다. 로그 쿼리에서 특정 결과를 반환하면 로그 쿼리 경고 규칙에서 경고를 만듭니다. 예를 들어 가상 머신에서 특정 이벤트가 만들어지면 경고를 받거나 스토리지 계정에 과도한 익명 요청이 있으면 경고를 보냅니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 다양한 종류의 리소스에 대한 경고 규칙을 지원하도록 설계된 미리 작성된 로그 쿼리에 액세스
> * 로그 쿼리 경고 규칙 만들기
> * 경고 세부 정보를 정의하는 작업 그룹 만들기


## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음이 필요합니다. 

- 모니터링할 Azure 리소스입니다. 진단 설정을 지원하는 Azure 구독의 모든 리소스를 사용할 수 있습니다. 리소스가 진단 설정을 지원하는지 확인하려면 Azure Portal에서 리소스 메뉴로 이동한 후 메뉴의 **모니터링** 섹션에 **진단 설정** 옵션이 있는지 확인합니다.


가상 머신 이외의 Azure 리소스를 사용하는 경우:

- 리소스 로그를 Azure 리소스에서 Log Analytics 작업 영역으로 보내는 진단 설정이 필요합니다. [자습서: Azure Monitor에서 Log Analytics 작업 영역 만들기](../essentials/tutorial-resource-logs.md)를 참조하세요.

Azure 가상 머신을 사용하는 경우:

- 게스트 로그 및 메트릭을 Log Analytics 작업 영역으로 보내는 데이터 수집 규칙이 필요합니다. [자습서: Azure 가상 머신에서 게스트 로그 및 메트릭 수집](../vm/tutorial-monitor-vm-guest.md)을 참조하세요.

   
 
 ## <a name="select-a-log-query-and-verify-results"></a>로그 쿼리 선택 및 결과 확인
KQL(Kusto 쿼리 언어)로 작성된 로그 쿼리를 사용하여 Log Analytics 작업 영역에서 데이터가 검색됩니다. Azure Monitor의 인사이트 및 솔루션은 특정 서비스에 대한 데이터를 검색하는 로그 쿼리를 제공하지만, Azure Portal에서 Log Analytics를 사용하여 로그 쿼리 및 해당 결과를 직접 작업할 수 있습니다. 

리소스 메뉴에서 **로그** 를 선택합니다. Log Analytics는 **리소스 종류** 에 대해 미리 작성된 쿼리가 포함된 **쿼리** 창과 함께 열립니다. **경고** 를 선택하여 경고 규칙을 위해 특별히 설계된 쿼리를 확인합니다.

> [!NOTE]
> **쿼리** 창이 열리지 않으면 오른쪽 위에서 **쿼리** 를 클릭합니다. 

:::image type="content" source="media/tutorial-log-alert/queries.png" lightbox="media/tutorial-log-alert/queries.png"alt-text="쿼리 창이 있는 Log Analytics":::

쿼리를 선택하고, **실행** 을 클릭하여 쿼리 편집기에서 쿼리를 로드하고 결과를 반환합니다. 쿼리는 수정하고 다시 실행할 수 있습니다. 예를 들어 스토리지 계정에 대한 **익명 요청 표시** 쿼리는 아래와 같습니다. **AuthenticationType** 을 수정하거나 다른 열을 필터링할 수 있습니다.

:::image type="content" source="media/tutorial-log-alert/query-results.png" lightbox="media/tutorial-log-alert/query-results.png"alt-text="쿼리 결과":::


## <a name="create-alert-rule"></a>경고 규칙 만들기
쿼리가 확인되면 경고 규칙을 만들 수 있습니다. **새 경고 규칙** 을 선택하여 현재 로그 쿼리를 기반으로 하는 새 경고 규칙을 만듭니다. **범위** 는 이미 현재 리소스로 설정됩니다. 이 값을 변경할 필요가 없습니다.

:::image type="content" source="media/tutorial-log-alert/create-alert-rule.png" lightbox="media/tutorial-log-alert/create-alert-rule.png"alt-text="경고 규칙 만들기":::
## <a name="configure-condition"></a>조건 구성

**조건** 탭에서 **로그 쿼리** 가 이미 채워져 있습니다. **측정** 섹션은 로그 쿼리의 레코드를 측정하는 방법을 정의합니다. 쿼리에서 요약을 수행하지 않는 경우 유일한 옵션은 **테이블 행** 수를 **계산** 하는 것입니다. 쿼리에 하나 이상의 요약된 열이 포함된 경우 **테이블 행** 수를 사용하거나 요약된 열을 기반으로 하는 계산을 사용할 수 있습니다. **집계 세분성** 은 수집된 값이 집계되는 시간 간격을 정의합니다. 

:::image type="content" source="media/tutorial-log-alert/alert-rule-condition.png" lightbox="media/tutorial-log-alert/alert-rule-condition.png"alt-text="경고 규칙 조건":::

### <a name="configure-dimensions"></a>차원 구성
**차원으로 분할** 을 사용하면 다양한 리소스에 대해 별도의 경고를 만들 수 있습니다. 이 설정은 여러 리소스에 적용되는 경고 규칙을 만들 때 유용합니다. 범위를 단일 리소스로 설정하면 이 설정은 일반적으로 사용되지 않습니다.

:::image type="content" source="media/tutorial-log-alert/alert-rule-dimensions.png" lightbox="media/tutorial-log-alert/alert-rule-dimensions.png"alt-text="경고 규칙 차원":::


## <a name="configure-alert-logic"></a>경고 논리 구성
경고 논리에서 **연산자** 및 **임계값** 을 구성하여 측정에서 반환된 값과 비교합니다.  이 값이 true이면 경고가 만들어집니다. 로그 쿼리가 실행되고 평가되는 빈도를 정의하는 **평가 빈도** 값을 선택합니다. 빈도가 낮을수록 경고 규칙의 비용이 증가합니다. 빈도를 선택하면 일정 기간 동안의 쿼리 결과 미리 보기와 함께 월별 예상 비용이 표시됩니다.

예를 들어 측정값이 **테이블 행** 수이면 경고 논리는 **0보다 큼** 일 수 있으며, 이는 하나 이상의 레코드가 반환되었음을 나타냅니다. 측정값이 열 값이면 논리가 특정 임계값보다 크거나 작아야 할 수 있습니다. 아래 예에서 로그 쿼리는 스토리지 계정에 대한 익명 요청을 찾고 있습니다. 익명 요청이 수행된 경우 경고를 트리거해야 합니다. 이 경우 반환된 단일 행에서 경고를 트리거하므로 경고 논리는 **0보다 큼** 이어야 합니다.

:::image type="content" source="media/tutorial-log-alert/alert-rule-alert-logic.png" lightbox="media/tutorial-log-alert/alert-rule-alert-logic.png"alt-text="경고 논리":::



## <a name="configure-actions"></a>작업 구성
[!INCLUDE [Action groups](../../../includes/azure-monitor-tutorial-action-group.md)]

## <a name="configure-details"></a>세부 정보 구성
[!INCLUDE [Alert details](../../../includes/azure-monitor-tutorial-alert-details.md)]

:::image type="content" source="media/tutorial-log-alert/alert-rule-details.png" lightbox="media/tutorial-log-alert/alert-rule-details.png"alt-text="경고 규칙 세부 정보":::

**경고 규칙 만들기** 를 클릭하여 경고 규칙을 만듭니다.

## <a name="view-the-alert"></a>경고 보기
[!INCLUDE [View alert](../../../includes/azure-monitor-tutorial-view-alert.md)]


## <a name="next-steps"></a>다음 단계
이제 Azure 리소스에 대한 로그 쿼리 경고를 만드는 방법을 알아보았으므로 모니터링 데이터에 대한 대화형 시각화를 만들기 위한 통합 문서를 살펴보세요.

> [!div class="nextstepaction"]
> [Azure Monitor 통합 문서](../visualize/workbooks-overview.md)