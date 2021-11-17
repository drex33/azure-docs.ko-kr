---
title: Microsoft 센티널에 보안 경고 커넥트
description: 클라우드에 대 한 Microsoft Defender의 보안 경고를 연결 하 고 Microsoft 센티널로 스트리밍하는 방법에 대해 알아봅니다.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 33d43aa25a8e51c0330c2eaba213ef8d5644e62f
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522121"
---
# <a name="connect-security-alerts-from-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud의 보안 경고 커넥트

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

## <a name="background"></a>배경

클라우드 [용](../security-center/security-center-introduction.md)Microsoft defender (클라우드 워크 로드 보호 플랫폼) 인 [Microsoft defender for](../security-center/azure-defender.md)cloud는 하이브리드 클라우드 워크 로드에서 위협에 대응 하 고 신속 하 게 대응할 수 있는 보안 관리 도구입니다.

이 커넥터를 사용 하 여 Defender for Cloud의 보안 경고를 Microsoft 센티널로 스트리밍할 수 있으므로 보안 경고 및 생성 되는 인시던트를 확인 하 고, 분석 하 고, 더 광범위 한 조직 위협 컨텍스트에서 볼 수 있습니다.

클라우드 자체의 Defender는 구독 당 사용 하도록 설정 되므로 클라우드 커넥터용 Defender도 각 구독에 대해 개별적으로 사용 하거나 사용 하지 않도록 설정 됩니다.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

### <a name="alert-synchronization"></a>경고 동기화

- Microsoft 센티널에 microsoft Defender for Cloud를 연결 하면 Microsoft 센티널로 수집 되는 보안 경고의 상태가 두 서비스 간에 동기화 됩니다. 예를 들어 Defender for Cloud에서 경고가 종결 되 면 해당 경고도 Microsoft 센티널 에서도 닫힘으로 표시 됩니다.

- Defender for Cloud에서 경고의 상태를 변경 해도 Microsoft 센티널 경고를 포함 하는 Microsoft 센티널 **인시던트의** 상태에는 영향을 주지 않습니다. 경고 자체의 상태에는 영향을 주지 *않습니다* .

### <a name="bi-directional-alert-synchronization"></a>양방향 경고 동기화

- **양방향 동기화** 를 사용 하도록 설정 하면 원래 보안 경고의 상태가 해당 경고를 포함 하는 Microsoft 센티널 인시던트의 상태와 자동으로 동기화 됩니다. 예를 들어 보안 경고를 포함 하는 Microsoft 센티널 인시던트가 종결 되 면 클라우드의 Microsoft Defender에서 해당 하는 원래 경고가 자동으로 닫힙니다.

## <a name="prerequisites"></a>필수 구성 요소

- Microsoft 센티널 작업 영역에 대 한 읽기 및 쓰기 권한이 있어야 합니다.

- 스트리밍할 로그의 구독에 보안 읽기 권한자 역할이 있어야 합니다.

- 커넥터를 사용 하도록 설정 하려는 각 구독에 대해 Microsoft Defender for Cloud 내에서 계획을 하나 이상 사용 하도록 설정 해야 합니다. 구독에서 Microsoft Defender 계획을 사용 하도록 설정 하려면 해당 구독에 대 한 **보안 관리자** 역할이 있어야 합니다.

- 양방향 동기화를 사용으로 설정하려면 관련 구독에 대한 **기여자** 또는 **보안 관리자** 역할이 있어야 합니다.

## <a name="connect-to-microsoft-defender-for-cloud"></a>클라우드 용 Microsoft Defender에 커넥트

1. Microsoft 센티널의 탐색 메뉴에서 **데이터 커넥터** 를 선택 합니다.

1. 데이터 커넥터 갤러리에서 **클라우드 용 Microsoft Defender** 를 선택 하 고 세부 정보 창에서 **커넥터 페이지 열기** 를 선택 합니다.

1. **구성** 아래에 테 넌 트의 구독 목록과 클라우드의 Microsoft Defender에 대 한 연결 상태가 표시 됩니다. Microsoft 센티널로 스트리밍할 경고를 포함 하는 각 구독 옆의 **상태** 설정/해제를 선택 합니다. 여러 구독을 한 번에 연결하려면 관련 구독 옆에 있는 확인란을 표시한 다음 목록 위의 표시줄에서 **연결** 단추를 선택하여 수행할 수 있습니다.

    > [!NOTE]
    > - 확인란과 **연결** 토글은 필요한 권한이 있는 구독에서만 활성입니다.
    > - **연결** 단추는 하나 이상의 구독 확인란이 표시된 경우에만 활성입니다.

