---
title: Azure VMware Solution by CloudSimple - 프라이빗 클라우드의 ID 원본으로 Azure AD 사용
description: Azure AD를 CloudSimple 프라이빗 클라우드의 ID 공급자로 추가하여 Azure에서 CloudSimple에 액세스하는 사용자를 인증하는 방법을 설명합니다.
author: suzizuber
ms.author: v-szuber
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a462e8606a9ab6be88052c45a3110682d188449f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132308013"
---
# <a name="use-azure-ad-as-an-identity-provider-for-vcenter-on-cloudsimple-private-cloud"></a>CloudSimple 프라이빗 클라우드에서 Azure AD를 vCenter용 ID 공급자로 사용

VMware 관리자가 vCenter에 액세스할 수 있도록 CloudSimple 프라이빗 클라우드 vCenter를 Azure Active Directory(Azure AD)로 인증하도록 설정할 수 있습니다. Single Sign-On ID 원본을 설정한 이후 **cloudowner** 사용자는 ID 원본에서 vCenter로 사용자를 추가할 수 있습니다.  

다음과 같은 방법으로 Active Directory 도메인 및 도메인 컨트롤러를 설정할 수 있습니다.

* 온-프레미스에서 실행되는 Active Directory 도메인 및 도메인 컨트롤러
* Azure 구독에서 가상 머신으로 Azure에서 실행되는 Active Directory 도메인 및 도메인 컨트롤러
* CloudSimple 프라이빗 클라우드에서 실행 중인 새 Active Directory 도메인 및 도메인 컨트롤러
* Azure Active Directory 서비스

이 가이드에서는 ID 원본으로 Azure AD를 설정하는 데 필요한 작업을 설명합니다.  온-프레미스 Active Directory 사용 또는 Azure에서 Active Directory 실행에 대한 자세한 내용은 [Active Directory를 사용할 vCenter ID 원본 설정](set-vcenter-identity.md)에서 ID 원본 설정에서의 구체적인 지침을 참조하세요.

## <a name="about-azure-ad"></a>Azure AD 정보

Azure AD는 Microsoft 다중 테넌트 클라우드 기반 디렉터리 및 ID 관리 서비스입니다.  Azure AD는 사용자가 Azure에서 다양한 서비스를 인증하고 액세스하는 데 사용할 수 있는 확장 가능하고 일관적이면서 안정적인 인증 메커니즘을 제공합니다.  또한 모든 타사 서비스에 대한 보안 LDAP 서비스를 제공하여 Azure AD를 인증/ID 원본으로 사용합니다.  Azure AD는 프라이빗 클라우드를 관리하는 사용자에 대한 프라이빗 클라우드 액세스 제공에 사용할 수 있는 핵심 디렉터리 서비스, 고급 ID 거버넌스 및 애플리케이션 액세스 관리를 결합합니다.

vCenter에 대한 ID 원본으로 Azure AD를 사용하려면 Azure AD 및 Azure AD Domain Services를 설정해야 합니다. 다음 지침을 따릅니다.

