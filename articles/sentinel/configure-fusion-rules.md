---
title: Microsoft Sentinel에서 다단계 공격 탐지(Fusion) 규칙 구성
description: Microsoft Sentinel의 Fusion 기술을 기반으로 공격 검색 규칙을 만들고 구성합니다.
author: yelevin
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 80b49350d8d74f86dae5a6a837bbc9a777531ad3
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132758033"
---
# <a name="configure-multistage-attack-detection-fusion-rules-in-microsoft-sentinel"></a>Microsoft Sentinel에서 다단계 공격 탐지(Fusion) 규칙 구성

> [!IMPORTANT]
> Fusion 분석 규칙의 새 버전은 현재 **미리 보기** 에 있습니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Microsoft Sentinel은 확장 가능한 기계 학습 알고리즘을 기반으로 하는 상관 관계 엔진인 Fusion을 사용하여 킬 체인의 다양한 단계에서 관찰되는 비정상적인 동작과 의심스러운 활동의 조합을 식별하여 다단계 공격을 자동으로 검색합니다. 이러한 검색을 기반으로 Microsoft Sentinel은 catch하기 어려운 인시던트도 생성합니다. 이러한 인시던트는 둘 이상의 경고 또는 활동으로 구성됩니다. 설계 의도에 따라 이러한 인시던트는 볼륨이 작고, 충실도가 높고, 심각도가 높습니다.

환경에 맞게 사용자 지정되는 이 감지 기술은 [가양성](false-positives.md) 비율을 줄일 뿐 아니라 정보가 제한되거나 누락된 공격도 감지할 수 있습니다.

## <a name="configure-fusion-rules"></a>Fusion 규칙 구성

이 검색은 Microsoft Sentinel에서 기본적으로 사용하도록 설정됩니다. 상태를 확인하거나 변경하려면 다음 지침을 사용합니다.

