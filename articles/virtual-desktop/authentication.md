---
title: Azure Virtual Desktop 인증 - Azure
description: Azure Virtual Desktop에 대한 인증 방법.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/16/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: c987ad26d28a6c96d187c420b32bd3103391c62d
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122567790"
---
# <a name="supported-authentication-methods"></a>지원되는 인증 방법

이 문서에서는 Azure Virtual Desktop에서 사용할 수 있는 인증 종류에 대한 간략한 개요를 제공합니다.

## <a name="identities"></a>Identities

Azure Virtual Desktop은 선택한 구성에 따라 다양한 유형의 ID를 지원합니다. 이 섹션에서는 각 구성에 사용할 수 있는 ID에 대해 설명합니다.

### <a name="on-premise-identity"></a>온-프레미스 ID

Azure Virtual Desktop에 액세스하려면 Azure AD(Azure Active Directory)를 통해 사용자를 검색할 수 있어야 하므로 AD DS(Active Directory Domain Services)에만 존재하는 사용자 ID는 지원되지 않습니다. 여기에는 AD FS(Active Directory Federation Services)를 포함하는 독립 실행형 Active Directory 배포가 포함됩니다.

### <a name="hybrid-identity"></a>하이브리드 ID

Azure Virtual Desktop은 AD FS를 사용하여 페더레이션된 ID를 포함하여 Azure AD를 통해 [하이브리드 ID](../active-directory/hybrid/whatis-hybrid-identity.md)를 지원합니다. AD DS에서 이러한 사용자 ID를 관리하고 [Azure AD Connect](../active-directory/hybrid/whatis-azure-ad-connect.md)를 사용하여 Azure AD와 동기화할 수 있습니다. Azure AD를 사용하여 이러한 ID를 관리하고 [Azure AD DS(Azure AD Directory Services)](../active-directory-domain-services/overview.md)와 동기화할 수도 있습니다.

하이브리드 ID를 사용하여 Azure Virtual Desktop에 액세스할 때 AD(Active Directory)의 사용자에 대한 UPN(사용자 보안 이름) 또는 SID(보안 식별자)가 Azure AD의 해당 항목과 일치하지 않는 경우가 있습니다. 예를 들어 AD 계정 user@contoso.local은 Azure AD의 user@contoso.com과 일치할 수 있습니다. Azure Virtual Desktop은 AD 및 Azure AD 계정에 대한 UPN 또는 SID가 일치하는 경우에만 이러한 유형의 구성을 지원합니다. SID는 AD의 사용자 개체 속성 "ObjectSID"와 Azure AD의 "OnPremisesSecurityIdentifier"를 나타냅니다.

### <a name="cloud-only-identity"></a>클라우드 전용 ID

Azure Virtual Desktop은 [Azure AD 조인 VM](deploy-azure-ad-joined-vm.md)을 사용할 때 클라우드 전용 ID를 지원합니다.

### <a name="external-identity"></a>외부 ID

Azure Virtual Desktop은 현재 [외부 ID](../active-directory/external-identities/index.yml)를 지원하지 않습니다.

## <a name="service-authentication"></a>서비스 인증

