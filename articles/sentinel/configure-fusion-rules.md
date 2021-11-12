---
title: Microsoft 센티널에서 퓨전 (다단계 attack detection) 규칙 구성
description: Microsoft 센티널의 Fusion 기술에 따라 공격 감지 규칙을 만들고 구성 합니다.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 799bcc2d153b5bd49431cca7e93a68297383b08b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132336807"
---
# <a name="configure-multistage-attack-detection-fusion-rules-in-microsoft-sentinel"></a>Microsoft 센티널에서 퓨전 (다단계 attack detection) 규칙 구성

> [!IMPORTANT]
> Fusion analytics 규칙의 새 버전은 현재 **미리 보기** 상태입니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Microsoft 센티널은 확장 가능한 기계 학습 알고리즘을 기반으로 하는 상관 관계 엔진인 Fusion을 사용 하 여 kill 체인의 여러 단계에서 관찰 되는 비정상 동작 및 의심 스러운 활동의 조합을 식별 함으로써 다단계 공격을 자동으로 감지 합니다. 이러한 검색의 기반으로 Microsoft 센티널은 catch 하기 어려운 인시던트를 생성 합니다. 이러한 인시던트는 둘 이상의 경고 또는 활동으로 구성됩니다. 설계 의도에 따라 이러한 인시던트는 볼륨이 작고, 충실도가 높고, 심각도가 높습니다.

환경에 맞게 사용자 지정되는 이 감지 기술은 [가양성](false-positives.md) 비율을 줄일 뿐 아니라 정보가 제한되거나 누락된 공격도 감지할 수 있습니다.

## <a name="configure-fusion-rules"></a>Fusion 규칙 구성

이 검색은 Microsoft 센티널에서 기본적으로 사용 하도록 설정 되어 있습니다. 상태를 확인 하거나 변경 하려면 다음 지침을 따르십시오.

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 고 **Microsoft 센티널** 을 입력 합니다.

1. Microsoft 센티널 탐색 메뉴에서 **Analytics** 를 선택 합니다.

1. **활성 규칙** 탭을 선택한 다음, **Fusion** 규칙 유형에 대한 목록을 필터링하여 **이름** 열에서 **고급 다단계 공격 감지** 를 찾습니다. **상태** 열을 확인하여 감지 사용 여부를 확인합니다.

    :::image type="content" source="./media/fusion/selecting-fusion-rule-type.png" alt-text="Fusion 분석 규칙의 스크린샷" lightbox="./media/fusion/selecting-fusion-rule-type.png":::

1. 상태를 변경하려면 이 항목을 선택하고 **고급 다단계 공격 감지** 블레이드에서 **편집** 을 선택합니다.

1. **분석 규칙 마법사** 의 **일반** 탭에서 상태 (사용/사용 안 함)를 확인 하거나 원하는 경우 변경 합니다.

    상태를 변경 했지만 변경 내용이 더 이상 없는 경우 **검토 및 업데이트** 탭을 선택 하 고 **저장** 을 선택 합니다.

    Fusion 검색 규칙을 추가로 구성 하려면 **다음: Fusion 구성** 을 선택 합니다.

    :::image type="content" source="media/configure-fusion-rules/configure-fusion-rule.png" alt-text="Fusion 규칙 구성의 스크린샷" lightbox="media/configure-fusion-rules/configure-fusion-rule.png":::

