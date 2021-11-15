---
title: Single Sign-On 배포 계획
description: Azure Active Directory에서 Single Sign-On을 배포하도록 계획 합니다.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: d6daa398c560bf7112539291f76fe4188abb76a1
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129231618"
---
# <a name="plan-a-single-sign-on-deployment-in-azure-active-directory"></a>Azure Active Directory에서 Single Sign-On 배포 계획

이 문서에서는 Azure AD(Azure Active Directory)에서 [SSO(Single Sign-On)](what-is-single-sign-on.md)를 배포하도록 계획하는 데 사용할 수 있는 정보를 제공합니다. Azure AD에서 애플리케이션을 사용하여 SSO를 배포하도록 계획하는 경우 다음 사항을 고려해야 합니다.

- 애플리케이션을 관리하는 데 필요한 관리 역할은 무엇인가요?
- 인증서를 갱신해야 하나요?
- SSO 구현과 관련된 변경 내용을 누구에게 알려야 하나요?
- 애플리케이션을 효과적으로 관리하는 데 필요한 라이선스는 무엇인가요?
- 애플리케이션에 액세스하는 데 공유 사용자 계정이 사용되나요?
- SSO 배포 옵션을 이해하고 있나요?

## <a name="administrative-roles"></a>관리 역할

항상 Azure AD 내에서 필요한 작업을 수행하는 데 사용할 수 있는 권한이 가장 적은 역할을 사용합니다. 사용 가능한 다양한 역할을 검토하고, 애플리케이션의 각 가상 사용자에 대한 요구 사항을 해결하는 데 적합한 역할을 선택합니다. 일부 역할은 임시로 적용했다가 배포가 완료된 후 제거해야 할 수도 있습니다.

| 가상 사용자 | 역할 | Azure AD 역할(필요한 경우) |
| ------- | ----- | --------------------------- |
| 지원 센터 관리자 | 계층 1 지원 | 없음 |
| ID 관리자 | Azure AD와 관련된 문제의 경우 구성 및 디버그 | 글로벌 관리자 |
| 애플리케이션 관리자 | 애플리케이션의 사용자 증명, 권한이 있는 사용자의 구성 | 없음 |
| 인프라 관리자 | 인증서 롤오버 소유자 | 글로벌 관리자 |
| 비즈니스 소유자/관련자 | 애플리케이션의 사용자 증명, 권한이 있는 사용자의 구성 | 없음 |

Azure AD 관리 역할에 대한 자세한 내용은 [Azure AD 기본 제공 역할](../users-groups-roles/directory-assign-admin-roles.md)을 참조하세요.

## <a name="certificates"></a>인증서

페더레이션된 SSO를 애플리케이션에 사용하도록 설정하는 경우 Azure AD는 기본적으로 3년 동안 유효한 인증서를 만듭니다. 필요한 경우 해당 인증서의 만료 날짜를 사용자 지정할 수 있습니다. 만료되기 전에 인증서 갱신을 위한 프로세스가 준비되었는지 확인해야 합니다. 

해당 인증서 기간은 Azure Portal에서 변경합니다. 만료를 문서화하고 인증서 갱신을 관리하는 방법을 알고 있어야 합니다. 서명 인증서의 수명 주기를 관리하는 것과 관련된 올바른 역할과 이메일 배포 목록을 식별해야 합니다. 권장되는 역할은 다음과 같습니다.

- 애플리케이션에서 사용자 속성을 업데이트하는 소유자
- 애플리케이션 문제 해결 지원을 대기 중인 소유자
- 인증서 관련 변경 알림을 위해 면밀히 모니터링되는 이메일 배포 목록

인증서를 관리하는 데 사용할 수 있는 방법은 다음과 같습니다.

- **자동 인증서 롤오버** - 서명 키 롤오버는 Azure AD에서 지원됩니다. 서명 키 롤오버는 증서를 관리하는 데 선호되는 방법이지만 모든 애플리케이션에서 이 시나리오를 지원하는 것은 아닙니다.

- **수동 업데이트** - 모든 애플리케이션에는 정의된 방식에 따라 만료되는 자체 인증서가 있습니다. 애플리케이션의 인증서가 만료되기 전에 새 인증서를 만들어 애플리케이션 공급자에 보냅니다. 이 정보는 페더레이션 메타데이터에서 가져올 수 있습니다. 자세한 내용은 [페더레이션 메타데이터](../azuread-dev/azure-ad-federation-metadata.md)를 참조하세요. 

