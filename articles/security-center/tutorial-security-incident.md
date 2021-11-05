---
title: 경고 응답 자습서 - Microsoft Defender for Cloud
description: 이 자습서에서는 보안 경고를 심사하고 경고의 근본 원인 및 범위를 확인하는 방법을 알아봅니다.
services: security-center
author: memildin
manager: rkarlin
ms.assetid: 181e3695-cbb8-4b4e-96e9-c4396754862f
ms.service: security-center
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 7053bcc1e28eeb262dda8833ddf98806aae770b5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131472108"
---
# <a name="tutorial-triage-investigate-and-respond-to-security-alerts"></a>자습서: 보안 경고 심사, 조사 및 대응

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for Cloud는 고급 분석 및 위협 인텔리전스를 사용하여 하이브리드 클라우드 워크로드를 지속적으로 분석하여 클라우드 리소스에서 잠재적으로 악의적인 활동에 대해 경고합니다. 다른 보안 제품 및 서비스의 경고를 Defender for Cloud에 통합할 수도 있습니다. 경고가 발생하면 잠재적인 보안 문제를 조사하고 수정하기 위한 신속한 조치가 필요합니다. 

이 자습서에서는 다음 작업 방법을 배웁니다.

> [!div class="checklist"]
> * 보안 경고 심사
> * 보안 경고를 조사하여 근본 원인 확인
> * 보안 경고에 대응하고 근본 원인 완화

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항
이 자습서에서 설명하는 기능을 단계별로 실행하려면 Defender for Cloud의 향상된 보안 기능을 사용하도록 설정해야 합니다. 무료로 사용해 볼 수 있습니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조하세요. [Defender for Cloud 시작](get-started.md) 빠른 시작에서는 업그레이드 방법을 안내합니다.


## <a name="triage-security-alerts"></a>보안 경고 심사
Defender for Cloud는 모든 보안 경고에 대해 통일된 보기를 제공합니다. 검색된 활동의 심각도를 기준으로 보안 경고의 순위가 매겨집니다. 

**보안 경고** 페이지에서 경고를 심사합니다.

:::image type="content" source="media/managing-and-responding-alerts/alerts-page.png" alt-text="Microsoft Defender for Cloud의 보안 경고 목록":::

이 페이지에서 현재 환경의 활성 보안 경고를 검토하여 어떤 경고를 먼저 조사할 것인지 결정할 수 있습니다.