1. 구독에서 양방향 동기화를 사용 하도록 설정 하려면 목록에서 구독을 찾고 **양방향 동기화** 열의 드롭다운 목록에서 **사용** 을 선택 합니다. 한 번에 여러 구독에서 양방향 동기화를 사용으로 설정하려면 해당 확인란을 표시하고 목록 위의 표시줄에서 **양방향 동기화 사용** 단추를 선택합니다.

    > [!NOTE]
    > - 확인란과 드롭다운 목록은 [필수 권한](#prerequisites)이 있는 구독에서만 활성입니다.
    > - **양방향 동기화 사용** 단추는 하나 이상의 구독 확인란이 표시된 경우에만 활성입니다.

1. 목록의 **Microsoft defender 요금제** 열에서 구독에 대해 microsoft defender 요금제를 사용 하도록 설정 했는지 여부를 확인할 수 있습니다 (커넥터를 사용 하도록 설정 하기 위한 필수 구성 요소). 이 열에 있는 각 구독의 값은 비어 있거나(사용으로 설정된 Defender 플랜이 없음), “모두 사용으로 설정됨”이거나 “일부 사용으로 설정됨”입니다. "일부 사용" 이라고 표시 되는 경우 선택할 수 있는 **모두 사용** 링크를 사용 하 여이 구독에 대 한 Microsoft Defender for Cloud 구성 대시보드로 이동 하 여 사용 하도록 설정할 수 있습니다. 목록의 위쪽 표시줄에 있는 **모든 구독에 대해 Microsoft Defender 사용** 링크 단추를 클릭 하면 클라우드 용 Microsoft defender 시작 페이지로 이동 하 여 클라우드 용 microsoft defender를 모두 사용 하도록 설정할 구독을 선택할 수 있습니다.

    :::image type="content" source="./media/connect-azure-security-center/azure-defender-config.png" alt-text="클라우드 커넥터 구성 용 Microsoft Defender 스크린샷":::

1. Microsoft에서 클라우드에 대 한 경고를 microsoft 센티널에서 자동으로 생성할지 여부를 선택할 수 있습니다. **인시던트 만들기** 에서 **사용** 을 선택하여 [경고에서 인시던트를 자동으로 생성](create-incidents-from-alerts.md)하는 기본 분석 규칙을 설정합니다. 그런 다음, **활성 규칙** 탭의 **분석** 에서 이 규칙을 편집할 수 있습니다.

    > [!TIP]
    > Microsoft Defender for Cloud에서 경고에 대 한 [사용자 지정 분석 규칙](detect-threats-custom.md) 을 구성 하는 경우 경고 심각도를 고려 하 여 정보 경고에 대 한 인시던트를 열지 않도록 합니다. 
    >
    > Microsoft Defender for Cloud의 정보 경고는 자체에 대 한 보안 위험을 나타내지 않으며 기존 미해결 인시던트의 컨텍스트에서만 관련이 있습니다. 자세한 내용은 [Microsoft Defender For Cloud의 보안 경고 및 인시던트](../security-center/security-center-alerts-overview.md)를 참조 하세요.
    > 
    

## <a name="find-and-analyze-your-data"></a>데이터 찾기 및 분석

> [!NOTE]
> ‘양방향’ 경고 동기화는 몇 분이 소요될 수 있습니다. 경고 상태의 변경 내용이 즉시 표시되지 않을 수 있습니다.

- 보안 경고는 Log Analytics 작업 영역의 *securityalert* 테이블에 저장 됩니다.

- Log Analytics에서 보안 경고를 쿼리하려면 다음을 쿼리 창에 시작 지점으로 복사 합니다.

    ```kusto
    SecurityAlert 
    | where ProductName == "Azure Security Center"
    ```

- 유용한 추가 샘플 쿼리, 분석 규칙 템플릿 및 권장 통합 문서는 커넥터 페이지의 **다음 단계** 탭을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 microsoft에 클라우드 용 Microsoft Defender를 연결 하 고이에 대해 경고를 동기화 하는 방법을 배웠습니다. Microsoft 센티널에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

- [데이터 및 잠재적 위협에 대한 가시성을 확보](get-visibility.md)하는 방법을 알아봅니다.
- [Microsoft 센티널을 사용 하 여 위협 검색을](detect-threats-built-in.md)시작 하세요.
- [위협을 탐지](detect-threats-custom.md)하는 고유한 규칙을 작성합니다.
