---
title: Akamai 웹 애플리케이션 방화벽을 사용하여 Azure Active Directory B2C를 구성하는 자습서
titleSuffix: Azure AD B2C
description: Azure AD B2C를 사용하여 Akamai 웹 애플리케이션 방화벽을 구성하는 자습서
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/15/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 09f35e9621f6704fb33720a43afb38fd99e9eec6
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123213914"
---
# <a name="tutorial-configure-akamai-with-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C를 사용하여 Akamai 구성

이 샘플 자습서에서는 사용자 지정 도메인을 사용하는 Azure AD(Active Directory) B2C 테넌트를 위해 [Akamai WAF(웹 애플리케이션 방화벽)](https://www.akamai.com/us/en/resources/web-application-firewall.jsp) 솔루션을 사용하도록 설정하는 방법을 알아봅니다. Akamai WAF를 통해 조직은 SQL 삽입 및 교차 사이트 스크립팅과 같은 취약성을 악용하려는 악의적인 공격으로부터 웹 애플리케이션을 보호할 수 있습니다.

>[!NOTE]
>이 기능은 공개 미리 보기 상태입니다.

Akamai WAF 솔루션 사용의 이점:

- 서비스에 대한 트래픽 관리를 허용하는 에지 플랫폼입니다.

- Azure AD B2C 테넌트 앞에 구성할 수 있습니다.

- ID 인프라를 보호하기 위한 세분화된 트래픽 조작을 허용합니다.

이 샘플 자습서는 Akamai에서 제공하는 [WAP(웹 애플리케이션 보호기)](https://www.akamai.com/us/en/products/security/web-application-protector-enterprise-waf-firewall-ddos-protection.jsp) 및 [KSD(Kona Site Defender)](https://www.akamai.com/us/en/products/security/kona-site-defender.jsp) WAF 솔루션 모두에 적용됩니다.

## <a name="prerequisites"></a>필수 조건

시작하려면 다음이 필요합니다.

- Azure 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

- [Azure AD B2C 테넌트](tutorial-create-tenant.md)를 Azure 구독에 연결

- [Akamai WAF](https://www.akamai.com/us/en/akamai-free-trials.jsp) 계정
 
## <a name="scenario-description"></a>시나리오 설명

Akamai WAF 통합에는 다음 구성 요소가 포함됩니다.

- **Azure AD B2C 테넌트** - 테넌트에 정의된 사용자 지정 정책을 사용하는 사용자의 자격 증명을 확인하는 권한 부여 서버입니다.  ID 공급자라고도 합니다.

- [**Azure Front Door**](../frontdoor/front-door-overview.md) – Azure B2C 테넌트에 대한 사용자 지정 도메인 사용을 설정합니다. Cloudflare WAF의 모든 트래픽은 Azure AD B2C 테넌트에 도착하기 전에 Azure Front Door로 라우팅됩니다.

- [**Akamai WAF**](https://www.akamai.com/us/en/resources/waf.jsp) – 권한 부여 서버로 전송되는 모든 트래픽을 관리하는 웹 애플리케이션 방화벽입니다.

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C와 통합

1. Azure AD B2C에서 사용자 지정 도메인을 사용하려면 Azure Front Door에서 제공하는 사용자 지정 도메인 기능을 사용해야 합니다. [Azure AD B2C 사용자 지정 도메인 사용](./custom-domain.md?pivots=b2c-user-flow) 방법을 알아봅니다.  

2. Azure Front Door를 사용하여 Azure AD B2C에 대한 사용자 지정 도메인을 구성한 후에는 계속 진행하기 전에 [사용자 지정 도메인을 테스트](./custom-domain.md?pivots=b2c-custom-policy#test-your-custom-domain)합니다.  

## <a name="onboard-with-akamai"></a>Akamai에 온보딩

Akamai 계정을 [등록](https://www.akamai.com)하고 만듭니다.

### <a name="create-and-configure-property"></a>속성 만들기 및 구성 

1. [새 속성을 만듭니다](https://control.akamai.com/wh/CUSTOMER/AKAMAI/en-US/WEBHELP/property-manager/property-manager-help/GUID-14BB87F2-282F-4C4A-8043-B422344884E6.html).

2. 속성 설정을 다음과 같이 구성합니다.  

| 속성 | 값 |
|:---------------|:---------------|
|속성 버전 | 표준 또는 고급 TLS(기본 설정)를 선택합니다. |
|속성 호스트 이름 | 속성 호스트 이름을 추가합니다. 사용자 지정 도메인의 이름(예: login.domain.com)입니다. <BR> 사용자 지정 도메인 이름에 대한 적절한 설정을 사용하여 인증서를 만들거나 수정합니다. 자세한 내용은 [this](https://learn.akamai.com/en-us/webhelp/property-manager/https-delivery-with-property-manager/GUID-9EE0EB6A-E62B-4F5F-9340-60CBD093A429.html)를 참조하세요. |

3. 원본 서버 속성 구성 설정을 다음과 같이 설정합니다.

|속성| 값 |
|:-----------|:-----------|
| 원본 형식 | 원본 |
| 원본 서버 호스트 이름 | yourafddomain.azurefd.net |
| 호스트 헤더 전달 | 들어오는 호스트 헤더 |
| 캐시 키 호스트 이름| 들어오는 호스트 헤더  |

### <a name="configure-dns"></a>DNS 구성

속성 호스트 이름 필드의 Edge 호스트 이름을 가리키는 login.domain.com 같은 CNAME 레코드를 DNS에 만듭니다.

### <a name="configure-akamai-waf"></a>Akamai WAF 구성

1. [Akamai WAF를 구성](https://learn.akamai.com/en-us/webhelp/kona-site-defender/kona-site-defender-quick-start/GUID-6294B96C-AE8B-4D99-8F43-11B886E6C39A.html#GUID-6294B96C-AE8B-4D99-8F43-11B886E6C39A)합니다.

2. **공격 그룹** 에 나열된 모든 항목에 대한 **규칙 동작** 이 **거부** 로 설정되어 있는지 확인합니다.

![거부로 설정된 규칙 동작을 보여 주는 이미지](./media/partner-akamai/rule-action-deny.png)

[컨트롤의 작동 방식 및 구성 옵션](https://control.akamai.com/dl/security/GUID-81C0214B-602A-4663-839D-68BCBFF41292.html)에 대해 자세히 알아보세요.

### <a name="test-the-settings"></a>설정 테스트

Azure AD B2C에 대한 모든 트래픽이 사용자 지정 도메인을 통과하는지 확인하려면 다음 사항을 확인합니다.

- Azure AD B2C 사용자 지정 도메인으로 들어오는 모든 요청이 Akamai WAF를 통해 라우팅되고 유효한 TLS 연결을 사용하는지 확인합니다.
- 사용자 지정 도메인에 대해 Azure AD B2C에서 모든 쿠키를 올바르게 설정했는지 확인합니다.
- Security Center 콘솔에서 사용할 수 있는 Akamai WAF 대시보드에 모든 공격 트래픽과 함께 WAF를 통과하는 모든 트래픽에 대한 차트가 표시됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD B2C에서 사용자 지정 도메인 구성](./custom-domain.md?pivots=b2c-user-flow)

- [Azure AD B2C의 사용자 지정 정책 시작하기](./tutorial-create-user-flows.md?pivots=b2c-custom-policy&tabs=applications)