보안 경고를 심사할 때 심각도가 높은 경고를 먼저 처리하도록 경고 심각도에 따라 경고의 우선 순위를 지정합니다. 경고 심각도에 대한 자세한 내용은 [경고 분류 방법](alerts-overview.md#how-are-alerts-classified)을 참조하세요.

> [!TIP]
> Microsoft Defender for Cloud를 Microsoft Sentinel을 비롯한 가장 널리 사용되는 SIEM 솔루션에 연결하고 선택한 도구의 경고를 사용할 수 있습니다. [SIEM, SOAR 또는 IT 서비스 관리 솔루션에 대한 경고 스트리밍](export-to-siem.md)에서 자세히 알아봅니다.


## <a name="investigate-a-security-alert"></a>보안 경고 조사

우선적으로 조사할 경고를 결정한 후:

1. 원하는 경고를 선택합니다.
1. 경고 개요 페이지에서 먼저 조사할 리소스를 선택합니다.
1. 왼쪽 창에서 조사를 시작합니다. 이 창에는 보안 경고에 대한 간략한 정보가 표시됩니다.

    :::image type="content" source="./media/tutorial-security-incident/alert-details-left-pane.png" alt-text="간략한 정보가 강조 표시된 경고 정보 페이지의 왼쪽 창.":::

    이 창에는 다음 정보가 표시됩니다.
    - 경고 심각도, 상태 및 활동 시간
    - 검색된 정확한 활동을 설명하는 설명
    - 영향을 받는 리소스
    - MITRE ATT&CK 매트릭스의 활동 킬 체인 의도

1. 의심스러운 활동을 조사하는 데 도움이 될 만한 자세한 정보를 보려면 **경고 세부 정보** 탭을 검사합니다.

1. 이 페이지의 정보를 검토하면 대응을 계속 진행하기에 충분할 것입니다. 정보가 더 필요한 경우:

    - 리소스 소유자에게 문의하여 검색된 활동이 가양성인지 확인합니다.
    - 공격 받은 리소스에서 생성한 원시 로그를 조사합니다.

## <a name="respond-to-a-security-alert"></a>보안 경고에 대응
보안 경고를 조사하고 해당 범위를 파악한 후에는 Microsoft Defender for Cloud 내에서 경고에 응답할 수 있습니다.

1.  **조치 수행** 탭을 열고 권장 대응을 확인합니다.

    :::image type="content" source="./media/tutorial-security-incident/alert-details-take-action.png" alt-text="보안 경고 조치 수행 탭." lightbox="./media/tutorial-security-incident/alert-details-take-action.png":::

1.  문제를 완화하는 데 필요한 수동 조사 단계에 대한 **위협 완화** 섹션을 검토합니다.
1.  리소스를 강화하고 추후 이러한 종류의 공격을 방지하려면 **추후 공격 방지** 섹션에서 보안 권장 사항에 따라 수정하세요.
1.  자동화된 대응 단계를 사용하여 논리 앱을 트리거하려면 **자동화된 응답** 트리거를 사용합니다.
1.  검색된 활동이 악의적 활동이 *아닌* 경우 **유사한 경고 표시 안 함** 섹션을 사용하여 앞으로 이러한 종류의 경고를 표시하지 않을 수 있습니다.

1.  경고 조사를 완료하고 적절한 방식으로 대응한 후에는 상태를 **해제됨** 으로 변경합니다.

    :::image type="content" source="./media/tutorial-security-incident/set-status-dismissed.png" alt-text="경고 상태 설정":::

    그러면 주 경고 목록에서 경고가 제거됩니다. 경고 목록 페이지의 필터를 사용하여 **해제됨** 상태의 모든 경고를 볼 수 있습니다.

1.  경고에 대한 피드백을 Microsoft에 제공하는 것이 좋습니다.
    1. 경고를 **유용함** 또는 **유용하지 않음** 으로 표시합니다.
    1. 이유를 선택하고 설명을 추가합니다.

        :::image type="content" source="./media/tutorial-security-incident/alert-feedback.png" alt-text="Microsoft에 경고의 유용성에 대한 피드백을 제공합니다.":::

    > [!TIP]
    > 보내주신 피드백은 알고리즘을 개선하고 보다 나은 보안 경고를 제공하는 데 사용됩니다.

## <a name="end-the-tutorial"></a>자습서 종료

이 컬렉션의 다른 빠른 시작과 자습서는 이 빠른 시작을 기반으로 하여 작성됩니다. 후속 빠른 시작 및 자습서를 계속 사용하려면 자동 프로비저닝 및 Defender for Cloud의 향상된 보안 기능을 설정된 상태로 유지합니다. 

계속 사용할 생각이 없거나 이러한 기능을 해제하려면 다음을 수행합니다.

1. Defender for Cloud 메뉴에서 **환경 설정** 을 엽니다.
1. 관련 구독을 선택합니다.
1. **Defender 플랜** 을 선택하고 **향상된 보안 해제** 를 선택합니다.

    :::image type="content" source="./media/enable-enhanced-security/disable-plans.png" alt-text="Defender for Cloud의 향상된 보안 기능을 사용하거나 사용하지 않도록 설정합니다.":::

1. **저장** 을 선택합니다.

    > [!NOTE]
    > 한 번에 단일 플랜 또는 모든 플랜을 사용하지 않도록 설정하는지 여부에 관계없이 향상된 보안 기능을 사용하지 않도록 설정한 후 데이터 수집이 짧은 기간 동안 계속될 수 있습니다. 

1. Defender for Cloud 메뉴에서 **환경 설정** 을 엽니다.
1. 관련 구독을 선택합니다.
1. **자동 프로비저닝** 을 선택합니다.
1. 관련 확장을 사용하지 않도록 설정합니다.

    >[!NOTE]
    > 자동 프로비저닝을 해제해도 이미 에이전트가 있는 Azure VM의 Log Analytics 에이전트는 제거되지 않습니다. 자동 프로비저닝을 사용하지 않도록 설정하면 리소스에 대한 보안 모니터링이 제한됩니다.

## <a name="next-steps"></a>다음 단계
이 자습서에서는 보안 경고에 대응할 때 사용되는 Defender for Cloud 기능에 대해 알아보았습니다. 관련 자료는 다음을 참조하세요.

- [Key Vault용 Microsoft Defender 경고에 대응](defender-for-key-vault-usage.md)
- [보안 경고 - 참조 가이드](alerts-reference.md)
- [Defender for Cloud 소개](defender-for-cloud-introduction.md)