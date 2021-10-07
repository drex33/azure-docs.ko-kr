---
title: Azure VMware Solution by CloudSimple - 프라이빗 클라우드에서 vCenter ID 원본 설정
description: VMware 관리자가 vCenter에 액세스할 수 있도록 Active Directory를 사용하여 인증하도록 프라이빗 클라우드 vCenter를 설정하는 방법을 설명합니다.
author: suzizuber
ms.author: v-szuber
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 36b45bcdc84b02dec5c54eff5f96cb7b07cf2ca5
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129616040"
---
# <a name="set-up-vcenter-identity-sources-to-use-active-directory"></a>Active Directory를 사용할 vCenter ID 원본 설정

## <a name="about-vmware-vcenter-identity-sources"></a>VMware vCenter ID 원본 정보

VMware vCenter는 vCenter에 액세스하는 사용자를 인증하는 데 다양한 ID 원본을 지원합니다.  VMware 관리자가 vCenter에 액세스할 수 있도록 CloudSimple 프라이빗 클라우드 vCenter를 Active Directory로 인증하도록 설정할 수 있습니다. 설정이 완료되면 **cloudowner** 사용자가 ID 원본에서 vCenter로 사용자를 추가할 수 있습니다.  

다음과 같은 방법으로 Active Directory 도메인 및 도메인 컨트롤러를 설정할 수 있습니다.

* 온-프레미스에서 실행되는 Active Directory 도메인 및 도메인 컨트롤러
* Azure 구독에서 가상 머신으로 Azure에서 실행되는 Active Directory 도메인 및 도메인 컨트롤러
* 프라이빗 클라우드에서 실행 중인 새 Active Directory 도메인 및 도메인 컨트롤러
* Azure Active Directory 서비스

이 가이드에서는 구독에서 온-프레미스 또는 가상 머신으로 실행되는 Active Directory 도메인 및 도메인 컨트롤러를 설정하는 작업에 대해 설명합니다.  Azure AD를 ID 원본으로 사용하려는 경우 ID 원본 설정에 대한 자세한 지침을 보려면 [CloudSimple 프라이빗 클라우드에서 vCenter용 ID 공급자로 Azure AD 사용](azure-ad.md)을 참조하세요.

