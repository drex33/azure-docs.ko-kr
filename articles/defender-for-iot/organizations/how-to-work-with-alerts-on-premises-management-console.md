---
title: 온-프레미스 관리 콘솔의 경고 작업
description: 온-프레미스 관리 콘솔을 사용하여 네트워크에서 최근에 발생한 위협에 대한 엔터프라이즈 관점을 얻고, 센서 사용자가 이를 처리하는 방법을 더 잘 이해할 수 있습니다.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 2f97f4109f34ef448ccf67c02758c8df2d556c6d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293310"
---
# <a name="work-with-alerts-on-the-on-premises-management-console"></a>온-프레미스 관리 콘솔의 경고 작업 

관리 콘솔의 **경고** 창에서 다음 작업을 수행할 수 있습니다.

- 경고 필터 작업

- 경고 카운터 작업

- 경고 정보 보기

- 경고 이벤트 관리

- 경고 제외 규칙 만들기

- 외부 시스템에서 경고 제외 규칙 트리거

- 경고 그룹을 사용하여 인시던트 워크플로 가속화

## <a name="view-alerts-in-the-on-premises-management-console"></a>온-프레미스 관리 콘솔에서 경고 보기

온-프레미스 관리 콘솔은 연결된 모든 센서의 경고를 집계합니다. 이를 통해 네트워크에서 최근에 발생한 위협에 대한 엔터프라이즈 관점을 제공하고 센서 사용자가 이를 처리하는 방법을 더욱 잘 이해할 수 있습니다.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alerts-with-samples.png" alt-text="경고 창의 스크린샷.":::

### <a name="work-with-alert-filters"></a>경고 필터 작업

**경고** 창에는 온-프레미스 관리 콘솔에 연결된 센서에 의해 생성된 경고가 표시됩니다. 연결된 센서에 대한 모든 경고를 보거나 다음에서 전송된 경고를 표시할 수 있습니다.

- 사이트

- 영역

- 디바이스

- 센서

모든 경고를 보려면 **필터 지우기** 를 선택합니다.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/clear-filters.png" alt-text="필터 지우기 단추를 선택하여 필터를 지웁니다.":::

### <a name="work-with-alert-counters"></a>경고 카운터 작업

경고 카운터는 심각도 및 승인된 상태별로 경고 내역을 제공합니다.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/number-of-alerts.png" alt-text="경고 카운터는 보유한 경고 수를 표시합니다.":::

경고 카운터에 표시되는 심각도 수준은 다음과 같습니다.

- **위험**: 즉시 처리해야 하는 악의적인 공격을 나타냅니다.

- **중요**: 해결해야 하는 중요한 보안 위협을 나타냅니다.

- **일반**: 보안 위협을 포함할 수 있는 기본 동작의 일부 편차를 나타냅니다.

- **경고**: 보안 위협이 없는 기본 동작의 일부 편차를 나타냅니다.

심각도 수준은 미리 정의되어 있습니다.

카운터를 조정하여 승인되거나 승인되지 않은 경고를 기반으로 숫자를 제공할 수 있습니다. 승인되지 않은 경고가 Defender for IoT 센서에서 트리거되었지만 센서의 운영자가 아직 검토하지 않았습니다.

**승인된 경고 표시** 옵션을 선택하면 모든 승인된 경고가 **경고** 창에 표시됩니다.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/show-acknowledged-alerts.png" alt-text="승인된 경고를 확인합니다.":::

### <a name="view-alert-information"></a>경고 정보 보기

경고는 다음 정보를 제공합니다.

- 경고 이벤트의 요약.

- 경고 심각도.

- 경고를 감지한 센서의 경고에 대한 링크.

- 경고 UUID. UUID는 센서에서 감지된 경고 이벤트에 연결된 경고 ID와 고유한 시스템 ID 번호를 하이픈으로 구분하여 구성됩니다.

**온-프레미스 관리 콘솔 경고 UUID**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/unauthorized-internet-connectivity.png" alt-text="디바이스가 연결되어 있지만 권한이 부여되지 않았습니다.":::

**센서 경고 ID**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/internet-connectivity-alert.png" alt-text="센서 경고 ID입니다.":::

