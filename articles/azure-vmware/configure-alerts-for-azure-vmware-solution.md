---
title: Azure VMware Solution의 경고 구성 및 메트릭 관련 작업
description: 경고를 사용하여 알림을 받는 방법에 대해 알아봅니다. 또한 Azure VMware Solution 프라이빗 클라우드에 대한 더 깊은 인사이트를 얻기 위한 메트릭 관련 작업 방법에 대해서도 알아봅니다.
ms.topic: how-to
ms.date: 07/23/2021
ms.openlocfilehash: 718838d5335ff10200fc41029a6431a96552894b
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114653579"
---
# <a name="configure-azure-alerts-in-azure-vmware-solution"></a>Azure VMware Solution에서 Azure 경고 구성 

이 문서에서는 [Microsoft Azure 알림](../azure-monitor/alerts/alerts-overview.md)에서 정의한 트리거 이벤트에 대한 알림을 받도록 [Azure 작업 그룹](../azure-monitor/alerts/action-groups.md)을 구성하는 방법에 대해 알아봅니다. 또한 [Azure Monitor 메트릭](../azure-monitor/essentials/data-platform-metrics.md)을 사용하여 Azure VMware Solution 프라이빗 클라우드에 대한 더 깊은 인사이트를 얻는 방법에 대해서도 배울 수 있습니다.

>[!NOTE]
>Azure VMware Solution 호스트와 해당 복원의 가용성에 영향을 주는 인시던트는 Azure VMware Solution 프라이빗 클라우드를 포함하는 구독의 계정 관리자, 서비스 관리자(클래식 권한),공동 관리자(클래식 권한), 소유자(RBAC 역할)에게 자동으로 전송됩니다.

## <a name="supported-metrics-and-activities"></a>지원되는 메트릭 및 활동

다음 메트릭은 Azure Monitor 메트릭을 통해서 볼 수 있습니다.

| **신호 이름**                                                         | **신호 유형** | **서비스 모니터링** |
|-------------------------------------------------------------------------|-----------------|---------------------|
| 데이터 저장소 디스크 총 용량                                           | 메트릭          | 플랫폼            |
| 사용된 데이터 저장소 디스크 백분율                                          | 메트릭          | 플랫폼            |
| CPU 사용률                                                          | 메트릭          | 플랫폼            |
| 평균 유효 메모리                                                | 메트릭          | 플랫폼            |
| 평균 메모리 오버헤드                                                 | 메트릭          | 플랫폼            |
| 평균 총 메모리                                                    | 메트릭          | 플랫폼            |
| 평균 메모리 사용량                                                    | 메트릭          | 플랫폼            |
| 사용된 데이터 저장소 디스크                                                     | 메트릭          | 플랫폼            |
| 모든 관리 작업                                           | 활동 로그    | 관리      |
| Microsoft.AVS 리소스 공급자를 등록합니다. (Microsoft.AVS/privateClouds) | 활동 로그    | 관리      |
| PrivateCloud를 만들거나 업데이트합니다. (Microsoft.AVS/privateClouds)          | 활동 로그    | 관리      |
| PrivateCloud를 삭제합니다. (Microsoft.AVS/privateClouds)                    | 활동 로그    | 관리      |

## <a name="configure-an-alert-rule"></a>알림 규칙 구성
1. Azure VMware Solution 프라이빗 클라우드에서 **모니터링** > **경고**, **새 경고 규칙** 을 차례로 선택합니다.
 
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-new-alert-rule.png" alt-text="Azure VMware Solution 프라이빗 클라우드에서 경고 규칙을 구성하는 위치를 보여주는 스크린샷." lightbox="media/configure-alerts-for-azure-vmware-solution/create-new-alert-rule.png":::

   다음을 수행할 수 있는 새 구성 화면이 열립니다.
   - 범위 정의
   - 조건 구성
   - 작업 그룹 설정
   - 경고 규칙 세부 정보 정의
    
   :::image type="content" source="../devtest-labs/media/activity-logs/create-alert-rule-done.png" alt-text="경고 규칙 만들기 창 스크린샷" lightbox="../devtest-labs/media/activity-logs/create-alert-rule-done.png":::

1. **범위** 에서 모니터링하려는 대상 리소스를 선택합니다. 기본적으로 경고 메뉴를 연 Azure VMware Solution 프라이빗 클라우드가 정의되어 있습니다.

