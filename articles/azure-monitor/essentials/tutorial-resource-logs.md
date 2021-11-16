---
title: 자습서 - Azure 리소스에서 리소스 로그 수집
description: 리소스 로그를 Azure 리소스에서 로그 쿼리를 사용하여 분석할 수 있는 Log Analytics 작업 영역으로 보내도록 진단 설정을 구성하는 자습서입니다.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 11/08/2021
ms.openlocfilehash: 90a1f193bf1f136e4d3501b68bfab8346604fbbe
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297980"
---
# <a name="tutorial-collect-and-analyze-resource-logs-from-an-azure-resource"></a>자습서: Azure 리소스에서 리소스 로그 수집 및 분석
리소스 로그는 Azure 리소스의 자세한 작업에 대한 인사이트를 제공하며 작업의 상태 및 가용성을 모니터링하는 데 유용합니다. Azure 리소스는 리소스 로그를 자동으로 생성하지만, 진단 설정을 만들어 수집해야 합니다. 이 자습서에서는 로그 쿼리를 사용하여 분석할 수 있는 Log Analytics 작업 영역으로 리소스 로그를 보내기 위한 진단 설정을 만드는 과정을 안내합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Monitor에서 Log Analytics 작업 영역 만들기
> * 리소스 로그를 수집하는 진단 설정 만들기 
> * 로그를 분석하는 간단한 로그 쿼리 만들기


## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다. 

- 모니터링할 Azure 리소스입니다. 진단 설정을 지원하는 Azure 구독의 모든 리소스를 사용할 수 있습니다. 리소스가 진단 설정을 지원하는지 확인하려면 Azure Portal에서 리소스 메뉴로 이동한 후 메뉴의 **모니터링** 섹션에 **진단 설정** 옵션이 있는지 확인합니다.


> [!NOTE]
> **진단 설정** 메뉴가 진단 확장을 구성하는 데 사용되므로 이 절차는 Azure 가상 머신에 적용되지 않습니다.

## <a name="create-a-log-analytics-workspace"></a>Log Analytics 작업 영역 만들기
[!INCLUDE [Create workspace](../../../includes/azure-monitor-tutorial-workspace.md)]

## <a name="create-a-diagnostic-setting"></a>진단 설정 만들기
[진단 설정](../essentials/diagnostic-settings.md)은 특정 리소스에 대한 리소스 로그를 보낼 위치를 정의합니다. 진단 설정 하나에 여러 [대상](../essentials/diagnostic-settings.md#destinations)이 있을 수 있지만, 이 자습서에서는 Log Analytics 작업 영역만 사용합니다.

리소스 메뉴의 **모니터링** 섹션 아래에서 **진단 설정** 을 선택하고, **진단 설정 추가** 를 클릭합니다.

> [!NOTE]
> 일부 리소스는 추가로 선택해야 할 수도 있습니다. 예를 들어 스토리지 계정을 사용하려면 **진단 설정 추가** 옵션이 표시되기 전에 리소스를 선택해야 합니다. 또한 진단 설정이 현재 공개 미리 보기에 있으므로 일부 리소스에 대한 **미리 보기** 레이블이 표시될 수 있습니다.

:::image type="content" source="media/tutorial-resource-logs/diagnostic-settings.png" lightbox="media/tutorial-resource-logs/diagnostic-settings.png"alt-text="진단 설정":::


각 진단 설정은 다음과 같은 세 가지 기본 부분으로 구성되어 있습니다.
 
   - **Name**: 별 영향이 없으며 알기 쉬운 이름이면 됩니다.
   - **범주**: 각 대상으로 보낼 로그 범주입니다. 범주 세트는 Azure 서비스마다 다릅니다.
   - **대상**: 로그를 보낼 하나 이상의 대상입니다. 모든 Azure 서비스는 사용 가능한 동일한 대상 세트를 공유합니다. 각 진단 설정은 하나 이상의 대상을 정의할 수 있지만, 대상을 하나만 정의할 수 있는 유형도 있습니다. 

진단 설정에 대한 이름을 입력하고 수집하려는 범주를 선택합니다. 사용 가능한 범주 정의는 각 서비스의 설명서를 참조하세요. **AllMetrics** 는 리소스에 대해 Azure Monitor 메트릭에서 사용할 수 있는 동일한 플랫폼 메트릭을 작업 영역에 보냅니다. 이를 통해 다른 모니터링 데이터와 함께 로그 쿼리를 사용하여 이 데이터를 분석할 수 있습니다. **Log Analytics 작업 영역에 보내기** 를 선택하고, 앞에서 만든 작업 영역을 선택합니다. 

:::image type="content" source="media/tutorial-resource-logs/diagnostic-setting-details.png" lightbox="media/tutorial-resource-logs/diagnostic-setting-details.png"alt-text="진단 설정 세부 정보":::

**저장** 을 클릭하여 진단 설정을 저장합니다.

    
 
 ## <a name="use-a-log-query-to-retrieve-logs"></a>로그 쿼리를 사용하여 로그 검색
KQL(Kusto 쿼리 언어)로 작성된 로그 쿼리를 사용하여 Log Analytics 작업 영역에서 데이터가 검색됩니다. 여러 Azure 서비스에 대해 미리 만들어진 쿼리 세트를 사용할 수 있으므로 시작하는 데 KQL에 대한 지식이 필요하지 않습니다.

리소스 메뉴에서 **로그** 를 선택합니다. Log Analytics는 **리소스 종류** 에 대해 미리 작성된 쿼리가 포함된 **쿼리** 창과 함께 열립니다. 

> [!NOTE]
> **쿼리** 창이 열리지 않으면 오른쪽 위에서 **쿼리** 를 클릭합니다. 

:::image type="content" source="media/tutorial-resource-logs/queries.png" lightbox="media/tutorial-resource-logs/queries.png"alt-text="리소스 로그를 사용하는 샘플 쿼리를 보여 주는 스크린샷":::


사용 가능한 쿼리를 찾아봅니다. 실행할 항목을 식별하고 **실행** 을 클릭합니다. 쿼리가 쿼리 창에 추가되고 결과가 반환됩니다.

:::image type="content" source="media/tutorial-resource-logs/query-results.png" lightbox="media/tutorial-resource-logs/query-results.png"alt-text="샘플 로그 쿼리의 결과를 보여 주는 스크린샷":::

## <a name="next-steps"></a>다음 단계
이제 리소스 로그를 수집하고 있으므로 로그 데이터에서 흥미로운 데이터가 식별되면 사전에 알림을 보낼 로그 쿼리 경고를 만듭니다.

> [!div class="nextstepaction"]
> [Azure 리소스에 대한 로그 쿼리 경고 만들기](../alerts/tutorial-log-alert.md)
