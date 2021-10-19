---
title: Azure Virtual Desktop 자동 크기 조정 기능에 대한 진단 설정
description: Azure Virtual Desktop 배포에서 크기 조정 서비스에 대한 진단 보고서를 설정하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 10/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: b5621d829050c6749795df1191ea53d835e16487
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130182368"
---
# <a name="set-up-diagnostics-for-the-autoscale-feature-preview"></a>자동 크기 조정 기능에 대한 진단 설정(미리 보기)

> [!IMPORTANT]
> 자동 크기 조정 기능은 현재 미리 보기로 제공됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

진단을 사용하면 잠재적인 문제를 모니터링하고 자동 크기 조정(미리 보기) 크기 조정 계획을 방해하기 전에 해결할 수 있습니다.

현재 자동 크기 조정 기능에 대한 진단 로그를 Azure Storage 계정에 보내거나 이벤트 허브에서 로그를 사용할 수 있습니다. Azure Storage 계정을 사용하는 경우 크기 조정 계획과 동일한 지역에 있는지 확인합니다. 진단 설정 만들기에서 [진단 설정에](../azure-monitor/essentials/diagnostic-settings.md)대해 자세히 알아보세요. 리소스 로그 데이터 수집 시간에 대한 자세한 내용은 [Azure Monitor 로그 데이터 수집 시간을](../azure-monitor/logs/data-ingestion-time.md)참조하세요.

## <a name="enable-diagnostics-for-scaling-plans"></a>크기 조정 계획에 대한 진단 사용

크기 조정 계획에 대한 진단을 사용하도록 설정하려면 다음을 수행합니다.

1. [https://portal.azure.com](https://portal.azure.com/)에서 Azure Portal에 로그인합니다.

2. **크기 조정 계획 을** 선택한 다음, 보고서에서 추적할 크기 조정 계획을 선택합니다.

3. 진단 **설정** 이동하여 **진단 설정 추가를** 선택합니다.

4. 진단 설정의 이름을 입력합니다.

5. 다음으로 **자동 크기 조정을** 선택하고 보고서를 보낼 위치에 따라 **스토리지 계정** 또는 **이벤트 허브를** 선택합니다.

6. **저장** 을 선택합니다.

## <a name="set-log-location-in-azure-storage"></a>Azure Storage 로그 위치 설정

진단 설정을 구성한 후 다음 지침에 따라 로그를 찾을 수 있습니다.

1. Azure Portal 진단 로그를 보낸 스토리지 그룹으로 이동합니다.

2. **컨테이너** 를 선택합니다. **insight-logs-autoscaling이라는 폴더가** 열립니다.

3. **insight-logs-autoscaling 폴더를** 선택하고 검토하려는 로그를 엽니다. JSON 파일이 표시될 때까지 해당 폴더 내의 폴더를 연 다음, 해당 폴더의 모든 항목을 선택하고 마우스 오른쪽 단추를 클릭하여 로컬 컴퓨터에 다운로드합니다.

4. 마지막으로, 선택한 텍스트 편집기에서 JSON 파일을 엽니다.

## <a name="view-diagnostic-logs"></a>진단 로그 보기

JSON 파일을 열었으므로 보고서의 각 부분에 대한 간략한 개요를 살펴보겠습니다.

- **CorrelationID는** 지원 사례를 만들 때 표시해야 하는 ID입니다.

- **OperationName은** 문제가 발생하는 동안 실행되는 작업의 유형입니다.

- **ResultType은** 작업의 결과입니다. 불완전한 결과가 발견되면 이 항목에서 문제가 있는 위치를 표시할 수 있습니다.

- **메시지는** 불완전한 작업에 대한 정보를 제공하는 오류 메시지입니다. 이 메시지에는 중요한 문제 해결 설명서에 대한 링크가 포함될 수 있으므로 신중하게 검토하세요.

다음 JSON 파일은 보고서를 열 때 표시되는 내용의 예입니다.

```json
{
    "host_Ring": "R0",
    "Level": 4,
    "ActivityId": "c1111111-1111-1111-b111-11111cd1ba1b1",
    "time": "2021-08-31T16:00:46.5246835Z",
    "resourceId": "/SUBSCRIPTIONS/AD11111A-1C21-1CF1-A7DE-CB1111E1D111/RESOURCEGROUPS/TEST/PROVIDERS/MICROSOFT.DESKTOPVIRTUALIZATION/SCALINGPLANS/TESTPLAN",
    "operationName": "HostPoolLoadBalancerTypeUpdated",
    "category": "Autoscale",
    "resultType": "Succeeded",
    "level": "Informational",
    "correlationId": "35ec619b-b5d8-5b5f-9242-824aa4d2b878",
    "properties": {
        "Message": "Host pool's load balancing algorithm updated",
        "HostPoolArmPath": "/subscriptions/AD11111A-1C21-1CF1-A7DE-CB1111E1D111/resourcegroups/test/providers/microsoft.desktopvirtualization/hostpools/testHostPool ",
        "PreviousLoadBalancerType": "BreadthFirst",
        "NewLoadBalancerType": "DepthFirst"
    }
}. L
```

## <a name="next-steps"></a>다음 단계

- Azure Virtual Desktop 세션 호스트에 대한 자동 크기 조정에서 크기 조정 계획을 만드는 방법을 [검토합니다.](autoscale-scaling-plan.md)
- [새 호스트 풀 또는 기존 호스트 풀에 크기 조정 계획을 할당합니다.](autoscale-new-existing-host-pool.md)