[ID 원본을 추가](#add-an-identity-source-on-vcenter)하기 전에 [vCenter 권한을 임시로 에스컬레이션](escalate-private-cloud-privileges.md)합니다.

> [!CAUTION]
> 새 사용자는 *클라우드 소유자 그룹*, *클라우드 글로벌 클러스터 관리자 그룹*, *클라우드 글로벌 스토리지 관리자 그룹*, *클라우드 글로벌 네트워크 관리자 그룹* 또는 *클라우드 글로벌 VM 관리자 그룹* 에만 추가해야 합니다.  *관리자* 그룹에 추가된 사용자는 자동으로 제거됩니다.  서비스 계정만 *관리자* 그룹에 추가해야 하며, 서비스 계정은 vSphere 웹 UI에 로그인하는 데 사용하면 안 됩니다.   


## <a name="identity-source-options"></a>ID 원본 옵션

* [온-프레미스 Active Directory를 Single Sign-On ID 원본으로 추가](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)
* [프라이빗 클라우드에서 새 Active Directory 설정](#set-up-new-active-directory-on-a-private-cloud)
* [Azure에서 Active Directory 설정](#set-up-active-directory-on-azure)

> [!IMPORTANT]
> **Active Directory(Windows 통합 인증)는 지원되지 않습니다.** LDAP를 통한 Active Directory 옵션만 ID 원본으로 지원됩니다.

## <a name="add-on-premises-active-directory-as-a-single-sign-on-identity-source"></a>온-프레미스 Active Directory를 Single Sign-On ID 원본으로 추가

온-프레미스 Active Directory를 Single Sign-On ID 원본으로 설정하려면 다음이 필요합니다.

* 온-프레미스 데이터 센터에서 프라이빗 클라우드로 [사이트 간 VPN 연결](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)
* vCenter 및 PSC(Platform Services Controller)에 추가된 온-프레미스 DNS 서버 IP

Active Directory 도메인을 설정할 때 다음 표의 정보를 사용합니다.

| **옵션** | **설명** |
|------------|-----------------|
| **Name** | ID 원본의 이름입니다. |
| **사용자의 기본 DN** | 사용자의 기본 고유 이름입니다. |
| **도메인 이름** | 도메인의 FQDN(예: example.com)입니다. 이 텍스트 상자에 IP 주소를 입력하지 마세요. |
| **도메인 별칭** | 도메인 NetBIOS 이름입니다. SSPI 인증을 사용하는 경우 Active Directory 도메인의 NetBIOS 이름을 ID 원본의 별칭으로 추가합니다. |
| **그룹의 기본 DN** | 그룹의 기본 고유 이름입니다. |
| **주 서버 URL** | 도메인에 대한 주 도메인 컨트롤러 LDAP 서버입니다.<br><br>`ldap://hostname:port` 또는 `ldaps://hostname:port` 형식을 사용합니다. 포트는 일반적으로 LDAP 연결의 경우 389이고, LDAPS 연결의 경우 636입니다. Active Directory 다중 도메인 컨트롤러 배포의 경우 포트는 일반적으로 LDAP의 경우 3268이고, LDAPS의 경우 3269입니다.<br><br>주 또는 보조 LDAP URL에서 `ldaps://`를 사용하는 경우 Active Directory 서버의 LDAPS 엔드포인트에 대한 신뢰를 설정하는 인증서가 필요합니다. |
| **보조 서버 URL** | 장애 조치(failover)에 사용되는 보조 도메인 컨트롤러 LDAP 서버의 주소입니다. |
| **인증서 선택** | Active Directory LDAP 서버 또는 OpenLDAP 서버 ID 원본에서 LDAPS를 사용하려는 경우 URL 텍스트 상자에 `ldaps://`를 입력하면 인증서 선택 단추가 표시됩니다. 보조 URL은 필요하지 않습니다. |
| **사용자 이름** | 도메인에서 사용자 및 그룹의 기본 DN에 대한 최소한의 읽기 전용 액세스 권한이 있는 사용자의 ID입니다. |
| **암호** | 사용자 이름으로 지정된 사용자의 암호입니다. |

위의 표에 나와 있는 정보를 사용할 경우 온-프레미스 Active Directory를 vCenter의 Single Sign-On ID 원본으로 추가할 수 있습니다.

> [!TIP]
> [VMware 설명서 페이지](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.psc.doc/GUID-B23B1360-8838-4FF2-B074-71643C4CB040.html)에서 Single Sign-On ID 원본에 대한 자세한 내용을 확인할 수 있습니다.

## <a name="set-up-new-active-directory-on-a-private-cloud"></a>프라이빗 클라우드에서 새 Active Directory 설정

프라이빗 클라우드에서 새 Active Directory 도메인을 설정하고 Single Sign-On에 대 한 ID 원본으로 사용할 수 있습니다.  Active Directory 도메인은 기존 Active Directory 포리스트에 속할 수도 있고 독립 포리스트로 설정할 수도 있습니다.

### <a name="new-active-directory-forest-and-domain"></a>새 Active Directory 포리스트 및 도메인

새 Active Directory 포리스트와 도메인을 설정하려면 다음이 필요합니다.

* 새 Active Directory 포리스트와 도메인의 도메인 컨트롤러로 사용할 Microsoft Windows Server를 실행하는 하나 이상의 가상 머신
* 이름 확인을 위해 DNS 서비스를 실행하는 하나 이상의 가상 머신

자세한 단계를 보려면 [새 Windows Server 2012 Active Directory 포리스트 설치](/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200-)를 참조하세요.

> [!TIP]
> 서비스의 고가용성을 위해 여러 도메인 컨트롤러와 DNS 서버를 설정하는 것이 좋습니다.

Active Directory 포리스트와 도메인을 설정한 후 새 Active Directory에 대한 [ID 원본을 vCenter에 추가할](#add-an-identity-source-on-vcenter) 수 있습니다.

### <a name="new-active-directory-domain-in-an-existing-active-directory-forest"></a>기존 Active Directory 포리스트의 새 Active Directory 도메인

기존 Active Directory 포리스트에 새 Active Directory 도메인을 설정하려면 다음이 필요합니다.

* Active Directory 포리스트 위치에 대한 사이트 간 VPN 연결
* 기존 Active Directory 포리스트의 이름을 확인하는 DNS 서버

자세한 단계를 보려면 [새 Windows Server 2012 Active Directory 자식 또는 트리 도메인 설치](/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-child-or-tree-domain--level-200-)를 참조하세요.

Active Directory 도메인을 설정한 후 새 Active Directory에 대한 [ID 원본을 vCenter에 추가할](#add-an-identity-source-on-vcenter) 수 있습니다.

## <a name="set-up-active-directory-on-azure"></a>Azure에서 Active Directory 설정

Azure에서 실행되는 Active Directory는 온-프레미스에서 실행되는 Active Directory와 비슷합니다.  vCenter에서 Single Sign-On ID 원본으로 Azure에서 실행되는 Active Directory를 설정하려면 vCenter 서버와 PSC에 Active Directory 서비스가 실행되는 Azure Virtual Network에 대한 네트워크 연결이 있어야 합니다.  Active Directory Services가 실행 중인 Azure 가상 네트워크에서 CloudSimple 프라이빗 클라우드로 [ExpressRoute를 통해 Azure Virtual Network 연결](azure-expressroute-connection.md)을 사용하여 이 연결을 설정할 수 있습니다.

네트워크 연결이 설정된 후 [온-프레미스 Active Directory를 Single Sign-On ID 원본으로 추가](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)의 단계에 따라 ID 원본으로 추가합니다.  

## <a name="add-an-identity-source-on-vcenter"></a>vCenter에서 ID 원본 추가

1. 프라이빗 클라우드에서 [권한을 에스컬레이션](escalate-private-cloud-privileges.md)합니다.

2. 프라이빗 클라우드의 vCenter에 로그인합니다.

3. **홈 > 관리** 를 선택합니다.

    ![관리](media/OnPremAD01.png)

4. **Single Sign-On > 구성** 을 선택합니다.

    ![Single Sign On](media/OnPremAD02.png)

5. **ID 원본** 탭을 열고 **+** 를 클릭하여 새 ID 원본을 추가합니다.

    ![ID 원본](media/OnPremAD03.png)

6. **LDAP 서버로 Active Directory** 를 선택하고 **다음** 을 클릭합니다.

    ![LDAP 서버 옵션으로 Active Directory를 강조 표시하는 스크린샷](media/OnPremAD04.png)

7. 사용자 환경에 대한 ID 원본 매개 변수를 지정하고 **다음** 을 클릭합니다.

    ![Active Directory](media/OnPremAD05.png)

8. 설정을 검토하고 **마침** 을 클릭합니다.
