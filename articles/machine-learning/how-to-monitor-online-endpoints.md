---
title: 관리형 온라인 엔드포인트 모니터링(미리 보기)
titleSuffix: Azure Machine Learning
description: Application Insights를 사용하여 관리형 온라인 엔드포인트를 모니터링하고 경고를 만듭니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 05/03/2021
ms.topic: conceptual
ms.custom: how-to, devplatv2
ms.openlocfilehash: c6b370d568b7448ae6417f050cbc902378571151
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128548168"
---
# <a name="monitor-managed-online-endpoints-preview"></a>관리형 온라인 엔드포인트 모니터링(미리 보기)

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

이 문서에서는 [Azure Machine Learning 관리형 온라인 엔드포인트(미리 보기)](concept-endpoints.md)를 모니터링하는 방법을 알아봅니다. Application Insights를 사용하여 메트릭을 보고 경고를 만들어 관리형 온라인 엔드포인트를 최신 상태로 유지합니다.

이 문서에서는 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * 관리형 온라인 엔드포인트에 대한 메트릭 보기
> * 메트릭에 대한 대시보드 만들기
> * 메트릭 경고 만들기

## <a name="prerequisites"></a>필수 구성 요소

- Azure Machine Learning 관리형 온라인 엔드포인트(미리 보기)를 배포합니다.
- 엔드포인트에 대한 [읽기 권한](../role-based-access-control/role-assignments-portal.md) 이상의 권한이 있어야 합니다.

## <a name="view-metrics"></a>메트릭 보기

다음 단계를 사용하여 관리형 엔드포인트 또는 배포에 대한 메트릭을 볼 수 있습니다.
1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. 관리형 온라인 엔드포인트 또는 배포 리소스로 이동합니다.

    관리형 온라인 엔드포인트 및 배포는 소유한 리소스 그룹으로 이동하여 찾을 수 있는 ARM(Azure Resource Manager) 리소스입니다. **Machine Learning 온라인 엔드포인트** 및 **Machine Learning 온라인 배포** 리소스 유형을 찾습니다.

1. 왼쪽 열에서 **메트릭** 을 선택합니다.

## <a name="available-metrics"></a>사용 가능한 메트릭

선택한 리소스에 따라 표시되는 메트릭은 다를 수 있습니다. 메트릭은 관리형 온라인 엔드포인트와 관리형 온라인 배포(미리 보기)에 대해 범위가 다르게 지정됩니다.

### <a name="metrics-at-endpoint-scope"></a>엔드포인트 범위의 메트릭

- 요청 대기 시간
- 요청 대기 시간 P50(50번째 백분위수의 요청 대기 시간)
- 요청 대기 시간 P90(90번째 백분위수의 요청 대기 시간)
- 요청 대기 시간 P95(95번째 백분위수의 요청 대기 시간)
- 분당 요청

다음 차원으로 분할합니다.

- 배포
- 상태 코드
- 상태 코드 클래스

### <a name="metrics-at-deployment-scope"></a>배포 범위의 메트릭

- CPU 사용률
- 배포 용량(요청된 인스턴스 유형의 인스턴스 수)
- 디스크 사용률
- GPU 메모리 사용률(GPU 인스턴스에만 적용)
- GPU 사용률(GPU 인스턴스에만 적용)
- 메모리 사용량

다음 차원의 Slit:

- InstanceId

## <a name="create-a-dashboard"></a>대시보드 만들기

사용자 지정 대시보드를 만들어 관리형 온라인 엔드포인트에 대한 메트릭을 포함하여 Azure Portal의 여러 원본에서 데이터를 시각화할 수 있습니다. 자세한 내용은 [Application Insights를 사용하여 사용자 지정 KPI 대시보드 만들기](../azure-monitor/app/tutorial-app-dashboards.md#add-custom-metric-chart)를 참조하세요.
    
## <a name="create-an-alert"></a>경고 만들기

관리형 온라인 엔드포인트에 대한 중요한 상태 업데이트를 알리는 사용자 지정 경고를 만들 수도 있습니다.

1. 메트릭 페이지의 오른쪽 위에서 **새 경고 규칙** 을 선택합니다.

    :::image type="content" source="./media/how-to-monitor-online-endpoints/online-endpoints-new-alert-rule.png" alt-text="온라인 엔드포인트 모니터링: 빨간색 상자로 둘러싸인 '새 경고 규칙' 단추를 보여주는 스크린샷":::

1. 경고가 트리거되어야 하는 시기를 지정할 조건 이름을 선택합니다.

    :::image type="content" source="./media/how-to-monitor-online-endpoints/online-endpoints-configure-signal-logic.png" alt-text="온라인 엔드포인트 모니터링: 빨간색 상자로 둘러싸인 '신호 논리 구성' 단추를 보여주는 스크린샷":::

1. **작업 그룹 추가** > **작업 그룹 만들기** 를 선택하여 경고가 트리거될 때 발생하는 작업을 지정합니다.

1. **경고 규칙 만들기** 를 선택하여 경고 만들기를 완료합니다.


## <a name="next-steps"></a>다음 단계

* [배포된 엔드포인트에 대한 비용을 확인](./how-to-view-online-endpoints-costs.md)하는 방법을 알아봅니다.
* [메트릭 탐색기](../azure-monitor/essentials/metrics-charts.md)에 대해 자세히 알아봅니다.