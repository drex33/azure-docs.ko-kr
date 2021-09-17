---
title: 워크플로를 사용하여 Azure IoT Central 애플리케이션을 다른 클라우드 서비스와 통합 | Microsoft Docs
description: 이 방법 문서에서는 빌더로서 IoT Central 애플리케이션을 다른 클라우드 서비스와 통합하는 규칙 및 작업을 구성하는 방법을 보여 줍니다. 고급 규칙을 만들려면 Power Automate 또는 Azure Logic Apps에서 IoT Central 커넥터를 사용합니다.
author: dominicbetts
ms.author: dobett
ms.date: 08/26/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: b90e96975dadc1f7fb168345aa89b98bb6a22b9c
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123473589"
---
# <a name="use-workflows-to-integrate-your-azure-iot-central-application-with-other-cloud-services"></a>워크플로를 사용하여 Azure IoT Central 애플리케이션을 다른 클라우드 서비스와 통합

임계값을 초과하는 디바이스 온도와 같은 원격 분석 기반 조건에 대한 응답으로 이메일 전송과 같은 작업을 트리거하는 IoT Central 규칙을 만들 수 있습니다.

Power Automate 및 Azure Logic Apps용 Azure IoT Central V3 커넥터를 사용하면 IoT Central에서 작업을 자동화하는 고급 규칙을 만들 수 있습니다.

- Azure IoT Central 앱에서 규칙이 실행되면 Power Automate 또는 Azure Logic Apps에서 워크플로를 트리거할 수 있습니다. 이러한 워크플로는 Microsoft 365 또는 타사 서비스와 같은 다른 클라우드 서비스에서 작업을 실행할 수 있습니다.
- 다른 클라우드 서비스(예: Microsoft 365)의 이벤트는 Power Automate 또는 Azure Logic Apps에서 워크플로를 트리거할 수 있습니다. 이러한 워크플로는 IoT Central 애플리케이션에서 작업을 실행하거나 데이터를 검색할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 방법 가이드의 단계를 완료하려면 다음이 필요합니다.

[!INCLUDE [iot-central-prerequisites-basic](../../../includes/iot-central-prerequisites-basic.md)]

> [!NOTE]
> 버전 2 IoT Central 애플리케이션을 사용하는 경우 이전 버전 설명서 사이트의 [Azure Logic Apps에서 IoT Central 커넥터를 사용하여 워크플로 빌드](/previous-versions/azure/iot-central/core/howto-build-azure-logic-apps) 및 Azure IoT Central V2 커넥터 사용을 참조하세요.

## <a name="trigger-a-workflow-from-a-rule"></a>규칙에서 워크플로 트리거

Power Automate 또는 Azure Logic Apps에서 워크플로를 트리거하려면 먼저 IoT Central 애플리케이션에서 규칙이 필요합니다. 자세한 내용은 [Azure IoT Central에서 규칙 및 작업 구성](./howto-configure-rules.md)을 참조하세요.

**Azure IoT Central V3 - 미리 보기** 커넥터를 Power Automate의 트리거로 추가하려면 다음을 수행합니다.

1. Power Automate에서 **+ 만들기** 를 선택하고 **사용자 지정** 탭을 선택합니다.
1. *IoT Central* 을 검색하고 **Azure IoT Central V3 - 미리 보기** 커넥터를 선택합니다.
1. 트리거 목록에서 **규칙이 실행되는 경우(미리 보기)** 를 선택합니다.
1. **규칙이 실행되는 경우** 단계에서 사용 중인 IoT Central 애플리케이션 및 규칙을 선택합니다.

**Azure IoT Central V3 - 미리 보기** 커넥터를 Azure Logic Apps의 트리거로 추가하려면 다음을 수행합니다.

1. **Logic Apps 디자이너** 에서 **빈 논리 앱** 템플릿을 선택합니다.
1. 디자이너에서 **사용자 지정** 탭을 선택합니다.
1. *IoT Central* 을 검색하고 **Azure IoT Central V3 - 미리 보기** 커넥터를 선택합니다.
1. 트리거 목록에서 **규칙이 실행되는 경우(미리 보기)** 를 선택합니다.
1. **규칙이 실행되는 경우** 단계에서 사용 중인 IoT Central 애플리케이션 및 규칙을 선택합니다.

:::image type="content" source="./media/howto-configure-rules-advanced/triggers.png" alt-text="Azure IoT Central- 미리 보기 커넥터를 찾고 트리거를 선택합니다.":::

