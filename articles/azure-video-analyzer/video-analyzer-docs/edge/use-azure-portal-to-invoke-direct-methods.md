---
title: Azure Portal을 사용하여 Azure Video Analyzer에 대한 직접 메서드를 호출하는 방법
description: 이 문서는 Azure Portal을 사용한 Azure Video Analyzer에 대한 직접 메서드 호출에 대한 개요입니다.
ms.topic: how-to
ms.date: 06/01/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1ca7a9b57fb43613d42a256675e374bcf4455e51
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053103"
---
# <a name="use-azure-portal-to-invoke-direct-methods-for-azure-video-analyzer"></a>Azure Portal을 사용하여 Azure Video Analyzer에 대한 직접 메서드 호출

[!INCLUDE [header](includes/edge-env.md)]

IoT Hub를 사용하면 클라우드의 에지 디바이스에서 [직접 메서드](../../../iot-hub/iot-hub-devguide-direct-methods.md#method-invocation-for-iot-edge-modules)를 호출할 수 있습니다. Azure Video Analyzer(Video Analyzer) 모듈은 라이브 비디오를 분석하기 위해 여러 파이프라인을 정의하고, 배포하고, 활성화하는 데 사용할 수 있는 여러 [직접 메서드](./direct-methods.md)를 제공합니다.

이 문서에서는 Azure Portal을 통해Video Analyzer 모듈에 대해 직접 메서드 호출을 호출하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

* [빠른 시작: Video Analyzer](get-started-detect-motion-emit-events.md)에서 설명하는 메서드 또는 [포털](./deploy-iot-edge-device.md)을 사용하여 에지 디바이스에서 Video Analyzer 모듈을 실행하고 있습니다.
* [Video Analyzer](../overview.md) 및 [파이프라인 개념](../pipeline.md)을 이해합니다.

## <a name="invoking-direct-methods-via-azure-portal"></a>Azure Portal을 통해 직접 메서드 호출

Video Analyzer 모듈에서 제공하는 각 [직접 메서드](./direct-methods.md)는 Azure Portal을 통해 호출할 수 있습니다. 아래 단계에서는 하나의 직접 메서드에 대한 세부 정보를 제공합니다. 비슷한 단계를 사용하여 다른 직접 메서드를 호출할 수 있습니다. 그러나 각 직접 메서드에는 특정 페이로드가 필요합니다.

`livePipelineList` 메서드 호출을 사용하여 Video Analyzer 모듈에 배포된 모든 파이프라인 토폴로지의 목록을 검색합니다. 다음 단계를 사용하여 이 직접 메서드를 호출합니다.

1. Azure Portal에 로그인
1. 포털 홈페이지에서 관련 리소스 그룹을 찾아 IoT Hub을 찾거나 IoT Hub를 알고 있는 경우 선택합니다.
    ![포털 홈 페이지의 리소스 그룹](./media/use-azure-portal-to-invoke-direct-methods/portal-rg-home.png)
1. IoT Hub 페이지에서 자동 디바이스 관리 아래에 있는 IoT Edge를 선택하여 여러 디바이스 ID를 나열합니다. 디바이스에서 실행되는 모듈을 나열하려면 관련 디바이스 ID를 선택합니다.
    ![IoT Hub 이름](./media/use-azure-portal-to-invoke-direct-methods/iot-hub-page.png)
1. 구성 페이지를 표시할 Video Analyzer 모듈을 선택합니다.<br><br>
    ![구성 페이지를 표시할 Video Analyzer 모듈 선택](./media/use-azure-portal-to-invoke-direct-methods/modules.png)
1. 직접 메서드 메뉴 옵션을 선택합니다. <br><br>
    ![직접 메서드 메뉴 옵션 클릭](./media/use-azure-portal-to-invoke-direct-methods/module-details.png)
    > [!NOTE]
    > 현재 페이지에서 볼 수 있듯이 연결 문자열 섹션에 값을 추가해야 할 수도 있습니다. 설정 이름 섹션에서 아무것도 숨기거나 변경할 필요가 없습니다. 공개하는 것도 괜찮습니다.

1. **메서드 이름** 필드에 *livePipelineList* 를 입력합니다.
1. **페이로드** 필드에 아래 JSON을 복사하여 붙여넣습니다.
    ```json
    {
    "@apiVersion": "1.1"
    }
    ```
1. 페이지 위쪽의 **메서드 호출** 단추를 선택합니다.<br><br>
    ![메서드 호출 단추](./media/use-azure-portal-to-invoke-direct-methods/direct-method.png)
1. **결과** 영역에 상태 200 메시지가 표시됩니다.<br><br>
    ![연결 시간 제한](./media/use-azure-portal-to-invoke-direct-methods/connection-timeout.png)

## <a name="responses"></a>응답

| 조건             | 상태 코드 | 자세한 오류 코드 |
|-----------------------|-------------|---------------------|
| Success               | 200         | 해당 없음                 |
| 일반 사용자 오류   | 400 범위   |                     |
| 일반 서버 오류 | 500 범위   |                     |

## <a name="next-steps"></a>다음 단계

더 많은 직접 메서드는 [직접 메서드](./direct-methods.md) 페이지에서 찾을 수 있습니다.

> [!NOTE]
> 파이프라인은 특정 토폴로지를 활성화하기 때문에 파이프라인을 활성화하기 전에 올바른 토폴로지가 설정되었는지 확인하세요.

[빠른 시작: 모션 내보내기 이벤트 감지](detect-motion-emit-events-quickstart.md)는 정확한 직접 메서드 호출 순서를 이해하기 위한 좋은 참고 자료입니다.
