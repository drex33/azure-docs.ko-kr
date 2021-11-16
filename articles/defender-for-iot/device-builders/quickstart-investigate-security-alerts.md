---
title: '빠른 시작: 보안 경고 조사'
description: IoT 디바이스에서 Defender for IoT 보안 경고를 이해, 드릴다운 및 조사합니다.
ms.topic: quickstart
ms.date: 11/09/2021
ms.openlocfilehash: c55392650edf85c753bcde32a03e8ad313ebd148
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132331512"
---
# <a name="quickstart-investigate-security-alerts"></a>빠른 시작: 보안 경고 조사

Defender for IoT에서 발급한 경고의 예약된 조사 및 수정 작업이 IoT 솔루션에 대한 규정 준수 및 보호를 보장하는 가장 좋은 방법입니다.

## <a name="investigate-new-security-alerts"></a>새 보안 경고 조사

IoT Hub 보안 경고 목록에는 IoT Hub에 대한 모든 집계된 보안 경고가 표시됩니다. 

1. Azure Portal에서 새 경고를 조사하려는 **IoT Hub** 를 엽니다.

1. **보안** 메뉴에서 **경고** 를 선택합니다. IoT Hub에 대한 모든 보안 경고가 표시되고 **새** 플래그가 포함된 경고는 지난 24시간 동안의 경고를 표시합니다.

    :::image type="content" source="media/quickstart/investigate-new-security-alerts.png" alt-text="새 경고 플래그를 사용하여 새 IoT 보안 경고 조사":::

1. 목록에서 경고를 선택하여 경고 세부 정보를 열고 경고 세부 사항을 파악합니다. 

## <a name="security-alert-details"></a>보안 경고 세부 정보

각 집계된 경고를 열면 경고를 트리거한 각 디바이스에 대한 자세한 경고 설명, 수정 단계 및 디바이스 ID가 표시됩니다. 경고 심각도 및 직접 조사는 Log Analytics를 사용하여 액세스할 수 있습니다. 

1. **IoT Hub** > **보안** > **경고** 로 이동합니다. 

1. 목록에서 보안 경고를 선택하여 엽니다. 

1. 집계 기간에 이 경고를 발행한 모든 디바이스의 경고 **설명**, **심각도**, **감지의 원본**, **디바이스 세부 정보** 를 검토합니다.

    :::image type="content" source="media/quickstart/drill-down-iot-alert-details.png" alt-text="집계된 경고에서 각 디바이스의 세부 정보 조사 및 검토"::: 

1. 경고 세부 정보를 검토한 후 **수동 수정 단계** 지침을 사용하여 경고를 발생시킨 문제를 수정 및 해결합니다.

    :::image type="content" source="media/quickstart/iot-alert-manual-remediation-steps.png" alt-text="수동 수정 단계를 수행하여 디바이스 보안 경고를 해결하거나 수정합니다.":::

1. 추가 조사가 필요한 경우 링크를 사용하여 **Log Analytics의 경고를 조사** 합니다.
 
    :::image type="content" source="media/quickstart/investigate-iot-alert-log-analytics.png" alt-text="경고를 자세히 조사하려면 화면에 제공된 로그 분석 링크를 사용하여 조사를 사용합니다.":::

## <a name="next-steps"></a>다음 단계

보안 경고 유형 및 가능한 사용자 지정에 대해 자세히 알아보려면 다음 문서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [IoT 보안 경고 이해](concept-security-alerts.md)
