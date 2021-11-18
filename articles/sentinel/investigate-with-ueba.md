---
title: UEBA 데이터를 사용하여 인시던트 조사 | Microsoft Docs
description: 조사하는 동안 UEBA 데이터를 사용하여 조직에서 발생할 수 있는 악의적인 활동에 대한 더 많은 컨텍스트를 얻는 방법을 알아봅니다.
author: batamig
ms.topic: how-to
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6d51d6c67db9afa664f16146315aeacb643fe44c
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132751615"
---
# <a name="tutorial-investigate-incidents-with-ueba-data"></a>자습서: UEBA 데이터를 사용하여 인시던트 조사

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 문서에서는 일반 조사 워크플로에서 [UEBA(사용자 엔터티 동작 분석)](identify-threats-with-entity-behavior-analytics.md)를 사용하기 위한 일반적인 방법 및 샘플 절차에 대해 설명합니다.

> [!IMPORTANT]
>
> 이 문서에서 언급된 기능은 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>

> [!NOTE]
> 이 자습서에서는 상위 고객 작업인 UEBA 데이터로 조사를 위한 시나리오 기반 절차를 제공합니다. 자세한 내용은 [Microsoft 센티널로 인시던트 조사](investigate-cases.md)를 참조 하세요.
>
## <a name="prerequisites"></a>사전 요구 사항

조사에서 UEBA 데이터를 사용 하려면 먼저 [Microsoft 센티널에서 사용자 및 엔터티 동작 분석 (ueba)을 사용 하도록 설정](enable-entity-behavior-analytics.md)해야 합니다.

UEBA가 사용하도록 설정되면 약 1주일 후에 컴퓨터 기반 인사이트 검색을 시작합니다.

## <a name="run-proactive-routine-searches-in-entity-data"></a>엔터티 데이터에서 일상적인 사전 검색 실행

사용자 작업을 통해 정기적인 사전 검색을 실행하여 추가 조사를 위한 단서를 만드는 것이 좋습니다.

