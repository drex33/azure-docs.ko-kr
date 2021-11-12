---
title: Azure VMware Solution by CloudSimple - VMware Site Recovery Manager를 사용하여 프라이빗 클라우드를 재해 복구 사이트로 설정
description: VMware Site Recovery Manager를 사용하여 CloudSimple 프라이빗 클라우드를 재해 복구 사이트로 설정하는 방법을 설명합니다.
author: suzizuber
ms.author: v-szuber
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2408fe3cee91efcaa6dfa734548d57f226e7f545
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132322534"
---
# <a name="set-up-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>VMware Site Recovery Manager를 사용하여 프라이빗 클라우드를 재해 복구 대상으로 설정

온-프레미스 VMware 워크로드에 대해 CloudSimple 프라이빗 클라우드를 DR(재해 복구) 사이트로 설정할 수 있습니다.

DR 솔루션은 vSphere 복제 및 VMware SRM(Site Recovery Manager)을 기반으로 합니다. 온-프레미스 복구 사이트에서 보호하는 기본 사이트로 프라이빗 클라우드를 사용하도록 설정하기 위해 유사한 접근 방식을 사용할 수 있습니다.

CloudSimple 솔루션:

* DR에 대한 데이터 센터를 설정하지 않아도 됩니다.
* 전 세계 지리적 복원력을 위해 CloudSimple이 배포되는 Azure 위치를 활용할 수 있습니다.
* 배포 비용과 DR 설정에 대한 총 소유 비용을 줄일 수 있는 옵션을 제공합니다.

CloudSimple 솔루션을 사용하려면 다음을 수행해야 합니다.

* 프라이빗 클라우드에서 vSphere 복제 및 SRM을 설치, 구성 및 관리합니다.
* 프라이빗 클라우드가 보호된 사이트인 경우 SRM에 대한 사용자 고유의 라이선스를 제공합니다. CloudSimple 사이트를 복구 사이트로 사용하는 경우 추가 SRM 라이선스가 필요하지 않습니다.

이 솔루션을 사용하면 vSphere 복제 및 SRM을 완벽하게 제어할 수 있습니다. 친숙한 UI, API 및 CLI 인터페이스를 사용하면 기존 스크립트 및 도구를 사용할 수 있습니다.

![Site Recovery Manager 배포](media/srm-deployment.png)

프라이빗 클라우드 및 온-프레미스 환경과 호환되는 모든 버전의 vRA 및 SRM을 사용할 수 있습니다. 이 가이드의 예제에서는 vRA 6.5 및 SRM 6.5를 사용합니다. 이러한 버전은 CloudSimple에서 지원되는 vSphere 6.5와 호환됩니다.

## <a name="deploy-the-solution"></a>솔루션 배포

다음 섹션에서는 프라이빗 클라우드에서 SRM을 사용하여 DR 솔루션을 배포하는 방법을 설명합니다.