1. **조건** 에서 **조건 추가** 를 선택하고 열리는 창에서 경고 규칙에 대해 만들려는 신호를 선택합니다. 

   이 예제에서는 [Azure VMware Solution SLA](https://aka.ms/avs/sla) 관점과 관련된 **사용된 데이터 저장소 디스크 백분율** 을 선택했습니다. 

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/configure-signal-logic-options.png" alt-text="경고 규칙에 대해 만들려는 신호가 있는 신호 논리 구성 창 스크린샷"::: 

1. 경고를 트리거하는 논리를 정의하고 **완료** 를 선택합니다. 

   이 예제에서는 평가의 **임계값** 및 **빈도** 만 조정되었습니다. 
   
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/define-alert-logic-threshold.png" alt-text="신호 경고 논리의 임계값, 연산자, 집계 유형 및 세분성, 평가 빈도 스크린샷"::: 

1. **작업** 에서 **작업 그룹 추가** 를 선택합니다. 작업 그룹은 알림을 받는 *방법* 및 알림을 받는 *대상* 을 정의합니다.   이메일, SMS, [Azure 모바일 앱 푸시 알림](https://azure.microsoft.com/features/azure-portal/mobile-app/) 또는 음성 메시지를 통해 알림을 받을 수 있습니다.

1. 기존 작업 그룹을 선택하거나 **작업 그룹 만들기** 를 선택하여 새로 만듭니다.
 
1. 열리는 창의 **기본** 탭에서 작업 그룹에 이름 및 표시 이름을 지정합니다.

1. **알림** 탭을 선택하고 **알림 유형** 및 **이름** 을 선택합니다. 그런 다음, **확인** 을 선택합니다.

   이 예제는 이메일 알림을 기반으로 합니다.

   :::image type="content" source="../azure-monitor/alerts/media/action-groups/action-group-2-notifications.png" alt-text="경고에 대한 메일, SMS 메시지, 푸시, 음성 설정 스크린샷" lightbox="../azure-monitor/alerts/media/action-groups/action-group-2-notifications.png":::    

1. (선택 사항) 사전 조치를 취하고 이벤트에 대한 알림을 받으려면 **작업** 을 구성합니다. 사용 가능한 **작업 유형** 을 선택한 다음 **검토 + 만들기** 를 선택합니다. 

   - **Automation Runbook** - 경고를 기준으로 작업 자동화

   - **Azure Functions** – 사용자 지정 이벤트 기반 서버리스 코드 실행

   - **ITSM** – ServiceNow와 같은 서비스 공급자와 통합하여 티켓 생성

   - **논리 앱** - 보다 복잡한 워크플로 오케스트레이션

   - **웹후크** - 다른 서비스에서 프로세스 트리거


1. **경고 규칙 세부 정보** 에서 경고 규칙을 저장할 이름, 설명, 리소스 그룹 및 심각도를 제공합니다. 그런 다음 **경고 규칙 만들기** 를 선택합니다.
   
   경고 규칙이 표시되고 Azure Portal에서 관리할 수 있습니다.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/existing-alert-rule.png" alt-text="규칙 창의 새 경고 규칙 스크린샷" lightbox="media/configure-alerts-for-azure-vmware-solution/existing-alert-rule.png":::      

   메트릭이 경고 규칙에 정의된 임계값에 도달하는 즉시 **경고** 메뉴가 업데이트되고 표시됩니다.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/threshold-alert.png" alt-text="경고 규칙에서 정의된 임계값에 도달한 후의 경고 스크린샷" lightbox="media/configure-alerts-for-azure-vmware-solution/threshold-alert.png":::     

   구성된 작업 그룹에 따라 구성된 미디어를 통해 알림을 받게 됩니다. 이 예제에서는 이메일을 구성했습니다.
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/alert-notification.png" alt-text="오류 문자열과 날짜 및 시간 이벤트가 트리거된 Azure Monitor 경고 스크린샷."::: 

## <a name="work-with-metrics"></a>메트릭으로 작업

1. Azure VMware Solution 프라이빗 클라우드에서 **모니터링** > **메트릭** 을 선택합니다. 그런 다음 드롭다운에서 원하는 메트릭을 선택합니다.
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics.png" alt-text="메트릭 창과 메트릭 드롭다운의 포커스 스크린샷" lightbox="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics.png":::   

1. **시간 범위** 또는 **시간 세분성** 과 같은 다이어그램의 매개 변수를 변경할 수 있습니다. 

   기타 옵션은 다음과 같습니다.
   - **로그로 드릴** 하고 관련 Log Analytics 작업 영역에서 데이터를 쿼리합니다.
   - 편의를 위해 Azure 대시보드에 **이 다이어그램을 고정** 합니다.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics-time-range-granularity.png" alt-text="메트릭에 대한 시간 범위 및 시간 세분성 옵션 스크린샷" lightbox="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics-time-range-granularity.png":::  
 
 
## <a name="next-steps"></a>다음 단계

이제 Azure VMware Solution 프라이빗 클라우드에 대한 경고 규칙을 구성했으므로 다음 사항에 대한 자세한 내용을 확인할 수 있습니다.
- [Azure Monitor 메트릭](../azure-monitor/essentials/data-platform-metrics.md)
- [Azure Monitor 경고](../azure-monitor/alerts/alerts-overview.md)
- [Azure 작업 그룹](../azure-monitor/alerts/action-groups.md)

다른 [Azure VMware Solution](index.yml) 방법 가이드 중 하나를 계속 진행할 수도 있습니다.
