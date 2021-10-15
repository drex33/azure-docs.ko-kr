---
title: 작업 규칙을 사용하여 Azure Stack Edge 디바이스 | 경고 알림 관리 Microsoft Docs
description: Azure Portal Azure Stack Edge 디바이스에 대한 경고 알림을 관리하는 작업 규칙을 정의하는 방법을 설명합니다.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/11/2021
ms.author: alkohli
ms.openlocfilehash: 804d956f934774b01b29301e0be8f9c1a6fd2657
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130043361"
---
# <a name="use-action-rules-to-manage-alert-notifications-on-azure-stack-edge-devices"></a>작업 규칙을 사용하여 Azure Stack Edge 디바이스에서 경고 알림 관리

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 리소스 그룹, Azure 구독 또는 개별 Azure Stack Edge 리소스 내에서 발생하는 디바이스 이벤트에 대한 경고 알림을 트리거하거나 표시하지 않는 작업 규칙을 Azure Portal 만드는 방법을 설명합니다.  

## <a name="about-action-rules"></a>작업 규칙 정보

작업 규칙은 경고 알림을 트리거하거나 표시하지 않을 수 있습니다. 작업 규칙은 리소스 또는 리소스 집합에 대해 다른 컨텍스트에서 트리거된 경고에 대해 작업해야 하는 사용자에게 알리는 데 사용되는 알림 기본 설정 집합인 *작업 그룹에* 추가됩니다.

작업 규칙에 대한 자세한 내용은 [작업 규칙 구성을 참조하세요.](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule) 작업 그룹에 대한 자세한 내용은 [Azure Portal 작업 그룹 만들기 및 관리를 참조하세요.](../azure-monitor/alerts/action-groups.md)

> [!NOTE]
> 작업 규칙 기능은 미리 보기로 제공됩니다. 프로세스가 구체화되면 일부 화면과 단계가 변경될 수 있습니다.


## <a name="create-an-action-rule"></a>작업 규칙 만들기

Azure Portal 다음 단계를 수행하여 Azure Stack Edge 디바이스에 대한 작업 규칙을 만듭니다.

> [!NOTE]
> 이러한 단계는 작업 그룹에 알림을 보내는 작업 규칙을 만듭니다. 알림을 표시하지 않을 작업 규칙을 만드는 방법에 대한 자세한 내용은 [작업 규칙 구성을](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule)참조하세요.

1. Azure Portal Azure Stack Edge 디바이스로 이동한 **다음, 모니터링 > 경고로** 이동합니다. **작업 관리** 를 선택합니다.

   ![경고 모니터링, 작업 보기 관리](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/action-rules-open-view-01.png)

2. **작업 규칙(미리 보기)** 을 선택한 **다음, + 새 작업 규칙 을** 선택합니다.

   ![작업 관리, 작업 규칙 옵션](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/action-rules-open-view-02.png)

3. 작업 **규칙 만들기** 화면에서 **범위를** 사용하여 Azure 구독, 리소스 그룹 또는 대상 리소스를 선택합니다. 작업 규칙은 해당 범위 내에서 생성된 모든 경고에 대해 작동합니다.

   1. **범위로** **선택을 선택합니다.**

      ![새 작업 규칙의 범위 선택](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-01.png)

   2. 작업 규칙에 대한 **구독을** 선택하고 선택적으로 **리소스** 유형별로 필터링합니다. Azure Stack Edge 리소스로 필터링하려면 **Data Box Edge 디바이스(dataBoxEdge)** 를 선택합니다.

      **리소스** 영역에는 선택 항목에 따라 사용 가능한 리소스가 나열됩니다.
  
      ![범위 선택 화면에서 사용 가능한 리소스](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-02.png)

   3. 규칙을 적용하려는 각 리소스별로 확인란을 선택합니다. 구독, 리소스 그룹 또는 개별 리소스를 선택할 수 있습니다. 그런 후 **완료** 를 선택합니다.

      ![작업 규칙 범위에 대한 샘플 설정](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-03.png)

      **작업 규칙 만들기** 화면에는 선택한 범위가 표시됩니다.

      ![Azure Stack Edge 작업 규칙의 완료된 범위](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-04.png)

