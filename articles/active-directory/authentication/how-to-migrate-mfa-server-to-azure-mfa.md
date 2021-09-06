---
title: Azure MFA 서버에서 Azure Multi-Factor Authentication으로 마이그레이션 - Azure Active Directory
description: Azure MFA 서버 온-프레미스에서 Azure Multi-Factor Authentication으로 마이그레이션하기 위한 단계별 참고 자료
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/22/2021
ms.author: BaSelden
author: BarbaraSelden
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4415e7ce86d2beb9e2903f23d0b6fa9ac7d3ec04
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122597836"
---
# <a name="migrate-from-azure-mfa-server-to-azure-multi-factor-authentication"></a>Azure MFA 서버에서 Azure Multi-Factor Authentication으로 마이그레이션

MFA(다단계 인증)는 악의적인 행위자로부터 인프라와 자산을 보호하는 데 중요합니다. Azure MFA 서버는 새 배포에 사용할 수 없으며 더 이상 사용되지 않습니다. MFA 서버를 사용하는 고객은 클라우드 기반 Azure AD(Azure Active Directory) 다단계 인증을 사용하는 것으로 전환해야 합니다. 이 설명서에서는 다음과 같은 하이브리드 환경을 사용하는 것으로 가정합니다.

- MFA용 MFA 서버 사용 중
- AD FS(Active Directory Federation Services) 또는 다른 ID 공급자 페더레이션 제품을 사용하여 Azure AD에서 페더레이션 사용 중
  - 이 문서의 범위는 AD FS로 지정되어 있지만, 유사한 단계가 다른 ID 공급자에게도 적용됩니다.
- MFA 서버는 AD FS와 통합됩니다. 
- 인증 시 AD FS를 사용하는 애플리케이션이 있을 수 있습니다.

목표에 따라 마이그레이션에 가능한 종료 상태는 여러 가지가 있습니다.

| <br> | 목표: MFA 서버만 해제 | 목표: MFA 서버 해제 후 Azure AD 인증으로 전환 | 목표: MFA 서버 및 AD FS 해제 |
|------|------------------------------------|-------------------------------------------------------------------|-----------------------------------------|
|MFA 공급자 | MFA 서버의 MFA 공급자를 Azure AD MFA로 변경합니다. | MFA 서버의 MFA 공급자를 Azure AD MFA로 변경합니다. |   MFA 서버의 MFA 공급자를 Azure AD MFA로 변경합니다. |
|사용자 인증  |Azure AD 인증 시 페더레이션을 계속 사용합니다. | 암호 해시 동기화(기본 설정) 또는 통과 인증 **및** 원활한 Single Sign-On을 사용하여 Azure AD로 전환합니다.| 암호 해시 동기화(기본 설정) 또는 통과 인증 **및** 원활한 Single Sign-On을 사용하여 Azure AD로 전환합니다. |
|애플리케이션 인증 | 애플리케이션에서 AD FS 인증을 계속 사용합니다. | 애플리케이션에서 AD FS 인증을 계속 사용합니다. | Azure MFA로 마이그레이션하기 전에 앱을 Azure AD로 전환합니다. |

가능한 경우 MFA와 사용자 인증을 모두 Azure로 전환합니다. 단계별 참고 자료는 [Azure AD MFA 및 Azure AD 사용자 인증으로 전환](how-to-migrate-mfa-server-to-azure-mfa-user-authentication.md)을 참조하세요. 

사용자 인증을 전환할 수 없는 경우 [페더레이션을 사용하여 Azure AD MFA로 전환](how-to-migrate-mfa-server-to-azure-mfa-with-federation.md)에 대한 단계별 참고 자료를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- AD FS 환경(MFA를 마이그레이션하기 전에 모든 앱을 Azure AD로 마이그레이션하지 않는 경우 필요)
  - Windows Server 2019 FBL(팜 동작 수준) 4용 AD FS로 업그레이드합니다. 이렇게 하면 그룹 구성원에 따라 인증 공급자를 선택하여 보다 원활하게 사용자를 전환할 수 있습니다. Windows Server 2016 FBL 3용 AD FS에서 마이그레이션할 수는 있지만, 사용자에게는 원활하지 않습니다. 마이그레이션 중에 마이그레이션이 완료될 때까지 인증 공급자(MFA 서버 또는 Azure MFA)를 선택하라는 메시지가 사용자에게 표시됩니다. 
