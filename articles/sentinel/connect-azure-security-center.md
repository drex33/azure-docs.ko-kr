---
title: Azure Sentinel에 Azure Defender 경고 연결
description: Azure Security Center에서 Azure Defender 경고를 연결하고 Azure Sentinel로 스트리밍하는 방법을 알아봅니다.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 07/08/2021
ms.author: yelevin
ms.openlocfilehash: 9612b5aaaec10358609abba3878f6f09405ad076
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123260123"
---
# <a name="connect-azure-defender-alerts-from-azure-security-center"></a>Azure Security Center에서 Azure Defender 경고 연결

## <a name="background"></a>배경

[Azure Security Center](../security-center/security-center-introduction.md)의 CWPP(통합 클라우드 워크로드 보호 플랫폼)인 [Azure Defender](../security-center/azure-defender.md)는 하이브리드 클라우드 워크로드 전반에서 위협을 탐지하고 신속하게 대응할 수 있는 보안 관리 도구입니다. 

이 커넥터를 사용하면 Azure Security Center에서 Azure Sentinel로 Azure Defender 보안 경고를 스트리밍할 수 있으므로, 더 광범위한 조직 위협 컨텍스트에서 Defender 경고와 이로 인해 생성되는 인시던트를 보고 분석하고 이에 대응할 수 있습니다.

Azure Defender 자체가 구독별로 사용되므로 Azure Defender 커넥터도 구독별로 각각 사용하거나 사용하지 않게 설정됩니다.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

### <a name="alert-synchronization"></a>경고 동기화

- Azure Defender를 Azure Sentinel에 연결하면 Azure Sentinel로 수집된 Azure Defender 경고의 상태가 두 서비스 간에 동기화됩니다. 예를 들어 Azure Defender에서 경고가 닫히면 해당 경고는 Azure Sentinel에서도 닫힘으로 표시됩니다.

- Azure Defender에서 경고 상태를 변경하면 Azure Sentinel 경고를 포함하는 Azure Sentinel **인시던트** 의 상태에 영향을 미치지 ‘않고’ 경고 자체에만 영향을 미칩니다.

### <a name="bi-directional-alert-synchronization"></a>양방향 경고 동기화

> [!IMPORTANT]
>
> - **양방향 경고 동기화** 기능은 현재 **미리 보기** 상태입니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

- **양방향 동기화** 를 사용으로 설정하면 원래 Azure Defender 경고의 상태가 해당 경고를 포함하는 Azure Sentinel 인시던트의 상태와 자동으로 동기화됩니다. 따라서 예를 들어 Azure Defender 경고가 포함된 Azure Sentinel 인시던트가 닫히면 해당 원본 경고가 Azure Defender에서 자동으로 닫힙니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure Sentinel 작업 영역에 대한 읽기 및 쓰기 권한이 있어야 합니다.

- 스트리밍할 로그의 구독에 보안 읽기 권한자 역할이 있어야 합니다.

- 커넥터를 사용으로 설정할 구독마다 Azure Security Center 내에서 하나 이상의 **Azure Defender** 플랜을 사용으로 설정해야 합니다. 구독에서 Azure Defender 플랜을 사용으로 설정하려면 해당 구독에 대한 **보안 관리자** 역할이 있어야 합니다.

-  양방향 동기화를 사용으로 설정하려면 관련 구독에 대한 **기여자** 또는 **보안 관리자** 역할이 있어야 합니다.

## <a name="connect-to-azure-defender"></a>Azure Defender에 연결

1. Azure Sentinel의 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다.

1. 데이터 커넥터 갤러리에서 **Azure Defender** 를 선택하고 세부 정보 창에서 **커넥터 페이지 열기** 를 선택합니다.

1. **구성** 아래에 테넌트의 구독 목록과 Azure Defender에 대한 연결 상태가 표시됩니다. 경고를 Azure Sentinel로 스트리밍하려는 각 구독 옆에 있는 **상태** 토글을 선택합니다. 여러 구독을 한 번에 연결하려면 관련 구독 옆에 있는 확인란을 표시한 다음 목록 위의 표시줄에서 **연결** 단추를 선택하여 수행할 수 있습니다.

    > [!NOTE]
    > - 확인란과 **연결** 토글은 필요한 권한이 있는 구독에서만 활성입니다.
    > - **연결** 단추는 하나 이상의 구독 확인란이 표시된 경우에만 활성입니다.

