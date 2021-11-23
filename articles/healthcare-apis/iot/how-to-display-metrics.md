---
title: IoT 커넥터 메트릭 로깅 표시-Azure 의료 Api
description: 이 문서에서는 IoT 커넥터 메트릭을 표시 하는 방법을 설명 합니다.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 11/22/2021
ms.author: jasteppe
ms.openlocfilehash: 88e5310a53bfd5fed79ae0a9a6efc06d91fdb0cf
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132933954"
---
# <a name="how-to-display-iot-connector-metrics"></a>IoT connector 메트릭을 표시 하는 방법

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 문서에서는 Azure Portal에 IoT connector 메트릭을 표시 하는 방법을 알아봅니다. 

## <a name="display-metrics"></a>메트릭 표시

1. Azure 의료 Api 작업 영역 내에서 **IoT 커넥터** 를 선택 합니다. 

     :::image type="content" source="media\iot-metrics\iot-workspace-displayed-with-connectors-button.png" alt-text="IoT 커넥터 단추를 선택 합니다." lightbox="media\iot-metrics\iot-connectors-button.png"::: 

2. 메트릭을 표시할 IoT 커넥터를 선택 합니다.

    :::image type="content" source="media\iot-metrics\iot-connector-select.png" alt-text="메트릭을 표시할 IoT 커넥터를 선택 합니다." lightbox="media\iot-metrics\iot-connector-select.png":::
    
3. IoT 커넥터 페이지에서 **메트릭** 을 선택 합니다.

   :::image type="content" source="media\iot-metrics\iot-select-metrics.png" alt-text="메트릭 단추를 선택합니다." lightbox="media\iot-metrics\iot-metrics-button.png"::: 

4. 메트릭 페이지에서 IoT 커넥터에 대해 표시 하려는 메트릭을 만들 수 있습니다. 이 예제에서는 다음 항목을 선택 합니다.

    * **범위** = IoT 커넥터 이름 (**기본값**)
    * **메트릭 네임 스페이스** = 표준 메트릭 (**기본값**) 
    * **메트릭** = 표시 하려는 IoT 커넥터 메트릭입니다. 이 예에서는 **들어오는 메시지 수** 를 선택 합니다.
    * **Aggregation** = 메트릭을 표시 하는 방법입니다. 이 예에서는 **Count** 를 선택 합니다. 

    :::image type="content" source="media\iot-metrics\iot-select-metrics-to-display.png" alt-text="표시할 메트릭을 선택 합니다." lightbox="media\iot-metrics\iot-metrics-selection-close-up.png"::: 

5. 이제 Azure Portal에 표시 되는 **들어오는 메시지 수** 에 대 한 IoT 커넥터 메트릭이 표시 될 수 있습니다.

    > [!TIP]
    > **메트릭 추가** 단추를 선택 하 고 원하는 항목을 선택 하 여 추가 메트릭을 추가할 수 있습니다.

    :::image type="content" source="media\iot-metrics\iot-metrics-add-button.png" alt-text="메트릭 추가 단추를 선택 하 여 메트릭을 더 추가 합니다." lightbox="media\iot-metrics\iot-add-metric-button.png":::

    > [!IMPORTANT]
    > 메트릭 페이지를 벗어나면 메트릭 설정이 손실 되므로 다시 만들어야 합니다. IoT connector 메트릭을 나중에 볼 수 있도록 저장 하려는 경우 Azure 대시보드에 타일로 고정할 수 있습니다.

## <a name="pinning-metrics-tile-on-azure-portal-dashboard"></a>Azure Portal 대시보드의 메트릭 고정 타일

1. 메트릭 타일을 Azure Portal 대시보드에 고정 하려면 **대시보드에 고정** 단추를 선택 합니다.

    :::image type="content" source="media\iot-metrics\iot-metrics-select-add-pin-to-dashboard.png" alt-text="대시보드에 고정 단추를 선택 합니다." lightbox="media\iot-metrics\iot-pin-to-dashboard-button.png":::

2. IoT connector 메트릭을 표시할 대시보드를 선택 합니다. 이 예에서는 라는 개인 대시보드를 사용 `IoT connector Metrics` 합니다. **고정** 을 선택 하 여 대시보드에 메트릭 타일을 추가 합니다.

    :::image type="content" source="media\iot-metrics\iot-select-pin-to-dashboard.png" alt-text="대시보드 및 고정 단추를 선택 하 여 대시보드 고정 프로세스를 완료 합니다." lightbox="media\iot-metrics\iot-select-pin-to-dashboard.png":::

3. 메트릭 타일이 대시보드에 성공적으로 추가 되었다는 확인 메시지가 표시 됩니다.

    :::image type="content" source="media\iot-metrics\iot-select-dashboard-pinned-successful.png" alt-text="메트릭 타일이 대시보드에 고정 되었습니다." lightbox="media\iot-metrics\iot-select-dashboard-pinned-successful.png":::

4. 확인이 성공적으로 완료 되 면 **대시보드** 를 선택 합니다.

    :::image type="content" source="media\iot-metrics\iot-select-dashboard-with-metrics-tile.png" alt-text="대시보드 단추를 선택 합니다." lightbox="media\iot-metrics\iot-dashboard-button.png":::

5. 메트릭 타일을 고정 한 대시보드를 선택 합니다. 이 예제에서 대시보드는 `IoT connector Metrics` 입니다. 대시보드는 이전 단계에서 만든 IoT 커넥터 메트릭 타일을 표시 합니다.

    :::image type="content" source="media\iot-metrics\iot-dashboard-with-metrics-tile-displayed.png" alt-text="고정 된 IoT 커넥터 메트릭 타일이 있는 대시보드." lightbox="media\iot-metrics\iot-dashboard-with-metrics-tile-displayed.png":::

> [!TIP]
> 일반적인 오류 및 문제 해결에 대 한 지원은 IoT 커넥터 [문제 해결 가이드](./iot-troubleshoot-guide.md) 를 참조 하세요.

## <a name="conclusion"></a>결론 

메트릭에 대 한 액세스는 모니터링 및 문제 해결에 필수적입니다.  IoT 커넥터는 메트릭을 통해 이러한 작업을 수행 하는 데 도움이 됩니다. 

## <a name="next-steps"></a>다음 단계

IoT 커넥터에 대 한 자주 묻는 질문과 대답을 확인 하세요.

>[!div class="nextstepaction"]
>[IoT 커넥터 Faq](iot-connector-faqs.md)

(FHIR&#174;)는 HL7의 등록 상표 이며 HL7의 사용 권한과 함께 사용 됩니다.
