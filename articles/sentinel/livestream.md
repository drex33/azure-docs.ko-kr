---
title: Microsoft Sentinel에서 헌팅 라이브 스트림을 사용하여 위협 탐지 | Microsoft Docs
description: 이 문서에서는 Microsoft Sentinel에서 Livestream 헌팅을 사용하여 데이터를 추적하는 방법을 설명합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.openlocfilehash: 22e3eafbbae9259ddea56e9927055da08236eedb
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520088"
---
# <a name="use-hunting-livestream-in-microsoft-sentinel-to-detect-threats"></a>Microsoft Sentinel에서 헌팅 라이브 스트림을 사용하여 위협 감지

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> - 리소스 간 쿼리 환경(아래에 표시된 항목 참조)은 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>

헌팅 라이브 스트림을 사용하여 이벤트가 발생할 때 새로 생성된 쿼리를 테스트하고, 일치 항목이 발견될 때 세션에서 알림을 받고, 필요한 경우 조사를 시작할 수 있는 대화형 세션을 만듭니다. Log Analytics 쿼리를 사용하여 라이브 스트림 세션을 신속하게 만들 수 있습니다.

- **이벤트가 발생하면 새로 생성된 쿼리 테스트**
    
    이벤트에 적극적으로 적용되는 현재 규칙에 대한 충돌 없이 쿼리를 테스트하고 조정할 수 있습니다. 이러한 새 쿼리가 예상대로 작동하는지 확인한 후에는 경고에 대한 세션을 상승시키는 옵션을 선택하여 사용자 지정 경고 규칙으로 승격하는 것이 쉽습니다.

- **위협이 발생하면 알림 받기**
    
    위협 데이터 피드를 집계된 로그 데이터와 비교하여 일치가 발생하면 알림을 받을 수 있습니다. 위협 데이터 피드는 잠재적 또는 현재 위협과 관련된 지속적인 데이터 스트림이므로 알림이 조직에 대한 잠재적 위협을 의미할 수 있습니다. 사용자 지정 경고 규칙을 유지하는 오버 헤드 없이 잠재적 문제에 대한 알림을 받으려면 사용자 지정 경고 규칙 대신 라이브 스트림 세션을 만듭니다.

- **조사 시작**
    
    호스트 또는 사용자와 같은 자산을 포함하는 활성 조사가 있는 경우 해당 자산에서 발생하는 로그 데이터의 특정(또는 임의) 활동을 볼 수 있습니다. 해당 작업이 발생하면 알림이 표시될 수 있습니다.


## <a name="create-a-livestream-session"></a>라이브 스트림 만들기

기존 헌팅 쿼리에서 라이브 스트림 세션을 만들거나 처음부터 세션을 만들 수 있습니다.

1. Azure Portal에서 **Sentinel** > **위협 관리** > **헌팅** 으로 이동합니다.

1. 헌팅 쿼리에서 라이브 스트림 세션을 만들려면 다음을 수행합니다.
    
    1. **쿼리** 탭에서 사용할 헌팅 쿼리를 찾습니다.
    1. 쿼리를 마우스 오른쪽 단추로 클릭하고 **라이브 스트림에 추가** 를 선택합니다. 예를 들면 다음과 같습니다.
    
    > [!div class="mx-imgBorder"]
    > ![Microsoft Sentinel 헌팅 쿼리에서 Livestream 세션 만들기](./media/livestream/livestream-from-query.png)

1. 라이브 스트림 세션을 처음부터 만들려면 다음을 수행합니다. 
    
    1. **라이브 스트림** 탭을 선택합니다
    1. **+ New 라이브 스트림** 을 클릭합니다.
    
1. **Livestream** 창에서 다음을 수행합니다.
    
    - 쿼리에서 라이브 스트림을 시작한 경우 쿼리를 검토하고 원하는 변경 작업을 수행합니다.
    - 라이브 스트림을 처음부터 시작했으면 쿼리를 만듭니다.

    > [!NOTE]
    > Livestream은 Azure Data Explorer의 데이터 **리소스 간 쿼리**(미리 보기)를 지원합니다. [**리소스 간 쿼리에 대해 자세히 알아보세요**](../azure-monitor/logs/azure-monitor-data-explorer-proxy.md#cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer).

1. 명령 모음에서 **재생** 을 선택합니다.
    
    명령 모음 아래의 상태 표시줄에는 라이브 스트림 세션이 실행 중인지 아니면 일시 중지되었는지 여부가 표시됩니다. 다음 예제에서는 세션이 실행됩니다.
    
    > [!div class="mx-imgBorder"]
    > ![Microsoft Sentinel 헌팅에서 라이브 스트림 세션 만들기](./media/livestream/livestream-session.png)

1. 명령 모음에서 **저장** 을 선택합니다.
    
    **일시 중지** 를 선택하지 않는 한 세션은 Azure Portal에서 로그아웃 될 때까지 계속 실행됩니다.

## <a name="view-your-livestream-sessions"></a>라이브 스트림 세션 보기

1. Azure Portal에서 **Sentinel** > **위협 관리** > **헌팅** > **라이브 스트림** 탭으로 이동합니다.

1. 보거나 편집하길 원하는 라이브 스트림 세션을 선택합니다. 예를 들면 다음과 같습니다.
    
    > [!div class="mx-imgBorder"]
    > ![Microsoft Sentinel 헌팅 쿼리에서 라이브 스트림 세션 만들기](./media/livestream/livestream-tab.png)
    
    선택한 라이브 스트림 세션이 재생, 일시 중지, 편집 등으로 열립니다.

## <a name="receive-notifications-when-new-events-occur"></a>새 이벤트가 발생할 때 알림 받기

새 이벤트에 대한 라이브 스트림 알림은 Azure Portal 알림을 사용하기 때문에 Azure Portal를 사용할 때마다 이러한 알림이 표시됩니다. 예를 들면 다음과 같습니다.

![라이브 스트림에 대한 Azure Portal 알림](./media/livestream/notification.png)

알림을 선택하여 **라이브 스트림** 창을 엽니다.
 
## <a name="elevate-a-livestream-session-to-an-alert"></a>라이브 스트림 세션을 경고로 상승

관련 라이브 스트림 세션의 명령 모음에서 **경고로 상승을** 선택하여 라이브 스트림 세션을 새 경고로 승격할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![라이브 스트림 세션을 경고로 상승](./media/livestream/elevate-to-alert.png)

이 작업을 수행하면 라이브 스트림 세션과 연결된 쿼리로 미리 채워져 있는 규칙 만들기 마법사가 열립니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft Sentinel에서 헌팅 라이브 스트림을 사용하는 방법을 배웠습니다. Microsoft Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [위협 요소를 사전에 헌팅하기](hunting.md)
- [Notebook을 사용하여 자동 헌팅 캠페인 실행](notebooks.md)