UUID를 사용하면 온-프레미스 관리 콘솔에 표시되는 각 경고를 검색하고 고유한 숫자로 식별할 수 있습니다. 이는 여러 센서에서 생성된 경고가 동일한 경고 ID를 생성할 수 있기 때문에 필요합니다.

> [!NOTE]
> 기본적으로 전달 규칙이 정의된 경우(ArcSight, syslog 서버, QRadar, Sentinel, NetWitness)에는 UUID가 다음과 같은 파트너 시스템에 표시됩니다. 특별한 설정은 필요하지 않습니다.

**경고 정보를 보려면 다음을 수행합니다.**

- 경고 목록에서 경고를 선택합니다.

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-information.png" alt-text="경고 정보의 스크린샷.":::

**센서에서 경고를 보려면 다음을 수행합니다.**

- 경고에서 **센서 열기** 를 선택합니다.

**영역 맵에서 디바이스를 보려면 다음을 수행합니다.**

- 경고 디바이스 및 연결된 모든 디바이스에 초점을 맞춘 디바이스 맵을 보려면 **디바이스 표시** 를 선택합니다.

## <a name="manage-alert-events"></a>경고 이벤트 관리

온-프레미스 관리 콘솔에서 경고 이벤트를 관리하는 데 사용할 수 있는 몇 가지 옵션이 있습니다.

- 경고 이벤트를 학습하거나 승인합니다. **학습 및 승인** 을 선택하여 승인할 수 있는 모든 경고 이벤트를 알아보고 현재 승인되지 않은 모든 경고 이벤트를 승인할 수 있습니다.

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/learn-and-acknowledge.png" alt-text="모두 알아보려면 학습 및 승인을 선택합니다.":::

- 경고 이벤트를 음소거 및 음소거 해제합니다.

경고 이벤트를 학습, 승인, 음소거하는 방법에 대한 자세한 내용은 센서 [경고 이벤트 관리](how-to-manage-the-alert-event.md) 문서를 참조하세요.

## <a name="export-alert-information"></a>경고 정보 내보내기

경고 정보를 .csv 파일로 내보냅니다. 검색된 모든 경고의 정보를 내보내거나 필터링된 보기를 기반으로 하여 정보를 내보낼 수 있습니다. 다음 정보를 내보냅니다.

- 소스 주소
- 대상 주소
- 경고 제목
- 경고 심각도
- 경고 메시지
- 추가 정보
- 확인된 상태
- PCAP 가용성

**경고를 내보내려면 다음을 수행합니다.**

1. 측면 메뉴에서 **경고** 를 선택합니다.

1. **내보내기** 를 선택합니다.

1. 여러 디바이스에 적용되는 각 경고에 대해 별도의 행으로 경고 정보를 내보내려면 **확장된 경고 내보내기** 를 선택합니다. 확장된 경고 내보내기를 선택하는 경우 .csv 파일은 각 행에 고유한 항목으로 경고의 중복 행을 만듭니다. 이 옵션을 사용하면 내보낸 경고 이벤트를 더욱 쉽게 조사할 수 있습니다.  

## <a name="create-forwarding-rules"></a>전달 규칙 만들기

**관리 콘솔에서 전달 규칙을 만들려면 다음을 수행합니다.**

1. 센서에 로그인합니다.

1. 측면 메뉴에서 **전달** 을 선택합니다.

1. :::image type="icon" source="media/how-to-work-with-alerts-on-premises-management-console/plus-add-icon.png" border="false"::: 아이콘을 선택합니다.

1. 전달 규칙 만들기 창에서 규칙의 이름을 입력합니다.

    :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/management-console-create-forwarding-rule.png" alt-text="전달 규칙 만들기 창의 필드에 의미 있는 이름을 입력합니다.":::

   전달 규칙을 트리거할 조건을 정의합니다. 전달 규칙 조건을 사용하면 센서에서 외부 시스템으로 전송되는 정보의 양을 정확하게 파악하고 관리할 수 있습니다.

1. 드롭다운 메뉴에서 심각도 수준을 선택합니다.

    심각도 수준을 기준으로, 전달할 최소 인시던트입니다. 예를 들어 **경미** 를 선택하면 경미한 경고 및 이 심각도 수준 이상의 모든 경고가 전달됩니다. 수준은 미리 정의됩니다.

