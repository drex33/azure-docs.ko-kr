---
title: Microsoft Sentinel에 보안 경고 커넥트
description: Microsoft Defender for Cloud의 보안 경고를 연결하고 Microsoft Sentinel로 스트리밍하는 방법을 알아봅니다.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: b30d3080b5c8e024f9266e6fbacc8a3c98e7457c
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730618"
---
# <a name="connect-security-alerts-from-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud에서 보안 경고 커넥트

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

## <a name="background"></a>배경

[Defender for Cloud의](../security-center/azure-defender.md)CWPP(통합 클라우드 워크로드 보호 플랫폼)인 [Microsoft Defender for Cloud는](../security-center/security-center-introduction.md)하이브리드 클라우드 워크로드에서 위협을 감지하고 신속하게 대응할 수 있는 보안 관리 도구입니다.

이 커넥터를 사용하면 Defender for Cloud에서 Microsoft Sentinel로 보안 경고를 스트리밍할 수 있으므로 광범위한 조직 위협 컨텍스트에서 보안 경고 및 생성한 인시던트 및 인시던트 보기, 분석 및 대응을 할 수 있습니다.

Defender for Cloud 자체는 구독별로 사용하도록 설정되기 때문에 Defender for Cloud 커넥터도 각 구독에 대해 별도로 사용하거나 사용하지 않도록 설정됩니다.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

### <a name="alert-synchronization"></a>경고 동기화

- 클라우드용 Microsoft Defender를 Microsoft Sentinel에 연결하면 Microsoft Sentinel에 검색되는 보안 경고의 상태가 두 서비스 간에 동기화됩니다. 따라서 예를 들어 Defender for Cloud에서 경고가 닫힌 경우 해당 경고는 Microsoft Sentinel에서도 닫힘으로 표시됩니다.

- Defender for Cloud에서 경고 상태를 변경해도 Microsoft Sentinel 경고가 포함된 Microsoft Sentinel **인시던트** 상태는 영향을 받지 않으며 경고 자체의 상태만 영향을 받지 *않습니다.*

### <a name="bi-directional-alert-synchronization"></a>양방향 경고 동기화

- 양방향 **동기화를** 사용하도록 설정하면 원래 보안 경고의 상태가 해당 경고가 포함된 Microsoft Sentinel 인시던트 상태와 자동으로 동기화됩니다. 따라서 예를 들어 보안 경고가 포함된 Microsoft Sentinel 인시던트 가 닫히면 해당 원래 경고가 Microsoft Defender for Cloud에서 자동으로 닫힙니다.

## <a name="prerequisites"></a>필수 구성 요소

- Microsoft Sentinel 작업 영역에 대한 읽기 및 쓰기 권한이 있어야 합니다.

- 스트리밍할 로그의 구독에 보안 읽기 권한자 역할이 있어야 합니다.

- 커넥터를 사용하도록 설정하려는 각 구독에 대해 Microsoft Defender for Cloud 내에서 하나 이상의 플랜을 사용하도록 설정해야 합니다. 구독에서 Microsoft Defender 계획을 사용하도록 설정하려면 해당 구독에 대한 **보안 관리자** 역할이 있어야 합니다.

- 양방향 동기화를 사용으로 설정하려면 관련 구독에 대한 **기여자** 또는 **보안 관리자** 역할이 있어야 합니다.

## <a name="connect-to-microsoft-defender-for-cloud"></a>클라우드용 Microsoft Defender에 커넥트

1. Microsoft Sentinel의 탐색 메뉴에서 **데이터 커넥터를** 선택합니다.

1. 데이터 커넥터 갤러리에서 **Microsoft Defender for Cloud를** 선택하고 세부 정보 창에서 커넥터 페이지 **열기를** 선택합니다.

1. **구성** 아래에 테넌트 구독 목록과 Microsoft Defender for Cloud에 대한 연결 상태가 표시됩니다. Microsoft Sentinel로 스트리밍하려는 경고가 있는 각 구독 옆에 있는 **상태** 토글을 선택합니다. 여러 구독을 한 번에 연결하려면 관련 구독 옆에 있는 확인란을 표시한 다음 목록 위의 표시줄에서 **연결** 단추를 선택하여 수행할 수 있습니다.

    > [!NOTE]
    > - 확인란과 **연결** 토글은 필요한 권한이 있는 구독에서만 활성입니다.
    > - **연결** 단추는 하나 이상의 구독 확인란이 표시된 경우에만 활성입니다.