- 사용 권한
  - Azure AD MFA용 AD FS 팜을 구성하기 위한 Active Directory의 엔터프라이즈 관리자 역할
  - Azure AD PowerShell을 사용하여 Azure AD를 구성하기 위한 Azure AD의 전역 관리자 역할


## <a name="considerations-for-all-migration-paths"></a>모든 마이그레이션 경로에 대한 고려 사항

MFA 서버에서 Azure AD MFA로 마이그레이션하려면 등록된 MFA 전화 번호를 전환하는 것 이상의 작업을 수행해야 합니다. Microsoft의 MFA 서버를 여러 시스템과 통합할 수 있으며, 이러한 시스템에서 MFA 서버를 사용하는 방법을 평가해 Azure AD MFA와 통합하는 가장 좋은 방법을 이해해야 합니다. 

### <a name="migrating-mfa-user-information"></a>MFA 사용자 정보 마이그레이션

사용자를 일괄 처리로 전환하는 데 생각할 수 있는 일반적인 방법으로는 지역, 부서 또는 관리자와 같은 역할별로 전환하는 것이 포함됩니다. 어떤 전략을 선택하든, 테스트 및 파일럿 그룹으로 시작하여 사용자를 반복적으로 전환하고 롤백 계획을 마련해 두어야 합니다. 

사용자의 등록된 MFA 전화 번호와 하드웨어 토큰을 마이그레이션할 수 있지만, Microsoft Authenticator 앱 설정과 같은 디바이스 등록은 마이그레이션할 수 없습니다. 사용자는 Authenticator 앱에 새 계정을 등록 및 추가하고 이전 계정을 제거해야 합니다. 

사용자가 MFA 서버에 연결된 이전 계정과 새로 추가된 계정을 구분할 수 있도록, MFA 서버의 모바일 앱에 대한 계정 이름을 두 계정을 구분하는 방식으로 지정해야 합니다. 예를 들어, MFA 서버의 모바일 앱에 표시되는 계정 이름은 OnPrem MFA 서버로 이름이 변경되었습니다. Authenticator 앱의 계정 이름은 사용자에게 다음 푸시 알림을 통해 변경됩니다. 

또한 전화 번호를 마이그레이션하면 부실 번호가 마이그레이션되고 사용자가 암호 없는 모드에서 Microsoft Authenticator 같이 더 안전한 방법을 설정하는 대신 전화 기반 MFA를 유지할 가능성이 높아질 수 있습니다. 따라서 선택한 마이그레이션 경로에 상관없이 모든 사용자가 [결합된 보안 정보](howto-registration-mfa-sspr-combined.md)를 등록하도록 하는 것이 좋습니다.


#### <a name="migrating-hardware-security-keys"></a>하드웨어 보안 키 마이그레이션

Azure AD는 OATH 하드웨어 토큰을 지원합니다. MFA 서버의 토큰을 Azure AD MFA로 토큰을 마이그레이션하려면, 일반적으로 ‘시드 파일’이라고 하는 [CSV 파일을 사용하여 토큰을 Azure AD에 업로드해야 합니다](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview). 시드 파일에는 비밀 키와 토큰 일련 번호뿐만 아니라, Azure AD로 토큰을 업로드하는 데 필요한 기타 필수 정보가 포함되어 있습니다. 

더 이상 비밀 키가 포함된 시드 파일이 없으면 MFA 서버에서 비밀 키를 내보낼 수 없습니다. 더 이상 비밀 키에 더 이상 액세스할 수 없으면 하드웨어 공급업체에 지원을 요청하세요.

MFA 서버 웹 서비스 SDK를 사용하여 지정된 사용자에게 할당된 OATH 토큰의 일련 번호를 내보낼 수 있습니다. IT 관리자는 시드 파일과 함께 이 정보를 사용하여 토큰을 Azure AD로 가져오고, 일련 번호를 기준으로 지정된 사용자에게 OATH 토큰을 할당할 수 있습니다. 또한 디바이스에서 등록을 완료하려면 OTP 정보를 제공하기 위해 가져올 때 사용자에게 연락해야 합니다. MFA 서버의 Multi-Factor Authentication 서버 도움말 파일에서 GetUserInfo > userSettings > OathTokenSerialNumber 항목을 참조하세요. 