Azure Virtual Desktop 리소스에 액세스하려면 먼저 Azure AD 계정에 로그인하여 서비스에서 인증을 받아야 합니다. 리소스를 검색하기 위해 작업 영역을 구독하거나 앱 또는 데스크톱에 연결할 때마다 인증이 수행됩니다. Azure AD와 페더레이션되기만 하면 [타사 ID 공급자](../active-directory/devices/azureadjoin-plan.md#federated-environment)를 사용할 수 있습니다.

### <a name="multifactor-authentication"></a>Multi-Factor Authentication

[Azure Virtual Desktop에서 다단계 인증 설정](set-up-mfa.md)의 지침에 따라 배포에 MFA(다단계 인증)를 사용하도록 설정하는 방법을 알아봅니다. 또한 이 문서에서는 사용자에게 자격 증명을 입력하라는 메시지가 표시되는 빈도를 구성하는 방법을 알려줍니다. Azure AD 조인 VM을 배포할 때 [Azure AD 조인 VM에 MFA 사용](deploy-azure-ad-joined-vm.md#enabling-mfa-for-azure-ad-joined-vms)에 포함된 구성 가이드를 따릅니다.

### <a name="smart-card-authentication"></a>스마트 카드 인증

스마트 카드를 사용하여 Azure AD에서 인증을 받으려면 먼저 [사용자 인증서 인증을 위해 AD FS를 구성](/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)해야 합니다.

## <a name="session-host-authentication"></a>세션 호스트 인증

[Single Sign-On](#single-sign-on-sso)을 사용하도록 설정하지 않았거나 자격 증명을 로컬로 저장하지 않은 경우 세션 호스트에서도 인증을 받아야 합니다. 다음은 Azure Virtual Desktop 클라이언트가 현재 지원하는 세션 호스트에 대한 로그인 방법입니다.

- Windows 데스크톱 클라이언트
    - 사용자 이름 및 암호
    - 스마트 카드
    - [비즈니스용 Windows Hello 인증서 신뢰](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust)
    - [비즈니스용 Windows Hello 인증서를 사용한 키 신뢰](/windows/security/identity-protection/hello-for-business/hello-deployment-rdp-certs)
- Windows 스토어 클라이언트
    - 사용자 이름 및 암호
- 웹 클라이언트
    - 사용자 이름 및 암호
- Android
    - 사용자 이름 및 암호
- iOS
    - 사용자 이름 및 암호
- macOS
    - 사용자 이름 및 암호

Azure Virtual Desktop은 세션 호스트 인증을 위해 NTLM(NT LAN Manager)과 Kerberos를 모두 지원합니다. 스마트 카드 및 비즈니스용 Windows Hello는 Kerberos만 사용하여 로그인할 수 있습니다. Kerberos를 사용하려면 클라이언트가 도메인 컨트롤러에서 실행되는 KDC(키 배포 센터) 서비스에서 Kerberos 보안 티켓을 가져와야 합니다. 티켓을 가져오려면 클라이언트는 도메인 컨트롤러에 대한 직접 네트워킹 가시권이 있어야 합니다. 회사 네트워크 내에서 직접 연결하거나, VPN 연결을 사용하거나, [KDC 프록시 서버](key-distribution-center-proxy.md)를 설정하면 이러한 가시권을 얻을 수 있습니다.

### <a name="single-sign-on-sso"></a>SSO(Single Sign-On)

Azure Virtual Desktop은 Windows 및 웹 클라이언트용 [ADFS(Active Directory Federation Services)를 사용하는 SSO](configure-adfs-sso.md)를 지원합니다. SSO를 사용하면 세션 호스트 인증을 건너뛸 수 있습니다.

그렇지 않은 경우 세션 호스트에 대한 자격 증명을 입력하라는 메시지가 표시되지 않도록 하는 유일한 방법은 클라이언트에 저장하는 것입니다. 다른 사용자가 리소스에 액세스하지 못하도록 보안 디바이스에서만 이 작업을 수행하는 것이 좋습니다.

## <a name="in-session-authentication"></a>세션 내 인증

원격 앱 또는 데스크톱에 연결되면 세션 내에서 인증을 받으라는 메시지가 표시될 수 있습니다. 이 섹션에서는 이 시나리오에서 사용자 이름 및 암호 이외의 자격 증명을 사용하는 방법을 설명합니다.

### <a name="smart-cards"></a>스마트 카드

세션에서 스마트 카드를 사용하려면 세션 호스트에 스마트 카드 드라이버를 설치하고 [스마트 카드 리디렉션](configure-device-redirections.md#smart-card-redirection)을 사용하도록 설정했는지 확인합니다. [클라이언트 비교 차트](/windows-server/remote/remote-desktop-services/clients/remote-desktop-app-compare#other-redirection-devices-etc)를 검토하여 클라이언트가 스마트 카드 리디렉션을 지원하는지 확인합니다.

### <a name="fido2-and-windows-hello-for-business"></a>FIDO2 및 비즈니스용 Windows Hello

Azure Virtual Desktop은 현재 FIDO2 또는 비즈니스용 Windows Hello에 대한 세션 내 인증을 지원하지 않습니다.

## <a name="next-steps"></a>다음 단계

- 배포를 안전하게 유지하는 다른 방법이 궁금하나요? [보안 모범 사례](security-guide.md)를 확인하세요.
- Azure AD 조인 VM에 연결하는 데 문제가 있나요? [Azure AD 조인 VM에 대한 연결 문제 해결](troubleshoot-azure-ad-connections.md)
- 회사 네트워크 외부에서 스마트 카드를 사용하려고 하나요? [KDC 프록시 서버](key-distribution-center-proxy.md)를 설정하는 방법을 검토합니다.