1. 구독에서 양방향 동기화를 사용하도록 설정하려면 목록에서 구독을 찾고 **양방향 동기화** 열의 드롭다운 목록에서 **사용을** 선택합니다. 한 번에 여러 구독에서 양방향 동기화를 사용으로 설정하려면 해당 확인란을 표시하고 목록 위의 표시줄에서 **양방향 동기화 사용** 단추를 선택합니다.

    > [!NOTE]
    > - 확인란과 드롭다운 목록은 [필수 권한](#prerequisites)이 있는 구독에서만 활성입니다.
    > - **양방향 동기화 사용** 단추는 하나 이상의 구독 확인란이 표시된 경우에만 활성입니다.

1. 목록의 **Microsoft Defender 계획** 열에서 구독에서 Microsoft Defender 계획이 사용하도록 설정되어 있는지 확인할 수 있습니다(커넥터를 사용하도록 설정하기 위한 필수 조건). 이 열에 있는 각 구독의 값은 비어 있거나(사용으로 설정된 Defender 플랜이 없음), “모두 사용으로 설정됨”이거나 “일부 사용으로 설정됨”입니다. "일부 사용"이라는 사용자는 선택할 수 있는 **모든** 사용 링크가 있습니다. 그러면 해당 구독에 대한 Microsoft Defender for Cloud 구성 대시보드로 연결되며, 여기서 Defender 계획을 선택하여 사용하도록 설정할 수 있습니다. 목록 위의 막대에 있는 **모든 구독에 대해 Microsoft Defender** 사용 링크 단추를 클릭하면 Microsoft Defender for Cloud 시작 페이지로 연결되며, 여기서 Microsoft Defender for Cloud를 사용하도록 설정할 구독을 선택할 수 있습니다.

    :::image type="content" source="./media/connect-defender-for-cloud/azure-defender-config.png" alt-text="클라우드용 Microsoft Defender 커넥터 구성의 스크린샷":::

1. Microsoft Defender for Cloud의 경고가 Microsoft Sentinel에서 인시던트 자동 생성을 할지 여부를 선택할 수 있습니다. **인시던트 만들기** 에서 **사용** 을 선택하여 [경고에서 인시던트를 자동으로 생성](create-incidents-from-alerts.md)하는 기본 분석 규칙을 설정합니다. 그런 다음, **활성 규칙** 탭의 **분석** 에서 이 규칙을 편집할 수 있습니다.

    > [!TIP]
    > Microsoft Defender for Cloud의 경고에 대한 [사용자 지정 분석 규칙을](detect-threats-custom.md) 구성할 때 정보 경고에 대한 인시던트 열기를 방지하기 위해 경고 심각도를 고려합니다. 
    >
    > Microsoft Defender for Cloud의 정보 경고는 자체 보안 위험을 나타내지 않으며 기존의 개방형 인시던트의 컨텍스트에서만 관련이 있습니다. 자세한 내용은 [Microsoft Defender for Cloud의 보안 경고 및 인시던트 를 참조하세요.](../security-center/security-center-alerts-overview.md)
    > 
    

## <a name="find-and-analyze-your-data"></a>데이터 찾기 및 분석

> [!NOTE]
> ‘양방향’ 경고 동기화는 몇 분이 소요될 수 있습니다. 경고 상태의 변경 내용이 즉시 표시되지 않을 수 있습니다.

- 보안 경고는 Log Analytics 작업 영역의 *SecurityAlert* 테이블에 저장됩니다.

- Log Analytics에서 보안 경고를 쿼리하려면 다음을 시작점으로 쿼리 창에 복사합니다.

    ```kusto
    SecurityAlert 
    | where ProductName == "Azure Security Center"
    ```

- 유용한 추가 샘플 쿼리, 분석 규칙 템플릿 및 권장 통합 문서는 커넥터 페이지의 **다음 단계** 탭을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft Defender for Cloud를 Microsoft Sentinel에 연결하고 이들 간에 경고를 동기화하는 방법을 배웠습니다. Microsoft Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터 및 잠재적 위협에 대한 가시성을 확보](get-visibility.md)하는 방법을 알아봅니다.
- [Microsoft Sentinel을 사용하여 위협 검색을](detect-threats-built-in.md)시작합니다.
- [위협을 탐지](detect-threats-custom.md)하는 고유한 규칙을 작성합니다.