### <a name="additional-migrations"></a>추가 마이그레이션

MFA 서버에서 Azure MFA로 마이그레이션하기로 결정하면 다른 마이그레이션의 가능성이 열립니다. 추가 마이그레이션을 완료하는 것은 특히 다음을 비롯한 여러 요인에 따라 달라집니다.

- 사용자를 위해 Azure AD 인증을 사용할 의향
- 애플리케이션을 Azure AD로 전환할 의향

MFA 서버는 애플리케이션 및 사용자 인증과 긴밀하게 통합되어 있으므로, MFA 마이그레이션의 일환으로 두 기능을 모두 Azure로 전환하고 최종적으로 AD FS를 해제하는 것이 좋습니다. 

권장 사항은 다음과 같습니다. 

- 더 강력한 보안/거버넌스를 사용할 수 있으므로 인증 시 Azure AD 사용
- 가급적 Azure AD로 애플리케이션 전환

조직에 가장 적합한 사용자 인증 방법을 선택하려면 [Azure AD 하이브리드 ID 솔루션에 적합한 인증 방법 선택](../hybrid/choose-ad-authn.md)을 참조하세요. PHS(암호 해시 동기화)를 사용하는 것이 좋습니다.

### <a name="passwordless-authentication"></a>암호 없는 인증

