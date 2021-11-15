---
title: Home Realm Discovery 정책을 통해 로그인 자동 가속 방지
titleSuffix: Azure AD
description: 페더레이션된 IDP로 domain_hint 자동 가속을 방지하는 방법에 대해 알아봅니다.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/12/2021
ms.author: davidmu
ms.reviewer: hirsin
ms.openlocfilehash: db874b82a8d0c966dbe6cdbecd14cf3c5fe521d2
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129615736"
---
# <a name="disable-auto-acceleration-sign-in-in-azure-active-directory"></a>Azure Active Directory에서 자동 가속 로그인 사용 안 함

HRD[(Home Realm Discovery) 정책](/graph/api/resources/homeRealmDiscoveryPolicy)은 관리자에게 사용자가 인증하는 방법 및 위치를 제어하는 여러 방법을 제공합니다. HRD 정책의 `domainHintPolicy` 섹션은 페더레이션된 사용자가 항상 Azure AD 로그인 페이지를 방문하고 도메인 힌트로 인해 페더레이션된 IDP로 자동 가속되지 않도록 함으로써 [FIDO](../authentication/howto-authentication-passwordless-security-key.md)와 같은 클라우드 관리 자격 증명으로 마이그레이션하는 데 사용됩니다.

이 정책은 애플리케이션에서 관리자가 로그인 중에 도메인 힌트 추가를 제어하거나 업데이트할 수 없는 경우에 필요합니다.  예를 들어, `outlook.com/contoso.com`은 `contoso.com` 도메인에 대한 페더레이션된 IDP에 직접 사용자를 자동으로 가속화하기 위해 `&domain_hint=contoso.com` 매개 변수가 추가된 로그인 페이지로 사용자를 보냅니다. 페더레이션된 IDP로 전송된 관리되는 자격 증명을 가진 사용자는 관리되는 자격 증명을 사용하여 로그인할 수 없으므로 보안이 저하되고 임의의 로그인 경험으로 인해 사용자가 불편을 겪습니다. 관리되는 자격 증명을 제공하는 관리자도 사용자가 항상 관리되는 자격 증명을 사용할 수 있도록 [이 정책을 설정해야](#suggested-use-within-a-tenant) 합니다.

## <a name="domainhintpolicy-details"></a>DomainHintPolicy 세부 정보

HRD 정책의 DomainHintPolicy 섹션은 관리자가 도메인 힌트 사용에서 특정 도메인 및 애플리케이션을 옵트아웃할 수 있도록 하는 JSON 개체입니다.  기능적으로 Azure AD 로그인 페이지에 로그인 요청의 `domain_hint` 매개 변수가 없는 것처럼 동작하도록 지시합니다.

### <a name="the-respect-and-ignore-policy-sections"></a>적용 및 무시 정책 섹션

|섹션 | 의미 | 값 |
|--------|---------|--------|
|`IgnoreDomainHintForDomains` |요청에서 이 도메인 힌트가 전송된 경우 이를 무시합니다. |도메인 주소의 배열(예: `contoso.com`). `all_domains`도 지원함|
|`RespectDomainHintForDomains`| 요청에서 이 도메인 힌트가 전송된 경우 `IgnoreDomainHintForApps`에서 요청에 있는 앱이 자동으로 가속되지 않아야 함을 나타내는 경우에도 해당 힌트를 적용합니다. 이는 네트워크에서 더 이상 사용되지 않는 도메인 힌트의 출시를 늦추는 데 사용됩니다. 일부 도메인은 계속 가속되어야 함을 나타낼 수 있습니다. | 도메인 주소의 배열(예: `contoso.com`). `all_domains`도 지원함|
|`IgnoreDomainHintForApps`| 이 애플리케이션의 요청에 도메인 힌트가 포함된 경우 이를 무시합니다. | 애플리케이션 ID의 배열(GUID). `all_apps`도 지원함|
|`RespectDomainHintForApps` |이 애플리케이션의 요청에 도메인 힌트가 포함된 경우 `IgnoreDomainHintForDomains`에 해당 도메인이 포함되더라도 이를 적용합니다. 도메인 힌트가 없이 중단을 발견할 경우 일부 앱이 계속 작동하는지 확인하는 데 사용됩니다. | 애플리케이션 ID의 배열(GUID). `all_apps`도 지원함|

### <a name="policy-evaluation"></a>정책 평가

DomainHintPolicy 논리는 도메인 힌트가 포함된 들어오는 각 요청에서 실행되고 요청에 포함된 두 개의 데이터(도메인 힌트의 도메인 및 클라이언트 ID(앱))에 따라 가속화됩니다. 간단히 말해서 도메인이나 앱에 대한 "적용"은 주어진 도메인이나 애플리케이션에 대한 도메인 힌트를 "무시"하라는 지침보다 우선합니다.

1. 도메인 힌트 정책이 없는 경우 또는 4개 섹션에서 언급된 앱이나 도메인 힌트를 참조하지 않는 경우 [나머지 HRD 정책이 평가됩니다](home-realm-discovery-policy.md#priority-and-evaluation-of-hrd-policies).
1. `RespectDomainHintForApps` 또는 `RespectDomainHintForDomains` 섹션 중 하나(또는 둘 다)가 요청에서 앱 또는 도메인 힌트를 포함하는 경우 요청된 대로 사용자가 페더레이션된 IDP로 자동 가속됩니다.
1. `IgnoreDomainHintsForApps` 또는 `IgnoreDomainHintsForDomains` 중 하나(또는 둘 다)가 요청에서 앱 또는 도메인 힌트를 참조하고 "적용" 섹션에서 참조되지 않는 경우 요청을 자동으로 가속하지 않으며 사용자는 Azure AD 로그인 페이지에서 사용자 이름을 제공하는 상태로 유지됩니다.

사용자가 로그인 페이지에서 사용자 이름을 입력하면 관리되는 자격 증명(등록된 경우)을 사용할 수 있습니다.  관리되는 자격 증명을 사용하지 않도록 선택하거나 등록되지 않은 경우 일반적으로 자격 증명 항목에 대한 페더레이션된 IDP로 이동됩니다.

## <a name="suggested-use-within-a-tenant"></a>테넌트 내에서 사용 추천

페더레이션된 도메인의 관리자는 4단계 계획에서 HRD 정책의 이 섹션을 설정해야 합니다. 이 계획의 목표는 궁극적으로 테넌트의 모든 사용자가 도메인 또는 애플리케이션에 관계없이 관리되는 자격 증명을 사용할 수 있는 기회를 갖도록 하고, `domain_hint` 사용량에 대한 종속성이 높은 앱을 저장하는 것입니다.  이 계획을 통해 관리자는 이러한 앱을 찾고 새 정책에서 제외하고 테넌트의 나머지 부분에 대한 변경 내용을 계속 적용할 수 있습니다.

1. 처음에 이 변경 내용을 적용할 도메인을 선택합니다.  이는 테스트 도메인이므로 UX 변경 내용(즉, 다른 로그인 페이지 표시)에 더 적합한 도메인을 선택합니다.  그러면 이 도메인 이름을 사용하는 모든 애플리케이션에서 모든 도메인 힌트가 무시됩니다. 테넌트 기본 HRD 정책에서 이 정책을 [설정](#configuring-policy-through-graph-explorer)합니다.

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": [] 
} 
```

2. 테스트 도메인 사용자의 의견을 수집합니다. 이러한 변경의 결과로 중단된 애플리케이션에 대한 세부 정보를 수집합니다. 이는 도메인 힌트 사용에 대한 종속성을 가지며 업데이트해야 합니다. 지금은 `RespectDomainHintForApps` 섹션에 추가합니다.

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

3. 새 도메인에 대한 정책 출시를 계속 확장하여 더 많은 의견을 수집합니다.

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com", "otherDomain.com", "anotherDomain.com"], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

4. 출시 완료 - 계속 가속해야 하는 도메인을 제외하고 모든 도메인을 대상으로 지정합니다.

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "*" ], 
    "RespectDomainHintForDomains": ["guestHandlingDomain.com"], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

4단계를 완료한 후에는 도메인 힌트가 페더레이션된 IDP로 자동 가속화되는 경우에도 `guestHandlingDomain.com`의 사용자를 제외한 모든 사용자가 AZURE AD 로그인 페이지에서 로그인할 수 있습니다.  이 예외는 로그인을 요청하는 앱이 제외된 앱 중 하나일 경우 해당 앱에 대해 모든 도메인 힌트가 계속 수락됩니다.

## <a name="configuring-policy-through-graph-explorer"></a>Graph 탐색기를 통해 정책 구성

평소와 같이 Microsoft Graph를 사용하여 [HRD 정책](/graph/api/resources/homeRealmDiscoveryPolicy)을 설정합니다.  

1. [Graph 탐색기](https://developer.microsoft.com/graph/graph-explorer)에서 Policy.ReadWrite.ApplicationConfiguration 권한을 부여합니다.  
1. URL `https://graph.microsoft.com/v1.0/policies/homeRealmDiscoveryPolicies`를 사용합니다.
1. 이 URL에 새 정책을 게시하거나 기존 정책을 덮어쓰는 경우 `/policies/homerealmdiscoveryPolicies/{policyID}`에 패치합니다.

게시 또는 패치 내용:

```json
{
    "displayName":"Home Realm Discovery Domain Hint Exclusion Policy",
    "definition":[
        "{\"HomeRealmDiscoveryPolicy\" : {\"DomainHintPolicy\": { \"IgnoreDomainHintForDomains\": [ \"Contoso.com\" ], \"RespectDomainHintForDomains\": [], \"IgnoreDomainHintForApps\": [\"sample-guid-483c-9dea-7de4b5d0a54a\"], \"RespectDomainHintForApps\": [] } } }"
    ],
    "isOrganizationDefault":true
}
```

그래프를 사용하는 경우 슬래시를 사용하여 `Definition` JSON 섹션을 이스케이프해야 합니다.  

`isOrganizationDefault`는 true여야 하지만 displayName 및 정의는 변경할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [암호 없는 보안 키 로그인 사용](../authentication/howto-authentication-passwordless-security-key.md)
* [Microsoft Authenticator 앱에서 암호 없는 로그인을 사용하도록 설정](../authentication/howto-authentication-passwordless-phone.md)
