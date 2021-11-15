---
title: 동의 피싱으로부터 보호
titleSuffix: Azure AD
description: Azure AD를 사용하여 앱 기반 동의 피싱 공격을 완화하는 방법을 알아봅니다.
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 08/09/2021
ms.custom: template-concept
ms.author: cchiedo
ms.reviewer: tilarso
ms.openlocfilehash: dc860dce0f75648488ff692a0024a850bf4c76ad
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129615698"
---
# <a name="protecting-against-consent-phishing-in-azure-active-directory"></a>Azure Active Directory의 동의 피싱으로부터 보호

생산성은 더 이상 프라이빗 네트워크에 국한되지 않으며 작업은 클라우드 서비스로 극적으로 이동했습니다. 클라우드 애플리케이션을 통해 직원은 원격으로 생산성을 높일 수 있지만 공격자는 애플리케이션 기반 공격을 사용하여 귀중한 조직 데이터에 액세스할 수도 있습니다. 귀하는 이메일 피싱 또는 자격 증명 손상과 같이 사용자에 초점을 맞춘 공격에 익숙할 것입니다. ***동의 피싱*** 은 주의해야 할 또 다른 위협 벡터입니다.
이 문서에서는 동의 피싱이 무엇인지, Microsoft가 사용자를 보호하기 위해 수행하는 작업 및 조직이 안전을 유지하기 위해 취할 수 있는 조치를 살펴봅니다.

## <a name="what-is-consent-phishing"></a>동의 피싱이란 무엇인가요?

동의 피싱 공격은 사용자를 속여 악성 클라우드 앱에 권한을 부여합니다. 그러면 이러한 악성 앱이 사용자에게 합법적인 클라우드 서비스 및 데이터에 액세스할 수 있습니다. 자격 증명 도용과 달리 동의 피싱을 수행하는 *위협 행위자* 는 개인 또는 조직 데이터에 대한 액세스 권한을 직접 부여할 수 있는 사용자를 대상으로 합니다. 동의 화면에는 앱에 주어지는 모든 권한이 표시됩니다. 애플리케이션이 합법적인 공급자(예: Microsoft ID 플랫폼)에서 호스팅되기 때문에 의심하지 않는 사용자가 약관에 동의하거나 ‘*동의*’를 누르면 악성 애플리케이션이 사용자 또는 조직의 데이터에 대해 요청된 권한을 부여합니다.

:::image type="content" source="./media/protect-consent-phishing/permissions-requested.png" alt-text="사용자 동의가 필요한 권한 요청 창을 보여 주는 스크린샷":::

*다양한 권한에 대한 액세스를 요청하는 OAuth 앱의 예*

## <a name="mitigating-consent-phishing-attacks-using-azure-ad"></a>Azure AD를 사용하여 동의 피싱 공격 완화

관리자, 사용자 또는 Microsoft 보안 연구원은 의심스럽게 작동하는 것으로 보이는 OAuth 애플리케이션에 플래그를 지정할 수 있습니다. 플래그가 지정된 애플리케이션은 Microsoft에서 검토하여 앱이 서비스 약관을 위반하는지 여부를 결정합니다. 위반이 확인되면 Azure AD는 애플리케이션을 사용하지 않도록 설정하고 모든 Microsoft 서비스에서 추가 사용을 방지합니다.

Azure AD가 OAuth 애플리케이션을 사용하지 않도록 설정하면 몇 가지 일이 발생합니다.
- 악성 애플리케이션 및 관련 서비스 주체는 완전히 사용하지 않도록 설정된 상태가 됩니다. 새로운 토큰 요청 또는 새로 고침 토큰에 대한 요청은 거부되지만 기존 액세스 토큰은 만료될 때까지 계속 유효합니다.
- Microsoft Graph의 관련 [애플리케이션](/graph/api/resources/application?view=graph-rest-1.0&preserve-view=true) 및 [서비스 주체](/graph/api/resources/serviceprincipal?view=graph-rest-1.0&preserve-view=true) 리소스 종류에서 *disabledByMicrosoftStatus* 라는 노출된 속성을 통해 사용하지 않도록 설정된 상태를 표시합니다.
- Microsoft에서 사용하지 않도록 설정하기 전에 애플리케이션에 동의한 조직의 사용자가 있을 수 있는 전역 관리자는 수행된 작업과 보안 태세를 조사하고 개선하기 위해 취할 수 있는 권장 단계를 반영하는 이메일을 받아야 합니다.

## <a name="recommended-response-and-remediation"></a>권장되는 대응 및 수정

조직이 Microsoft에서 사용하지 않도록 설정한 애플리케이션의 영향을 받은 경우 환경을 안전하게 유지하기 위해 다음과 같은 즉각적인 단계를 수행하는 것이 좋습니다.

1. 다음을 포함하여 사용하지 않도록 설정된 애플리케이션에 대한 애플리케이션 활동을 조사합니다.
    - 애플리케이션에서 요청한 위임된 권한 또는 애플리케이션 권한
    - 애플리케이션의 활동 및 애플리케이션을 사용할 권한이 있는 사용자의 로그인 활동에 대한 Azure AD 감사 로그