1. **Fusion 검색을 위한 원본 신호 구성**: 모든 심각도 수준에서 나열 된 모든 원본 신호를 포함 하 여 최상의 결과를 제공 하는 것이 좋습니다. 기본적으로 이미 모두 포함 되어 있지만 다음과 같은 방법으로 변경할 수 있습니다.

    > [!NOTE]
    > 특정 원본 신호 또는 경고 심각도 수준을 제외 하면 해당 원본의 신호를 사용 하는 Fusion 검색 또는 해당 심각도 수준과 일치 하는 경고가 트리거되지 않습니다. 
    
    - 비정상, 다양 한 공급자의 경고 및 원시 로그를 포함 하 여 **Fusion 검색에서 신호를 제외** 합니다.
     
        *사용 사례:* 잡음이 있는 경고를 생성 하는 것으로 알려진 특정 신호 원본을 테스트 하는 경우 Fusion 검색에 대해 해당 특정 신호 원본의 신호를 일시적으로 해제할 수 있습니다.

    - **각 공급자에 대 한 경고 심각도 구성**: 기본적으로 Fusion ML 모델은 여러 데이터 원본의 kill 체인에서 비정상적인 신호를 기반으로 낮은 정확도 신호를 단일 높은 심각도 인시던트에 연결 합니다. Fusion에 포함 된 경고는 일반적으로 낮은 심각도 (보통, 낮음, 정보) 이지만, 경우에 따라 관련 된 높은 심각도 경고가 포함 됩니다.
     
        *사용 사례:* 높은 심각도 경고를 심사 하 고 조사 하는 별도의 프로세스를 사용 하 고 Fusion에 이러한 경고를 포함 하지 않으려는 경우 Fusion 검색에서 높은 심각도 경고를 제외 하도록 원본 신호를 구성할 수 있습니다. 



    - **Fusion 검색에서 특정 검색 패턴을 제외** 합니다. 특정 Fusion 검색은 사용자 환경에 적용 되지 않거나 가양성을 생성 하기 쉽기 때문일 수 있습니다. 특정 Fusion 검색 패턴을 제외 하려면 아래 지침을 따르세요.

        1. 제외 하려는 종류의 Fusion 인시던트를 찾아서 엽니다.

        1. **설명** 섹션에서 **자세히 표시** 를 선택 합니다.

        1. **이 특정 검색 패턴 제외** 에서 **제외 링크** 를 선택 하면 분석 규칙 마법사의 **Fusion 구성** 탭으로 리디렉션됩니다.

            :::image type="content" source="media/configure-fusion-rules/exclude-fusion-incident.png" alt-text="Fusion 인시던트의 스크린샷. 제외 링크를 선택 합니다.":::

        **Fusion 구성** 탭에서 검색 패턴이 추가 된 시간을 제외 목록에 추가 하는 검색 패턴 (fusion 인시던트에 경고 및 비정상의 조합)을 볼 수 있습니다.

        검색 패턴에서 휴지통 아이콘을 선택 하 여 언제 든 지 제외 된 검색 패턴을 제거할 수 있습니다.

        :::image type="content" source="media/configure-fusion-rules/exclusion-patterns-list.png" alt-text="제외 된 검색 패턴 목록의 스크린샷":::

        제외 된 검색 패턴과 일치 하는 인시던트는 계속 트리거되고 **활성 인시던트 큐에 표시 되지** 않습니다. 이러한 값은 다음 값으로 자동으로 채워집니다.

        - **상태**: "닫힘"
        
        - **분류 종료**: "결정 되지 않음"

        - **설명**: "자동 폐쇄, 제외 된 Fusion 검색 패턴"

        - **태그**: "ExcludedFusionDetectionPattern"-이 태그를 쿼리하여이 검색 패턴과 일치 하는 모든 인시던트를 볼 수 있습니다.

            :::image type="content" source="media/configure-fusion-rules/auto-closed-incident.png" alt-text="자동 닫힘, 제외 된 Fusion 인시던트의 스크린샷.":::



> [!NOTE]
> Microsoft 센티널은 현재 30 일간의 기록 데이터를 사용 하 여 기계 학습 시스템을 학습 합니다. 이 데이터는 기계 학습 파이프라인을 통과할 때 항상 Microsoft의 키를 사용하여 암호화됩니다. 그러나 Microsoft 센티널 작업 영역에서 CMK를 사용 하도록 설정한 경우에는 [고객 관리 키 (cmk)](customer-managed-keys.md) 를 사용 하 여 학습 데이터를 암호화 하지 않습니다. Fusion을 옵트아웃 하려면 **Microsoft 센티널** \> **Configuration** \> **Analytics \> 활성 규칙** 으로 이동 하 고 **Advanced 다단계 Attack 검색** 규칙을 마우스 오른쪽 단추로 클릭 한 다음 **사용 안 함을 선택 합니다.**