1. [Azure AD 및 Azure AD Domain Services를 설정하는 방법](#set-up-azure-ad-and-azure-ad-domain-services)
2. [프라이빗 클라우드 vCenter에서 ID 원본을 설정하는 방법](#set-up-an-identity-source-on-your-private-cloud-vcenter)

## <a name="set-up-azure-ad-and-azure-ad-domain-services"></a>Azure AD 및 Azure AD Domain Services 설정

시작하기 전에 전역 관리자 권한으로 Azure 구독에 액세스해야 합니다.  다음 단계는 일반 지침을 제공합니다. 자세한 내용은 Azure 설명서에 포함되어 있습니다.

### <a name="azure-ad"></a>Azure AD

> [!NOTE]
> Azure AD가 있는 경우 이 섹션을 건너뛰어도 됩니다.

1. [Azure AD 설명서](../active-directory/fundamentals/active-directory-whatis.md)에서 설명하는 것과 같이 구독에서 Azure AD를 설정합니다.
2. [Azure Active Directory Premium에 등록](../active-directory/fundamentals/active-directory-get-started-premium.md)에서 설명하는 것과 같이 구독에 Azure Active Directory Premium을 사용합니다.
3. [Azure Active Directory에 사용자 지정 도메인 이름 추가](../active-directory/fundamentals/add-custom-domain.md)에서 설명하는 것과 같이 사용자 지정 도메인 이름을 설정하고 확인합니다.
    1. Azure에서 제공하는 정보를 사용하여 도메인 등록자에서 DNS 레코드를 설정합니다.
    2. 사용자 지정 도메인 이름을 기본 도메인으로 설정합니다.

선택적으로 다른 Azure AD 기능을 구성할 수 있습니다.  Azure AD에서 vCenter 인증을 사용하도록 설정하는 데 필요하지 않습니다.

### <a name="azure-ad-domain-services"></a>Azure AD Domain Services

> [!NOTE]
> vCenter의 ID 원본으로 Azure AD를 사용하도록 설정하는 데 있어 중요한 단계입니다.  문제를 방지하려면 모든 단계를 제대로 수행했는지 확인합니다.

1. [Azure Portal을 사용하여 Azure Active Directory Domain Services 사용 설정](../active-directory-domain-services/tutorial-create-instance.md)에서 설명하는 것과 같이 Azure Active Directory Domain Services를 사용합니다.
2. [Azure Portal을 사용하여 Azure Active Directory Domain Services 사용 설정](../active-directory-domain-services/tutorial-create-instance.md)에서 설명하는 것과 같이 Azure AD Domain Services에서 사용하는 네트워크를 설정합니다.
3. [Azure Portal을 사용하여 Azure Active Directory Domain Services 사용 설정](../active-directory-domain-services/tutorial-create-instance.md)에서 설명하는 것과 같이 Azure AD Domain Services 관리를 위한 관리자 그룹을 구성합니다.
4. [Azure Active Directory Domain Services 사용 설정](../active-directory-domain-services/tutorial-create-instance.md)에서 설명하는 것과 같이 Azure AD Domain Services에 대한 DNS 설정을 업데이트합니다.  인터넷을 통해 AD에 연결하려는 경우 Azure AD Domain Services의 공용 IP 주소에 대한 DNS 레코드를 도메인 이름으로 설정합니다.
5. 사용자에 대한 암호 해시 동기화를 사용합니다.  이 단계는 NTLM(NT LAN Manager) 및 Kerberos 인증에 필요한 암호 해시를 Azure AD Domain Services로 동기화하도록 설정합니다. 암호 해시 동기화를 설정하면 사용자는 회사 자격 증명을 사용하여 관리되는 도메인에 로그인할 수 있습니다. [Azure Active Directory Domain Services에 암호 해시 동기화 사용](../active-directory-domain-services/tutorial-create-instance.md)을 참조하세요.
    1. 클라우드 전용 사용자가 있는 경우 암호 해시가 NTLM 또는 Kerberos에서 요구하는 형식으로 저장되도록 <a href="https://myapps.microsoft.com/" target="_blank">Azure AD 액세스 패널</a>을 사용하여 암호를 변경해야 합니다.  [클라우드 전용 사용자 계정에 대한 관리되는 도메인으로 암호 해시 동기화 사용](../active-directory-domain-services/tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)의 지침을 따르세요.  Azure Portal 또는 Azure AD PowerShell cmdlet을 사용하여 Azure AD Directory에서 만든 개별 사용자 및 새 사용자에 대해 이 단계를 수행해야 합니다. Azure AD Domain Services에 대한 액세스가 필요한 사용자는 <a href="https://myapps.microsoft.com/" target="_blank">Azure AD 액세스 패널</a>을 사용하고 프로필에 액세스하여 암호를 변경해야 합니다.

        > [!NOTE]
        > 조직에 클라우드 전용 사용자 계정이 있는 경우 Azure Active Directory Domain Services를 사용해야 하는 사용자는 모두 암호를 변경해야 합니다. 클라우드 전용 사용자 계정은 Azure Portal 또는 Azure AD PowerShell cmdlet을 사용하여 Azure AD 디렉터리에 만든 계정입니다. 이러한 사용자 계정은 온-프레미스 디렉터리에서 동기화되지 않습니다.

    2. 온-프레미스 Active Directory에서 암호를 동기화하는 경우 [Active Directory 설명서](../active-directory-domain-services/tutorial-configure-password-hash-sync.md)의 단계를 따릅니다.

6.  [Azure AD Domain Services 관리형 도메인에 대한 보안 LDAP(LDAPS) 구성](../active-directory-domain-services/tutorial-configure-ldaps.md)에서 설명하는 것과 같이 Azure Active Directory Domain Services에서 보안 LDAP를 구성합니다.
    1. Azure 토픽 [보안 LDAP에 대한 인증서 가져오기](../active-directory-domain-services/tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap)에서 설명하는 것과 같이 보안 LDAP에 사용할 인증서를 업로드합니다.  CloudSimple은 vCenter에서 인증서를 신뢰할 수 있도록 인증 기관에서 발급한 서명된 인증서를 사용할 것을 권장합니다.
    2. [Azure AD Domain Services 관리되는 도메인에 대해 보안 LDAP(LDAPS) 사용하도록 설정](../active-directory-domain-services/tutorial-configure-ldaps.md)에서 설명하는 것과 같이 보안 LDAP를 사용 설정합니다.
    3. ID 원본을 구성하는 동안 vCenter에서 사용할 .cer 형식 인증서의 공개 부분(프라이빗 키 제외)을 저장합니다.
    4. Azure AD Domain Services에 대한 인터넷 액세스가 필요한 경우 '인터넷을 통한 LDAP에 대한 보안 액세스 허용' 옵션을 사용합니다.
    5. TCP 포트 636에 대한 Azure AD Domain Services NSG의 인바운드 보안 규칙을 추가합니다.

## <a name="set-up-an-identity-source-on-your-private-cloud-vcenter"></a>프라이빗 클라우드 vCenter에서 ID 원본 설정

1. 프라이빗 클라우드 vCenter에 대한 [권한을 에스컬레이션](escalate-private-cloud-privileges.md)합니다.
2. ID 원본을 설정하는 데 필요한 구성 매개 변수를 수집합니다.

    | **옵션** | **설명** |
    |------------|-----------------|
    | **Name** | ID 원본의 이름입니다. |
    | **사용자의 기본 DN** | 사용자의 기본 고유 이름입니다.  Azure의 경우 다음을 사용: `OU=AADDC Users,DC=<domain>,DC=<domain suffix>`  예: `OU=AADDC Users,DC=cloudsimplecustomer,DC=com`.|
    | **도메인 이름** | 도메인의 FQDN(예: example.com)입니다. 이 텍스트 상자에 IP 주소를 입력하지 마세요. |
    | **도메인 별칭** | (선택 사항) 도메인 NetBIOS 이름. SSPI 인증을 사용하는 경우 Active Directory 도메인의 NetBIOS 이름을 ID 원본의 별칭으로 추가합니다. |
    | **그룹의 기본 DN** | 그룹의 기본 고유 이름입니다. Azure의 경우 다음을 사용: `OU=AADDC Users,DC=<domain>,DC=<domain suffix>`  예: `OU=AADDC Users,DC=cloudsimplecustomer,DC=com`|
    | **주 서버 URL** | 도메인에 대한 주 도메인 컨트롤러 LDAP 서버입니다.<br><br>`ldaps://hostname:port` 형식을 사용합니다. LDAPS 연결의 경우 포트는 일반적으로 636입니다. <br><br>주 또는 보조 LDAP URL에서 `ldaps://`를 사용하는 경우 Active Directory 서버의 LDAPS 엔드포인트에 대한 신뢰를 설정하는 인증서가 필요합니다. |
    | **보조 서버 URL** | 장애 조치(failover)에 사용되는 보조 도메인 컨트롤러 LDAP 서버의 주소입니다. |
    | **인증서 선택** | Active Directory LDAP 서버 또는 OpenLDAP 서버 ID 원본에서 LDAPS를 사용하려는 경우 URL 텍스트 상자에 `ldaps://`를 입력하면 인증서 선택 단추가 표시됩니다. 보조 URL은 필요하지 않습니다. |
    | **사용자 이름** | 도메인에서 사용자 및 그룹의 기본 DN에 대한 최소한의 읽기 전용 액세스 권한이 있는 사용자의 ID입니다. |
    | **암호** | 사용자 이름으로 지정된 사용자의 암호입니다. |

3. 권한이 에스컬레이션된 후 프라이빗 클라우드 vCenter에 로그인합니다.
4. 이전 단계의 값을 사용하여 [vCenter에 ID 원본 추가](set-vcenter-identity.md#add-an-identity-source-on-vcenter)의 지침을 따라 Azure Active Directory를 ID 원본으로 설정합니다.
5. VMware 토픽 [vCenter Single Sign-On 그룹에 멤버 추가](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)에서 설명하는 것과 같이 Azure AD의 사용자/그룹을 vCenter 그룹에 추가합니다.

> [!CAUTION]
> 새 사용자는 클라우드 소유자 그룹, 클라우드 글로벌 클러스터 관리자 그룹, 클라우드 글로벌 스토리지 관리자 그룹, 클라우드 글로벌 네트워크 관리자 그룹 또는 클라우드 글로벌 VM 관리자 그룹에만 추가해야 합니다.  관리자 그룹에 추가된 사용자는 자동으로 제거됩니다.  서비스 계정만 관리자 그룹에 추가해야 합니다.

## <a name="next-steps"></a>다음 단계

* [프라이빗 클라우드 권한 모델 알아보기](learn-private-cloud-permissions.md)
