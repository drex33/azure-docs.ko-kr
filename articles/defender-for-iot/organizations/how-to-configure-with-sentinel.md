---
title: 조직용 Defender for IoT를 사용하여 Azure Sentinel 구성
description: Defender for IoT 솔루션에서 데이터를 수신하도록 Azure Sentinel을 구성하는 방법을 설명합니다.
ms.topic: how-to
ms.date: 06/14/2021
ms.openlocfilehash: f747f5c2d32e0f0485677bdd1f1b878b4dc1e6d3
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122529846"
---
# <a name="connect-your-data-from-defender-for-iot-for-organizations-to-azure-sentinel-public-preview"></a>조직의 Defender for IoT에서 Azure Sentinel로 데이터 연결(공개 미리 보기)

Defender for IoT 커넥터를 사용하여 모든 Defender for IoT 이벤트를 Azure Sentinel로 스트리밍합니다. 

해당 통합을 통해 조직은 IT와 OT의 경계를 넘나드는 다단계 공격을 신속하게 감지할 수 있습니다. 또한 Azure Sentinel의 SOAR(보안 오케스트레이션, 자동화, 응답) 기능과 Defender for IoT의 통합으로 기본 제공 OT 최적화 플레이북을 사용하여 응답과 예방을 자동화할 수 있습니다. 

## <a name="prerequisites"></a>필수 구성 요소

- Azure Sentinel이 배포된 작업 영역에 대한 **읽기** 및 **쓰기** 권한
- **Defender for IoT** 를 관련 IoT Hub에서 **사용하도록 설정** 해야 합니다.
- 연결하려는 **구독** 에 대한 **기여자** 권한이 있어야 합니다.

## <a name="connect-to-defender-for-iot"></a>Defender for IoT에 연결

1. Azure Sentinel에서 **데이터 커넥터** 를 선택한 다음 갤러리에서 **Defender for IoT**(아직 IoT용 Azure Security Center라고 되어 있을 수 있음)를 선택합니다.

1. 오른쪽 창 하단에서 **커넥터 페이지 열기** 를 클릭합니다.

1. Azure Sentinel로 경고 및 디바이스 경고를 스트리밍할 각 IoT Hub 구독 옆에 있는 **연결** 을 클릭합니다.
    - 구독 내에서 하나 이상의 IoT Hub에서 Defender for IoT를 사용하도록 설정하지 않은 경우 오류 메시지가 표시됩니다. IoT Hub 내에서 Defender for IoT를 사용하도록 설정하여 오류를 제거합니다.

1. Defender for IoT의 경고에서 Azure Sentinel에 인시던트를 자동으로 생성할지를 결정할 수 있습니다. **인시던트 만들기** 에서 **사용** 을 선택해 기본 분석 규칙을 사용하도록 설정하여 생성된 경고로부터 인시던트를 자동으로 만듭니다. 이 규칙은 **분석** > **활성 규칙** 에서 변경하거나 편집할 수 있습니다.

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

**구독** 을 연결한 후 약 15분 후에 허브 데이터를 Azure Sentinel에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Defender for IoT를 Azure Sentinel에 연결하는 방법을 알아봤습니다. 위협 탐지 및 보안 데이터 액세스에 대해 자세히 알아보려면 다음 문서를 참조하세요.

- Azure Sentinel을 사용하여 [빠른 시작: Azure Sentinel을 시작](/azure/defender-for-iot/organizations/articles/sentinel/get-visibility.md)하는 방법을 알아봅니다.