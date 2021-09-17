---
title: Azure VMware Solution by CloudSimple - 프라이빗 클라우드 사이트를 사용하여 VMware Horizon을 사용하는 가상 데스크톱 인프라 호스트
description: CloudSimple 프라이빗 클라우드 사이트를 사용하여 VMware Horizon을 사용하는 가상 데스크톱 인프라를 호스트하는 방법을 설명합니다.
author: shortpatti
ms.author: v-patsho
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 73435cbc7a630e77f7ed2d7804afd314f68c1a87
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123433227"
---
# <a name="use-cloudsimple-private-cloud-site-to-host-a-virtual-desktop-infrastructure-using-vmware-horizon"></a>CloudSimple 프라이빗 클라우드 사이트를 사용하여 VMware Horizon을 사용하는 가상 데스크톱 인프라 호스트

CloudSimple 프라이빗 클라우드 사이트에서 VMware Horizon 7.x를 사용하는 VDI(가상 데스크톱 인프라)를 호스트할 수 있습니다. 다음 그림은 VDI에 대한 논리적 솔루션 아키텍처를 보여 줍니다.

![Horizon 배포](media/horizon-deployment.png)

이 솔루션을 사용하면 Horizon View Manager 및 App Volume을 완전히 제어할 수 있습니다. 친숙한 UI, API 및 CLI 인터페이스를 사용하면 기존 스크립트 및 도구를 사용하도록 설정할 수 있습니다.

CloudSimple 솔루션을 사용하려면 다음을 수행해야 합니다.

* 프라이빗 클라우드에서 VMware Horizon 7.x를 설치, 구성 및 관리합니다.
* 고유한 Horizon 라이선스를 제공합니다.

## <a name="deploy-the-solution"></a>솔루션 배포

다음 섹션에서는 프라이빗 클라우드에서 Horizon을 사용하는 VDI 솔루션을 배포하는 방법을 설명합니다.

