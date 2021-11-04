---
title: Microsoft Defender for Cloud의 적응형 애플리케이션 제어
description: 이 문서는 Microsoft Defender for Cloud에서 적응형 애플리케이션 제어를 사용하여 Azure 머신에 대해 실행되는 애플리케이션의 허용 목록을 만드는 데 도움이 됩니다.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 11/02/2021
ms.author: memildin
ms.openlocfilehash: ae757f9facc7365dafb8412ec164d549e73f1df7
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131576171"
---
# <a name="use-adaptive-application-controls-to-reduce-your-machines-attack-surfaces"></a>적응형 애플리케이션 제어를 통해 컴퓨터의 공격 표면 축소

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for Cloud의 적응형 애플리케이션 제어의 이점과 이 데이터 기반 인텔리전트 기능을 사용하여 보안을 강화하는 방법을 알아봅니다.

## <a name="what-are-adaptive-application-controls"></a>적응형 애플리케이션 제어란?

적응형 애플리케이션 제어는 컴퓨터에 대해 안전한 것으로 알려진 애플리케이션의 허용 목록을 정의할 수 있는 자동화된 지능형 솔루션입니다. 

조직에서 동일한 프로세스를 일상적으로 실행하는 컴퓨터 모음이 있는 경우가 종종 있습니다. Microsoft Defender for Cloud는 기계 학습을 사용하여 머신에서 실행되는 애플리케이션을 분석하고 알려진 안전한 소프트웨어 목록을 만듭니다. 허용 목록은 특정 Azure 워크로드를 기반으로 하며 아래 지침에 따라 권장 사항을 추가로 사용자 지정할 수 있습니다.

적응형 애플리케이션 제어를 사용하도록 설정하고 구성하면 안전한 것으로 정의한 애플리케이션이 아닌 다른 애플리케이션이 실행될 경우 보안 경고가 표시됩니다.


## <a name="what-are-the-benefits-of-adaptive-application-controls"></a>적응형 애플리케이션 제어의 이점은 무엇인가요?

알려진 안전한 애플리케이션 목록을 정의하고 다른 항목이 실행될 때 경고를 생성하여 여러 감독 및 규정 준수 목표를 달성할 수 있습니다.

- 맬웨어 방지 솔루션에서 놓칠 수 있는 모든 잠재적 맬웨어 식별
- 라이선스가 부여된 소프트웨어만 사용하도록 규정하는 로컬 보안 정책으로 규정 준수 개선
- 오래된 버전 또는 지원되지 않는 애플리케이션 버전 식별 
- 조직에서 금지되었지만 머신에서 실행되는 소프트웨어를 식별합니다.
- 민감한 데이터에 액세스하는 앱에 대한 감독 강화

현재 사용할 수 있는 적용 옵션은 없습니다. 적응형 애플리케이션 제어는 안전한 것으로 정의된 애플리케이션 이외의 애플리케이션이 실행될 때 보안 경고를 제공하기 위한 것입니다.

## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|가격 책정:|[서버용 Microsoft Defender 필요](defender-for-servers-introduction.md)|
|지원되는 컴퓨터:|:::image type="icon" source="./media/icons/yes-icon.png"::: Windows와 Linux를 실행하는 Azure 및 비 Azure 머신<br>:::image type="icon" source="./media/icons/yes-icon.png"::: [Azure Arc](../azure-arc/index.yml) 머신|
|필요한 역할 및 권한:|**보안 리더** 및 **리더** 역할은 그룹과 안전한 것으로 알려진 애플리케이션 목록을 모두 볼 수 있습니다.<br>**기여자** 및 **보안 관리자** 역할은 그룹과 안전한 것으로 알려진 애플리케이션 목록을 모두 편집할 수 있습니다.|
|클라우드:|:::image type="icon" source="./media/icons/yes-icon.png"::: 상용 클라우드<br>:::image type="icon" source="./media/icons/yes-icon.png"::: 국가/소버린(Azure Government, Azure 중국 21Vianet)|
|||



## <a name="enable-application-controls-on-a-group-of-machines"></a>특정 컴퓨터 그룹에 애플리케이션 제어를 사용하도록 설정

