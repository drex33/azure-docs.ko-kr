---
title: Azure Activity 로그 및 Azure Service Health를 사용하여 앱 수명 주기 이벤트 모니터링
description: 앱 수명 주기 이벤트를 모니터링하고 Azure Activity 로그 및 Azure Service Health 통해 경고를 설정합니다.
author: karlerickson
ms.author: shiqiu
ms.service: spring-cloud
ms.topic: how-to
ms.date: 08/19/2021
ms.custom: devx-track-java
ms.openlocfilehash: 77e6704808cf54f84f0261c07236bfa27d17a1f6
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835866"
---
# <a name="monitor-app-lifecycle-events-using-azure-activity-log-and-azure-service-health"></a>Azure Activity 로그 및 Azure Service Health를 사용하여 앱 수명 주기 이벤트 모니터링

이 문서에서는 앱 수명 주기 이벤트를 모니터링하고 Azure Activity 로그 및 Azure Service Health를 사용하여 경고를 설정하는 방법을 보여줍니다.

Azure Spring Cloud는 애플리케이션의 상태를 모니터링하는 기본 제공 도구를 제공합니다. 앱 수명 주기 이벤트는 애플리케이션에 적용된 변경 내용을 이해하는 데 도움을 주기 때문에, 필요에 따라 조치를 취할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건

- Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- 배포된 Azure Spring Cloud 서비스 인스턴스, 서비스 인스턴스에 이미 만들어진 하나 이상의 애플리케이션. 자세한 내용은 [빠른 시작 - 첫 번째 Azure Spring Cloud 애플리케이션 배포](quickstart.md)를 참조하세요. 

## <a name="monitor-app-lifecycle-events-triggered-by-users-in-azure-activity-logs"></a>Azure Activity 로그에서 사용자가 트리거한 앱 수명 주기 이벤트 모니터링

[Azure Activity 로그](../azure-monitor/essentials/activity-log.md)에는 구독의 리소스에서 수행된 작업에서 내보내는 리소스 이벤트가 포함됩니다. 애플리케이션 수명 주기 이벤트(시작, 중지, 다시 시작)에 대한 다음 세부 정보가 Azure Activity 로그에 추가됩니다.

- 작업이 발생한 시간
- 작업의 상태
- 앱을 시작할 때 생성되는 인스턴스
- 앱을 중지할 때 삭제되는 인스턴스
- 앱을 다시 시작할 때 삭제되고 생성되는 인스턴스

예를 들어, 앱을 다시 시작할 때, Azure Portal **활동 로그** 세부 정보 페이지에서 영향을 받는 인스턴스를 찾을 수 있습니다.

:::image type="content" source="media/monitor-app-lifecycle-events/activity-log-restart-detail.png" lightbox="media/monitor-app-lifecycle-events/activity-log-restart-detail.png" alt-text="활동 로그의 다시 시작 세부 정보 Azure Portal 스크린샷":::

## <a name="monitor-app-lifecycle-events-in-azure-service-health"></a>Azure Service Health 앱 수명 주기 이벤트 모니터링

[Azure Resource Health](../service-health/resource-health-overview.md)는 서비스의 가용성에 영향을 줄 수 있는 문제를 진단하고 지원하는 데 도움이 됩니다. 이러한 문제에는 서비스 인시던트, 계획된 유지 관리 기간 및 지역 가동 중단이 포함됩니다. 애플리케이션 다시 시작 이벤트가 Azure Service Health에 추가됩니다. 여기에는 예정된 작업(예: 계획된 유지 관리)과 예기치 않은 인시던트(예: 계획에 없는 앱 충돌)가 모두 포함됩니다.

### <a name="monitor-unplanned-app-lifecycle-events"></a>계획되지 않은 앱 수명 주기 이벤트 모니터링

계획되지 않은 이벤트로 인해 앱이 다시 시작되면, Azure Spring Cloud 인스턴스는 Azure Portal의 **리소스 상태** 섹션에서 **성능 저하** 상태를 표시합니다. 성능 저하는 리소스를 계속 사용할 수는 있지만 리소스에서 잠재적 성능 저하가 검색되었음을 의미합니다. 계획되지 않은 이벤트의 예로는 앱 작동 중단, 상태 검사 실패 및 시스템 중단이 있습니다.

:::image type="content" source="media/monitor-app-lifecycle-events/resource-health-detail.png" alt-text="리소스 상태 창 스크린샷":::

상태 기록 페이지에서 최신 상태, 근본 원인 및 영향을 받는 인스턴스를 찾을 수 있습니다.

:::image type="content" source="media/monitor-app-lifecycle-events/unplanned-app-lifecycle-event-details.png" lightbox="media/monitor-app-lifecycle-events/unplanned-app-lifecycle-event-details.png" alt-text="계획되지 않은 앱 수명 주기 이벤트에 대한 예제 로그의 스크린샷":::


### <a name="monitor-planned-app-lifecycle-events"></a>계획된 앱 수명 주기 이벤트 모니터링

플랫폼 유지 관리 중에 앱이 다시 시작될 수 있습니다. Azure Service Health의 **계획된 유지 관리** 페이지에서 유지 관리 알림을 미리 받을 수 있습니다.

:::image type="content" source="media/monitor-app-lifecycle-events/planned-maintenance-notification.png" lightbox="media/monitor-app-lifecycle-events/planned-maintenance-notification.png" alt-text="계획된 유지 관리 알림 예제 스크린샷":::

