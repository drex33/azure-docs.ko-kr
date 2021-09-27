---
title: V2 Azure IoT Central 애플리케이션을 V3에 마이그레이션하기 | Microsoft Docs
description: V2 Azure IoT Central 애플리케이션을 V3에 마이그레이션하는 방법 알아보기
author: troyhopwood
ms.author: troyhop
ms.date: 09/17/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: contperf-fy22q1
ms.openlocfilehash: 6a7a0d8f6fe2b057baf1fa568700b4139ec31398
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128660902"
---
# <a name="migrate-your-v2-iot-central-application-to-v3"></a>V2 IoT Central 애플리케이션을 V3에 마이그레이션하기

현재, IoT Central 애플리케이션을 새로 만드는 경우, V3 애플리케이션이 됩니다. 이전에 애플리케이션을 만들었다면 그 시기에 따라 V2일 수 있습니다. 본 문서에서는 IoT Central의 최신 기능을 사용하도록 V2 애플리케이션을 V3 애플리케이션으로 마이그레이션하는 방법을 설명합니다.

자세한 내용은 중지 [공지](/answers/questions/529295/retirement-announcement-upgrade-to-iot-central-v3.html)를 참조하세요.

IoT Central 애플리케이션의 버전을 식별하는 방법을 알아보려면 [내 애플리케이션에 대한 정보를 가져오려면 어떻게 해야 하나요?](howto-faq.yml#how-do-i-get-information-about-my-application-)를 참조하세요.

V2 애플리케이션을 V3 애플리케이션으로 마이그레이션하려면 다음 단계를 거칩니다.

1. V2 애플리케이션에서 V3 애플리케이션을 새로 만듭니다.
1. V3 애플리케이션을 구성합니다.
1. V2 애플리케이션은 삭제합니다.

## <a name="create-a-new-v3-application"></a>새 V3 애플리케이션 만들기

IoT Central 기존 V3 애플리케이션으로 마이그레이션할 수 없습니다. V2에서 V3 애플리케이션으로 기존 디바이스를 이동하려면 마이그레이션 마법사를 사용하여 V3 애플리케이션을 만듭니다.

마이그레이션 마법사는:

- 새 V3 애플리케이션을 만듭니다.
- V3와의 호환성을 위하여 디바이스 템플릿을 확인합니다.
- 디바이스 템플릿을 모두 새 V3 애플리케이션에 복사합니다.
- 사용자 및 역할 할당을 모두 새 V3 애플리케이션에 복사합니다.

> [!NOTE]
> V3와의 디바이스 호환성을 확보하기 위하여 디바이스 템플릿의 기본 형식은 개체 속성이 됩니다. 디바이스 코드는 변경하지 않아도 됩니다.

본인이 관리자인 경우여야 V3 애플리케이션으로 마이그레이션할 수 있습니다.

1. **관리** 메뉴에서 **V3 애플리케이션으로 마이그레이션** 을 선택합니다.

    :::image type="content" source="media/howto-migrate/migrate-menu.png" alt-text="애플리케이션 마이그레이션 마법사를 보여 주는 스크린샷":::

1. 새 **애플리케이션 이름** 을 입력합니다. 자동 생성된 **URL** 을 변경하는 것은 옵션입니다. URL은 현재 V2 애플리케이션의 URL과 달라야 합니다. 그러나, V2 애플리케이션을 삭제한 뒤 URL을 변경할 수 있습니다.

1. **새 V3 앱 만들기** 를 선택합니다.

    :::image type="content" source="media/howto-migrate/create-app.png" alt-text="애플리케이션 마이그레이션 마법사의 옵션을 보여 주는 스크린샷":::

    디바이스 템플릿이 몇 개이며 얼마나 복잡한지에 따라 프로세스 완료에 몇 분 정도 걸릴 수 있습니다.

    > [!Warning]
    > V3 애플리케이션 만들기는 디바이스 템플릿에 숫자로 시작하거나 `+`, `*`, `?`, `^`, `$`, `(`, `)`, `[`, `]`, `{`, `}`, `|`, `\` 가운데 하나의 문자라도 포함하는 필드가 있는 경우 실패합니다. V3 애플리케이션이 사용하는 DTDL 디바이스 템플릿 스키마에서는 해당 문자들을 허용하지 않습니다. V3 애플리케이션으로 마이그레이션하기 전에 이러한 문제를 해결하기 위하여 디바이스 템플릿을 업데이트하세요.

1. 새 V3 앱이 준비되면 **새 앱 열기** 를 선택하여 앱을 엽니다.

    :::image type="content" source="media/howto-migrate/open-app.png" alt-text="애플리케이션 마이그레이션이 끝난 뒤의 애플리케이션 마이그레이션 마법사를 보여 주는 스크린샷":::

## <a name="configure-the-v3-application"></a>V3 애플리케이션 구성하기

새 V3 애플리케이션을 만들고 나면 V2 애플리케이션에서 V3 애플리케이션으로 디바이스를 옮기기 전에 구성을 변경합니다.

> [!TIP]
> V2와는 몇 가지 차이점이 있기 때문에 [V3에 익숙해지도록](overview-iot-central-tour.md#navigate-your-application) 잠시 시간을 내어 보세요.

다음은 고려하면 좋을 몇 가지 구성 단계입니다.

- [대시보드 구성](howto-manage-dashboards.md)
- [데이터 내보내기 구성](howto-export-data.md)
- [규칙 및 작업 구성](quick-configure-rules.md)
- [애플리케이션 UI 사용자 지정](howto-customize-ui.md)

요구에 맞춰 V3 애플리케이션을 구성하면, V2 애플리케이션에서 V3 애플리케이션으로 디바이스를 옮길 준비가 된 것입니다.

## <a name="move-your-devices-to-the-v3-application"></a>디바이스를 V3 애플리케이션으로 이동하기

해당 단계가 완료되면, 모든 디바이스는 새 V3 애플리케이션과의 통신만을 수행합니다.

> [!IMPORTANT]
> 디바이스를 V3 애플리케이션으로 이동하기 전에 V3 애플리케이션에서 만들었던 디바이스는 모두 삭제합니다.

이번 단계에서는 기존의 디바이스를 모두 새 V3 애플리케이션으로 이동합니다. 디바이스 데이터는 복사하지 않습니다.

디바이스 이동을 시작하려면 **모든 디바이스 이동** 을 선택합니다. 단계 완료에 몇 분 정도 걸릴 수 있습니다.

:::image type="content" source="media/howto-migrate/move-devices.png" alt-text="디바이스 이동 옵션이 표시된 상태의 애플리케이션 마이그레이션 마법사를 보여 주는 스크린샷":::

이동을 완료한 다음에는 디바이스를 모두 재시작하여 확실히 새 V3 애플리케이션에 연결되도록 합니다.

> [!WARNING]
> V2 애플리케이션이 작동하지 않는 상황이므로 V3 애플리케이션을 삭제하면 안됩니다.

## <a name="delete-your-old-v2-application"></a>기존 V2 애플리케이션 삭제하기

새 V3 애플리케이션에서 모든 것이 예상대로 작동함을 확인하고 나면, 기존의 V2 애플리케이션은 삭제합니다. 본 단계를 통하여 더 이상 사용하지 않는 애플리케이션에 대한 요금 청구를 확실히 중단합니다.

> [!Note]
> 애플리케이션을 삭제하려면 애플리케이션을 만들 때 선택한 Azure 구독의 리소스 삭제 권한을 가지고 있어야 합니다. 자세한 내용은 [역할 기반 액세스 제어를 사용하여 Azure 구독 리소스에 대한 액세스 관리](../../role-based-access-control/role-assignments-portal.md)를 참조하세요.

1. V2 애플리케이션에서 메뉴의 **관리** 탭을 선택합니다.
2. **삭제** 를 선택하여 IoT Central 애플리케이션을 영구적으로 삭제합니다. 해당 애플리케이션과 관련된 모든 데이터를 영구적으로 삭제하는 옵션입니다.

## <a name="next-steps"></a>다음 단계

이제 애플리케이션의 마이그레이션 방법을 확인한 상태이므로, 다음 단계에서는 [Azure IoT Central UI](overview-iot-central-tour.md)를 검토하여 V3에서 변경된 점을 확인하는 것이 좋습니다.