1. 구독에서 양방향 동기화를 사용으로 설정하려면 목록에서 구독을 찾고 **양방향 동기화(미리 보기)** 열의 드롭다운 목록에서 **사용** 을 선택합니다. 한 번에 여러 구독에서 양방향 동기화를 사용으로 설정하려면 해당 확인란을 표시하고 목록 위의 표시줄에서 **양방향 동기화 사용** 단추를 선택합니다.

    > [!NOTE]
    > - 확인란과 드롭다운 목록은 [필수 권한](#prerequisites)이 있는 구독에서만 활성입니다.
    > - **양방향 동기화 사용** 단추는 하나 이상의 구독 확인란이 표시된 경우에만 활성입니다.

1. 목록의 **Azure Defender 플랜** 열에서 구독에 Azure Defender 계획이 사용으로 설정되어 있는지 확인할 수 있습니다(커넥터를 사용으로 설정하기 위한 전제 조건). 이 열에 있는 각 구독의 값은 비어 있거나(사용으로 설정된 Defender 플랜이 없음), “모두 사용으로 설정됨”이거나 “일부 사용으로 설정됨”입니다. “일부 사용으로 설정됨”이라고 표시된 항목에는 선택할 수 있는 **모두 사용으로 설정** 링크도 있습니다. 이 링크를 사용하면 해당 구독의 Azure Defender 구성 대시보드로 이동하여 사용으로 설정할 Defender 플랜을 선택할 수 있습니다. 목록 위의 표시줄에 있는 **모든 구독에 대해 Azure Defender 사용** 링크 단추를 누르면 Azure Defender를 모두 사용으로 설정할 구독을 선택할 수 있는 Azure Defender 시작하기 페이지로 이동합니다.

    :::image type="content" source="./media/connect-azure-security-center/azure-defender-config.png" alt-text="Azure Defender 커넥터 구성의 스크린샷":::

1. Azure Defender의 경고가 Azure Sentinel에서 인시던트를 자동으로 생성할지 여부를 선택할 수 있습니다. **인시던트 만들기** 에서 **사용** 을 선택하여 [경고에서 인시던트를 자동으로 생성](create-incidents-from-alerts.md)하는 기본 분석 규칙을 설정합니다. 그런 다음, **활성 규칙** 탭의 **분석** 에서 이 규칙을 편집할 수 있습니다.

    > [!TIP]
    > Azure Defender의 경고에 대한 [사용자 지정 분석 규칙](detect-threats-custom.md)을 구성할 때 정보 경고에 대한 인시던트가 열리지 않도록 경고 심각도를 고려합니다. 
    >
    > Azure Security Center의 정보 경고는 그 자체로 보안 위험을 나타내지 않으며 기존의 미해결 인시던트의 컨텍스트에서만 관련이 있습니다. 자세한 내용은 [Azure Security Center의 보안 경고 및 인시던트](../security-center/security-center-alerts-overview.md)를 참조하세요.
    > 
    

## <a name="find-and-analyze-your-data"></a>데이터 찾기 및 분석

> [!NOTE]
> ‘양방향’ 경고 동기화는 몇 분이 소요될 수 있습니다. 경고 상태의 변경 내용이 즉시 표시되지 않을 수 있습니다.

- Azure Defender 경고는 Log Analytics 작업 영역의 *SecurityAlert* 표에 저장됩니다.

- Log Analytics에서 Azure Defender 경고를 쿼리하려면 다음을 시작점으로 쿼리 창에 복사합니다.

    ```kusto
    SecurityAlert 
    | where ProductName == "Azure Security Center"
    ```

- 유용한 추가 샘플 쿼리, 분석 규칙 템플릿 및 권장 통합 문서는 커넥터 페이지의 **다음 단계** 탭을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Defender를 Azure Sentinel에 연결하고 둘 사이에 경고를 동기화하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터 및 잠재적 위협에 대한 가시성을 확보](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
- [위협을 탐지](detect-threats-custom.md)하는 고유한 규칙을 작성합니다.