플랫폼 유지 관리가 발생하면, Azure Spring Cloud 인스턴스의 **성능 저하** 상태도 표시됩니다. 플랫폼 유지 관리 중에 다시 시작해야하는 경우, Azure Spring Cloud가 롤링 업데이트를 수행하여 애플리케이션을 업데이트합니다. 롤링 업데이트는 가동 중지 시간 없이 워크로드를 업데이트하도록 설계되었습니다. 상태 기록 페이지에서 최신 상태를 찾을 수 있습니다.

:::image type="content" source="media/monitor-app-lifecycle-events/planned-maintenance-in-progress.png" lightbox="media/monitor-app-lifecycle-events/planned-maintenance-in-progress.png" alt-text="진행 중인 계획된 유지 관리 로그 예시 스크린샷":::

## <a name="set-up-alerts"></a>경고 설정

앱 수명 주기 이벤트에 대한 경고를 설정할 수 있습니다. 서비스 상태 알림은 Azure 활동 로그에 저장됩니다. 활동 로그에는 많은 양의 정보가 저장되므로, 서비스 상태 알림에 대한 경고를 보다 쉽게 확인하고 설정할 수 있도록 별도의 사용자 인터페이스가 있습니다.

다음 목록에서는 경고를 설정하는 데 필요한 주요 단계를 설명합니다. 

1. 경고가 트리거될 때 수행할 작업으로 작업 그룹을 설정합니다. 음성 통화, SMS, 이메일 보내기나, 여러 자동화된 작업 유형 트리거를 예로 들 수 있습니다. 사용자의 요구 사항에 따라 다양한 경고가 동일한 작업 그룹을 사용할 수도 있고 서로 다른 작업 그룹을 사용할 수도 있습니다.
2. 경고 규칙을 설정합니다. 경고는 작업 그룹을 사용하여 특정 앱 수명 주기 이벤트에 대한 경고가 트리거되면 사용자에게 알립니다.

### <a name="set-up-alerts-on-activity-log"></a>활동 로그에 대한 경고 설정

다음 단계에서는 Azure Portal 활동 로그 경고 규칙을 만드는 방법을 보여줍니다.

1. **활동 로그** 로 이동하여 활동 로그에 대한 세부 정보 페이지를 연 다음, **새 경고 규칙** 을 선택합니다.

   :::image type="content" source="media/monitor-app-lifecycle-events/activity-log-alert.png" lightbox="media/monitor-app-lifecycle-events/activity-log-alert.png" alt-text="활동 로그 경고 스크린샷":::

2. 경고에 대한 **범위** 를 선택합니다.

3. 경고 **조건** 을 지정합니다.

   :::image type="content" source="media/monitor-app-lifecycle-events/activity-log-alert-condition.png" lightbox="media/monitor-app-lifecycle-events/activity-log-alert-condition.png" alt-text="활동 로그 경고 조건 스크린샷":::

4. **작업** 을 선택하고 **경고 규칙 세부 사항** 을 추가합니다.

5. **경고 규칙 만들기** 를 선택합니다.

### <a name="set-up-alerts-to-monitor-app-lifecycle-events-in-azure-service-health"></a>Azure Service Health의 앱 수명 주기 이벤트를 모니터링하도록 경고 설정

다음 단계에서는 Azure Portal 서비스 상태 알림에 대한 경고 규칙을 만드는 방법을 보여줍니다.

1. **서비스 상태** 아래의 **리소스 상태** 로 이동하여 **리소스 상태 경고 추가** 를 선택합니다.

   :::image type="content" source="media/monitor-app-lifecycle-events/add-resource-health-alert.png" alt-text="'리소스 상태 경고 추가' 단추가 강조 표시된 리소스 상태 창의 스크린샷":::

2. 경고에 대한 **리소스** 를 선택합니다.

   :::image type="content" source="media/monitor-app-lifecycle-events/resource-health-alert-target.png" alt-text="리소스 상태 경고 대상 스크린샷":::

3. **경고 조건** 을 지정합니다.

   :::image type="content" source="media/monitor-app-lifecycle-events/resource-health-alert-condition.png" alt-text="리소스 상태 경고 조건 스크린샷":::

4. **작업** 을 선택하고 **경고 규칙 세부 사항** 을 추가합니다.

5. **경고 규칙 만들기** 를 선택합니다.

### <a name="set-up-alerts-to-monitor-the-planned-maintenance-notification"></a>계획된 유지 관리 알림을 모니터링하도록 경고 설정

다음 단계에서는 Azure Portal에서 계획된 유지 관리 알림에 대한 경고 규칙을 만드는 방법을 보여줍니다.

1. **서비스 상태** 아래의 **상태 경고** 로 이동하여 **서비스 상태 경고 추가** 를 선택합니다.

   :::image type="content" source="media/monitor-app-lifecycle-events/add-service-health-alert.png" alt-text="'서비스 상태 경고 추가' 단추가 강조 표시된 상태 경고 창의 스크린샷":::

2. **구독**, **서비스**, **지역**, **이벤트 유형**, **작업**, **경고 규칙 세부 사항** 의 값을 입력합니다.

   :::image type="content" source="media/monitor-app-lifecycle-events/add-service-health-alert-details.png" lightbox="media/monitor-app-lifecycle-events/add-service-health-alert-details.png" alt-text="Service Health 대한 '규칙 경고 만들기' 창의 스크린샷":::

3. **경고 규칙 만들기** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

[Azure Spring Cloud의 자체 진단 및 문제 해결](how-to-self-diagnose-solve.md)
