---
title: 하이브리드 FIDO2 보안 키 배포에 대한 FAQ - Azure Active Directory
description: Azure Active Directory를 사용하여 암호 없는 하이브리드 FIDO2 보안 키 로그인에 대한 몇 가지 자주 묻는 질문에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 853174bd96f0a88f8513df848aab7a3d34aceb28
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131451847"
---
# <a name="deployment-frequently-asked-questions-faqs-for-hybrid-fido2-security-keys-in-azure-ad"></a>Azure AD에서 하이브리드 FIDO2 보안 키에 대한 배포 FAQ(질문과 대답) 

이 문서는 하이브리드 Azure AD 조인 디바이스에 대한 배포 FAQ(질문과 대답) 및 온-프레미스 리소스에 대한 암호 없는 로그인에 대해 다룹니다. 이 암호 없는 기능을 사용하면 FIDO2 보안 키를 사용하여 하이브리드 Azure AD 조인 디바이스에 대해 Windows 10 디바이스에서 Azure AD 인증을 사용하도록 설정할 수 있습니다. 사용자는 FIDO2 키와 같은 최신 자격 증명을 사용하여 디바이스에서 Windows에 로그인하고, 온-프레미스 리소스에 대해 원활한 SSO(Single Sign-On) 환경을 사용하여 기존 Active Directory Domain Services(AD DS) 기반 리소스에 액세스할 수 있습니다.

하이브리드 환경의 사용자를 위해 다음 시나리오가 지원됩니다.

* FIDO2 보안 키를 사용하여 하이브리드 Azure AD 조인 디바이스에 로그인하고 온-프레미스 리소스에 대한 SSO 액세스를 가져옵니다.
* FIDO2 보안 키를 사용하여 Azure AD 조인 디바이스에 로그인하고 온-프레미스 리소스에 대한 SSO 액세스를 가져옵니다.

FIDO2 보안 키 및 온-프레미스 리소스에 대한 하이브리드 액세스를 시작하려면 다음 문서를 참조하세요.