두 번째 요소로 Microsoft Authenticator를 사용하도록 사용자를 등록하는 과정에서 등록의 일환으로 암호 없는 휴대폰 로그인을 사용하도록 설정하는 것이 좋습니다. FIDO 및 비즈니스용 Windows Hello와 같이 암호 없는 기타 방법 등에 대한 자세한 내용은 [Azure AD를 통한 암호 없는 인증 배포 계획](howto-authentication-passwordless-deployment.md#plan-for-and-deploy-the-microsoft-authenticator-app)을 참조하세요.

### <a name="microsoft-identity-manager-self-service-password-reset"></a>Microsoft Identity Manager 셀프 서비스 암호 재설정 

MIM(Microsoft Identity Manager) SSPR에서는 MFA 서버를 사용하여 암호 재설정 흐름의 일부로 SMS 일회성 암호를 호출할 수 있습니다. Azure MFA를 사용하도록 MIM을 구성할 수는 없습니다. SSPR 서비스를 Azure AD SSPR로 전환하는 과정을 평가하는 것이 좋습니다.

Azure MFA에 등록하는 사용자의 기회를 이용해 결합된 등록 환경을 사용함으로써 Azure AD SSPR에 등록할 수 있습니다.


### <a name="radius-clients-and-azure-ad-mfa"></a>RADIUS 클라이언트 및 Azure AD MFA

MFA 서버는 RADIUS를 지원하여 프로토콜을 지원하는 애플리케이션과 네트워크 디바이스에 대해 MFA를 호출합니다. MFA 서버에서 RADIUS를 사용하는 경우 클라이언트 애플리케이션을 Azure AD의 SAML, Open ID Connect 또는 OAuth 같은 최신 프로토콜로 전환하는 것이 좋습니다. 애플리케이션을 업데이트할 수 없는 경우 Azure MFA 확장을 통해 NPS(네트워크 정책 서버)를 배포할 수 있습니다. NPS(네트워크 정책 서버) 확장은 RADIUS 기반 애플리케이션과 Azure AD MFA 사이에서 어댑터 역할을 하여 두 번째 인증 요소를 제공합니다. 이렇게 하면 RADIUS 클라이언트를 Azure MFA로 전환하고 MFA 서버를 해제할 수 있습니다.

#### <a name="important-considerations"></a>중요 고려 사항

RADIUS 클라이언트에서 NPS를 사용하는 경우 제한 사항이 있으며, RADIUS 클라이언트를 평가하여 최신 인증 프로토콜로 업그레이드할 수 있는지 확인하는 것이 좋습니다. 지원되는 제품 버전과 각각의 기능은 서비스 공급자에게 문의하세요. 

- NPS 확장은 Azure AD 조건부 액세스 정책을 사용하지 않습니다. RADIUS를 유지하고 NPS 확장을 사용하는 경우 NPS로 가는 모든 인증 요청 시 사용자가 MFA를 수행해야 합니다.
- 사용자는 Azure AD MFA에 등록한 후 NPS 확장을 사용해야 합니다. 그렇지 않으면 확장이 사용자를 인증하지 못해 지원 센터를 호출할 수 있습니다.
- NPS 확장이 MFA를 호출하면 MFA 요청이 사용자의 기본 MFA 메서드로 전송됩니다. 
  - 타사 애플리케이션에서 로그인하기 때문에, MFA가 필요하고 요청이 디바이스에 전송되었다는 시각적 알림을 사용자에게 표시할 가능성이 거의 없습니다.
  - MFA 요구 사항 중에 사용자가 기본 인증 방법에 액세스할 수 있어야 MFA 요구 사항을 완료할 수 있습니다. 대체 방법을 선택할 수 없습니다. 기본 인증 방법은 테넌트 인증 방법과 MFA 정책에서 사용하지 않도록 설정된 경우에도 사용됩니다.
  - 사용자는 보안 정보 페이지(aka.ms/mysecurityinfo)에서 기본 MFA 방법을 변경할 수 있습니다.
- Radius 클라이언트에서 사용 가능한 MFA 방법은 RADIUS 액세스 요청을 보내는 클라이언트 시스템에 의해 제어됩니다.
  - 암호를 입력한 후 사용자가 입력해야 하는 MFA 방법은 RADIUS로 액세스 시도 응답을 지원하는 시스템에서만 사용할 수 있습니다. 입력 방법에는 OTP, 하드웨어 OATH 토큰 또는 Microsoft Authenticator 애플리케이션이 포함될 수 있습니다.
  - 일부 시스템은 Microsoft Authenticator 푸시 알림과 전화 통화에 사용하는 데 MFA 방법을 제한할 수 있습니다.


>[!NOTE]
>RADIUS 클라이언트와 NPS 시스템 사이에 사용되는 암호 암호화 알고리즘과 클라이언트에서 사용할 수 있는 입력 방법은 사용 가능한 인증 방법에 영향을 줍니다. 자세한 내용은 [사용자가 이용할 수 있는 인증 방법 확인](howto-mfa-nps-extension.md)을 참조하세요. 

일반적인 RADIUS 클라이언트 통합에는 [원격 데스크톱 게이트웨이](howto-mfa-nps-extension-rdg.md) 및 [VPN 서버](howto-mfa-nps-extension-vpn.md) 같은 애플리케이션이 포함됩니다. 그 외 다음을 포함할 수 있습니다.

- Citrix 게이트웨이
  - [Citrix 게이트웨이](https://docs.citrix.com/en-us/citrix-gateway)는 RADIUS와 NPS 확장 통합 및 SAML 통합을 모두 지원합니다.
- Cisco VPN
  - Cisco VPN은 RADIUS와 [SSO용 SAML 인증](../saas-apps/cisco-anyconnect.md)을 모두 지원합니다.
  - RADIUS 인증에서 SAML으로 전환하면 NPS 확장을 배포하지 않고 Cisco VPN을 통합할 수 있습니다.
- 모든 VPN
  - 가능하면 VPN을 SAML 앱으로 페더레이션하는 것이 좋습니다. 이렇게 하면 조건부 액세스를 사용할 수 있습니다. 자세한 내용은 [Azure AD 앱 갤러리에 통합된 VPN 공급업체의 목록](../manage-apps/secure-hybrid-access.md#secure-hybrid-access-through-azure-ad-partner-integrations)을 참조하세요.


### <a name="resources-for-deploying-nps"></a>NPS 배포에 대한 리소스

- [새 NPS 인프라 추가](/windows-server/networking/technologies/nps/nps-top)
- [NPS 배포 모범 사례](https://www.youtube.com/watch?v=qV9wddunpCY)
- [Azure MFA NPS 확장 상태 검사 스크립트](/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/)
- [Azure AD MFA와 기존 NPS 인프라 통합](howto-mfa-nps-extension-vpn.md)

## <a name="next-steps"></a>다음 단계

- [페더레이션을 사용하여 Azure AD MFA로 전환](how-to-migrate-mfa-server-to-azure-mfa-with-federation.md)
- [Azure AD MFA 및 Azure AD 사용자 인증으로 전환](how-to-migrate-mfa-server-to-azure-mfa-user-authentication.md)


