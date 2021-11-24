---
title: 빠른 시작 - Azure IoT Central에서 규칙 및 작업 구성
description: 이 빠른 시작에서는 IoT Central 애플리케이션에서 원격 분석 기반 규칙 및 작업을 구성하는 방법을 보여 줍니다.
author: dominicbetts
ms.author: dobett
ms.date: 05/27/2021
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc, mode-other
ms.openlocfilehash: 0164d0e3354db03be2747ba15438261cc657b02b
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133044197"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>빠른 시작: Azure IoT Central에서 디바이스에 대한 규칙 및 작업 구성

이 빠른 시작에서는 누군가가 전화를 뒤집으면 메일을 보내는 IoT Central 규칙을 만듭니다.

## <a name="prerequisites"></a>필수 조건

시작하기 전에 이전 빠른 시작 [Azure IoT Central 애플리케이션 만들기 및 사용](./quick-deploy-iot-central.md)을 완료하고 **IoT 플러그 앤 플레이** 스마트폰 앱을 IoT Central 애플리케이션에 연결해야 합니다.

## <a name="create-a-telemetry-based-rule"></a>원격 분석 기반 규칙 만들기

이 스마트폰 앱은 가속도계 센서의 값을 포함하는 원격 분석을 보냅니다. 휴대폰이 아래를 향하고 있으면 **z** 값이 `9`보다 큰 것이고, 휴대폰이 위를 향하고 있으면 **z** 값이 `-9`보다 작은 것입니다.

1. 애플리케이션에 새 원격 분석 기반 규칙을 추가하려면 왼쪽 창에서 **규칙** 을 선택합니다.

1. 새 규칙을 만들려면 **규칙 만들기** 를 선택합니다.

1. 규칙 이름으로 **Phone turned over** 를 입력합니다.

1. **대상 디바이스** 섹션에서 **IoT 플러그 앤 플레이 모바일** 을 **디바이스 템플릿** 으로 선택합니다. 이 옵션은 디바이스 템플릿 유형별로 규칙이 적용되는 디바이스를 필터링합니다. **+ 필터** 를 선택하여 더 많은 필터 조건을 추가할 수 있습니다.

1. **조건** 섹션에서 규칙을 트리거하는 항목을 정의합니다. 다음 정보를 사용하여 가속도계 z축 원격 분석을 기준으로 단일 조건을 정의합니다. 이 규칙은 집계를 사용하여 5분마다 각 디바이스에 대해 최대 하나의 메일을 받습니다.

    | 필드            | 값            |
    |------------------|------------------|
    | 시간 집계 | On, 5 minutes    |
    | 원격 분석        | Acceleration / Z |
    | 연산자         | 보다 작음     |
    | 집계      | 최소          |
    | 값            | -9               |

    :::image type="content" source="media/quick-configure-rules/rule-target-condition.png" alt-text="규칙 조건을 보여 주는 스크린샷":::

1. 규칙이 트리거될 때 실행할 메일 작업을 추가하려면 **동작** 섹션에서 **+ 메일** 을 선택합니다.

1. 다음 표의 정보를 사용하여 작업을 정의한 다음, **완료** 를 선택합니다.

    | 설정      | 값                    |
    |--------------|--------------------------|
    | 표시 이름 | Your phone moved         |
    | 수행할 작업           | 이메일 주소       |
    | 메모        | Your phone is face down! |

    > [!TIP]
    > 메일 알림을 받으려면 이메일 주소가 [애플리케이션의 사용자 ID](howto-manage-users-roles.md)여야 하며, 사용자는 최소 한 번 이상 애플리케이션에 로그인했어야 합니다.

    :::image type="content" source="media/quick-configure-rules/rule-action.png" alt-text="규칙에 추가된 이메일 작업을 보여주는 스크린샷":::

1. **저장** 을 선택합니다. 이제 **규칙** 페이지에 규칙이 나열됩니다.

## <a name="test-the-rule"></a>규칙 테스트

규칙은 저장 한 직후 발효됩니다. 규칙에서 정의된 조건이 충족되면 IoT Central이 작업에서 지정된 주소로 메일을 보냅니다.

이 규칙을 트리거하려면 스마트폰 앱이 데이터를 보내고 있는지 확인하고 앞면이 아래를 향하게 하여 책상 위에 놓습니다. 이제 앱은 `-9`보다 작은 가속도계 z축 원격 분석 값을 전송합니다. 5분 후에 IoT Central은 스마트폰 앞면이 아래를 향하고 있음을 알리는 메일을 전송합니다.

테스트를 완료한 후에는 받은 편지함에 더 이상 알림 메일이 수신되지 않도록 이 규칙을 사용하지 않도록 설정합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 다음을 수행하는 방법을 알아보았습니다.

* 원격 분석 기반 규칙 만들기
* 작업 추가

IoT Central 애플리케이션을 다른 서비스와 통합하는 방법에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [IoT Central 애플리케이션에서 데이터 내보내기 및 처리](quick-export-data.md).
