---
title: 자습서 - Azure Monitor에서 메트릭 차트 만들기
description: Azure 메트릭 탐색기를 사용하여 메트릭 차트를 만드는 방법을 알아봅니다.
author: bwren
ms.author: bwren
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 029202b1b05c43c29f0011d01d63cc5d5c5f029f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128649703"
---
# <a name="tutorial-create-a-metrics-chart-in-azure-monitor"></a>자습서: Azure Monitor에서 메트릭 차트 만들기
메트릭 탐색기는 메트릭 값으로 차트를 만들고, 추세를 시각적으로 상호 연결하고, 메트릭 값의 급증 및 급락을 조사할 수 있는 Azure Portal의 Azure Monitor 기능입니다. 메트릭 탐색기를 사용하여 Azure 리소스의 상태 및 사용률을 조사하거나 사용자 지정 메트릭에서 차트를 그릴 수 있습니다. 

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 차트를 그릴 메트릭 선택
> * 메트릭 값을 다양한 방법으로 집계
> * 차트의 시간 범위 및 세분성 수정

다음은 이 문서에서 설명하는 절차보다 더 광범위한 시나리오를 보여 주는 비디오입니다. 메트릭을 처음 접하는 경우 이 문서를 먼저 읽은 다음, 비디오를 확인하여 자세한 내용을 확인하는 것이 좋습니다. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4qO59]

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 모니터링할 Azure 리소스가 필요합니다. 메트릭을 지원하는 Azure 구독의 모든 리소스를 사용할 수 있습니다. 리소스가 메트릭을 지원하는지 확인하려면 Azure Portal에서 리소스 메뉴로 이동한 후 해당 메뉴의 **모니터링** 섹션에 **메트릭** 옵션이 있는지 확인합니다.


## <a name="log-in-to-azure"></a>Azure에 로그인
Azure Portal([https://portal.azure.com](https://portal.azure.com))에 로그인합니다.

## <a name="open-metrics-explorer-and-select-a-scope"></a>메트릭 탐색기를 열고 범위 선택
Azure Monitor 메뉴 또는 Azure Portal의 리소스 메뉴에서 메트릭 탐색기를 열 수 있습니다. 사용하는 옵션에 관계없이 모든 리소스의 메트릭을 사용할 수 있습니다. 

1. **Azure Monitor** 메뉴 또는 리소스 메뉴의 **모니터링** 섹션에서 **메트릭** 를 선택합니다.

1. 메트릭을 보려는 리소스인 **범위** 를 선택합니다. 리소스의 메뉴에서 메트릭 탐색기를 연 경우 범위가 이미 채워져 있습니다. 리소스 범위 선택기의 다양한 기능에 대해 자세히 알아보려면 [이 문서](../essentials/metrics-charts.md#resource-scope-picker)를 방문하세요.

    ![범위 선택](media/tutorial-metrics-explorer/scope-picker.png)

2. 범위에 네임스페이스가 여러 개 있는 경우 **네임스페이스** 를 선택합니다. 네임스페이스는 메트릭을 쉽게 찾을 수 있도록 구성하는 방법일 뿐입니다. 예를 들어 스토리지 계정에는 파일, 테이블, Blob 및 큐 메트릭을 저장하기 위한 별도의 네임스페이스가 있습니다. 여러 리소스 유형 중 대부분은 네임스페이스가 하나만 있습니다.

3. 선택한 범위와 네임스페이스에 사용 가능한 메트릭 목록에서 메트릭을 선택합니다.

    ![메트릭 선택](media/tutorial-metrics-explorer/metric-picker.png)

4. 필요에 따라 메트릭 **집계** 를 변경합니다. 집계는 그래프의 시간 세분성에서 메트릭 값을 집계하는 방법을 정의합니다. 예를 들어 시간 세분성을 15분으로 설정하고 집계를 합계로 설정하면 그래프의 각 지점은 각각의 15분 세그먼트 동안 수집된 모든 값의 합계가 됩니다.

    ![스크린샷은 contosoretailweb에 대한 Sum Ingress라는 제목의 차트를 표시합니다.](media/tutorial-metrics-explorer/chart.png)

5. 동일한 차트에 여러 메트릭을 표시하려면 **메트릭 추가** 단추를 사용하여 이 단계를 반복합니다. 한 보기에서 여러 차트를 보려면 **새 차트** 단추를 선택합니다.

## <a name="select-a-time-range-and-granularity"></a>시간 범위 및 세분성 선택

기본적으로 차트는 최근 24시간 동안의 메트릭 데이터를 표시합니다. 시간 선택기를 사용하여 차트의 **시간 범위** 또는 각 데이터 요소의 시간 범위를 정의하는 **시간 세분성** 을 변경할 수 있습니다. 차트는 지정된 집계를 사용하여 지정된 시간 세분성에서 샘플링된 모든 값을 계산합니다.

![시간 범위 변경 패널](media/tutorial-metrics-explorer/time-picker.png)


**시간 브러시** 를 사용하여 급증 또는 급락처럼 차트의 흥미로운 영역을 조사할 수 있습니다. 영역의 시작 부분에 마우스 포인터를 놓고 마우스 왼쪽 단추를 누른 채 영역의 반대쪽으로 끈 후 단추를 놓습니다. 그러면 해당 시간 범위에서 차트가 확대됩니다. 

![시간 브러시](media/tutorial-metrics-explorer/time-brush.png)

## <a name="apply-dimension-filters-and-splitting"></a>차원 필터 및 분할 적용
다음 자료를 참조하여 메트릭에 대한 추가 분석을 수행하고 데이터의 잠재적 이상값을 식별할 수 있는 고급 기능을 알아보세요.

- [필터링](../essentials/metrics-charts.md#filters)을 사용하여 차트에 포함할 차원 값을 선택할 수 있습니다. 예를 들어 *서버 응답 시간* 메트릭을 차트에 표시할 때 성공한 요청만 표시할 수 있습니다. 

- [분할](../essentials/metrics-charts.md#apply-splitting)은 차원의 각 값에 대한 별도의 줄을 차트에 표시할 것인지 아니면 값을 한 줄로 집계할 것인지 여부를 제어합니다. 예를 들어 모든 서버 인스턴스의 평균 응답 시간을 한 줄로 표시할 수도 있고 서버마다 별도의 줄을 표시할 수도 있습니다. 

필터링 및 분할이 적용된 차트는 [차트 예제](../essentials/metric-chart-samples.md)를 참조하세요.

## <a name="advanced-chart-settings"></a>고급 차트 설정

차트 스타일 및 제목을 사용자 지정하고 고급 차트 설정을 수정할 수 있습니다. 사용자 지정을 마쳤으면 대시보드에 고정하여 작업을 저장합니다. 메트릭 경고를 구성할 수도 있습니다. Azure Monitor 메트릭 탐색기의 이러한 기능과 기타 고급 기능에 대한 자세한 내용은 [Azure 메트릭 탐색기의 고급 기능](../essentials/metrics-charts.md#locking-the-range-of-the-y-axis)을 참조하세요.


## <a name="next-steps"></a>다음 단계
Azure Monitor에서 메트릭을 사용하는 방법을 배웠으므로, 이제 메트릭을 사용하여 사전 경고를 보내는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [Azure Monitor를 사용하여 메트릭 경고 만들기, 보기 및 관리](../essentials/metrics-charts.md#alert-rules)