1. [VMware 제품 버전 호환 여부 확인](#verify-that-vmware-product-versions-are-compatible)
2. [DR 환경의 크기 예측](#estimate-the-size-of-your-dr-environment)
3. [사용자 환경에 맞는 프라이빗 클라우드 만들기](#create-a-private-cloud-for-your-environment)
4. [SRM 솔루션에 대한 프라이빗 클라우드 네트워킹 설정](#set-up-private-cloud-networking-for-the-srm-solution)
5. [온-프레미스 네트워크와 프라이빗 클라우드 간에 사이트 간 VPN 연결 설정 및 필요한 포트 열기](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports)
6. [프라이빗 클라우드에서 인프라 서비스 설정](#set-up-infrastructure-services-in-your-private-cloud)
7. [온-프레미스 환경에서 vSphere 복제 어플라이언스 설치](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [프라이빗 클라우드 환경에서 vSphere 복제 어플라이언스 설치](#install-vsphere-replication-appliance-in-your-private-cloud-environment)
9. [온-프레미스 환경에서 SRM 서버 설치](#install-srm-server-in-your-on-premises-environment)
10. [프라이빗 클라우드에서 SRM 서버 설치](#install-srm-server-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>VMware 제품 버전 호환 여부 확인

이 가이드의 구성에는 다음과 같은 호환성 요구 사항이 적용됩니다.

* 동일한 버전의 SRM이 프라이빗 클라우드 및 온-프레미스 환경에 배포되어야 합니다.
* 동일한 버전의 vSphere 복제가 프라이빗 클라우드 및 온-프레미스 환경에 배포되어야 합니다.
* 프라이빗 클라우드와 온-프레미스 환경의 PSC(Platform Services Controller) 버전이 호환되어야 합니다.
* 프라이빗 클라우드와 온-프레미스 환경의 vCenter 버전이 호환되어야 합니다.
* SRM 및 vSphere 복제의 버전은 서로 호환되어야 하며 PSC 및 vCenter 버전과도 호환되어야 합니다.

관련 VMware 문서 및 호환성 정보에 대한 링크를 보려면 [VMware Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/index.html) 문서로 이동하세요.

프라이빗 클라우드에서 vCenter 및 PSC의 버전을 확인하려면 CloudSimple 포털을 엽니다. **리소스** 로 이동하여 프라이빗 클라우드를 선택하고 **vSphere 관리 네트워크** 탭을 클릭합니다.

![프라이빗 클라우드의 vCenter & PSC 버전](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>DR 환경의 크기 예측

1. 식별된 온-프레미스 구성이 지원되는 제한 범위 내에 있는지 확인합니다. SRM 6.5에 대한 제한은 [Site Recovery Manager 6.5에 대한 운영 제한](https://kb.vmware.com/s/article/2147110)에 대한 VMware 기술 자료 문서에 설명되어 있습니다.
2. 워크로드 크기 및 RPO 요구 사항을 충족하기에 충분한 네트워크 대역폭이 있는지 확인합니다. [vSphere 복제에 대한 대역폭 요구 사항 계산](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)에 대한 VMware 기술 자료 문서는 대역폭 제한에 대한 지침을 제공합니다.
3. CloudSimple Sizer 도구를 사용하여 온-프레미스 환경을 보호하기 위해 DR 사이트에서 필요한 리소스를 예상할 수 있습니다.

### <a name="create-a-private-cloud-for-your-environment"></a>사용자 환경에 맞는 프라이빗 클라우드 만들기

[프라이빗 클라우드 만들기](create-private-cloud.md)의 지침 및 크기 조정 권장 사항에 따라 CloudSimple 포털에서 프라이빗 클라우드를 만듭니다.

### <a name="set-up-private-cloud-networking-for-the-srm-solution"></a>SRM 솔루션에 대한 프라이빗 클라우드 네트워킹 설정

CloudSimple 포털에 액세스하여 SRM 솔루션에 대한 프라이빗 클라우드 네트워킹을 설정합니다.

SRM 솔루션 네트워크에 대한 VLAN을 만들고 서브넷 CIDR을 할당합니다. 자세한 내용은 [VLAN/서브넷 생성 및 관리](create-vlan-subnet.md)를 참조하세요.

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports"></a>온-프레미스 네트워크와 프라이빗 클라우드 간에 사이트 간 VPN 연결 설정 및 필요한 포트 열기

온-프레미스 네트워크와 프라이빗 클라우드 사이에서 사이트 간 연결을 설정합니다. 지침은 [CloudSimple 프라이빗 클라우드에 대한 VPN 연결 구성](set-up-vpn.md)을 참조하세요.

### <a name="set-up-infrastructure-services-in-your-private-cloud"></a>프라이빗 클라우드에서 인프라 서비스 설정

워크로드 및 도구를 쉽게 관리할 수 있도록 프라이빗 클라우드에서 인프라 서비스를 구성합니다.

다음 중 하나를 수행하려는 경우 [Azure AD를 CloudSimple 프라이빗 클라우드에서 vCenter용 ID 공급자로 사용](azure-ad.md)에 설명된 대로 외부 ID 공급자를 추가할 수 있습니다.

* 프라이빗 클라우드의 온-프레미스 AD(Active Directory)에서 사용자를 식별합니다.
* 모든 사용자에 대해 프라이빗 클라우드에서 AD를 설정합니다.
* Azure AD를 사용합니다.

프라이빗 클라우드의 워크로드에 대한 IP 주소 조회, IP 주소 관리 및 이름 확인 서비스를 제공하려면 [CloudSimple 프라이빗 클라우드에서 DNS 및 DHCP 애플리케이션 및 워크로드 설정](dns-dhcp-setup.md)에 설명된 대로 DHCP 및 DNS 서버를 설정합니다.

*.cloudsimple.io 도메인은 프라이빗 클라우드의 관리 VM 및 호스트에서 사용됩니다. 이 도메인에 대한 요청을 해결하려면 [조건부 전달자 만들기](on-premises-dns-setup.md#create-a-conditional-forwarder)에 설명된 대로 DNS 서버에서 DNS 전달을 구성합니다.

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>온-프레미스 환경에서 vSphere 복제 어플라이언스 설치

VMware 문서에 따라 온-프레미스 환경에 vRA(vSphere 복제 어플라이언스)를 설치합니다. 설치는 다음과 같은 고급 단계로 구성됩니다.

1. vRA 설치를 위한 온-프레미스 환경을 준비합니다.

2. vmware.com에서 VR ISO의 OVF를 사용하여 온-프레미스 환경에서 vRA를 배포합니다. vRA 6.5의 경우 [이 VMware 블로그](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices)에 관련 정보가 있습니다.

3. 온-프레미스 사이트에서 vCenter Single Sign-On을 사용하여 온-프레미스 vRA를 등록합니다. vSphere 복제 6.5에 대한 자세한 지침은 VMware 문서 [VMware vSphere 복제 6.5 설치 및 구성](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)을 참조하세요.

## <a name="install-vsphere-replication-appliance-in-your-private-cloud-environment"></a>프라이빗 클라우드 환경에서 vSphere 복제 어플라이언스 설치

시작하기 전에 다음이 있는지 확인합니다.

* 온-프레미스 환경의 서브넷에서 프라이빗 클라우드의 관리 서브넷으로의 IP 연결
* 온-프레미스 vSphere 환경의 복제 서브넷에서 프라이빗 클라우드의 SRM 솔루션 서브넷으로의 IP 연결

지침은 [CloudSimple 프라이빗 클라우드에 대한 VPN 연결 구성](set-up-vpn.md)을 참조하세요. 이러한 단계는 온-프레미스 설치와 비슷합니다.

CloudSimple은 vRA 및 SRM을 설치하는 동안 IP 주소 대신 FQDN을 사용하는 것이 좋습니다. 프라이빗 클라우드에서 vCenter 및 PSC의 FQDN을 확인하려면 CloudSimple 포털을 엽니다. **리소스** 로 이동하여 프라이빗 클라우드를 선택하고 **vSphere 관리 네트워크** 탭을 클릭합니다.

![프라이빗 클라우드에서 vCenter/PSC의 FQDN 찾기](media/srm-resources.png)

CloudSimple을 사용하려면 기본 'cloudowner' 사용자를 이용하여 vRA 및 SRM을 설치하지 말고 새 사용자를 만들어야 합니다. 이는 프라이빗 클라우드 vCenter 환경에 대한 작동 시간 및 가용성을 높이는 데 도움이 됩니다. 그러나 프라이빗 클라우드 vCenter의 기본 cloudowner 사용자에게는 관리자 권한으로 새 사용자를 만들 수 있는 충분한 권한이 없습니다.

vRA 및 SRM을 설치하기 전에 cloudowner 사용자의 vCenter 권한을 높이고 vCenter SSO 도메인에서 관리자 권한이 있는 사용자를 만들어야 합니다. 기본 프라이빗 클라우드 사용자 및 권한 모델에 대한 자세한 내용은 [프라이빗 클라우드 권한 모델 알아보기](learn-private-cloud-permissions.md)를 참조하세요.

설치는 다음과 같은 고급 단계로 구성됩니다.

1. [권한을 높입니다](escalate-private-cloud-privileges.md).
2. 프라이빗 클라우드에서 vSphere 복제 및 SRM 설치를 위한 사용자를 만듭니다. 아래 수록된 [vCenter UI: vRA & SRM 설치를 위해 프라이빗 클라우드에서 사용자 만들기](#vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation)에 설명되어 있습니다.
3. vRA 설치를 위한 프라이빗 클라우드 환경을 준비합니다.
4. vmware.com에서 VR ISO의 OVF를 사용하여 프라이빗 클라우드에 vRA를 배포합니다. vRA 6.5의 경우 [이 VMware 블로그](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices)에 관련 정보가 있습니다.
5. vRA에 대한 방화벽 규칙을 구성합니다. 아래 수록된 [CloudSimple 포털: vRA에 대한 방화벽 규칙 구성](#cloudsimple-portal-configure-firewall-rules-for-vra)에 설명되어 있습니다.
6. 프라이빗 클라우드 사이트에 vCenter Single Sign-On을 사용하여 프라이빗 클라우드 vRA를 등록합니다.
7. 두 어플라이언스 간의 vSphere 복제 연결을 구성합니다. 필요한 포트가 방화벽에서 열려 있는지 확인합니다. vSphere 복제 6.5에 대해 열려 있어야 하는 포트 번호 목록은 [이 VMware 기술 자료 문서](https://kb.vmware.com/s/article/2087769)를 참조하세요.

vSphere 복제 6.5에 대한 자세한 설치 지침은 VMware 문서 [VMware vSphere 복제 6.5 설치 및 구성](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)을 참조하세요.

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation"></a>vCenter UI: vRA & SRM 설치를 위해 프라이빗 클라우드에서 사용자 만들기

CloudSimple 포털에서 권한을 높인 후 cloudowner 사용자 자격 증명을 사용하여 vCenter에 로그인합니다.

vCenter에서 새 사용자 `srm-soln-admin`을 만들고 vCenter의 관리자 그룹에 추가합니다.
cloudowner 사용자는 vCenter에서 로그아웃하고 *srm-soln-admin* 사용자로 로그인합니다.

#### <a name="cloudsimple-portal-configure-firewall-rules-for-vra"></a>CloudSimple 포털: vRA에 대한 방화벽 규칙 구성

[방화벽 테이블 및 규칙 설정](firewall.md)에서 설명한 대로 방화벽 규칙을 구성하여 다음 사이에서 통신을 활성화할 수 있도록 포트를 엽니다.

* SRM 솔루션 네트워크의 vRA 및 관리 네트워크의 vCenter 및 ESXi 호스트.
* 두 사이트의 vRA 어플라이언스.

vSphere 복제 6.5에 대해 열려 있어야 하는 포트 번호 목록은 이 [VMware 기술 자료 문서](https://kb.vmware.com/s/article/2087769)를 참조하세요.

### <a name="install-srm-server-in-your-on-premises-environment"></a>온-프레미스 환경에서 SRM 서버 설치

시작하기 전에 다음을 확인합니다.

* vSphere 복제 어플라이언스는 온-프레미스 및 프라이빗 클라우드 환경에 설치됩니다.
* 두 사이트 모두 vSphere 복제 어플라이언스는 서로 연결되어 있습니다.
* 필수 조건 및 모범 사례에 대한 VMware 정보를 검토했습니다. SRM 6.5의 경우 VMware 문서 [SRM 6.5에 대한 필수 조건 및 모범 사례](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)를 참조할 수 있습니다.

VMware 문서에 따라 이 [VMware 문서](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)에 설명된 대로 배포 모델 ‘두 사이트 토폴로지(Platform Services Controller 당 하나의 vCenter 인스턴스 포함)’에서 SRM 서버를 설치합니다. SRM 6.5에 대한 설치 지침은 VMware 문서 [Site Recovery Manager 설치](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)에서 확인할 수 있습니다.

### <a name="install-srm-server-in-your-private-cloud"></a>프라이빗 클라우드에서 SRM 서버 설치

시작하기 전에 다음을 확인합니다.

* vSphere 복제 어플라이언스는 온-프레미스 및 프라이빗 클라우드 환경에 설치됩니다.
* 두 사이트 모두 vSphere 복제 어플라이언스는 서로 연결되어 있습니다.
* 필수 조건 및 모범 사례에 대한 VMware 정보를 검토했습니다. SRM 6.5의 경우 [Site Recovery Manager 6.5 서버 설치에 대한 필수 조건 및 모범 사례](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)를 참조할 수 있습니다.

다음 단계에서는 프라이빗 클라우드 SRM 설치에 대해 설명합니다.

1. [vCenter UI: SRM 설치](#vcenter-ui-install-srm)
2. [CloudSimple 포털: SRM에 대한 방화벽 규칙 구성](#cloudsimple-portal-configure-firewall-rules-for-srm)
3. [vCenter UI: SRM 구성](#vcenter-ui-configure-srm)
4. [CloudSimple 포털: 권한 내리기](#cloudsimple-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>vCenter UI: SRM 설치

srm-soln-admin 자격 증명을 사용하여 vCenter에 로그인한 후 VMware 문서에 따라 이 [VMware 문서](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)에 설명된 대로 배포 모델 ‘두 사이트 토폴로지(Platform Services Controller 당 하나의 vCenter 인스턴스 포함)’에서 SRM 서버를 설치합니다. SRM 6.5에 대한 설치 지침은 VMware 문서 [Site Recovery Manager 설치](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)에서 확인할 수 있습니다.

#### <a name="cloudsimple-portal-configure-firewall-rules-for-srm"></a>CloudSimple 포털: SRM에 대한 방화벽 규칙 구성

[방화벽 테이블 및 규칙 설정](firewall.md)에서 설명한 대로 방화벽 규칙을 구성하여 다음 사이에서 통신을 허용합니다.

프라이빗 클라우드의 SRM 서버 및 vCenter/PSC
두 사이트의 SRM 서버

vSphere 복제 6.5에 대해 열려 있어야 하는 포트 번호 목록은 [이 VMware 기술 자료 문서](https://kb.vmware.com/s/article/2087769)를 참조하세요.

#### <a name="vcenter-ui-configure-srm"></a>vCenter UI: SRM 구성

SRM이 프라이빗 클라우드에 설치된 후 VMware Site Recovery Manager 설치 및 구성 가이드의 섹션에 설명된 대로 다음 작업을 수행합니다. SRM 6.5에 대한 설치 지침은 VMware 문서 [Site Recovery Manager 설치](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)에서 확인할 수 있습니다.

1. 보호된 사이트와 복구 사이트에서 Site Recovery Manager 서버 인스턴스를 연결합니다.
2. 원격 Site Recovery Manager 서버 인스턴스에 대한 클라이언트 연결을 설정합니다.
3. Site Recovery Manager 라이선스 키를 설치합니다.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>CloudSimple 포털: 권한 내리기

권한을 내리려면 [권한 내리기](escalate-private-cloud-privileges.md#de-escalate-privileges)를 참조하세요.

## <a name="ongoing-management-of-your-srm-solution"></a>SRM 솔루션의 지속적인 관리

프라이빗 클라우드 환경에서 vSphere 복제 및 SRM 소프트웨어를 완전히 제어할 수 있으며 필요한 소프트웨어 수명 주기 관리를 수행할 것으로 예상됩니다. vSphere 복제 또는 SRM을 업데이트하거나 업그레이드하기 전에 새 버전의 소프트웨어가 프라이빗 클라우드 vCenter 및 PSC와 호환되는지 확인합니다.

> [!NOTE]
> 현재 CloudSimple은 관리되는 DR 서비스를 제공하기 위한 옵션을 탐색 중입니다. 

## <a name="multiple-replication-configuration"></a>다중 복제 구성

 [배열 기반 복제와 vSphere 복제 기술은 동시에 SRM과 함께 사용할 수 있습니다](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication). 그러나 별도의 VM 집합에 적용해야 합니다. 지정된 VM은 배열 기반 복제 또는 vSphere 복제를 통해 보호될 수 있지만 둘 다로 보호할 수는 없습니다. 또한 CloudSimple 사이트는 여러 보호된 사이트에 대한 복구 사이트로 구성될 수 있습니다. 다중 사이트 구성에 대한 자세한 내용은 [SRM 다중 사이트 옵션](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/)을 참조하세요.

## <a name="references"></a>참조

* [VMware Site Recovery Manager 문서](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [Site Recovery Manager 6.5에 대한 운영 제한](https://kb.vmware.com/s/article/2147110)
* [vSphere 복제에 대한 대역폭 요구 사항 계산](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)
* [vSphere 복제 6.5 배포 시 OVF 선택 사항](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/)
* [VMware vSphere 복제 6.5 설치 및 구성](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [SRM 6.5에 대한 필수 조건 및 모범 사례](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [Platform Services Controller 당 하나의 vCenter Server 인스턴스가 있는 두 사이트 토폴로지의 Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [VMware Site Recovery Manager 6.5 설치 및 구성 가이드](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [배열 기반 복제 및 vSphere 복제를 사용하는 SRM에 대한 VMware 블로그](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [SRM 다중 사이트 옵션에 대한 VMware 블로그](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [vSphere 복제 5.8.x, 6.x 및 8에 대해 열려 있어야 하는 포트 번호](https://kb.vmware.com/s/article/2147112)
