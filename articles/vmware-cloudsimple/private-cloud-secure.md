---
title: Azure VMware Solution by CloudSimple - 보안 프라이빗 클라우드
description: CloudSimple 프라이빗 클라우드로 Azure VMware Solution을 보호하는 방법을 설명합니다.
author: suzizuber
ms.author: v-szuber
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 54731484a72743d38028a40ec34fbf7d44c7ffc8
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132322078"
---
# <a name="how-to-secure-your-private-cloud-environment"></a>프라이빗 클라우드 환경을 보호하는 방법

CloudSimple 서비스, CloudSimple 포털 및 Azure의 프라이빗 클라우드에 대한 RBAC(역할 기반 액세스 제어)를 정의합니다.  프라이빗 클라우드의 vCenter에 액세스하기 위한 사용자, 그룹 및 역할은 VMware SSO를 사용하여 지정됩니다.  

## <a name="azure-rbac-for-cloudsimple-service"></a>CloudSimple 서비스용 Azure RBAC

CloudSimple 서비스를 만들려면 Azure 구독에 대한 **소유자** 또는 **기여자** 역할이 필요합니다.  기본적으로 모든 소유자와 기여자는 CloudSimple 서비스를 만들고 CloudSimple 포털에 액세스하여 프라이빗 클라우드를 만들고 관리할 수 있습니다.  지역당 하나의 CloudSimple 서비스만 만들 수 있습니다.  특정 관리자에 대한 액세스를 제한하려면 아래 절차를 따르세요.

1. Azure Portal의 새 **리소스 그룹** 에 CloudSimple 서비스 만들기
2. 리소스 그룹에 대해 Azure RBAC를 지정합니다.
3. 노드 구매 및 CloudSimple 서비스와 동일한 리소스 그룹 사용

리소스 그룹에 대한 **소유자** 또는 **기여자** 권한이 있는 사용자만 CloudSimple 서비스를 보고 CloudSimple 포털을 시작할 수 있습니다.

자세한 내용은 [Azure RBAC(Azure 역할 기반 액세스 제어)란?](../role-based-access-control/overview.md)을 참조하세요.

## <a name="rbac-for-private-cloud-vcenter"></a>프라이빗 클라우드 vCenter용 RBAC

프라이빗 클라우드가 만들어지면 기본 사용자 `CloudOwner@cloudsimple.local`이 vCenter SSO 도메인에 만들어집니다.  CloudOwner 사용자는 vCenter 관리 권한이 있습니다. 다른 사용자에게 액세스 권한을 부여하기 위해 추가 ID 원본이 vCenter SSO에 추가됩니다.  사전 정의된 역할 및 그룹은 추가 사용자를 추가하는 데 사용할 수 있는 vCenter에 설정됩니다.

### <a name="add-new-users-to-vcenter"></a>vCenter에 새 사용자 추가

1. 프라이빗 클라우드에서 **CloudOwner\@cloudsimple.local** 사용자의 [권한을 에스컬레이션](escalate-private-cloud-privileges.md)합니다.
2. **CloudOwner\@cloudsimple.local** 을 사용하여 vCenter에 로그인합니다.
3. [vCenter Single Sign-On 사용자 추가](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html).
4. [vCenter Single Sign-on 그룹](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)에 사용자를 추가합니다.

사전 정의된 역할 및 그룹에 대한 자세한 내용은 [VMware vCenter의 CloudSimple 프라이빗 클라우드 권한 모델](learn-private-cloud-permissions.md) 문서를 참조하세요.

### <a name="add-new-identity-sources"></a>새 ID 원본 추가

프라이빗 클라우드의 vCenter SSO 도메인에 대한 추가 ID 공급자를 추가할 수 있습니다.  ID 공급자는 인증을 제공하고 vCenter SSO 그룹은 사용자에 대한 권한 부여를 제공합니다.

* 프라이빗 클라우드 vCenter에서 [Active Directory를 ID 공급자로 사용](set-vcenter-identity.md)
* 프라이빗 클라우드 vCenter에서 [Azure AD를 공급자로 사용](azure-ad.md)

1. 프라이빗 클라우드에서 **CloudOwner\@cloudsimple.local** 사용자의 [권한을 에스컬레이션](escalate-private-cloud-privileges.md)합니다.
2. **CloudOwner\@cloudsimple.local** 을 사용하여 vCenter에 로그인합니다.
3. ID 공급자의 사용자를 [vCenter Single Sign-On 그룹](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)에 추가합니다.

## <a name="secure-network-on-your-private-cloud-environment"></a>프라이빗 클라우드 환경의 보안 네트워크

프라이빗 클라우드 환경의 네트워크 보안은 네트워크 액세스를 보호하고 리소스 간의 네트워크 트래픽을 제어하여 제어됩니다.

### <a name="access-to-private-cloud-resources"></a>프라이빗 클라우드 리소스에 액세스

프라이빗 클라우드 vCenter 및 리소스 액세스는 보안 네트워크 연결을 통해 이루어집니다.

* **[ExpressRoute 연결](on-premises-connection.md)** . ExpressRoute는 온-프레미스 환경에서 안전하고 높은 대역폭 및 짧은 대기 시간 연결을 제공합니다.  연결을 사용하면 온-프레미스 서비스, 네트워크 및 사용자가 프라이빗 클라우드 vCenter에 액세스할 수 있습니다.
* **[사이트 간 VPN Gateway](vpn-gateway.md)** . 사이트 간 VPN을 사용하면 보안 터널을 통해 온-프레미스에서 프라이빗 클라우드 리소스에 액세스할 수 있습니다.  프라이빗 클라우드로 네트워크 트래픽을 보내고 받을 수 있는 온-프레미스 네트워크를 지정합니다.
* **[지점 및 사이트 간 VPN Gateway](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)** . 프라이빗 클라우드 vCenter에 대한 빠른 원격 액세스를 위해 지점 및 사이트 간 VPN 연결을 사용합니다.

### <a name="control-network-traffic-in-private-cloud"></a>프라이빗 클라우드의 네트워크 트래픽 제어

방화벽 테이블 및 규칙은 프라이빗 클라우드의 네트워크 트래픽을 제어합니다.  방화벽 테이블을 사용하면 테이블에 정의된 규칙 조합을 기반으로 원본 네트워크 또는 IP 주소와 대상 네트워크 또는 IP 주소 간의 네트워크 트래픽을 제어할 수 있습니다.

1. [방화벽 테이블](firewall.md#add-a-new-firewall-table)을 만듭니다.
2. 방화벽 테이블에 [규칙을 추가](firewall.md#create-a-firewall-rule)합니다.
3. [VLAN/서브넷에 방화벽 테이블 연결](firewall.md#attach-vlans-subnet).