1. [Azure Portal](https://portal.azure.com) 로그인하고 **Microsoft Sentinel** 을 입력합니다.

1. Microsoft Sentinel 탐색 메뉴에서 **분석을** 선택합니다.

1. **활성 규칙** 탭을 선택한 다음, **Fusion** 규칙 유형에 대한 목록을 필터링하여 **이름** 열에서 **고급 다단계 공격 감지** 를 찾습니다. **상태** 열을 확인하여 감지 사용 여부를 확인합니다.

    :::image type="content" source="./media/fusion/selecting-fusion-rule-type.png" alt-text="Fusion 분석 규칙의 스크린샷." lightbox="./media/fusion/selecting-fusion-rule-type.png":::

1. 상태를 변경하려면 이 항목을 선택하고 **고급 다단계 공격 감지** 블레이드에서 **편집** 을 선택합니다.

1. **분석 규칙 마법사의** **일반** 탭에서 상태(사용/사용 안 함)를 확인하거나 원하는 경우 변경합니다.

    상태를 변경했지만 변경할 내용이 더 이상 없는 경우 **검토 및 업데이트 탭을** 선택하고 **저장을** 선택합니다.

    Fusion 검색 규칙을 추가로 구성하려면 **다음: Fusion 구성을** 선택합니다.

    :::image type="content" source="media/configure-fusion-rules/configure-fusion-rule.png" alt-text="Fusion 규칙 구성의 스크린샷." lightbox="media/configure-fusion-rules/configure-fusion-rule.png":::

1. **Fusion 검색을 위한 원본 신호 구성:** 최상의 결과를 위해 나열된 모든 원본 신호를 모든 심각도 수준과 함께 포함하는 것이 좋습니다. 기본적으로 모두 포함되어 있지만 다음과 같은 방법으로 변경할 수 있습니다.

    > [!NOTE]
    > 특정 원본 신호 또는 경고 심각도 수준을 제외하는 경우 해당 원본의 신호 또는 해당 심각도 수준과 일치하는 경고에 의존하는 Fusion 검색은 트리거되지 않습니다. 
    
    - 변칙, 다양한 공급자의 경고 및 원시 로그를 포함하여 **Fusion 검색에서 신호를 제외합니다.**
     
        *사용 사례:* 노이즈 경고를 생성하는 것으로 알려진 특정 신호 원본을 테스트하는 경우 Fusion 검색을 위해 해당 특정 신호 원본에서 신호를 일시적으로 끌 수 있습니다.

    - **각 공급자에 대한 경고 심각도 구성:** Fusion ML 모델은 의도적으로 낮은 충실도 신호를 여러 데이터 원본의 킬 체인에서 비정상 신호를 기반으로 하는 단일 높은 심각도 인시던트의 상관 관계를 지정합니다. Fusion에 포함된 경고는 일반적으로 심각도가 낮지만(중간, 낮음, 정보 제공) 관련 높은 심각도 경고가 포함되는 경우도 있습니다.
     
        *사용 사례:* 높은 심각도 경고를 심사하고 조사하는 별도의 프로세스가 있고 이러한 경고를 Fusion에 포함하지 않으려는 경우 Fusion 검색에서 높은 심각도 경고를 제외하도록 원본 신호를 구성할 수 있습니다. 



    - **Fusion 검색 에서 특정 검색 패턴을 제외합니다.** 특정 Fusion 검색은 사용자 환경에 적용되지 않거나 가양성 생성에 취약할 수 있습니다. 특정 Fusion 검색 패턴을 제외하려면 아래 지침을 따르세요.

        1. 제외하려는 종류의 Fusion 인시던트 찾기 및 열기

        1. **설명** 섹션에서 자세히 **표시를** 선택합니다.

        1. **이 특정 검색 패턴 제외** 아래에서 제외 **링크를** 선택합니다. 그러면 분석 규칙 마법사의 **Fusion 구성** 탭으로 리디렉션됩니다.

            :::image type="content" source="media/configure-fusion-rules/exclude-fusion-incident.png" alt-text="Fusion 인시던트 스크린샷. 제외 링크를 선택합니다.":::

        Fusion **구성** 탭에서 검색 패턴(Fusion 인시던트의 경고 및 변칙 조합)이 검색 패턴이 추가된 시간과 함께 제외 목록에 추가된 것을 볼 수 있습니다.

        언제든지 해당 검색 패턴에서 휴지통 아이콘을 선택하여 제외된 검색 패턴을 제거할 수 있습니다.

        :::image type="content" source="media/configure-fusion-rules/exclusion-patterns-list.png" alt-text="제외된 검색 패턴 목록의 스크린샷.":::

        제외된 검색 패턴과 일치하는 인시던트도 여전히 트리거되지만 **활성 인시던트 큐에는 표시되지 않습니다.** 다음 값으로 자동 채워집니다.

        - **상태:**"닫힘"
        
        - **닫는 분류:**"Undetermined"

        - **주석:**"자동 닫힘, 제외된 Fusion 검색 패턴"

        - **태그:**"ExcludedFusionDetectionPattern" - 이 태그를 쿼리하여 이 검색 패턴과 일치하는 모든 인시던트만 볼 수 있습니다.

            :::image type="content" source="media/configure-fusion-rules/auto-closed-incident.png" alt-text="자동 닫힘, 제외된 Fusion 인시던트 스크린샷.":::



> [!NOTE]
> Microsoft Sentinel은 현재 30일간의 기록 데이터를 사용하여 기계 학습 시스템을 학습합니다. 이 데이터는 기계 학습 파이프라인을 통과할 때 항상 Microsoft의 키를 사용하여 암호화됩니다. 그러나 Microsoft Sentinel 작업 영역에서 CMK를 사용하도록 설정한 경우 [CMK(고객 관리형 키)를](customer-managed-keys.md) 사용하여 학습 데이터가 암호화되지 않습니다. Fusion을 옵트아웃하려면 **Microsoft Sentinel** \> **구성** 분석 \> **활성 \> 규칙으로** 이동하여 **고급 다단계 공격 검색** 규칙을 마우스 오른쪽 단추로 클릭하고 **사용 안 함을 선택합니다.**

## <a name="configure-scheduled-analytics-rules-for-fusion-detections"></a>Fusion 검색에 대한 예약된 분석 규칙 구성

> [!IMPORTANT]
>
> - 분석 규칙 경고를 사용하는 Fusion 기반 검색은 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

**Fusion은** [예약된 분석 규칙](detect-threats-custom.md)에서 생성된 경고를 사용하여 시나리오 기반 다단계 공격 및 새로운 위협을 검색할 수 있습니다. Microsoft Sentinel의 Fusion 기능을 최대한 활용할 수 있도록 다음 단계를 수행하여 이러한 규칙을 구성하고 사용하도록 설정하는 것이 좋습니다.

1. 새로운 위협에 대한 Fusion은 킬 체인(전술) 및 엔터티 매핑 정보를 포함하는 [예약된](detect-threats-built-in.md#scheduled) 분석 규칙(기본 제공 및 [보안 분석가가 만든](detect-threats-custom.md)규칙)에서 생성된 경고를 사용할 수 있습니다. Fusion에서 분석 규칙의 출력을 사용하여 새로운 위협을 검색할 수 있도록 하려면 다음을 수행합니다.

    - 이러한 예약된 규칙에 대한 **엔터티 매핑** 을 검토합니다. [엔터티 매핑 구성 섹션을](map-data-fields-to-entities.md) 사용하여 쿼리 결과의 매개 변수를 Microsoft Sentinel 인식 엔터티에 매핑합니다. Fusion은 엔터티(예: *사용자 계정* 또는 *IP 주소)에* 따라 경고의 상관 관계를 지정하므로 ML 알고리즘은 엔터티 정보 없이 경고 일치를 수행할 수 없습니다.

    - 분석 규칙 세부 정보에서 **전술** 을 검토합니다. Fusion ML 알고리즘은 [MITRE ATT&CK](https://attack.mitre.org/) 전술 정보를 사용하여 다단계 공격을 감지하고 분석 규칙에 레이블을 지정하는 전술이 결과 인시던트에 표시됩니다. 수신 경고에 전술 정보가 누락된 경우 Fusion 계산이 영향을 받을 수 있습니다.

1. Fusion은 또한 분석 **블레이드의** 규칙 템플릿 탭에서 찾을 수 있는 다음과 같은 **예약된 분석 규칙 템플릿을** 기반으로 **규칙을** 사용하여 시나리오 기반 위협을 검색할 수 있습니다. 이러한 검색을 사용하도록 설정하려면 템플릿 갤러리에서 규칙 이름을 선택하고 세부 정보 창에서 **규칙 만들기를** 클릭합니다.

    - [Cisco - 방화벽이 차단하지만 Azure AD에 성공적으로 로그온](https://github.com/Azure/Azure-Sentinel/blob/60e7aa065b196a6ed113c748a6e7ae3566f8c89c/Detections/MultipleDataSources/SigninFirewallCorrelation.yaml)
    - [Fortinet - 비콘 패턴이 검색됨](https://github.com/Azure/Azure-Sentinel/blob/83c6d8c7f65a5f209f39f3e06eb2f7374fd8439c/Detections/CommonSecurityLog/Fortinet-NetworkBeaconPattern.yaml)
    - [실패한 여러 Azure AD 로그인이 있는 IP가 Palo Alto VPN에 성공적으로 로그인함](https://github.com/Azure/Azure-Sentinel/blob/60e7aa065b196a6ed113c748a6e7ae3566f8c89c/Detections/MultipleDataSources/HostAADCorrelation.yaml)
    - [사용자에 의한 다중 암호 재설정](https://github.com/Azure/Azure-Sentinel/blob/83c6d8c7f65a5f209f39f3e06eb2f7374fd8439c/Detections/MultipleDataSources/MultiplePasswordresetsbyUser.yaml)
    - [드문 애플리케이션 동의](https://github.com/Azure/Azure-Sentinel/blob/83c6d8c7f65a5f209f39f3e06eb2f7374fd8439c/Detections/AuditLogs/RareApplicationConsent.yaml)
    - [이전에 보지 못한 IP를 통한 SharePointFileOperation](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/OfficeActivity/SharePoint_Downloads_byNewIP.yaml)
    - [의심스러운 리소스 배포](https://github.com/Azure/Azure-Sentinel/blob/83c6d8c7f65a5f209f39f3e06eb2f7374fd8439c/Detections/AzureActivity/NewResourceGroupsDeployedTo.yaml)

    > [!NOTE]
    > Fusion에서 사용하는 예약된 분석 규칙 세트의 경우 ML 알고리즘은 템플릿에 제공된 KQL 쿼리에 대해 유사 일치를 수행합니다. 템플릿 이름을 변경해도 Fusion 검색에 영향을 주지 않습니다.

## <a name="next-steps"></a>다음 단계

[Microsoft Sentinel의 Fusion 검색에](fusion.md)대해 자세히 알아봅니다.

다양한 [시나리오 기반 Fusion 검색에](fusion-scenario-reference.md)대해 자세히 알아봅니다.

이제 고급 다단계 공격 검색에 대해 자세히 알아보았습니다. 데이터 및 잠재적 위협에 대한 가시성을 얻는 방법을 알아보려면 [Microsoft Sentinel 시작](get-visibility.md)빠른 시작을 참조하세요.

생성된 인시던트 조사를 수행할 준비가 되면 [Microsoft Sentinel을](investigate-cases.md)사용하여 인시던트 조사 자습서를 참조하세요.