4. **필터** 옵션을 사용하여 선택한 범위 내에서 경고의 하위 집합으로 규칙의 적용 범위를 좁힐 수 있습니다.

   1. **추가를** 선택하여 **필터 추가** 창을 엽니다.

      ![작업 규칙에 필터를 추가하는 옵션](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-filter-01.png)

   2. **필터** 아래에서 적용하려는 각 필터를 추가합니다. 각 필터에 대해 필터 유형, **연산자** 및 **값** 을 선택합니다.
   
      필터 옵션 목록은 필터 조건 을 [참조하세요.](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#filter-criteria)

      아래 샘플 필터는 모니터 서비스가 Azure Stack Edge 리소스에 대해 발생시키는 심각도 수준 2, 3 및 4의 모든 경고에 적용됩니다.

      필터 추가를 마치면 **완료를** 선택합니다.
   
      ![작업 규칙에 대한 샘플 필터](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-filter-02.png)

5. 작업 **규칙 만들기** 화면에서 **작업 그룹을** 선택하여 알림을 보내는 규칙을 만듭니다. 그런 다음 **작업에서** 선택을 **선택합니다.**

   ![알림을 보내는 작업 규칙을 만들기 위한 작업 그룹 옵션](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-action-group-01.png)

   > [!NOTE]
   > 알림을 표시하지 않는 규칙을 만들려면 표시 안 **함 을** 선택합니다. 자세한 내용은 [작업 규칙 구성을 참조하세요.](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule)

6. 이 작업 규칙과 함께 사용할 작업 그룹을 선택합니다. 그런 다음, **선택** 을 선택합니다. 선택한 작업 그룹의 알림 기본 설정에 새 작업 규칙이 추가됩니다.

   새 작업 그룹을 만들어야 하는 경우 **+ 작업 그룹 만들기를** 선택하고 Azure Portal 사용하여 작업 그룹 [만들기의](../azure-monitor/alerts/action-groups.md#create-an-action-group-by-using-the-azure-portal)단계를 따릅니다.

   ![규칙과 함께 사용할 작업 그룹을 선택한 다음, 선택을 선택합니다.](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-action-group-02.png)

7. 새 작업 규칙에 **이름** 및 **설명** 을 지정하고 리소스 그룹에 규칙을 할당합니다.

9. 새 규칙은 기본적으로 사용하도록 설정되어 있습니다. 규칙을 즉시 사용하지 않으려면 규칙 **업데이트 만들기 사용에서** **아니요를** 선택합니다.

10. 설정을 마치면 **만들기를** 선택합니다.

    ![경고 알림을 보낼 작업 규칙의 완료된 설정](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-completed-settings.png)

    **작업 규칙(미리 보기)** 화면이 열리지만 새 작업 규칙이 즉시 표시되지 않을 수 있습니다. 모든 **리소스** 그룹에 중점을 두고 있습니다.

11. 새 작업 규칙을 보려면 규칙에 대한 리소스 그룹을 선택합니다.

    ![새 규칙이 표시된 작업 규칙 화면](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-displayed.png)


## <a name="view-notifications"></a>알림 확인

알림은 새 이벤트가 작업 규칙의 범위 내에 있는 리소스에 대한 경고를 트리거할 때 발생합니다.

규칙의 작업 그룹은 알림을 받는 사람 및 전송된 알림 유형(이메일, SMS(짧은 메시지 서비스) 메시지 또는 둘 다)을 설정합니다.

경고가 트리거된 후 알림을 받는 데 몇 분 정도 걸릴 수 있습니다.

이메일 알림은 다음과 유사합니다.

![작업 규칙에 대한 샘플 이메일 알림](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/sample-action-rule-email-notification.png)


## <a name="next-steps"></a>다음 단계

- [디바이스 경고를 봅니다.](azure-stack-edge-alerts.md)
- [경고 메트릭을 사용하여 작업합니다.](../azure-monitor/alerts/alerts-metric.md)
- [Azure Monitor 설정합니다.](azure-stack-edge-gpu-enable-azure-monitor.md)
