---
title: 장치 빌더의 IoT 용 Defender를 사용 하 여 Microsoft 센티널 구성
description: 이 문서에서는 장치 빌더 용 IoT 용 Defender 솔루션에서 데이터를 받도록 Microsoft 센티널을 구성 하는 방법을 설명 합니다.
ms.topic: how-to
ms.date: 11/09/2021
ms.openlocfilehash: e64b0db2af1c1b742c3997e616eea43f5c969522
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306171"
---
# <a name="connect-your-data-from-defender-for-iot-for-device-builders-to-microsoft-sentinel-public-preview"></a>장치 빌더가 IoT 용 Defender의 데이터를 Microsoft 센티널로 커넥트 (공개 미리 보기)

IoT 용 Defender 커넥터를 사용 하 여 모든 IoT 이벤트 용 Defender를 Microsoft 센티널로 스트리밍합니다. 

해당 통합을 통해 조직은 IT와 OT의 경계를 넘나드는 다단계 공격을 신속하게 감지할 수 있습니다. 또한 Microsoft 센티널의 보안 오케스트레이션, 자동화 및 대응 (대화 충성도) 기능을 사용 하 여 IoT 용 Defender를 통합 하면 기본 제공 되는 OT 기반 플레이 북을 사용 하 여 자동화 된 응답과 방지를 사용할 수 있습니다. 

## <a name="prerequisites"></a>필수 구성 요소

- Microsoft 센티널을 배포할 작업 영역에 대 한 **읽기** 및 **쓰기** 권한
- **Defender for IoT** 를 관련 IoT Hub에서 **사용하도록 설정** 해야 합니다.
- 연결하려는 **구독** 에 대한 **기여자** 권한이 있어야 합니다.

## <a name="connect-to-defender-for-iot"></a>Defender for IoT에 연결

1. Microsoft 센티널에서 **데이터 커넥터** 를 선택한 다음 갤러리에서 **iot 용 Defender** (이전의 Azure Security Center iot)를 선택 합니다.

1. 오른쪽 창 하단에서 **커넥터 페이지 열기** 를 클릭합니다.

1. Microsoft 센티널로 스트리밍할 경고 및 장치 경고를 포함 하는 각 IoT Hub 구독 옆에 있는 **커넥트** 를 클릭 합니다.
    - 구독 내에서 하나 이상의 IoT Hub에서 Defender for IoT를 사용하도록 설정하지 않은 경우 오류 메시지가 표시됩니다. IoT Hub 내에서 Defender for IoT를 사용하도록 설정하여 오류를 제거합니다.

1. Microsoft 센티널에서 IoT에 대 한 경고를 자동으로 생성할지 여부를 결정할 수 있습니다. **인시던트 만들기** 에서 **사용** 을 선택해 기본 분석 규칙을 사용하도록 설정하여 생성된 경고로부터 인시던트를 자동으로 만듭니다. 이 규칙은 **분석** > **활성 규칙** 에서 변경하거나 편집할 수 있습니다.

> [!NOTE]
> 연결을 변경한 후 **구독** 목록을 새로 고치는 데 10초 이상 걸릴 수 있습니다. 

## <a name="log-analytics-alert-view"></a>Log Analytics 경고 보기

Log Analytics에서 관련 스키마를 사용하여 Defender for IoT 경고를 표시하려면 다음을 수행합니다.

1. **로그** > **SecurityInsights** > **SecurityAlert** 를 열거나 **SecurityAlert** 를 검색합니다.

1. 다음 kql 필터를 사용하여 Defender for IoT에서 생성된 경고만 표시하도록 필터링합니다.

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
```

### <a name="service-notes"></a>서비스 참고

**구독** 을 연결한 후에는 약 15 분 후에 허브 데이터를 Microsoft 센티널에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 IoT 용 Defender를 Microsoft 센티널에 연결 하는 방법을 알아보았습니다. 위협 탐지 및 보안 데이터 액세스에 대해 자세히 알아보려면 다음 문서를 참조하세요.

- Microsoft 센티널을 사용 하 여 [빠른 시작: Microsoft 센티널 시작](../../sentinel/get-visibility.md)을 사용 하는 방법을 알아봅니다.
- [IoT 보안 데이터에 액세스](how-to-security-data-access.md)하는 방법 알아보기