1. [VMware 제품 버전 호환 여부 확인](#verify-that-vmware-product-versions-are-compatible)
2. [데스크톱 환경의 크기 예측](#estimate-the-size-of-your-desktop-environment)
3. [사용자 환경에 맞는 프라이빗 클라우드 만들기](#create-a-private-cloud-for-your-environment)
4. [프라이빗 클라우드에 VMware Horizon 설치](#install-vmware-horizon-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>VMware 제품 버전 호환 여부 확인

* Horizon, App Volume, Unified Access Gateway 및 User Environment Manager의 현재 및 계획된 버전이 서로 호환되고 프라이빗 클라우드의 vCenter 및 PSC와 호환되는지 확인합니다. 호환성 정보는 [Horizon 7.5에 대한 VMware 호환성 매트릭스](https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#interop&260=2877&0=)를 참조하세요.
* 프라이빗 클라우드에 있는 vCenter 및 PSC의 현재 버전을 확인하려면 [CloudSimple 포털](access-cloudsimple-portal.md)에서 **리소스** 로 이동하여 프라이빗 클라우드를 선택한 다음 **vSphere 관리 네트워크** 탭을 클릭합니다.

![vCenter 및 PSC 버전](media/private-cloud-vsphere-versions.png)

### <a name="estimate-the-size-of-your-desktop-environment"></a>데스크톱 환경의 크기 예측

* 식별된 구성이 VMware 작동 제한 내에 있는지 확인합니다.
* 모든 데스크톱 및 Horizon 관리 구성 요소에 필요한 리소스를 예측합니다.

### <a name="create-a-private-cloud-for-your-environment"></a>사용자 환경에 맞는 프라이빗 클라우드 만들기

1. [프라이빗 클라우드 환경 구성](quickstart-create-private-cloud.md)의 지침에 따라 CloudSimple 포털에서 프라이빗 클라우드를 만듭니다.  CloudSimple은 새로 생성된 모든 프라이빗 클라우드에 'cloudowner'라는 기본 vCenter 사용자를 만듭니다. 기본 프라이빗 클라우드 사용자 및 권한 모델에 대한 자세한 내용은 [프라이빗 클라우드 권한 모델 알아보기](learn-private-cloud-permissions.md)를 참조하세요.
2. 프라이빗 클라우드에서 Horizon 관리 평면에 대한 VLAN을 만들어 서브넷 CIDR을 할당합니다. 자세한 내용은 [VLAN/서브넷 만들기 및 관리](create-vlan-subnet.md)를 참조하세요. 이는 모든 솔루션 구성 요소(Unified Access Gateway, Connection Server, App Volume Server 및 User Environment Manager Server)가 설치될 네트워크입니다.
3. 프라이빗 클라우드 vCenter에서 외부 ID 공급자를 사용할지 결정합니다. 그렇다면 다음 옵션 중 하나를 선택합니다.
    * 온-프레미스 Active Directory를 외부 ID 공급자로 사용합니다. 지침은 [vCenter ID 원본](set-vcenter-identity.md)을 참조하세요.
    * 프라이빗 클라우드의 Horizon 관리 평면 VLAN에서 Active Directory 서버를 설정하여 외부 ID 공급자로 사용할 수 있습니다. 지침은 [vCenter ID 원본](set-vcenter-identity.md)을 참조하세요.
    * 프라이빗 클라우드의 Horizon 관리 평면 VLAN에서 DHCP 및 DNS 서버를 설정합니다. 자세한 내용은 [CloudSimple 프라이빗 클라우드에서 DNS 및 DHCP 애플리케이션과 워크로드 설정](dns-dhcp-setup.md)을 참조하세요.
4. 프라이빗 클라우드에 설치된 DNS 서버에서 DNS 전달을 구성합니다. 자세한 내용은 [조건부 전달자 만들기](on-premises-dns-setup.md#create-a-conditional-forwarder)를 참조하세요.

### <a name="install-vmware-horizon-in-your-private-cloud"></a>프라이빗 클라우드에 VMware Horizon 설치

다음 배포 다이어그램은 프라이빗 클라우드에 배포된 Horizon 솔루션을 보여 줍니다. Unified Access Gateway, AD/DC, View 및 App Volume 서버는 사용자가 만든 VLAN 234에 설치됩니다. Unified Access Gateway에는 인터넷에서 연결할 수 있는 할당된 공용 IP 주소가 있습니다. Horizon 데스크톱 풀 VM은 추가 격리 및 보안을 제공하기 위해 VLAN 235에 배포됩니다.

![프라이빗 클라우드에서 Horizon 배포](media/horizon-private-cloud.png)

다음 섹션에서는 그림에 표시된 것과 유사한 배포를 설정하기 위한 지침을 간략하게 설명합니다. 시작하기 전에 다음이 있는지 확인합니다.

* CloudSimple 포털을 사용하여 만들어진, 데스크톱 풀을 실행하기에 충분한 용량이 있는 프라이빗 클라우드.
* 데스크톱의 네트워크 트래픽을 지원하기 위해 온-프레미스 환경과 프라이빗 클라우드 환경 간의 충분한 대역폭.
* 온-프레미스 데이터 센터와 프라이빗 클라우드 간에 설정된 사이트 간 VPN 터널.
* 온-프레미스 환경의 최종 사용자 서브넷에서 CloudSimple 프라이빗 클라우드 서브넷으로의 IP 연결 가능성.
* 프라이빗 클라우드용으로 설치된 AD/DHCP/DNS.

#### <a name="cloudsimple-portal-create-a-dedicated-vlansubnet-for-desktop-pools"></a>CloudSimple 포털: 데스크톱 풀을 위한 전용 VLAN/서브넷 만들기

Horizon 데스크톱 풀에 대한 VLAN을 만들어 서브넷 CIDR을 할당합니다. 자세한 내용은 [VLAN/서브넷 만들기 및 관리](create-vlan-subnet.md)를 참조하세요. 이는 모든 데스크톱 가상 머신이 실행될 네트워크입니다.

표준 보안 모범 사례를 따라 Horizon 배포를 보호합니다.

* 데스크톱 VM에 대한 데스크톱 RDP 트래픽/SSH 트래픽만 허용합니다.
* Horizon 관리 평면 VLAN과 데스크톱 풀 VLAN 간의 관리 트래픽만 허용합니다.
* 온-프레미스 네트워크의 관리 트래픽만 허용합니다.

CloudSimple 포털에서 [방화벽 규칙](firewall.md)을 구성하여 이러한 모범 사례를 적용할 수 있습니다.

#### <a name="cloudsimple-portal-configure-firewall-rules-to-secure-horizon-management-plane"></a>CloudSimple 포털: Horizon 관리 평면을 보호하기 위한 방화벽 규칙 구성

CloudSimple 포털에서 다음 규칙을 설정합니다. 지침은 [방화벽 테이블 및 규칙 설정](firewall.md)을 참조하세요.

1. VMware 문서 [Horizon 포트 목록](https://docs.vmware.com/en/VMware-Horizon-7/7.1/com.vmware.horizon-client-agent.security.doc/GUID-52807839-6BB0-4727-A9C7-EA73DE61ADAB.html)에 나열된 네트워크 포트만 허용되도록 CloudSimple N-S 방화벽에서 방화벽 규칙을 구성하여 온-프레미스 서브넷과 Horizon 관리 VLAN 간의 통신을 허용합니다.

2. 프라이빗 클라우드에서 Horizon 관리 VLAN과 데스크톱 풀 VLAN 간에 E-W 방화벽 규칙을 만듭니다.

#### <a name="cloudsimple-portal-create-a-public-ip-address-for-unified-access-gateway"></a>CloudSimple 포털: Unified Access Gateway에 대한 공용 IP 주소 만들기

인터넷에서 데스크톱 클라이언트 연결을 사용하도록 설정하려면 Unified Access Gateway 어플라이언스에 대한 공용 IP 주소를 만듭니다. 지침은 [공용 IP 주소 할당](public-ips.md)을 참조하세요.

설정이 완료되면 공용 IP 주소가 할당되고 공용 IP 페이지에 나열됩니다.

#### <a name="cloudsimple-portal-escalate-privileges"></a>CloudSimple 포털: 권한 높이기

기본 'cloudowner' 사용자는 프라이빗 클라우드 vCenter에서 Horizon을 설치하기에 충분한 권한이 없으므로 사용자의 vCenter 권한을 에스컬레이션해야 합니다. 자세한 내용은 [권한 에스컬레이션](escalate-private-cloud-privileges.md)을 참조하세요.

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-horizon-installation"></a>vCenter UI: Horizon을 설치하기 위해 Private Cloud에서 사용자 만들기

1. 'cloudowner' 사용자 자격 증명을 사용하여 vCenter에 로그인합니다.
2. vCenter에서 새 사용자 'horizon-soln-admin'을 만들고 vCenter의 관리자 그룹에 사용자를 추가합니다.
3. vCenter에서 'cloudowner' 사용자를 로그아웃하고 'horizon-soln-admin' 사용자로 로그인합니다.

#### <a name="vcenter-ui-install-vmware-horizon"></a>vCenter UI: VMware Horizon 설치

이전 논리적 아키텍처 섹션에서 언급했듯이 Horizon 솔루션에는 다음 구성 요소가 있습니다.

* VMware Horizon 보기View
* VMware Unified Access Gateway
* VMware App Volume Manager
* VMware User Environment Manager

다음과 같이 구성 요소를 설치합니다.

1. VMware 문서 [VMware Unified Access Gateway 배포 및 구성](https://docs.vmware.com/en/Unified-Access-Gateway/3.3.1/com.vmware.uag-331-deploy-config.doc/GUID-F5CE0D5E-BE85-4FA5-BBCF-0F86C9AB8A70.html)에 제공된 지침에 따라 Unified Access Gateway를 설치 및 구성합니다.

2. [View 설치 가이드](https://docs.vmware.com/en/VMware-Horizon-7/7.4/horizon-installation/GUID-37D39B4F-5870-4188-8B11-B6C41AE9133C.html)의 지침에 따라 프라이빗 클라우드에 Horizon View를 설치합니다.

3. [VMware App Volume 설치 및 구성](https://docs.vmware.com/en/VMware-App-Volumes/4/com.vmware.appvolumes.install.doc/GUID-3F92761D-9F83-4610-978C-4DAA55E07D14.html)의 지침에 따라 App Volume Manager를 설치합니다.

4. [VMware User Environment Manager 설치 및 구성 정보](https://docs.vmware.com/en/VMware-User-Environment-Manager/9.4/com.vmware.user.environment.manager-install-config/GUID-DBBC82E4-483F-4B28-9D49-4D28E08715BC.html)의 지침에 따라 User Environment Manager를 설치하고 구성합니다.

#### <a name="file-a-support-request-to-upload-vmware-horizon-pre-packaged-app-volumes"></a>VMware Horizon 사전 패키징된 앱 볼륨을 업로드하기 위한 지원 요청 제출

설치 프로세스의 일부로 App Volume Manager는 사전 패키징된 볼륨을 사용하여 앱 스택 및 쓰기 가능한 볼륨을 프로비저닝합니다. 이러한 볼륨은 앱 스택 및 쓰기 가능한 볼륨에 대한 템플릿 역할을 합니다.

볼륨을 프라이빗 클라우드 데이터 저장소에 업로드하려면 ESXi 루트 암호가 필요합니다. 도움이 필요하면 [지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 제출하세요. CloudSimple 지원 담당자가 템플릿을 프라이빗 클라우드 환경에 업로드할 수 있도록 AppVolumes 설치 프로그램 번들을 연결합니다.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>CloudSimple 포털: 권한 에스컬레이션 해제

이제 'cloudowner' 사용자의 [권한을 에스컬레이션 해제](escalate-private-cloud-privileges.md#de-escalate-privileges)할 수 있습니다.

## <a name="ongoing-management-of-your-horizon-solution"></a>Horizon 솔루션의 지속적인 관리

프라이빗 클라우드 환경에서 Horizon 및 App Volume Manager 소프트웨어를 완전히 제어할 수 있으며 필요한 소프트웨어 수명 주기 관리를 수행해야 합니다. Horizon 또는 App Volume을 업데이트하거나 업그레이드하기 전에 새 버전의 소프트웨어가 프라이빗 클라우드 vCenter 및 PSC와 호환되는지 확인합니다.