Microsoft 센티널 [사용자 및 엔터티 동작 분석 통합 문서](identify-threats-with-entity-behavior-analytics.md#hunting-queries-and-exploration-queries) 를 사용 하 여 다음과 같은 데이터를 쿼리할 수 있습니다.

- **가장 위험한 사용자** - 변칙 또는 연결된 인시던트가 있는 사용자
- **특정 사용자에 대한 데이터** - 주체가 실제로 손상되었는지 여부 또는 사용자 프로필에서 벗어난 작업으로 인한 내부자 위협이 있는지 여부를 확인합니다.

또한 UEBA 통합 문서에서 일상적이지 않은 작업을 캡처하고, 이를 사용하여 비정상적인 작업과 잠재적인 비준수 사례를 찾습니다.

### <a name="investigate-an-anomalous-sign-in"></a>비정상적인 로그인 조사

예를 들어 다음 단계에서는 이전에 한 번도 사용하지 않은 VPN에 연결한 사용자를 조사합니다. 이는 비정상적인 작업입니다.

1. Sentinel **통합 문서** 영역에서 **사용자 및 엔터티 동작 분석** 통합 문서를 검색하여 엽니다.
1. 조사할 특정 사용자 이름을 검색하고, **조사할 상위 사용자** 테이블에서 해당 이름을 선택합니다.
1. **인시던트 분석** 및 **변칙 분석** 테이블을 아래로 스크롤하여 선택한 사용자와 관련된 인시던트 및 변칙을 확인합니다.
1. **성공한 비정상 로그온** 과 같은 변칙에서 아래 표에 설명된 세부 정보를 검토하여 조사합니다. 예를 들어:

    |단계  |Description  |
    |---------|---------|
    |**오른쪽의 설명 참고**     |    각 변칙에는 [MITRE ATT&CK 기술 자료](https://attack.mitre.org/)에서 자세히 알아볼 수 있는 링크가 포함된 설명이 있습니다. <br>예를 들어: <br><br>        **_초기 액세스_* _ <br>_악의적 사용자가 네트워크에 연결하려고 합니다.* <br>* 초기 액세스는 다양한 입력 벡터를 사용하여 네트워크 내에서 초기 기반을 확보하는 기술로 구성됩니다. 기반을 확보하는 데 사용되는 기술로 대상이 지정된 스피어 피싱과 퍼블릭 웹 서버의 취약성 악용이 있습니다. 초기 액세스를 통해 확보된 기반은 유효한 계정 및 외부 원격 서비스 사용과 같은 지속적인 액세스를 허용하거나 암호 변경으로 인해 사용이 제한될 수 있습니다.*     |
    |**설명 열의 텍스트 참고**     |   변칙 행에서 오른쪽으로 스크롤하여 추가 설명을 확인합니다. 전체 텍스트를 보려면 링크를 선택합니다. 예를 들어: <br><br> *악의적 사용자는 자격 증명 액세스 기술을 사용하여 특정 사용자 또는 서비스 계정의 자격 증명을 도용하거나, 초기 액세스를 얻기 위해 소셜 엔지니어링을 통해 정찰 프로세스 초기에 자격 증명을 캡처할 수 있습니다. 예를 들어 APT33은 유효한 계정을 초기 액세스에 사용했습니다. 아래의 쿼리는 사용자가 이전에 연결한 적이 없고 피어도 없는 새 지리적 위치에서 수행한 성공적인 로그인에 대한 출력을 생성합니다.*     |
    |**UsersInsights 데이터 참고**     |  변칙 행에서 오른쪽으로 더 스크롤하여 계정 표시 이름 및 계정 개체 ID와 같은 사용자 인사이트 데이터를 확인합니다. 오른쪽에서 전체 데이터를 보려면 텍스트를 선택합니다.         |
    |**증거 데이터 참고**     |  변칙 행에서 오른쪽으로 더 스크롤하여 변칙에 대한 증거 데이터를 확인합니다. 오른쪽에서 전체 데이터를 보려면 다음 필드와 같은 텍스트를 선택합니다. <br><br>-  **ActionUncommonlyPerformedByUser** <br>- **UncommonHighVolumeOfActions** <br>- **FirstTimeUserConnectedFromCountry** <br>- **CountryUncommonlyConnectedFromAmongPeers** <br>- **FirstTimeUserConnectedViaISP** <br>- **ISPUncommonlyUsedAmongPeers** <br>- **CountryUncommonlyConnectedFromInTenant** <br>- **ISPUncommonlyUsedInTenant** |
    |     |         |

**사용자 및 엔터티 동작 분석** 통합 문서에 있는 데이터를 사용하여 사용자 작업이 의심스럽고 추가 작업이 필요한지 여부를 확인합니다.

## <a name="use-ueba-data-to-analyze-false-positives"></a>UEBA 데이터를 사용하여 가양성 분석

조사에서 캡처된 인시던트가 가양성인 경우가 있습니다.

가양성의 일반적인 예는 동일한 시간 내에 뉴욕과 런던에서 애플리케이션 또는 포털에 로그인한 사용자와 같이 불가능한 이동 작업이 검색되는 경우입니다. Microsoft 센티널에서 비정상으로 이동 하는 것을 확인 하는 동안 사용자를 조사 하면 VPN이 사용자의 실제 위치에 대 한 대체 위치와 함께 사용 되었다는 것을 명확히 설명할 수 있습니다.

### <a name="analyze-a-false-positive"></a>가양성 분석

예를 들어 **불가능한 이동** 인시던트인 경우 VPN이 사용되었음을 사용자에게 확인한 후 인시던트에서 사용자 엔터티 페이지로 이동합니다. 여기에 표시된 데이터를 사용하여 캡처된 위치가 일반적으로 알려진 사용자의 위치에 포함되는지 여부를 확인합니다.

예를 들어:

[ ![인시던트의 사용자 엔터티 페이지 열기](media/ueba/open-entity-pages.png) ](media/ueba/open-entity-pages.png#lightbox)

사용자 엔터티 페이지는 [인시던트 페이지](investigate-cases.md#how-to-investigate-incidents) 자체와 [조사 그래프](investigate-cases.md#use-the-investigation-graph-to-deep-dive)에서도 연결됩니다.

> [!TIP]
> 사용자 엔터티 페이지에서 인시던트에 연결 된 특정 사용자에 대 한 데이터를 확인 한 후 Microsoft 센티널 **구하기** 영역으로 이동 하 여 사용자의 피어가 일반적으로 동일한 위치에서 연결 되는지 여부를 파악 합니다. 그렇다면 이 지식은 가양성에 대한 훨씬 더 강력한 사례를 만듭니다.
>
> **헌팅** 영역에서 **비정상적인 지리적 위치 로그온** 쿼리를 실행합니다. 자세한 내용은 [Microsoft 센티널로 위협 구하기](hunting.md)를 참조 하세요.
>

### <a name="embed-identityinfo-data-in-your-analytics-rules-public-preview"></a>분석 규칙에 IdentityInfo 데이터 포함(공개 미리 보기)

공격자가 조직의 사용자 및 서비스 계정을 사용하는 경우가 많기 때문에 조사 과정에서 사용자 ID와 권한을 비롯한 사용자 계정에 대한 데이터가 분석가에게 매우 중요합니다.

**IdentityInfo 테이블** 의 데이터를 포함하여 사용 사례에 맞게 분석 규칙을 미세 조정하고, 가양성을 줄이고, 조사 프로세스를 가속화할 수 있습니다.

예를 들면 다음과 같습니다.

- **IT** 부서 외부 사용자가 서버에 액세스할 경우 트리거되는 경고의 **IdentityInfo** 테이블과 보안 이벤트의 상관 관계를 만들려면 다음을 수행 합니다.

    ```kusto
    SecurityEvent
    | where EventID in ("4624","4672")
    | where Computer == "My.High.Value.Asset"
    | join kind=inner  (
        IdentityInfo
        | summarize arg_max(TimeGenerated, *) by AccountObjectId) on $left.SubjectUserSid == $right.AccountSID
    | where Department != "IT"
    ```

- 특정 보안 그룹의 구성원이 아닌 사용자가 애플리케이션에 액세스하는 경우 트리거되는 경고의 **IdentityInfo** 테이블과 Azure AD 로그인 로그의 상관 관계를 지정하려면 다음을 수행합니다.

    ```kusto
    SigninLogs
    | where AppDisplayName == "GithHub.Com"
    | join kind=inner  (
        IdentityInfo
        | summarize arg_max(TimeGenerated, *) by AccountObjectId) on $left.UserId == $right.AccountObjectId
    | where GroupMembership !contains "Developers"
    ```

**IdentityInfo** 테이블은 Azure AD 작업 영역과 동기화하여 사용자 메타데이터, 그룹 정보, 각 사용자에게 할당된 Azure AD 역할과 같은 사용자 프로필 데이터의 스냅샷을 만듭니다. 자세한 내용은 UEBA 보강 참조의 [IdentityInfo 테이블](ueba-enrichments.md#identityinfo-table-public-preview)을 참조하세요.

## <a name="identify-password-spray-and-spear-phishing-attempts"></a>암호 스프레이 및 스피어 피싱 시도 식별

MFA(다단계 인증)가 사용하도록 설정되지 않으면 사용자 자격 증명은 [암호 스프레이](https://www.microsoft.com/security/blog/2020/04/23/protecting-organization-password-spray-attacks/) 또는 [스피어 피싱](https://www.microsoft.com/security/blog/2019/12/02/spear-phishing-campaigns-sharper-than-you-think/) 시도로 공격을 손상시키려는 공격자에게 취약합니다.

### <a name="investigate-a-password-spray-incident-with-ueba-insights"></a>UEBA 인사이트를 사용하여 암호 스프레이 인시던트 조사

예를 들어 UEBA 인사이트를 사용하여 암호 스프레이 인시던트를 조사하려면 다음을 수행하여 자세히 알아볼 수 있습니다.

1. 인시던트의 왼쪽 아래에서 **조사** 를 선택하여 공격의 대상이 될 수 있는 계정, 컴퓨터 및 기타 데이터 요소를 확인합니다.

    데이터를 검색하면 로그온 실패 횟수가 비교적 많은 관리자 계정이 표시될 수 있습니다. 이 경우 의심스럽지만 추가 확인 없이 계정을 제한하지 않을 수 있습니다.

1. 맵에서 관리 사용자 엔터티를 선택한 다음, 오른쪽에서 **인사이트** 를 선택하여 시간이 지남에 따른 로그인 그래프와 같은 자세한 정보를 찾습니다.

1. 오른쪽에서 **정보** 를 선택한 다음, **전체 세부 정보 보기** 를 선택하여 더 자세히 드릴다운할 수 있는 [사용자 엔터티 페이지](identify-threats-with-entity-behavior-analytics.md#entity-pages)로 이동합니다. 

    예를 들어 사용자의 첫 번째 잠재적 암호 스프레이 인시던트인지 여부를 확인하거나, 사용자의 로그인 기록을 조사하여 비정상적인 오류인지 여부를 파악합니다.

> [!TIP]
> **실패한 비정상 로그온** [헌팅 쿼리](hunting.md)를 실행하여 조직의 실패한 비정상 로그온을 모두 모니터링할 수도 있습니다. 쿼리의 결과를 사용하여 가능한 암호 스프레이 공격에 대한 조사를 시작합니다.
>

## <a name="url-detonation-public-preview"></a>URL 데토네이션(공개 미리 보기)

로그에 Microsoft 수집에 대 한 Url이 있는 경우 해당 Url은 심사 프로세스를 가속화 하는 데 도움이 되도록 자동으로 detonated 됩니다. 

조사 그래프에는 다음 세부 정보뿐만 아니라 데토네이트된 URL에 대한 노드가 포함됩니다.

- **DetonationVerdict** 데토네이션의 높은 수준의 부울 결정 예를 들어 **Bad** 는 해당 측면이 호스팅 맬웨어 또는 피싱 콘텐츠로 분류되었음을 의미합니다.
- **DetonationFinalURL** 원래 URL에서 모두 리디렉션된 후 관찰된 최종 방문 페이지 URL
- **DetonationScreenshot** 경고가 트리거되었을 때 페이지가 어떻게 생겼는지 보여 주는 스크린샷 확대할 스크린샷을 선택합니다.

예를 들면 다음과 같습니다.

:::image type="content" source="media/investigate-with-ueba/url-detonation-example.png" alt-text="조사 그래프에 표시된 샘플 URL 데토네이션":::

> [!TIP]
> 로그에 URL이 표시되지 않으면 보안 웹 게이트웨이, 웹 프록시, 방화벽 또는 레거시 IDS/IPS에 대해 URL 로깅(위협 로깅이라고도 함)이 활성화되어 있는지 확인합니다.
>
> 추가 조사를 위해 관심 있는 특정 URL을 Microsoft Sentinel에 채널로 보내는 사용자 지정 로그를 만들 수도 있습니다.
>

## <a name="next-steps"></a>다음 단계

UEBA, 조사 및 헌팅에 대해 자세히 알아보세요.

- [Microsoft Sentinel에서 UEBA(사용자 및 엔터티 동작 분석)를 사용하여 고급 위협 식별](identify-threats-with-entity-behavior-analytics.md)
- [Microsoft Sentinel UEBA 보강 참조](ueba-enrichments.md)
- [자습서: Microsoft Sentinel을 사용하여 인시던트 조사](investigate-cases.md)
- [Microsoft Sentinel을 사용하여 위협 헌츠](hunting.md)