Microsoft Defender for Cloud가 구독에서 비슷한 애플리케이션 집합을 일관되게 실행하는 컴퓨터 그룹을 식별한 경우 다음 권장 사항이 표시됩니다. **머신에서 안전한 애플리케이션을 정의하기 위한 적응형 애플리케이션 제어를 사용하도록 설정해야 합니다.**

권장 사항을 선택하거나 적응형 애플리케이션 제어 페이지를 열어 안전한 것으로 알려진 애플리케이션 및 컴퓨터 그룹의 제안된 목록을 봅니다.

1. 워크로드 보호 대시보드를 열고 고급 보호 영역에서 **적응형 애플리케이션 제어를 선택합니다.**

    :::image type="content" source="./media/adaptive-application/opening-adaptive-application-control.png" alt-text="Azure 대시보드에서 적응형 애플리케이션 제어 열기" lightbox="./media/adaptive-application/opening-adaptive-application-control.png":::

    다음 탭으로 그룹화된 VM이 있는 **적응형 애플리케이션 제어** 페이지가 열립니다.

    - **구성됨** - 애플리케이션의 허용 목록이 이미 정의된 컴퓨터 그룹입니다. 구성됨 탭에는 그룹별로 다음이 표시됩니다.
        - 그룹에 속한 컴퓨터의 수
        - 최근 경고

    - **권장됨** - 동일한 애플리케이션을 일관되게 실행하고 허용 목록이 구성되지 않은 컴퓨터 그룹입니다. 이 그룹에는 적응형 애플리케이션 제어를 사용하도록 설정하는 것이 좋습니다.
    
      > [!TIP]
      > 접두사 "REVIEWGROUP"이 있는 그룹 이름이 표시되면 부분적으로 일관된 애플리케이션 목록이 있는 컴퓨터가 포함됩니다. Microsoft Defender for Cloud는 패턴을 볼 수 없지만 이 그룹을 검토하여 그룹의 적응형 애플리케이션 제어 규칙 편집에 설명된 대로 일부 적응형 [애플리케이션 제어 규칙을](#edit-a-groups-adaptive-application-controls-rule)수동으로 정의할 _수_ 있는지 확인하는 것이 좋습니다.
      >
      > [한 그룹에서 다른 그룹으로 컴퓨터 이동](#move-a-machine-from-one-group-to-another)에 설명된 대로 이 그룹에서 다른 그룹으로 컴퓨터를 이동할 수도 있습니다.

    - **권장 사항 없음** - 애플리케이션 허용 목록이 정의되지 않고 이 기능을 지원하지 않는 컴퓨터입니다. 다음과 같은 이유로 컴퓨터가 이 탭에 속할 수 있습니다.
      - Log Analytics 에이전트가 없습니다.
      - Log Analytics 에이전트가 이벤트를 보내지 않습니다.
      - GPO 또는 로컬 보안 정책에서 사용하도록 설정된 기존 [AppLocker](/windows/security/threat-protection/windows-defender-application-control/applocker/applocker-overview) 정책이 있는 Windows 컴퓨터입니다.

      > [!TIP]
      > Defender for Cloud는 머신 그룹당 고유한 권장 사항을 정의하려면 2주 이상의 데이터가 필요합니다. 최근에 생성되었거나 최근에 서버용 Microsoft Defender에 의해 보호된 구독에 속한 컴퓨터는 권장 **사항 없음** 탭 아래에 표시됩니다.

1. **권장됨** 탭을 엽니다. 권장 허용 목록이 있는 컴퓨터 그룹이 나타납니다.

   ![권장 탭](./media/adaptive-application/adaptive-application-recommended-tab.png)

1. 그룹을 선택합니다. 

1. 새 규칙을 구성하려면 특정 컴퓨터 그룹에 고유한 이 **애플리케이션 제어 규칙 구성** 페이지의 다양한 섹션과 그 내용을 검토합니다.

   ![새 규칙을 구성합니다.](./media/adaptive-application/adaptive-application-create-rule.png)

   1. **컴퓨터 선택** - 기본적으로 식별된 그룹 내의 모든 컴퓨터가 선택됩니다. 이 규칙에서 제거하려면 선택을 취소합니다.
   
   1. **권장 애플리케이션** - 이 그룹 내의 컴퓨터에 공통적이고 실행을 허용하도록 권장되는 이 애플리케이션 목록을 검토합니다.
   
   1. **더 많은 애플리케이션** - 이 그룹 내의 컴퓨터에서 덜 자주 보이거나 악용 가능한 것으로 알려진 이 애플리케이션 목록을 검토합니다. 경고 아이콘은 공격자가 특정 애플리케이션을 사용하여 애플리케이션 허용 목록을 우회할 수 있음을 나타냅니다. 이러한 애플리케이션을 신중하게 검토하는 것이 좋습니다.

      > [!TIP]
      > 두 애플리케이션 목록에는 모두 특정 애플리케이션을 특정 사용자로 제한하는 옵션이 있습니다. 가능한 한 최소 권한 원칙을 적용해야 합니다.
      > 
      > 애플리케이션은 게시자에 의해 정의되며, 애플리케이션에 게시자 정보가 없는 경우(서명되지 않은 경우) 특정 애플리케이션의 전체 경로에 대한 경로 규칙이 생성됩니다.

   1. 규칙을 적용하려면 **감사** 를 선택합니다. 




## <a name="edit-a-groups-adaptive-application-controls-rule"></a>그룹의 적응형 애플리케이션 제어 규칙 편집

조직의 알려진 변경 사항으로 인해 컴퓨터 그룹에 대한 허용 목록을 편집해야 할 수도 있습니다. 

컴퓨터 그룹에 대한 규칙을 편집하려면:

1. **워크로드 보호 대시보드를** 열고 고급 보호 영역에서 **적응형 애플리케이션 제어를 선택합니다.**

1. **구성됨** 탭에서 편집할 규칙이 있는 그룹을 선택합니다.

1. [컴퓨터 그룹에 적응형 애플리케이션 제어를 사용하도록 설정](#enable-application-controls-on-a-group-of-machines)에 설명된 대로 **애플리케이션 제어 규칙 구성** 페이지의 다양한 섹션을 검토합니다.

1. 필요한 경우 하나 이상의 사용자 지정 규칙을 추가합니다.

   1. **규칙 추가** 를 선택합니다.

      ![사용자 지정 규칙을 추가합니다.](./media/adaptive-application/adaptive-application-add-custom-rule.png)

   1. 안전한 것으로 알려진 경로를 정의하는 경우 **규칙 유형** 을 '경로'로 변경하고 단일 경로를 입력합니다. 경로에 와일드카드 문자를 포함할 수 있습니다.
   
      > [!TIP]
      > 경로에 와일드카드 문자를 사용하면 유용할 수 있는 몇 가지 상황은 다음과 같습니다.
      > 
      > * 와일드카드 문자를 경로 끝 부분에 사용하여 이 폴더와 하위 폴더에 있는 모든 실행 파일 허용.
      > * 와일드카드 문자를 경로 중간 부분에 사용하여 해당 폴더의 이름이 바뀌는(예: 알려진 실행 파일이 있는 개인 사용자 폴더, 자동으로 생성된 폴더 이름 등) 알려진 실행 파일을 사용하도록 설정.
  
   1. 허용되는 사용자 및 보호되는 파일 형식을 정의합니다.

   1. 규칙 정의를 마쳤으면 **추가** 를 선택합니다.

1. 변경 내용을 적용하려면 **저장** 을 선택합니다.


## <a name="review-and-edit-a-groups-settings"></a>그룹 설정 검토 및 편집

1. 그룹의 세부 정보 및 설정을 보려면 **그룹 설정** 을 선택합니다.

    이 창에는 그룹 이름(수정 가능함), OS 유형, 위치 및 기타 관련 세부 정보가 표시됩니다.

    :::image type="content" source="./media/adaptive-application/adaptive-application-group-settings.png" alt-text="적응형 애플리케이션 제어의 그룹 설정 페이지" lightbox="./media/adaptive-application/adaptive-application-group-settings.png":::

1. 필요한 경우 그룹의 이름 또는 파일 형식 보호 모드를 수정합니다.

1. **적용** 및 **저장** 을 선택합니다.



## <a name="respond-to-the-allowlist-rules-in-your-adaptive-application-control-policy-should-be-updated-recommendation"></a>"적응형 애플리케이션 제어 정책의 허용 목록 규칙을 업데이트해야 합니다" 권장 사항에 대응

Cloud용 Defender의 기계 학습에서 이전에 허용되지 않은 합법적인 동작을 식별하면 이 권장 사항이 표시됩니다. 이 권장 사항은 가양성 경고의 수를 줄이기 위해 기존 정의에 대한 새 규칙을 제안합니다.

이 문제를 수정하려면:

1. 권장 사항 페이지에서 **적응형 애플리케이션 제어 정책의 허용 목록 규칙을 업데이트해야 합니다** 권장 사항을 선택하여 새로 식별된 잠재적으로 합법적인 동작을 나타내는 그룹을 표시합니다.

1. 편집할 규칙이 있는 그룹을 선택합니다.

1. [컴퓨터 그룹에 적응형 애플리케이션 제어를 사용하도록 설정](#enable-application-controls-on-a-group-of-machines)에 설명된 대로 **애플리케이션 제어 규칙 구성** 페이지의 다양한 섹션을 검토합니다.

1. 변경 내용을 적용하려면 **감사** 를 선택합니다.




## <a name="audit-alerts-and-violations"></a>감사 경고 및 위반

1. **워크로드 보호 대시보드를** 열고 고급 보호 영역에서 **적응형 애플리케이션 제어를 선택합니다.**

1. 최근 경고가 있는 컴퓨터의 그룹을 확인하려면 **구성됨** 탭에 나열된 그룹을 살펴봅니다.

1. 자세히 조사하려면 그룹을 선택합니다.

   ![최근 경고](./media/adaptive-application/recent-alerts.png)

1. 자세한 내용과 함께 영향을 받는 컴퓨터의 목록을 보려면 경고를 선택합니다.

    경고 페이지에는 경고에 대한 자세한 내용이 표시되고 위협을 완화하는 방법에 대한 권장 사항이 있는 **작업 수행** 링크가 제공됩니다.

    :::image type="content" source="media/adaptive-application/adaptive-application-alerts-start-time.png" alt-text="적응형 애플리케이션 제어 경고의 시작 시간은 적응형 애플리케이션 제어에서 경고를 만든 시간입니다.":::

    > [!NOTE]
    > 적응형 애플리케이션 제어는 12시간마다 한 번씩 이벤트 수를 계산합니다. 경고 페이지에 표시된 "활동 시작 시간"은 의심스러운 프로세스가 활성화된 시간이 **아니라** 적응형 애플리케이션 제어에서 경고를 만든 시간입니다.


## <a name="move-a-machine-from-one-group-to-another"></a>그룹 간 컴퓨터 이동

한 그룹에서 다른 그룹으로 컴퓨터를 이동하면 해당 그룹에 적용된 애플리케이션 제어 정책이 이동한 대상 그룹의 설정으로 변경됩니다. 구성된 그룹에서 구성되지 않은 그룹으로 컴퓨터를 이동할 수도 있습니다. 이렇게 하면 해당 컴퓨터에 적용된 모든 애플리케이션 제어 규칙이 제거됩니다.

1. **워크로드 보호 대시보드를** 열고 고급 보호 영역에서 **적응형 애플리케이션 제어를 선택합니다.**

1. **적응형 애플리케이션 제어** 페이지의 **구성됨** 탭에서 이동할 컴퓨터가 포함된 그룹을 선택합니다.

1. **구성된 컴퓨터** 목록을 엽니다.

1. 행 끝 부분에 있는 세 개의 점에서 컴퓨터의 메뉴를 열고 **이동** 을 선택합니다. **컴퓨터를 다른 그룹으로 이동** 창이 열립니다.

1. 대상 그룹을 선택하고 **컴퓨터 이동** 을 선택합니다.

1. 변경 내용을 저장하려면 **저장** 을 선택합니다.





## <a name="manage-application-controls-via-the-rest-api"></a>REST API를 통해 애플리케이션 제어 관리 

적응형 애플리케이션 제어를 프로그래밍 방식으로 관리하려면 REST API를 사용합니다. 

관련 API 설명서는 [Defender for Cloud API 문서의 적응형 애플리케이션 제어 섹션에서](/rest/api/securitycenter/adaptiveapplicationcontrols)사용할 수 있습니다.

REST API에서 사용 가능한 몇 가지 함수는 다음과 같습니다.

* **List** 는 모든 그룹 권장 사항을 검색하고 각 그룹에 대한 개체가 있는 JSON을 제공합니다.

* **Get** 은 전체 권장 사항 데이터(즉, 컴퓨터 목록, 게시자/경로 규칙 등)가 있는 JSON을 검색합니다.

* **Put** 은 규칙을 구성합니다(**Get** 을 사용하여 검색한 JSON을 이 요청의 본문으로 사용).
 
   > [!IMPORTANT]
   > **Put** 함수에는 Get 명령에서 반환된 JSON에 포함된 것보다 더 적은 수의 매개 변수가 필요합니다.
   >
   > Put 요청에서 JSON을 사용하기 전에 recommendationStatus, configurationStatus, issues, location 및 sourceSystem 속성을 제거합니다.


## <a name="faq---adaptive-application-controls"></a>FAQ - 적응형 애플리케이션 제어

- [애플리케이션 제어를 적용하는 옵션이 있나요?](#are-there-any-options-to-enforce-the-application-controls)
- [내 권장 애플리케이션에 Qualys 앱이 표시되는 이유는 무엇인가요?](#why-do-i-see-a-qualys-app-in-my-recommended-applications)

### <a name="are-there-any-options-to-enforce-the-application-controls"></a>애플리케이션 제어를 적용하는 옵션이 있나요?
현재 사용할 수 있는 적용 옵션은 없습니다. 적응형 애플리케이션 제어는 안전한 것으로 정의된 애플리케이션 이외의 애플리케이션이 실행될 때 **보안 경고** 를 제공하기 위한 것입니다. 이 페이지에 나와 있는 것처럼 다양한 이점이 있습니다([적응형 애플리케이션 제어의 이점은 무엇인가요?](#what-are-the-benefits-of-adaptive-application-controls)).

### <a name="why-do-i-see-a-qualys-app-in-my-recommended-applications"></a>내 권장 애플리케이션에 Qualys 앱이 표시되는 이유는 무엇인가요?
[서버용 Microsoft Defender에는](defender-for-servers-introduction.md) 추가 비용 없이 머신에 대한 취약성 검사가 포함됩니다. Qualys 라이선스 또는 Qualys 계정이 필요하지 않습니다. 모든 것이 Defender for Cloud 내에서 원활하게 처리됩니다. 이 스캐너의 세부 정보 및 배포 방법에 대한 지침은 [Defender for Cloud의 통합 Qualys 취약성 평가 솔루션을 참조하세요.](deploy-vulnerability-assessment-vm.md)

Defender for Cloud가 스캐너를 배포할 때 경고가 생성되지 않도록 하기 위해 적응형 애플리케이션 제어 권장 허용 목록에는 모든 컴퓨터에 대한 스캐너가 포함됩니다. 


## <a name="next-steps"></a>다음 단계
이 페이지에서는 Microsoft Defender for Cloud에서 적응형 애플리케이션 제어를 사용하여 Azure 및 비 Azure 머신에서 실행되는 애플리케이션의 허용 목록을 정의하는 방법을 배웠습니다. 다른 클라우드 워크로드 보호 기능에 대한 자세한 내용은 다음을 참조하세요.

* [JIT(Just-In-Time) VM 액세스 이해](just-in-time-access-overview.md)
* [Azure Kubernetes 클러스터 보안 설정](defender-for-kubernetes-introduction.md)