1. 사용하지 않도록 설정된 애플리케이션 또는 검토 중에 발견된 기타 의심스러운 앱에 대한 감사 권한 및 동의를 포함하여 Microsoft 클라우드 제품의 [불법 동의 부여에 대한 방어 지침](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants)을 검토하고 구현합니다.
1. 아래에 설명된 동의 피싱에 대한 강화를 위한 모범 사례를 구현합니다.


## <a name="best-practices-for-hardening-against-consent-phishing-attacks"></a>동의 피싱 공격 방지를 위한 모범 사례

Microsoft에서는 조직 내에서 애플리케이션을 허용하고 사용하는 방법을 제어할 수 있는 올바른 인사이트와 기능을 제공하여 관리자가 제어할 수 있도록 하려고 합니다. 공격자는 결코 쉬지 않지만 조직은 보안 태세를 개선하기 위해 취할 수 있는 조치가 있습니다. 다음은 일부 모범 사례입니다.

* 권한 및 동의 프레임워크의 작동 방식에 대해 조직에게 교육합니다.
    - 애플리케이션이 요청하는 데이터 및 권한을 이해하고 플랫폼 내에서  [권한 및 동의](../develop/v2-permissions-and-consent.md)가 작동하는 방식을 이해합니다.
    - 관리자가  [동의 요청을 관리하고 평가](./manage-consent-requests.md)하는 방법을 알고 있는지 확인합니다.
    - 조직에서 정기적으로 [앱 및 동의한 권한을 감사](../../security/fundamentals/steps-secure-identity.md#audit-apps-and-consented-permissions) 하여 사용 중인 애플리케이션이 필요한 데이터에만 액세스하고 최소한의 권한 원칙을 준수하는지 확인합니다.
* 일반적인 동의 피싱 전술을 식별하고 차단하는 방법을 알아보기
    - 잘못된 맞춤법과 문법이 있는지 확인합니다. 이메일 메시지 또는 애플리케이션의 동의 화면에 맞춤법 및 문법 오류가 있는 경우 의심스러운 애플리케이션일 수 있습니다. 이 경우 “*여기에 보고*” 링크를 사용하여 [동의 확인 프롬프트](../develop/application-consent-experience.md#building-blocks-of-the-consent-prompt)에서 직접 보고할 수 있습니다. 그러면 Microsoft에서 악성 애플리케이션인지 조사하며 확인 후 이를 사용하지 않도록 설정합니다.
    - 앱 이름과 도메인 URL을 인증 원본으로 사용하지 마세요. 공격자는 악성 앱에 대한 동의를 유도하기 위해 합법적인 서비스나 회사에서 제공한 것처럼 보이게 하는 앱 이름과 도메인을 스푸핑하는 것을 좋아합니다. 대신 도메인 URL의 원본을 확인하고 가능한 경우 [확인된 게시자](../develop/publisher-verification-overview.md)의 애플리케이션을 사용합니다.
    - 공격자가 조직의 알려진 사용자를 사칭하는 피싱 캠페인으로부터 보호하여 [Office 365용 Microsoft Defender로 피싱 동의 이메일](/microsoft-365/security/office-365-security/set-up-anti-phishing-policies#impersonation-settings-in-anti-phishing-policies-in-microsoft-defender-for-office-365)을 차단합니다.
    -  [활동 정책](/cloud-app-security/user-activity-policies),  [변칙 검색](/cloud-app-security/anomaly-detection-policy),  [OAuth 앱 정책](/cloud-app-security/app-permission-policy) 과 같은 Microsoft 클라우드 앱 보안 정책을 구성하여 비정상적인 애플리케이션 활동을 관리하고 조직에 조치를 취합니다.
    - [Microsoft 365 Defender를 사용한 고급 헌팅](/microsoft-365/security/defender/advanced-hunting-overview)에 대한 지침에 따라 동의 피싱 공격을 조사하고 찾아냅니다.
* 신뢰하는 앱에 대한 액세스를 허용하고 신뢰하지 않는 앱으로부터 보호
    - 게시자가 인증한 애플리케이션을 사용합니다. [게시자 확인](../develop/publisher-verification-overview.md)은 관리자와 최종 사용자가 Microsoft에서 지원하는 심사 프로세스를 통해 애플리케이션 개발자의 신뢰성을 이해하는 데 도움이 됩니다.
    - [사용자 동의 설정을 구성](./configure-user-consent.md?tabs=azure-portal)하여 조직에서 개발한 애플리케이션이나 확인된 게시자가 제공하는 애플리케이션과 같이 신뢰할 수 있는 특정 애플리케이션에만 사용자가 동의할 수 있도록 합니다.
    - 사전 예방적 [앱 거버넌스](/microsoft-365/compliance/app-governance-manage-app-governance) 정책을 만들어 Microsoft 365 플랫폼에서 타사 앱 동작을 모니터링하여 일반적인 의심스러운 앱 동작을 해결합니다.

## <a name="next-steps"></a>다음 단계

* [앱 동의 부여 조사](/security/compass/incident-response-playbook-app-consent)
* [앱에 대한 액세스 관리](./what-is-access-management.md)
* [Azure AD에서 사용자 동의 작업 제한](../../security/fundamentals/steps-secure-identity.md#restrict-user-consent-operations)