* [암호 없는 FIDO2 보안 키](howto-authentication-passwordless-security-key.md)
* [암호 없는 Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [암호 없는 온-프레미스](howto-authentication-passwordless-security-key-on-premises.md)

## <a name="security-keys"></a>보안 키

* [조직에서 리소스에 액세스하기 위한 2단계 인증을 요구합니다. 이 요구 사항을 지원하기 위해 수행할 수 있는 것은?](#my-organization-requires-multi-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement)
* [호환되는 FIDO2 보안 키를 어디에서 찾을 수 있나요?](#where-can-i-find-compliant-fido2-security-keys)
* [내 보안 키를 분실한 경우 어떻게 해야 하나요?](#what-if-i-lose-my-security-key)
* [FIDO2 보안 키의 데이터는 어떻게 보호되나요?](#how-is-the-data-protected-on-the-fido2-security-key)
* [FIDO2 보안 키 등록은 어떻게 작동하나요?](#how-does-the-registering-of-fido2-security-keys-work)
* [관리자가 사용자에 대한 키를 직접 프로비전할 방법이 있나요?](#is-there-a-way-for-admins-to-provision-the-keys-for-the-users-directly)

### <a name="my-organization-requires-multi-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>조직에서 리소스에 액세스하기 위한 다단계 인증을 요구합니다. 이 요구 사항을 지원하기 위해 수행할 수 있는 작업은 무엇인가요?

FIDO2 보안 키는 다양한 폼 팩터를 제공합니다. 관심 있는 디바이스 제조업체에 문의하여 PIN 또는 생체 인식을 두 번째 요소로 사용하여 디바이스를 활성화하는 방법을 알아봅니다. 지원되는 공급자 목록은 [FIDO2 보안 키 공급자](concept-authentication-passwordless.md#fido2-security-key-providers)를 참조하세요.

### <a name="where-can-i-find-compliant-fido2-security-keys"></a>호환되는 FIDO2 보안 키를 어디에서 찾을 수 있나요?

지원되는 공급자 목록은 [FIDO2 보안 키 공급자](concept-authentication-passwordless.md#fido2-security-key-providers)를 참조하세요.

### <a name="what-if-i-lose-my-security-key"></a>내 보안 키를 분실한 경우 어떻게 해야 하나요?

**보안 정보** 페이지로 이동하고 FIDO2 보안 키를 제거하여 Azure Portal에서 키를 제거할 수 있습니다.

### <a name="how-is-the-data-protected-on-the-fido2-security-key"></a>FIDO2 보안 키의 데이터는 어떻게 보호되나요?

FIDO2 보안 키에는 저장된 프라이빗 키를 보호하는 보안 enclave가 있습니다. FIDO2 보안 키에는 프라이빗 키를 추출할 수 없는 Windows Hello와 같이 기본 제공되는 안티 해머링(anti-hammering) 속성도 있습니다.

### <a name="how-does-the-registering-of-fido2-security-keys-work"></a>FIDO2 보안 키 등록은 어떻게 작동하나요?

FIDO2 보안 키를 등록하고 사용하는 방법에 대한 자세한 내용은 [암호 없는 보안 키 로그인 사용](howto-authentication-passwordless-security-key.md)을 참조하세요.

### <a name="is-there-a-way-for-admins-to-provision-the-keys-for-the-users-directly"></a>관리자가 사용자에 대한 키를 직접 프로비전할 방법이 있나요?

아니요, 현재로는 안 됩니다.

### <a name="why-i-am-getting-notallowederror-in-the-browser-when-registering-fido2-keys"></a>FIDO2 키를 등록할 때 브라우저에 "NotAllowedError"가 표시되는 이유는 무엇인가요?

fido2 키 등록 페이지에서 "NotAllowedError"를 받게 됩니다. 이 문제는 일반적으로 사용자가 프라이빗(Incognito) 창에 있거나 FIDO2 프라이빗 키 액세스를 사용할 수 없는 원격 데스크톱을 사용하는 경우에 발생합니다.

## <a name="prerequisites"></a>필수 구성 요소

* [인터넷에 연결되지 않은 경우 이 기능이 작동하나요?](#does-this-feature-work-if-theres-no-internet-connectivity)
* [Azure AD에서 열려 있어야 하는 특정 엔드포인트는 무엇인가요?](#what-are-the-specific-end-points-that-are-required-to-be-open-to-azure-ad)
* [Windows 10 디바이스에 대한 도메인 가입 유형(Azure AD 조인 또는 하이브리드 Azure AD 조인)을 어떻게 식별하나요?](#how-do-i-identify-the-domain-join-type-azure-ad-joined-or-hybrid-azure-ad-joined-for-my-windows-10-device)
* [패치를 적용해야 하는 DC 수에 대한 권장 사항은 무엇인가요?](#whats-the-recommendation-on-the-number-of-dcs-that-should-be-patched)
* [온-프레미스 전용 디바이스에 FIDO2 자격 증명 공급자를 배포할 수 있나요?](#can-i-deploy-the-fido2-credential-provider-on-an-on-premises-only-device)
* [도메인 관리자 또는 기타 높은 권한 계정에 대해 FIDO2 보안 키 로그인이 작동하지 않습니다. 이유는 무엇인가요?](#fido2-security-key-sign-in-isnt-working-for-my-domain-admin-or-other-high-privilege-accounts-why)

### <a name="does-this-feature-work-if-theres-no-internet-connectivity"></a>인터넷에 연결되지 않은 경우 이 기능이 작동하나요?

인터넷 연결은 이 기능을 사용하기 위한 필수 구성 요소입니다. 사용자가 FIDO2 보안 키를 사용하여 처음 로그인할 때 인터넷에 연결되어 있어야 합니다. 후속 로그인 이벤트의 경우 캐시된 로그인이 작동하고 사용자가 인터넷 연결 없이 인증할 수 있습니다.

일관된 환경을 위해 디바이스가 인터넷에 연결되어 있고 DC에 연결되어 있는지 확인합니다.

### <a name="what-are-the-specific-end-points-that-are-required-to-be-open-to-azure-ad"></a>Azure AD에서 열려 있어야 하는 특정 엔드포인트는 무엇인가요?

등록 및 인증에는 다음 엔드포인트가 필요합니다.

* * *.microsoftonline.com*
* * *.microsoftonline-p.com*
* * *.msauth.net*
* * *.msauthimages.net*
* * *.msecnd.net*
* * *.msftauth.net*
* * *.msftauthimages.net*
* * *.phonefactor.net*
* *enterpriseregistration.windows.net*
* *management.azure.com*
* *policykeyservice.dc.ad.msft.net*
* *secure.aadcdn.microsoftonline-p.com*

Microsoft 온라인 제품을 사용하는 데 필요한 전체 엔드포인트 목록은 [Office 365 URL 및 IP 주소 범위](/microsoft-365/enterprise/urls-and-ip-address-ranges)를 참조하세요.

### <a name="how-do-i-identify-the-domain-join-type-azure-ad-joined-or-hybrid-azure-ad-joined-for-my-windows-10-device"></a>Windows 10 디바이스에 대한 도메인 가입 유형(Azure AD 조인 또는 하이브리드 Azure AD 조인)을 어떻게 식별하나요?

Windows 10 클라이언트 디바이스에 올바른 도메인 가입 유형이 있는지 확인하려면 다음 명령을 사용합니다.

```console
Dsregcmd/status
```

다음 샘플 출력은 *AzureADJoined* 가 *YES* 로 설정되었으므로 디바이스가 Azure AD에 가입된 것을 보여 줍니다.

```output
+---------------------+
| Device State        |
+---------------------+

AzureADJoined: YES
EnterpriseJoined: NO
DomainedJoined: NO
```

다음 샘플 출력은 *DomainedJoined* 도 *YES* 로 설정되었으므로 디바이스가 하이브리드 Azure AD에 가입된 것을 보여 줍니다. *DomainName* 도 표시됩니다.

```output
+---------------------+
| Device State        |
+---------------------+

AzureADJoined: YES
EnterpriseJoined: NO
DomainedJoined: YES
DomainName: CONTOSO
```

Windows Server 2016 또는 2019 도메인 컨트롤러에서 다음 패치가 적용되었는지 확인합니다. 필요한 경우 Windows 업데이트를 실행하여 다음을 설치합니다.

* Windows Server 2016 - [KB4534307](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
* Windows Server 2019 - [KB4534321](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

클라이언트 디바이스에서 다음 명령을 실행하여 패치가 설치된 적절한 도메인 컨트롤러에 대한 연결을 확인합니다.

```console
nltest /dsgetdc:<domain> /keylist /kdc
```

### <a name="whats-the-recommendation-on-the-number-of-dcs-that-should-be-patched"></a>패치를 적용해야 하는 DC 수에 대한 권장 사항은 무엇인가요?

조직의 인증 요청 부하를 처리할 수 있도록 패치를 사용하여 대부분의 Windows Server 2016 또는 2019 도메인 컨트롤러를 패치하는 것이 좋습니다.

Windows Server 2016 또는 2019 도메인 컨트롤러에서 다음 패치가 적용되었는지 확인합니다. 필요한 경우 Windows 업데이트를 실행하여 다음을 설치합니다.

* Windows Server 2016 - [KB4534307](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
* Windows Server 2019 - [KB4534321](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

### <a name="can-i-deploy-the-fido2-credential-provider-on-an-on-premises-only-device"></a>온-프레미스 전용 디바이스에 FIDO2 자격 증명 공급자를 배포할 수 있나요?

아니요, 이 기능은 온-프레미스 전용 디바이스에 대해서는 지원되지 않습니다. FIDO2 자격 증명 공급자가 표시되지 않습니다.

### <a name="fido2-security-key-sign-in-isnt-working-for-my-domain-admin-or-other-high-privilege-accounts-why"></a>내 도메인 관리자 또는 기타 높은 권한 계정에 대해 FIDO2 보안 키 로그인이 작동하지 않습니다. 그 이유는

기본 보안 정책은 온-프레미스 리소스에 대한 높은 권한 계정에 로그인할 수 있는 권한을 Azure AD에 부여하지 않습니다.

계정을 차단 해제하려면 **Active Directory 사용자 및 컴퓨터** 를 사용하여 *Azure AD Kerberos 컴퓨터 개체(CN=AzureADKerberos,OU=Domain Controllers,\<domain-DN>)* 의 *msDS-NeverRevealGroup* 속성을 수정합니다.

## <a name="under-the-hood"></a>기본적인 이해

* [Azure AD Kerberos가 내 온-프레미스 Active Directory Domain Services 환경에 어떻게 연결되나요?](#how-is-azure-ad-kerberos-linked-to-my-on-premises-active-directory-domain-services-environment)
* [AD에서 생성되고 Azure AD에 게시된 이러한 Kerberos 서버 개체를 어디에서 볼 수 있나요?](#where-can-i-view-these-kerberos-server-objects-that-are-created-in-ad-ds-and-published-in-azure-ad)
* [인터넷에 종속되지 않도록 온-프레미스 AD DS에 공개 키를 등록할 수 없는 이유는 무엇인가요?](#why-cant-we-have-the-public-key-registered-to-on-premises-ad-ds-so-there-is-no-dependency-on-the-internet)
* [Kerberos 서버 개체에서 키는 어떻게 회전하나요?](#how-are-the-keys-rotated-on-the-kerberos-server-object)
* [Azure AD Connect가 필요한 이유는 무엇인가요? Azure AD에서 AD DS 정보를 다시 작성하나요?](#why-do-we-need-azure-ad-connect-does-it-write-any-info-back-to-ad-ds-from-azure-ad)
* [PRT + 부분 TGT를 요청할 때 HTTP 요청/응답은 어떻게 표시되나요?](#what-does-the-http-requestresponse-look-like-when-requesting-prt-partial-tgt)

### <a name="how-is-azure-ad-kerberos-linked-to-my-on-premises-active-directory-domain-services-environment"></a>Azure AD Kerberos가 내 온-프레미스 Active Directory Domain Services 환경에 어떻게 연결되나요?

온-프레미스 AD DS 환경과 Azure AD 테넌트의 두 부분이 있습니다.

**AD DS(Active Directory Domain Services)**

Azure AD Kerberos 서버는 온-프레미스 AD DS 환경에서 DC(도메인 컨트롤러) 개체로 표시됩니다. 이 DC 개체는 여러 개체로 구성됩니다.

* *CN=AzureADKerberos,OU=Domain Controllers,\<domain-DN>*
    
    AD DS의 RODC(읽기 전용 도메인 컨트롤러)를 나타내는 *Computer* 개체입니다. 이 개체와 연결된 컴퓨터가 없습니다. 대신 DC의 논리적 표현입니다.

* *CN=krbtgt_AzureAD,CN=Users,\<domain-DN>*

    RODC Kerberos TGT(Ticket Granting Ticket) 암호화 키를 나타내는 *User* 개체입니다.

* *CN=900274c4-b7d2-43c8-90ee-00a9f650e335,CN=AzureAD,CN=System,\<domain-DN>*

    Azure AD Kerberos 서버 개체에 대한 메타데이터를 저장하는 *ServiceConnectionPoint* 개체입니다. 관리 도구는 이 개체를 사용하여 Azure AD Kerberos 서버 개체를 식별하고 찾습니다.

**Azure Active Directory**

Azure AD Kerberos 서버는 Azure AD에서 *KerberosDomain* 개체로 표시됩니다. 각 온-프레미스 AD DS 환경은 Azure AD 테넌트에서 단일 *KerberosDomain* 개체로 표시됩니다.

예를 들어 *contoso.com* 및 *fabrikam.com* 과 같은 두 개의 도메인이 있는 AD DS 포리스트가 있을 수 있습니다. Azure AD에서 전체 포리스트에 대해 Kerberos TGT(Ticket Granting Ticket)를 발급하도록 허용하는 경우 Azure AD에는 *contoso.com* 에 하나, *fabrikam.com* 에 하나, 이렇게 두 개의 *KerberosDomain* 개체가 있습니다.

AD DS 포리스트가 여러 개인 경우 각 포리스트의 각 도메인에 대해 하나의 *KerberosDomain* 개체가 있습니다.

### <a name="where-can-i-view-these-kerberos-server-objects-that-are-created-in-ad-ds-and-published-in-azure-ad"></a>AD DS에서 생성되고 Azure AD에 게시된 이러한 Kerberos 서버 개체를 어디에서 볼 수 있나요?

모든 개체를 보려면 최신 버전의 Azure AD Connect에 포함된 Azure AD Kerberos 서버 PowerShell cmdlet을 사용합니다.

개체를 보는 방법에 대한 지침을 비롯한 자세한 내용은 [Kerberos 서버 개체 만들기](howto-authentication-passwordless-security-key-on-premises.md#create-a-kerberos-server-object)를 참조하세요.

### <a name="why-cant-we-have-the-public-key-registered-to-on-premises-ad-ds-so-there-is-no-dependency-on-the-internet"></a>인터넷에 종속되지 않도록 온-프레미스 AD DS에 공개 키를 등록할 수 없는 이유는 무엇인가요?

비즈니스용 Windows Hello에 대한 배포 모델의 복잡성에 대한 의견을 받았습니다. 따라서 인증서 및 PKI(FIDO2는 인증서를 사용하지 않음)를 사용하지 않고도 배포 모델을 단순화하고자 했습니다.

### <a name="how-are-the-keys-rotated-on-the-kerberos-server-object"></a>Kerberos 서버 개체에서 키는 어떻게 회전하나요?

다른 DC와 마찬가지로, Azure AD Kerberos 서버 암호화 *krbtgt* 키는 정기적으로 회전해야 합니다. 다른 모든 AD DS *krbtgt* 키를 회전하는 데 사용하는 것과 동일한 일정을 따르는 것이 좋습니다.

> [!NOTE]
> *krbtgt* 키를 회전하는 다른 도구가 있지만 [PowerShell cmdlet을 사용하여 Azure AD Kerberos 서버의 *krbtgt* 키를 회전](howto-authentication-passwordless-security-key-on-premises.md#rotate-the-azure-ad-kerberos-server-key)해야 합니다. 이 방법을 사용하면 온-프레미스 AD DS 환경과 Azure AD 모두에서 키가 업데이트됩니다.

### <a name="why-do-we-need-azure-ad-connect-does-it-write-any-info-back-to-ad-ds-from-azure-ad"></a>Azure AD Connect가 필요한 이유는 무엇인가요? Azure AD에서 AD DS 정보를 다시 작성하나요?

Azure AD Connect는 Azure AD에서 AD DS로 정보를 다시 쓰지 않습니다. 이 유틸리티에는 AD DS에서 Kerberos 서버 개체를 만들고 Azure AD에 게시하기 위한 PowerShell 모듈이 포함되어 있습니다.

### <a name="what-does-the-http-requestresponse-look-like-when-requesting-prt-partial-tgt"></a>PRT + 부분 TGT를 요청할 때 HTTP 요청/응답은 어떻게 표시되나요?

HTTP 요청은 표준 PRT(기본 새로 고침 토큰) 요청입니다. 이 PRT 요청은 Kerberos TGT(Ticket Granting Ticket)가 필요함을 나타내는 클레임을 포함합니다.

| 클레임 | 값 | Description                             |
|-------|-------|-----------------------------------------|
| tgt   | true  | 클레임은 클라이언트에 TGT가 필요함을 나타냅니다. |

Azure AD는 암호화된 클라이언트 키와 메시지 버퍼를 PRT 응답에 추가 속성으로 결합합니다. 페이로드는 Azure AD 디바이스 세션 키를 사용하여 암호화됩니다.

| 필드              | 형식   | Description  |
|--------------------|--------|--------------|
| tgt_client_key     | 문자열 | Base64 인코딩된 클라이언트 키(비밀)입니다. 이 키는 TGT를 보호하는 데 사용되는 클라이언트 암호입니다. 이 암호 없는 시나리오에서 클라이언트 암호는 각 TGT 요청의 일부로 서버에서 생성된 다음, 응답에서 클라이언트로 반환됩니다. |
| tgt_key_type       | int    | 클라이언트 키와 KERB_MESSAGE_BUFFER에 포함된 Kerberos 세션 키 모두에 사용되는 온-프레미스 AD DS 키 유형입니다. |
| tgt_message_buffer | 문자열 | Base64 인코딩 KERB_MESSAGE_BUFFER입니다. |

## <a name="next-steps"></a>다음 단계

FIDO2 보안 키 및 온-프레미스 리소스에 대한 하이브리드 액세스를 시작하려면 다음 문서를 참조하세요.

* [암호 없는 FIDO2 보안 키](howto-authentication-passwordless-security-key.md)
* [암호 없는 Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [암호 없는 온-프레미스](howto-authentication-passwordless-security-key-on-premises.md)
