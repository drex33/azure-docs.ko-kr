---
title: Azure Active Directory 애플리케이션 프록시의 와일드카드 애플리케이션
description: Azure Active Directory 애플리케이션 프록시에서 와일드카드 애플리케이션을 사용하는 방법을 알아봅니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: d84ca4e53ada2310097e0c7af5314bdf05fd2613
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129988564"
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Azure Active Directory 애플리케이션 프록시의 와일드카드 애플리케이션

Azure AD(Azure Active Directory)에서 다수의 온-프레미스 애플리케이션 구성은 빠르게 관리가 불가능해질 수 있으며, 많은 애플리케이션에 동일한 설정이 필요한 경우 불필요하게 구성 오류 위험이 발생합니다. [Azure AD 애플리케이션 프록시](application-proxy.md)를 사용하면 와일드카드 애플리케이션 게시를 통해 여러 애플리케이션을 한 번에 게시 및 관리하여 이 문제를 해결할 수 있습니다. 이 솔루션은 다음과 같은 장점이 있습니다.

- 관리 오버헤드를 간소화합니다.
- 잠재적 구성 오류 수를 줄입니다.
- 사용자가 더 많은 리소스에 안전하게 액세스할 수 있도록 합니다.

이 문서에서는 사용자 환경에 와일드카드 애플리케이션 게시를 구성하는 데 필요한 정보를 제공합니다.

## <a name="create-a-wildcard-application"></a>와일드카드 애플리케이션 만들기

동일한 구성을 가진 애플리케이션 그룹이 있는 경우 와일드카드(*) 애플리케이션을 만들 수 있습니다. 다음 설정을 공유하는 애플리케이션은 와일드카드 애플리케이션이 될 수 있습니다.

- 액세스 권한이 있는 사용자 그룹
- SSO 메서드
- 액세스 프로토콜(http, https)

내부 및 외부 URL이 둘 다 다음 형식인 경우에는 와일드카드를 사용하여 애플리케이션을 게시할 수 있습니다.

> http(s)://*.\<domain\>

예: `http(s)://*.adventure-works.com`.

내부 및 외부 URL이 서로 다른 도메인을 사용할 수도 있지만 동일한 것이 좋습니다. 애플리케이션을 게시할 때 URL 중 하나에 와일드카드가 없으면 오류가 표시됩니다.

와일드카드 애플리케이션 생성은 다른 모든 애플리케이션에 사용할 수 있는 것과 동일한 [애플리케이션 게시 흐름](application-proxy-add-on-premises-application.md)을 기반으로 합니다. 유일한 차이점은 URL에 와일드카드를 포함하고 잠재적으로 SSO 구성이라는 것뿐입니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 해당 요구 사항을 충족했는지 확인합니다.

### <a name="custom-domains"></a>사용자 지정 도메인

[사용자 지정 도메인](./application-proxy-configure-custom-domain.md)은 다른 모든 애플리케이션에서 선택 사항이지만 와일드카드 애플리케이션의 경우 필수 조건입니다. 사용자 지정 도메인을 만들려면 다음을 수행해야 합니다.

1. Azure 내에서 확인된 도메인을 만듭니다.
1. PFX 형식의 TLS/SSL 인증서를 애플리케이션 프록시에 업로드합니다.

만들려는 애플리케이션에 대응할 와일드카드 인증서를 사용하는 것이 좋습니다. 

보안상의 이유로 하드 요구 사항이며, 외부 URL에 사용자 지정 도메인을 사용할 수 없는 애플리케이션에 대해서는 와일드카드를 지원하지 않습니다.

### <a name="dns-updates"></a>DNS 업데이트

사용자 지정 도메인을 사용하는 경우 애플리케이션 프록시 엔드포인트의 외부 URL을 가리키는 외부 URL(예: `*.adventure-works.com`)에 대한 CNAME 레코드를 사용하여 DNS 항목을 만들어야 합니다. 와일드카드 애플리케이션의 경우 CNAME 레코드는 관련 외부 URL을 가리켜야 합니다.

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