Azure AD와 애플리케이션 간의 인증서 변경을 처리하는 방법에 대한 프로세스를 설정합니다. 이 프로세스가 준비되면 인증서 만료 또는 강제 인증서 롤오버로 인한 중단을 방지하거나 최소화할 수 있습니다. 자세한 내용은 [Azure Active Directory에서 페더레이션된 Single Sign-On에 대한 인증서 관리](manage-certificates-for-federated-single-sign-on.md)를 참조합니다.

## <a name="communications"></a>통신

통신은 새 서비스의 성공에 대단히 중요합니다. 사용자에게 사용자 환경의 변경 방법에 대해 사전에 알려줍니다. 변경되는 시기와 문제가 발생하는 경우 지원을 받는 방법을 알려줍니다. 사용자가 SSO 사용 애플리케이션에 액세스하는 방법에 대한 옵션을 검토하고, 통신을 선택 항목과 일치하도록 만듭니다.

커뮤니케이션 계획을 이행합니다. 사용자에게 예정된 변경, 변경된 시기 및 지금 수행해야 할 작업을 알려야 합니다. 또한 지원을 찾는 방법에 대한 정보를 제공해야 합니다.

## <a name="licensing"></a>라이선스

애플리케이션에서 다음 라이선스 요구 사항을 충족하는지 확인합니다.

- **Azure AD 라이선스** - 미리 통합된 엔터프라이즈 애플리케이션에 대한 SSO는 무료입니다. 그러나 디렉터리의 개체 수와 배포하려는 기능에 따라 더 많은 라이선스가 필요할 수 있습니다. 라이선스 요구 사항에 대한 전체 목록은 [Azure Active Directory 가격 책정](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)을 참조하세요.

- **애플리케이션 라이선스** - 비즈니스 요구 사항을 충족하려면 애플리케이션에 적절한 라이선스가 필요합니다. 애플리케이션 소유자와 협력하여 애플리케이션에 할당된 사용자에게 애플리케이션 내 역할에 적합한 라이선스가 있는지 확인합니다. Azure AD가 역할을 기반으로 자동 프로비저닝을 관리하는 경우에는 Azure AD에 할당된 역할이 애플리케이션 내에서 소유하는 라이선스 수와 일치해야 합니다. 애플리케이션에서 소유한 라이선스 수가 적절하지 않으면 사용자 계정을 프로비전하거나 업데이트하는 동안 오류가 발생할 수 있습니다.

## <a name="shared-accounts"></a>공유 계정

로그인 관점에서 공유 계정이 있는 애플리케이션은 암호 SSO를 개별 사용자에 사용하는 엔터프라이즈 애플리케이션과 다르지 않습니다. 그러나 공유 계정을 사용하도록 애플리케이션을 계획하고 구성하는 경우 더 많은 단계가 필요합니다.
- 사용자와 협력하여 다음 정보를 문서화합니다.
    - 애플리케이션을 사용할 조직의 사용자 집합
    - 사용자 집합과 연결된 애플리케이션의 기존 자격 증명 집합
- 사용자 집합과 자격 증명의 각 조합에 대해, 요구 사항에 따라 클라우드 또는 온-프레미스에 보안 그룹을 만듭니다.
- 공유 자격 증명을 재설정합니다. 애플리케이션이 Azure AD에 배포되면 개인에게 공유 계정의 암호가 필요하지 않습니다. Azure AD는 암호를 저장하므로 길고 복잡한 암호를 설정하는 것을 고려해야 합니다.
- 애플리케이션에서 지원하는 경우 암호의 자동 롤오버를 구성합니다. 이렇게 하면 초기 설정을 수행한 관리자도 공유 계정의 암호를 알 수 없습니다.

## <a name="single-sign-on-options"></a>Single Sign-On 옵션

애플리케이션에서 SSO를 사용하도록 구성하는 여러 가지 방법이 있습니다. 애플리케이션의 인증이 구성된 방식에 따라 선택해야 하는 SSO가 달라집니다.
- 클라우드 애플리케이션은 OpenID Connect, OAuth, SAML, 암호 기반 또는 연결됨을 SSO에 사용할 수 있습니다. Single Sign-On을 사용하지 않도록 설정할 수도 있습니다.
- 온-프레미스 애플리케이션은 암호 기반, Windows 통합 인증, 헤더 기반, 연결됨을 SSO에 사용할 수 있습니다. 온-프레미스 선택은 [애플리케이션 프록시](../app-proxy/what-is-application-proxy.md)에 대해 애플리케이션을 구성할 때 작동합니다.