이제 워크플로에 더 많은 단계를 추가하여 통합 시나리오를 구축할 수 있습니다.

## <a name="run-an-action"></a>작업 실행

Power Automate 및 Azure Logic Apps 워크플로에서 IoT Central 애플리케이션의 작업을 실행할 수 있습니다. 먼저 워크플로를 만들고 커넥터를 사용하여 워크플로를 시작하는 트리거를 정의합니다. 그런 다음, **Azure IoT Central V3 - 미리 보기** 커넥터를 작업으로 사용합니다.

**Azure IoT Central V3 - 미리 보기** 커넥터를 Power Automate의 작업으로 추가하려면 다음을 수행합니다.

1. Power Automate의 **작업 선택** 패널에서 **사용자 지정** 탭을 선택합니다.
1. *IoT Central* 을 검색하고 **Azure IoT Central V3 - 미리 보기** 커넥터를 선택합니다.
1. 작업 목록에서 사용하려는 IoT Central 작업을 선택합니다.
1. 작업 단계에서 선택한 작업에 대한 구성을 완료합니다. 그런 다음 **저장** 을 선택합니다.

**Azure IoT Central V3 - 미리 보기** 커넥터를 Azure Logic Apps의 작업으로 추가하려면 다음을 수행합니다.

1. **Logic Apps 디자이너** 의 **작업 선택** 패널에서 **사용자 지정** 탭을 선택합니다.
1. *IoT Central* 을 검색하고 **Azure IoT Central V3 - 미리 보기** 커넥터를 선택합니다.
1. 작업 목록에서 사용하려는 IoT Central 작업을 선택합니다.
1. 작업 단계에서 선택한 작업에 대한 구성을 완료합니다. 그런 다음 **저장** 을 선택합니다.

:::image type="content" source="./media/howto-configure-rules-advanced/actions.png" alt-text="Azure IoT Central V3 커넥터를 찾고 작업을 선택합니다.":::

## <a name="list-of-actions"></a>작업 목록

다음 목록에서는 **Azure IoT Central V3 - 미리 보기** 커넥터 및 해당 구성 옵션에서 사용 가능한 모든 IoT Central 작업을 보여 줍니다. 대부분의 필드에는 동적으로 생성된 콘텐츠가 있을 수 있습니다. 예를 들어 이전 단계에서 현재 단계가 작동하는 디바이스 ID를 결정할 수 있습니다.

### <a name="create-or-update-a-device"></a>도메인을 만들기 또는 업데이트

이 작업을 사용하여 IoT Central 애플리케이션에서 디바이스를 만들거나 업데이트합니다.

| 필드 | Description |
| ----- | ----------- |
| 애플리케이션 | IoT Central 애플리케이션 목록에서 선택합니다. |
| 디바이스 | 만들거나 업데이트할 디바이스의 고유 ID입니다. |
| 승인됨 | 디바이스가 IoT Central에 연결하도록 승인되었는지 여부를 선택합니다. |
| 디바이스 설명 | 디바이스에 대한 상세한 설명입니다. |
| 디바이스 이름 | 디바이스의 표시 이름입니다. |
| 디바이스 템플릿 | IoT Central 애플리케이션의 디바이스 템플릿 목록에서 선택합니다. |
| 시뮬레이션됨 | 디바이스가 시뮬레이션되는지 여부를 선택합니다. |

### <a name="delete-a-device"></a>디바이스 삭제

이 작업을 사용하여 IoT Central 애플리케이션에서 디바이스를 삭제할 수 있습니다.

| 필드 | Description |
| ----- | ----------- |
| 애플리케이션 | IoT Central 애플리케이션 목록에서 선택합니다. |
| 디바이스 | 삭제할 디바이스의 고유 ID입니다. |

### <a name="execute-a-device-command"></a>디바이스 명령 실행

이 작업을 사용하여 디바이스 인터페이스 중 하나에 정의된 명령을 실행합니다.

| 필드 | Description |
| ----- | ----------- |
| 애플리케이션 | IoT Central 애플리케이션 목록에서 선택합니다. |
| 디바이스 | 삭제할 디바이스의 고유 ID입니다. |
| 디바이스 구성 요소 | 명령을 포함하는 디바이스 템플릿의 인터페이스입니다. |
| 디바이스 명령 | 선택한 인터페이스에서 명령 중 하나를 선택합니다. |
| 디바이스 템플릿 | IoT Central 애플리케이션의 디바이스 템플릿 목록에서 선택합니다. |
| 디바이스 명령 요청 페이로드 | 명령에 요청 페이로드가 필요한 경우 여기에 추가합니다.  |