1. 적용할 프로토콜을 선택합니다.

    검색된 트래픽이 특정 프로토콜을 통해 실행된 경우에만 전달 규칙을 트리거합니다. 드롭다운 목록에서 필요한 프로토콜을 선택하거나 모두 선택합니다.

1. 규칙을 적용할 엔진을 선택합니다.

    
   필요한 엔진을 선택하거나 모두 선택합니다. 선택한 엔진의 경고가 전송됩니다. 

1. 전달 규칙에서 시스템 알림을 보고하려면 확인란을 선택합니다.
  
1. 전달 규칙에서 경고 알림을 보고하려면 확인란을 선택합니다.

1. 적용할 작업을 추가하려면 **추가** 를 선택합니다. 선택한 작업에 필요한 매개 변수를 입력합니다.

    전달 규칙 작업은 센서에게 파트너 공급 업체 또는 서버에 경고 정보를 전달하도록 지시합니다. 각 전달 규칙에 대해 여러 작업을 만들 수 있습니다.

1. 원하는 경우 다른 작업을 추가합니다.

1. **저장** 을 선택합니다.

[전달된 경고 정보](how-to-forward-alert-information-to-partners.md#about-forwarded-alert-information)에 대해 자세히 알아볼 수 있습니다. [전달 규칙을 테스트](how-to-forward-alert-information-to-partners.md#test-forwarding-rules)하거나 [전달 규칙을 편집 및 삭제](how-to-forward-alert-information-to-partners.md#edit-and-delete-forwarding-rules)할 수도 있습니다. [전달 규칙 및 경고 제외 규칙](how-to-forward-alert-information-to-partners.md#forwarding-rules-and-alert-exclusion-rules)에 대해 자세히 알아볼 수도 있습니다.

## <a name="create-alert-exclusion-rules"></a>경고 제외 규칙 만들기

다음을 기준으로 경고 트리거를 무시하도록 Defender for IoT에 지시합니다.

- 표준 시간대 및 기간

- 디바이스 주소(IP, MAC, 서브넷)

- 경고 이름

- 특정 센서

Defender for IoT에서 경고를 트리거하는 작업을 무시하게 하려는 경우 경고 제외 규칙을 만듭니다.

예를 들어 특정 센서에 의해 모니터링되는 모든 디바이스에서 2일 동안 유지 관리 절차를 수행하는 경우 미리 정의된 기간 동안 이 센서가 검색한 경고를 표시하지 않도록 Defender for IoT에 지시하는 제외 규칙을 정의할 수 있습니다.

### <a name="alert-exclusion-logic"></a>경고 제외 논리

경고 규칙 논리는 `AND` 기반입니다. 즉, 모든 규칙 조건이 충족될 때만 경고가 트리거됩니다.

규칙 조건이 정의되지 않은 경우 모든 옵션이 조건에 포함됩니다. 예를 들어 규칙에 센서의 이름을 포함하지 않으면 모든 센서에 적용됩니다.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-v2.png" alt-text="제외 규칙 만들기 보기의 스크린샷.":::

규칙 요약은 **제외 규칙** 창에 표시됩니다.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/exclusion-summary-v2.png" alt-text="제외 규칙 요약 보기의 스크린샷.":::

제외 규칙을 사용하는 것 외에도 알림을 음소거하여 알림을 표시하지 않을 수 있습니다.

### <a name="create-exclusion-rules"></a>제외 규칙 만들기

**제외 규칙을 만들려면 다음을 수행합니다.**

1. 온-프레미스 관리 콘솔의 왼쪽 창에서 **경고 제외** 를 선택합니다. 열리는 창의 오른쪽 위 모퉁이에 있는 **추가** 아이콘 :::image type="icon" source="media/how-to-work-with-alerts-on-premises-management-console/add-icon.png" border="false":::을 선택하여 새 제외 규칙을 정의합니다. **제외 규칙 만들기** 대화 상자가 열립니다.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-view.png" alt-text="여기에 정보를 입력하여 경고 제외를 만듭니다.":::

1. **이름** 필드에 규칙 이름을 입력합니다. 이름에는 따옴표(`"`)를 사용할 수 없습니다.

1. **표준 시간대/기간** 섹션에서 특정 표준 시간대 내의 기간을 입력합니다. 한 표준 시간대에서 특정 기간 제외 규칙이 생성되지만 다른 표준 시간대에서 동시에 구현되어야 하는 경우 이 기능을 사용합니다. 예를 들어 세 가지 표준 시간대에서 오전 8시와 오전 10시 사이에 제외 규칙을 적용해야 할 수 있습니다. 이 경우에는 동일한 기간 및 관련 표준 시간대를 사용하는 세 가지 별도의 제외 규칙을 만듭니다.

1. **ADD** 를 선택합니다. 제외 기간 연결된 센서에는 경고가 생성되지 않습니다.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/by-the-time-period.png" alt-text="기간별로 보기의 스크린샷.":::

1. **디바이스 주소** 섹션에서 다음을 정의합니다.

  - 제외하려는 디바이스 IP 주소, MAC 주소 또는 서브넷 주소.

  - 제외된 디바이스, 원본, 대상에 대한 트래픽 방향.

1. **ADD** 를 선택합니다.

1. **경고 제목** 섹션에서 경고 제목 입력을 시작합니다. 드롭다운 목록에서 경고 제목 또는 제외하려는 경고 제목을 선택합니다.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-title.png" alt-text="경고 제목 보기의 스크린샷.":::

1. **ADD** 를 선택합니다.

1. **센서 이름** 섹션에서 센서 이름 입력을 시작합니다. 드롭다운 목록에서 센서 또는 제외하려는 센서를 선택합니다.

1. **ADD** 를 선택합니다.

1. **SAVE**(저장)를 선택합니다. 규칙 목록에 새 규칙이 표시됩니다.

경고를 음소거하거나 경고 제외 규칙을 만들어 경고를 표시하지 않을 수 있습니다. 이 섹션에서는 두 기능에 대한 잠재적 사용 사례에 관해 설명합니다.

- **제외 규칙**. 다음 경우에 제외 규칙을 작성합니다.

  - 데이터베이스에서 이벤트를 제외할 시간을 미리 알고 있습니다. 예를 들어 특정 센서에서 검색된 시나리오는 관련이 없는 경고를 트리거합니다. 예를 들어 특정 사이트의 조직 PLC에 대한 유지 관리 작업을 수행하고 이 사이트의 PLC와 관련된 경고를 표시하지 않을 것입니다.

  - Defender for IoT에서 특정 시간 범위(시스템 유지 관리 작업의 경우)에 대한 이벤트를 무시하도록 하려고 합니다.

  - 특정 서브넷의 이벤트를 무시하려고 합니다.

  - 하나의 규칙을 사용하여 여러 센서에서 생성된 경고 이벤트를 제어하려고 합니다.

  - 경고 제외를 이벤트 로그의 이벤트로 추적하지 않으려고 합니다.

- **음소거**. 다음 경우에 경고를 음소거합니다.

  - 음소거해야 하는 항목이 정해지지 않습니다. 이벤트의 상관관계를 미리 알 수 없습니다.

  - **경고** 창에서 경고를 표시하지 않으려는 경우에도 이벤트 로그에서 경고를 추적하려고 합니다.

  - 특정 채널에서 이벤트를 무시하려고 합니다.

### <a name="trigger-alert-exclusion-rules-from-external-systems"></a>외부 시스템에서 경고 제외 규칙 트리거

외부 시스템에서 경고 제외 규칙을 트리거합니다. 예를 들어, 엔터프라이즈 발권 시스템 또는 네트워크 유지 관리 프로세스를 관리하는 시스템에서 제외 규칙을 관리합니다.

규칙을 적용할 센서, 엔진, 시작 시간, 종료 시간을 정의합니다. 자세한 내용은 [Defender for IoT API 센서 및 관리 콘솔 API](references-work-with-defender-for-iot-apis.md)를 참조하세요.

API를 사용하여 만든 규칙은 **제외 규칙** 창에 RO로 표시됩니다.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/edit-exclusion-rule-screen.png" alt-text="제외 규칙 편집 보기의 스크린샷.":::

## <a name="next-steps"></a>다음 단계

[센서의 경고를 사용합니다](how-to-work-with-alerts-on-your-sensor.md).
[Defender for IoT 엔진 경고](alert-engine-messages.md)를 검토합니다.