CNAME을 제대로 구성했는지 확인하려면 대상 엔드포인트 중 하나(예: `expenses.adventure-works.com`)에 대해 [nslookup](/windows-server/administration/windows-commands/nslookup)을 사용할 수 있습니다.  응답에 이미 언급된 별칭(`<yourAADTenantId>.tenant.runtime.msappproxy.net`)이 포함되어야 합니다.

### <a name="using-connector-groups-assigned-to-an-app-proxy-cloud-service-region-other-than-the-default-region"></a>기본 지역이 아닌 앱 프록시 클라우드 서비스 지역에 할당된 커넥터 그룹 사용
기본 테넌트 지역과 다른 지역에 커넥터를 설치한 경우 커넥터 그룹이 최적화된 지역으로 변경하여 이러한 애플리케이션에 액세스하는 성능을 향상하는 것이 좋습니다. 자세한 정보는 [커넥터 그룹을 최적화하여 가장 가까운 애플리케이션 프록시 클라우드 서비스 사용](application-proxy-network-topology.md#optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview)을 참조하세요.
 
와일드카드 애플리케이션에 할당된 커넥터 그룹에서 **기본 지역과 다른 지역** 을 사용하는 경우에는 지역 특정 외부 URL을 가리키도록 CNAME 레코드를 업데이트해야 합니다. 다음 표를 사용하여 관련 URL을 결정합니다.

| 커넥터 할당 지역 | 외부 URL |
| ---   | ---         |
| 아시아 | `<yourAADTenantId>.asia.tenant.runtime.msappproxy.net`|
| 오스트레일리아  | `<yourAADTenantId>.aus.tenant.runtime.msappproxy.net` |
| 유럽  | `<yourAADTenantId>.eur.tenant.runtime.msappproxy.net`|
| 북아메리카  | `<yourAADTenantId>.nam.tenant.runtime.msappproxy.net` |

## <a name="considerations"></a>고려 사항

와일드카드 애플리케이션에 대한 몇 가지 고려 사항이 있습니다.

### <a name="accepted-formats"></a>허용되는 형식

와일드카드 애플리케이션의 경우 **내부 URL** 은 `http(s)://*.<domain>` 형식이어야 합니다.

![내부 URL의 경우 http(s)://*.\<domain> 형식을 사용합니다.](./media/application-proxy-wildcard/22.png)

**외부 URL** 을 구성하는 경우 `https://*.<custom domain>` 형식을 사용해야 합니다.

![외부 URL의 경우 https://*.\<custom domain> 형식을 사용합니다.](./media/application-proxy-wildcard/21.png)

다른 와일드카드 위치, 다중 와일드카드 또는 기타 정규식 문자열은 지원되지 않으며 오류가 발생합니다.

### <a name="excluding-applications-from-the-wildcard"></a>와일드카드에서 애플리케이션 제외

다음을 수행하면 와일드카드 애플리케이션에서 애플리케이션을 제외할 수 있습니다.

- 예외 애플리케이션을 일반 애플리케이션으로 게시
- DNS 설정을 통해 특정 애플리케이션에 대해서만 와일드카드 사용

애플리케이션을 일반 애플리케이션으로 게시하여 와일드카드에서 애플리케이션을 제외하는 것이 더 좋습니다. 제외된 애플리케이션을 와일드카드 애플리케이션보다 먼저 게시하여 처음부터 예외가 적용되도록 해야 합니다. 가장 구체적인 애플리케이션이 항상 우선합니다. `budgets.finance.adventure-works.com`으로 게시된 애플리케이션이 `*.finance.adventure-works.com` 애플리케이션보다 우선하고, 이 애플리케이션이 `*.adventure-works.com` 애플리케이션보다 우선합니다.

DNS 관리를 통해 와일드카드를 특정 애플리케이션에 대해서만 작동하도록 제한할 수도 있습니다. 와일드카드를 포함하고 구성한 외부 URL의 형식과 일치하는 CNAME 항목을 만드는 것이 좋습니다. 그러나 특정 애플리케이션 URL이 대신 와일드카드를 가리키도록 설정할 수 있습니다. 예를 들어 `*.adventure-works.com` 대신 `hr.adventure-works.com`, `expenses.adventure-works.com` 및 `travel.adventure-works.com individually`가 `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`을 가리키도록 설정합니다.

이 옵션을 사용하는 경우 동일한 위치를 가리키는 `AppId.domain` 값의 다른 CNAME 항목(예: `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com`)도 필요합니다. 와일드카드 애플리케이션의 애플리케이션 속성 페이지에서 **AppId** 를 찾을 수 있습니다.

![앱의 속성 페이지에서 애플리케이션 ID 찾기](./media/application-proxy-wildcard/01.png)

### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>MyApps 패널의 홈페이지 URL 설정

와일드카드 애플리케이션은 [MyApps](https://myapps.microsoft.com) 패널에서 하나의 타일로 표시됩니다. 기본적으로 이 타일은 숨겨집니다. 타일을 표시하고 사용자가 특정 페이지를 방문하게 하려면

1. [홈페이지 URL 설정](application-proxy-configure-custom-home-page.md) 지침을 따릅니다.
1. 애플리케이션 속성 페이지에서 **애플리케이션 표시** 를 **true** 로 설정합니다.

### <a name="kerberos-constrained-delegation"></a>Kerberos 제한 위임

[SSO 메서드로 KCD(Kerberos 제한된 위임)](./application-proxy-configure-single-sign-on-with-kcd.md)을 사용하는 애플리케이션의 경우 SSO 메서드에 대해 나열된 SPN에 와일드카드가 필요할 수도 있습니다. 예를 들어 SPN이 `HTTP/*.adventure-works.com`일 수 있습니다. 이 경우에도 백 엔드 서버에 개별 SPN(예: `HTTP/expenses.adventure-works.com and HTTP/travel.adventure-works.com`)을 구성해야 합니다.

## <a name="scenario-1-general-wildcard-application"></a>시나리오 1: 일반 와일드카드 애플리케이션

이 시나리오에서는 세 가지 애플리케이션을 게시하려고 합니다.

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

세 애플리케이션은 모두 다음과 같은 특성을 갖습니다.

- 모든 사용자가 사용함
- *Windows 통합 인증* 사용
- 동일한 속성이 있음

[Azure AD 애플리케이션 프록시를 사용하여 애플리케이션 게시](application-proxy-add-on-premises-application.md)에 요약된 단계를 사용하여 와일드카드 애플리케이션을 게시할 수 있습니다. 이 시나리오에서는 다음과 같이 가정합니다.

- ID가 `000aa000-11b1-2ccc-d333-4444eee4444e`인 테넌트가 있습니다.
- `adventure-works.com`이라는 확인된 도메인이 구성되어 있습니다.
- `*.adventure-works.com`이 `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`을 가리키도록 설정하는 **CNAME** 항목이 만들어져 있습니다.

[문서화된 단계](application-proxy-add-on-premises-application.md)에 따라 테넌트에 새 애플리케이션 프록시 애플리케이션을 만듭니다. 이 예제에서 와일드카드는 다음 필드에 있습니다.

- 내부 URL:

    ![예: 내부 URL의 와일드카드](./media/application-proxy-wildcard/42.png)

- 외부 URL:

    ![예: 외부 URL의 와일드카드](./media/application-proxy-wildcard/43.png)

- 내부 애플리케이션 SPN:

    ![예: SPN 구성의 와일드카드](./media/application-proxy-wildcard/44.png)

와일드카드 애플리케이션을 게시하면 이제 친숙한 URL(예: `travel.adventure-works.com`)로 이동하여 세 애플리케이션에 액세스할 수 있습니다.

구성은 다음 구조를 구현합니다.

![예제 구성에 의해 구현된 구조를 보여 줍니다.](./media/application-proxy-wildcard/05.png)

| 색 | Description |
| ---   | ---         |
| 파랑  | 애플리케이션이 명시적으로 게시되었으며 Azure Portal에 표시됩니다. |
| 회색  | 부모 애플리케이션을 통해 액세스할 수 있는 애플리케이션입니다. |

## <a name="scenario-2-general-wildcard-application-with-exception"></a>시나리오 2: 예외가 있는 일반 와일드카드 애플리케이션

이 시나리오에서는 세 가지 일반 애플리케이션 외에도 재무 부서에서만 액세스할 수 있는 다른 애플리케이션 `finance.adventure-works.com`이 있습니다. 현재 애플리케이션 구조에서는 와일드카드 애플리케이션을 통해 모든 직원이 재무 애플리케이션에 액세스할 수 있습니다. 이를 변경하려면 더 제한적인 권한을 가진 별도의 애플리케이션으로 재무를 구성하여 와일드카드에서 애플리케이션을 제외합니다.

`finance.adventure-works.com`이 애플리케이션의 애플리케이션 프록시 페이지에 지정된 애플리케이션 특정 엔드포인트를 가리키도록 지정하는 CNAME 레코드가 있는지 확인해야 합니다. 이 시나리오에서 `finance.adventure-works.com`은 `https://finance-awcycles.msappproxy.net/`를 가리킵니다.

[문서화된 단계](application-proxy-add-on-premises-application.md)에 따라 이 시나리오에는 다음 설정이 필요합니다.

- **내부 URL** 에 와일드카드 대신 **재무** 를 설정합니다.

    ![예: 내부 URL에 와일드카드 대신 재무를 설정](./media/application-proxy-wildcard/52.png)

- **외부 URL** 에 와일드카드 대신 **재무** 를 설정합니다.

    ![예: 외부 URL에 와일드카드 대신 재무를 설정](./media/application-proxy-wildcard/53.png)

- 내부 애플리케이션 SPN에 와일드카드 대신 **재무** 를 설정합니다.

    ![예: SPN 구성에서 와일드카드 대신 재무를 설정](./media/application-proxy-wildcard/54.png)

이 구성은 다음 시나리오를 구현합니다.

![샘플 시나리오에서 구현된 구성을 보여 줍니다.](./media/application-proxy-wildcard/09.png)

`finance.adventure-works.com`이 `*.adventure-works.com`보다 더 구체적인 URL이므로 우선 적용됩니다. `finance.adventure-works.com`으로 이동하는 사용자는 Finance Resources 애플리케이션에 지정된 환경을 사용합니다. 이 경우 재무 직원만 `finance.adventure-works.com`에 액세스할 수 있습니다.

재무용으로 여러 애플리케이션이 게시되어 있고 확인된 도메인으로 `finance.adventure-works.com`이 있는 경우 다른 와일드카드 애플리케이션 `*.finance.adventure-works.com`을 게시할 수 있습니다. 이 애플리케이션이 일반 `*.adventure-works.com`보다 더 구체적이므로 사용자가 재무 도메인의 애플리케이션에 액세스하는 경우 우선 적용됩니다.

## <a name="next-steps"></a>다음 단계

- **사용자 지정 도메인** 에 대한 자세한 내용은 [Azure AD 애플리케이션 프록시에서 사용자 지정 도메인 작업](./application-proxy-configure-custom-domain.md)을 참조하세요.
- **애플리케이션 게시** 에 대한 자세한 내용은 [Azure AD 애플리케이션 프록시를 사용하여 애플리케이션 게시](application-proxy-add-on-premises-application.md)를 참조하세요.