> [!NOTE]
> 디바이스 템플릿을 선택할 때까지 디바이스 구성 요소를 선택할 수 없습니다.

### <a name="get-a-device-by-id"></a>ID로 디바이스 가져오기

이 작업을 사용하여 디바이스의 세부 정보를 검색합니다.

| 필드 | Description |
| ----- | ----------- |
| 애플리케이션 | IoT Central 애플리케이션 목록에서 선택합니다. |
| 디바이스 | 삭제할 디바이스의 고유 ID입니다. |

다른 작업에서 동적 식에 반환된 세부 정보를 사용할 수 있습니다. 반환되는 디바이스 세부 정보는 다음과 같습니다. **승인됨**, **본문**, **디바이스 설명**, **디바이스 이름**, **디바이스 템플릿**, **프로비전됨**, **시뮬레이션됨**.

### <a name="get-device-cloud-properties"></a>디바이스 클라우드 속성 가져오기

이 작업을 사용하여 특정 디바이스에 대한 클라우드 속성 값을 검색합니다.

| 필드 | Description |
| ----- | ----------- |
| 애플리케이션 | IoT Central 애플리케이션 목록에서 선택합니다. |
| 디바이스 | 삭제할 디바이스의 고유 ID입니다. |
| 디바이스 템플릿 | IoT Central 애플리케이션의 디바이스 템플릿 목록에서 선택합니다. |

다른 작업에서 동적 식에 반환된 클라우드 속성 값을 사용할 수 있습니다.

### <a name="get-device-properties"></a>디바이스 속성 가져오기

이 작업을 사용하여 특정 디바이스에 대한 속성 값을 검색합니다.

| 필드 | Description |
| ----- | ----------- |
| 애플리케이션 | IoT Central 애플리케이션 목록에서 선택합니다. |
| 디바이스 | 삭제할 디바이스의 고유 ID입니다. |
| 디바이스 템플릿 | IoT Central 애플리케이션의 디바이스 템플릿 목록에서 선택합니다. |

다른 작업에서 동적 식에 반환된 속성 값을 사용할 수 있습니다.

### <a name="get-device-telemetry-value"></a>디바이스 원격 분석 값 가져오기

이 작업을 사용하여 특정 디바이스에 대한 원격 분석 값을 검색합니다.

| 필드 | Description |
| ----- | ----------- |
| 애플리케이션 | IoT Central 애플리케이션 목록에서 선택합니다. |
| 디바이스 | 삭제할 디바이스의 고유 ID입니다. |
| 디바이스 템플릿 | IoT Central 애플리케이션의 디바이스 템플릿 목록에서 선택합니다. |

다른 작업에서 동적 식에 반환된 원격 분석 값을 사용할 수 있습니다.

### <a name="update-device-cloud-properties"></a>디바이스 클라우드 속성 업데이트

이 작업을 사용하여 특정 디바이스에 대한 클라우드 속성 값을 업데이트합니다.

| 필드 | Description |
| ----- | ----------- |
| 애플리케이션 | IoT Central 애플리케이션 목록에서 선택합니다. |
| 디바이스 | 삭제할 디바이스의 고유 ID입니다. |
| 디바이스 템플릿 | IoT Central 애플리케이션의 디바이스 템플릿 목록에서 선택합니다. |
| 클라우드 속성 | 디바이스 템플릿을 선택하면 템플릿에 정의된 각 클라우드 속성에 대한 필드가 추가됩니다. |

### <a name="update-device-properties"></a>디바이스 속성 업데이트

이 작업을 사용하여 특정 디바이스에 대한 쓰기 가능한 속성 값을 업데이트합니다.

| 필드 | Description |
| ----- | ----------- |
| 애플리케이션 | IoT Central 애플리케이션 목록에서 선택합니다. |
| 디바이스 | 삭제할 디바이스의 고유 ID입니다. |
| 디바이스 템플릿 | IoT Central 애플리케이션의 디바이스 템플릿 목록에서 선택합니다. |
| 쓰기 가능한 속성 | 디바이스 템플릿을 선택하면 템플릿에 정의된 각 쓰기 가능한 속성에 대한 필드가 추가됩니다. |

## <a name="next-steps"></a>다음 단계

Azure IoT Central 애플리케이션에서 고급 규칙을 만드는 방법을 배웠으므로 이제 [Azure IoT Central 애플리케이션에서 디바이스 데이터를 분석](howto-create-analytics.md)하는 방법을 배울 수 있습니다.