## <a name="configure-scheduled-analytics-rules-for-fusion-detections"></a>Fusion 검색에 대해 예약 된 분석 규칙 구성

> [!IMPORTANT]
>
> - 분석 규칙 경고를 사용하는 Fusion 기반 검색은 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

**Fusion** 은 [예약 된 분석 규칙](detect-threats-custom.md)에 의해 생성 된 경고를 사용 하 여 시나리오 기반 다단계 공격과 새로운 위협을 검색할 수 있습니다. Microsoft 센티널의 Fusion 기능을 최대한 활용할 수 있도록 이러한 규칙을 구성 하 고 사용 하도록 설정 하려면 다음 단계를 수행 하는 것이 좋습니다.

1. 새로운 위협에 대 한 Fusion은 [기본 제공](detect-threats-built-in.md#scheduled) 되는 모든 예약 된 분석 규칙에 의해 생성 된 경고를 사용 하 고, [보안 분석가에 의해](detect-threats-custom.md)생성 된 경고를 사용 하 여 kill 체인 (전술) 및 엔터티 매핑 정보를 포함 합니다. Fusion에서 분석 규칙의 출력을 사용 하 여 새로운 위협을 검색할 수 있도록 합니다.

    - 이러한 예약된 규칙에 대한 **엔터티 매핑** 을 검토합니다. [엔터티 매핑 구성 섹션](map-data-fields-to-entities.md) 을 사용 하 여 쿼리 결과의 매개 변수를 Microsoft 센티널 인식 엔터티로 매핑할 수 있습니다. 퓨전은 엔터티 (예: *사용자 계정* 또는 *IP 주소*)를 기반으로 하는 경고의 상관 관계를 설정 하므로 해당 ML 알고리즘은 엔터티 정보 없이 경고 일치를 수행할 수 없습니다.

    - 분석 규칙 세부 정보에서 **전술** 을 검토합니다. Fusion ML 알고리즘은 [MITRE at&t&헤드](https://attack.mitre.org/) 방법 정보를 사용 하 여 다단계 공격을 검색 하 고 분석 규칙을 레이블 하는 전략을 결과 인시던트에 표시 합니다. 수신 경고에 전술 정보가 누락된 경우 Fusion 계산이 영향을 받을 수 있습니다.

1. 또한 Fusion은 **분석** 블레이드의 **규칙 템플릿** 탭에 있는 다음과 같은 예약 된 **분석 규칙 템플릿을** 기반으로 하는 규칙을 사용 하 여 시나리오 기반 위협을 검색할 수 있습니다. 이러한 검색을 사용 하도록 설정 하려면 템플릿 갤러리에서 규칙 이름을 선택 하 고 세부 정보 창에서 **규칙 만들기** 를 클릭 합니다.

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

[Microsoft 센티널의 Fusion](fusion.md)검색에 대해 자세히 알아보세요.

여러 [시나리오 기반 Fusion](fusion-scenario-reference.md)검색에 대해 자세히 알아보세요.

이제 고급 다단계 공격 검색에 대해 자세히 알아보았습니다. 다음 빠른 시작을 사용 하 여 데이터 및 잠재적 위협에 대 한 가시성을 얻는 방법에 대해 알아볼 수 있습니다. [Microsoft 센티널을 시작](get-visibility.md)하세요.

사용자를 위해 생성 된 인시던트를 조사할 준비가 된 경우 다음 자습서: [Microsoft 센티널로 인시던트 조사](investigate-cases.md)를 참조 하세요.
