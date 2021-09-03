---
title: Azure Web Application Firewall을 사용하여 Azure Active Directory B2C를 구성하는 자습서
titleSuffix: Azure AD B2C
description: 악의적인 공격으로부터 애플리케이션을 보호하기 위해 Azure Web Application Firewall을 사용하여 Azure Active Directory B2C를 구성하는 자습서
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/17/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 03c66f9610ab8dc309098e1eee695ded477938bc
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768448"
---
# <a name="tutorial-configure-azure-web-application-firewall-with-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C를 사용하여 Azure Web Application Firewall 구성

이 샘플 자습서에서는 사용자 지정 도메인을 사용하는 Azure AD(Active Directory) B2C 테넌트에서 [Azure WAF(Web Application Firewall)](https://azure.microsoft.com/services/web-application-firewall/#overview) 솔루션을 사용하도록 설정하는 방법을 알아봅니다. Azure WAF는 일반적인 악용과 취약성으로부터 웹 애플리케이션을 중앙 집중식으로 보호합니다.

>[!NOTE]
>이 기능은 공개 미리 보기 상태입니다.

## <a name="prerequisites"></a>필수 조건

시작하려면 다음이 필요합니다.

- Azure 구독 - 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/free/)을 이용할 수 있습니다.

- [Azure AD B2C 테넌트](tutorial-create-tenant.md) - 테넌트에 정의된 사용자 지정 정책을 사용하는 사용자의 자격 증명을 확인하는 권한 부여 서버입니다.  ID 공급자라고도 합니다.

- [AFD(Azure Front Door)](https://docs.microsoft.com/azure/frontdoor/) – Azure AD B2C 테넌트에 대한 사용자 지정 도메인 사용을 설정합니다.  

- [Azure WAF](https://azure.microsoft.com/services/web-application-firewall/#overview) - 권한 부여 서버로 전송되는 모든 트래픽을 관리합니다.

## <a name="azure-ad-b2c-setup"></a>Azure AD B2C 설정

Azure AD B2C에서 사용자 지정 도메인을 사용하려면 AFD에서 제공하는 사용자 지정 도메인 기능을 사용해야 합니다. [Azure AD B2C 사용자 지정 도메인 사용](https://docs.microsoft.com/azure/active-directory-b2c/custom-domain?pivots=b2c-user-flow) 방법을 알아봅니다.  

AFD를 사용하여 Azure AD B2C에 대한 사용자 지정 도메인을 구성한 후에는 계속 진행하기 전에 [사용자 지정 도메인을 테스트](https://docs.microsoft.com/azure/active-directory-b2c/custom-domain?pivots=b2c-custom-policy#test-your-custom-domain)합니다.  

## <a name="onboard-with-azure-waf"></a>Azure WAF로 온보딩

Azure WAF를 사용하도록 설정하려면 WAF 정책을 구성하고 보호를 위해 해당 정책을 AFD에 연결합니다.

### <a name="create-a-waf-policy"></a>WAF 정책 만들기

[Azure Portal](https://portal.azure.com)에서 관리형 DRS(기본 규칙 집합)로 기본 WAF 정책을 만듭니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다. **리소스 만들기** 를 선택한 다음 Azure WAF를 검색합니다. **Azure WAF(Web Application Firewall)**  > **만들기** 를 선택합니다.

2. **WAF 정책 만들기** 페이지로 이동하여 **기본** 탭을 선택합니다. 다음 정보를 입력하고 나머지 설정은 기본값을 적용합니다.

| 값 | Description |
|:--------|:-------|
| 정책 | 글로벌 WAF(Front Door)|
| Front Door SKU | 기본, 표준 또는 프리미엄 SKU 중에서 선택합니다. |
|구독 | Front Door 구독 이름을 선택합니다. |
| Resource group | Front Door 리소스 그룹 이름을 선택합니다. |
| 정책 이름 | WAF 정책의 고유한 이름을 입력합니다. |
| 정책 상태 | 사용으로 설정합니다. |
| 정책 모드 | 검색으로 설정 |

3. **검토 + 만들기** 를 선택합니다.

4. WAF 정책 만들기 페이지의 **연결** 탭으로 이동하여 + **Front Door 프로필 연결** 을 선택하고, 다음 설정을 입력합니다.

| 값 | Description |
|:----|:------|
| Front Door | Azure AD B2C 사용자 지정 도메인과 연결된 Front Door 이름을 선택합니다. |
| 도메인 | WAF 정책을 연결할 Azure AD B2C 사용자 지정 도메인을 선택합니다.|

5. **추가** 를 선택합니다.

6. **검토 + 생성** 를 선택한 다음, **생성** 를 선택합니다.

### <a name="change-policy-mode-from-detection-to-prevention"></a>정책 모드를 검색에서 방지로 변경

WAF 정책을 만드는 경우 기본적으로 정책은 검색 모드입니다. 검색 모드에서 WAF는 아무 요청도 차단하지 않으며, WAF 규칙과 일치하는 요청은 WAF 로그에 기록됩니다. WAF 로깅에 대한 자세한 내용은 [Azure WAF 모니터링 및 로깅](https://docs.microsoft.com/azure/web-application-firewall/afds/waf-front-door-monitor)을 참조하세요.

샘플 쿼리는 지난 24시간 동안 WAF 정책에 의해 차단된 모든 요청을 보여 줍니다. 세부 정보에는 규칙 이름, 요청 데이터, 정책에서 수행한 작업 및 정책 모드가 포함됩니다.

![차단된 요청을 보여 주는 이미지](./media/partner-azure-web-application-firewall/blocked-requests-query.png)

![차단된 요청 세부 정보를 보여 주는 이미지](./media/partner-azure-web-application-firewall/blocked-requests-details.png)

검색 모드에서 WAF 캡처 요청을 허용하는 것이 좋습니다. WAF 로그를 검토하여 가양성 결과를 유발하는 규칙이 정책에 있는지 확인합니다. 그런 다음 [WAF 로그를 기반으로 WAF 규칙을 제외](https://docs.microsoft.com/azure/web-application-firewall/afds/waf-front-door-exclusion#define-exclusion-based-on-web-application-firewall-logs)합니다.

작동 중인 WAF를 보려면 방지 모드로 전환을 사용하여 검색 모드에서 방지 모드로 변경합니다. DRS(기본 규칙 집합)에 정의된 규칙과 일치하는 모든 요청이 차단되고 WAF 로그에 기록됩니다.

![방지 모드로의 전환을 보여 주는 이미지](./media/partner-azure-web-application-firewall/switch-to-prevention-mode.png)

검색 모드로 다시 전환하려면 검색 모드로 전환 옵션을 사용하면 됩니다.

![검색 모드로의 전환을 보여 주는 이미지](./media/partner-azure-web-application-firewall/switch-to-detection-mode.png)

## <a name="next-steps"></a>다음 단계

- [Azure WAF 모니터링 및 로깅](https://docs.microsoft.com/azure/web-application-firewall/afds/waf-front-door-monitor/)

- [Front Door 서비스 제외 목록이 포함된 WAF](https://docs.microsoft.com/azure/web-application-firewall/afds/waf-front-door-exclusion/)