다음 순서도는 상황에 가장 적합한 SSO 방법을 결정하는 데 도움이 될 수 있습니다.

![Single Sign-On 메서드의 의사 결정 순서도](./media/plan-sso-deployment/single-sign-on-options.png)
 
사용할 수 있는 SSO 프로토콜은 다음과 같습니다.

- **OpenID Connect 및 OAuth** - 연결하려는 애플리케이션에서 지원하는 경우 OpenID Connect 및 OAuth 2.0을 선택합니다. 자세한 내용은 [Microsoft ID 플랫폼의 OAuth 2.0 및 OpenID Connect 프로토콜](../develop/active-directory-v2-protocols.md)을 참조하세요. OpenID Connect SSO를 구현하는 단계는 [Azure Active Directory에서 애플리케이션에 대한 OIDC 기반 Single Sign-On 설정](add-application-portal-setup-oidc-sso.md)을 참조하세요.

- **SAML** - OpenID Connect 또는 OAuth를 사용하지 않는 기존 애플리케이션에 대해 가능할 때마다 SAML을 선택합니다. 자세한 내용은 [Single Sign-On SAML 프로토콜](../develop/single-sign-on-saml-protocol.md)을 참조하세요. SAML SSO 구현에 대한 간략한 소개는 [빠른 시작: Azure Active Directory에서 애플리케이션에 대한 SAML 기반 Single Sign-On 설정](add-application-portal-setup-sso.md)을 참조하세요. 

- **암호 기반** - 애플리케이션에 HTML 로그인 페이지가 있는 경우 암호 기반을 선택합니다. 암호 기반 SSO는 암호 보관이라고도 합니다. 암호 기반 SSO를 사용하면 ID 페더레이션을 지원하지 않는 웹 애플리케이션에 대한 사용자 액세스 및 암호를 관리할 수 있습니다. 또한 여러 사용자가 조직의 소셜 미디어 앱 계정과 같은 단일 계정을 공유해야 하는 경우 유용합니다. 

    암호 기반 SSO는 사용자 이름 및 암호 필드 이외의 추가 항목이 로그인하는 데 필요한 애플리케이션에 대해 다중 로그인 필드가 필요한 애플리케이션을 지원합니다. 사용자가 자격 증명을 입력할 때 [내 앱]에 표시되는 사용자 이름 및 암호 필드의 레이블을 사용자 지정할 수 있습니다. 암호 기반 SSO를 구현하는 단계는 [암호 기반 Single Sign-On](configure-password-single-sign-on-non-gallery-applications.md)을 참조하세요.

- **연결됨** - 애플리케이션이 다른 ID 공급자 서비스에서 SSO로 구성된 경우 연결됨을 선택합니다. 연결됨 옵션을 사용하면 사용자가 포털에서 조직의 애플리케이션을 선택할 때 대상 위치를 구성할 수 있습니다. AD FS(Active Directory Federation Services)와 같이 현재 페더레이션을 사용하는 사용자 지정 웹 애플리케이션에 대한 링크를 추가할 수 있습니다. 

    또한 사용자의 액세스 패널에 표시하려는 특정 웹 페이지 및 인증을 요구하지 않는 앱에 대한 링크를 추가할 수 있습니다. 연결됨 옵션은 Azure AD 자격 증명을 통해 로그온 기능을 제공하지 않습니다. 연결된 SSO를 구현하는 단계는 [연결된 Single Sign-On](configure-linked-sign-on.md)을 참조하세요.

- **사용 안 함** - 애플리케이션이 SSO로 구성될 준비가 되지 않은 경우 사용하지 않도록 설정된 SSO를 선택합니다.

- **IWA(Windows 통합 인증)** - IWA를 사용하는 애플리케이션 또는 클레임 ​​인식 애플리케이션의 경우 IWA Single Sign-On을 선택합니다. 자세한 내용은 [애플리케이션 프록시를 사용하여 애플리케이션에 대한 Single Sign-On의 Kerberos 제한된 위임](../app-proxy/application-proxy-configure-single-sign-on-with-kcd.md)을 참조하세요.

- **헤더 기반** - 애플리케이션에서 헤더를 인증에 사용하는 경우 헤더 기반 Single Sign-On을 선택합니다. 자세한 내용은 [헤더 기반 SSO](../app-proxy/application-proxy-configure-single-sign-on-with-headers.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
- [앱에 대한 액세스 관리](what-is-access-management.md)
