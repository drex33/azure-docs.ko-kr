---
title: FAQ - Azure VMware Solution by CloudSimple
description: Azure VMware Solution by CloudSimple에 대한 질문과 대답에는 연결, 네트워킹, 보안, 스토리지, Azure 통합 등이 포함되어 있습니다.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3551327f9aa07fe218d3b05f73057e36f92abcad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895566"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>VMware Solution by CloudSimple에 대한 질문과 대답

## <a name="cloudsimple-service"></a>CloudSimple 서비스

**Azure VMware Solution by CloudSimple이란?**

Azure VMware Solution by CloudSimple은 몇 분 내에 VMware 워크로드를 변환하여 Azure의 전용 프라이빗 클라우드로 확장할 수 있습니다. CloudSimple은 인프라의 프로비전 및 관리, 온-프레미스와 Azure 간의 워크로드 오케스트레이션을 관리합니다. 앱이 온-프레미스와 Azure에서 동일하게 실행되기 때문에 복잡하게 앱을 다시 설계할 필요 없이 클라우드의 탄력성과 서비스를 활용하는 것이 좋습니다. CloudSimple은 주문형 프로비전, 종량제 및 용량 최적화를 제공하는 클라우드 소비 모델을 사용하여 총 소유 비용을 낮춥니다.  기능, 이점 및 시나리오는 [VMware Solution on Azure by CloudSimple이란?](cloudsimple-vmware-solutions-overview.md)을 참조하세요.

**CloudSimple 프라이빗 클라우드란?**

CloudSimple 프라이빗 클라우드는 Azure 위치의 Microsoft Azure 인프라(하드웨어 및 데이터 센터 공간)에 배포된 고성능 컴퓨팅, 스토리지 및 네트워킹 환경으로 구성된 전용 프라이빗 클라우드입니다.  프라이빗 클라우드는 기본 VMware 'Platform as a Service'를 제공합니다. VMware 사용 약관에 따라 각 프라이빗 클라우드에는 정확히 하나의 vCenter Server 인스턴스가 포함됩니다. vCenter Server는 해당하는 vSAN(가상 SAN) 스토리지와 함께 하나 이상의 vSphere 클러스터에 포함된 여러 ESXi 노드를 관리합니다. CloudSimple 서비스는 Azure 구독에 여러 프라이빗 클라우드를 포함할 수 있습니다.  자세한 내용은 [프라이빗 클라우드 개요](cloudsimple-private-cloud.md)를 참조하세요.

**CloudSimple 서비스는 어디에서 사용할 수 있나요?**

CloudSimple은 미국 동부, 미국 서부 및 서유럽 지역에서 사용할 수 있으며 추가 지역에도 곧 제공될 예정입니다.

**CloudSimple에 대한 구독을 사용하도록 설정하려면 어떻게 할까요?**

[azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com)에서 Microsoft 계정 담당자에게 연락하여 CloudSimple 서비스에 대한 구독을 사용하도록 설정할 수 있습니다. CloudSimple 서비스를 사용하도록 설정할 구독 ID를 이메일로 제공합니다.  

**CloudSimple 포털에 액세스하려면 어떻게 할까요?**

Azure Portal에서 CloudSimple 포털에 액세스합니다.  자세한 내용은 [Azure Portal에서 VMware Solution by CloudSimple 포털에 액세스](access-cloudsimple-portal.md)를 참조하세요.

**프라이빗 클라우드의 용량을 늘리려면 어떻게 할까요?**

용량을 늘리려면 Azure Portal에서 추가 노드를 구매한 후 이 노드를 사용하여 CloudSimple 포털에서 프라이빗 클라우드를 확장합니다.  기존 vSphere 클러스터에 노드를 더 추가하거나 새 vSphere 클러스터에 추가할 수 있습니다.  자세한 내용은 [CloudSimple 프라이빗 클라우드 확장](expand-private-cloud.md)을 참조하세요.

**유지 관리 중에 프라이빗 클라우드는 어떻게 되나요?**

CloudSimple은 예약된 유지 관리 간격에서 며칠 전에 알림을 제공합니다.  유지 관리는 프라이빗 클라우드의 가용성을 보장하는 중단 없는 방법으로 수행됩니다.  유지 관리는 다음 형식일 수 있습니다.

* **CloudSimple 인프라**.  CloudSimple 인프라는 항상 사용 가능하도록 설계되었습니다.  이 형식의 유지 관리 간격 동안에는 중복 구성 요소가 한 번에 하나씩 업데이트되어 서비스 중단을 방지합니다. 프라이빗 클라우드 vCenter, 모든 가상 머신, 프라이빗 클라우드의 인터넷 연결, 온-프레미스 또는 Azure 연결에 대한 액세스를 유지합니다.
* **CloudSimple 포털**. 이 형식의 유지 관리 간격 동안에는 CloudSimple 포털의 일부 기능을 사용하지 못하거나 액세스하지 못할 수 있습니다.  유지 관리 간격 이전의 알림에는 유지 관리를 수행하는 동안 기능 제한에 대한 세부 정보가 포함됩니다.

## <a name="connectivity"></a>연결

**CloudSimple 지역 네트워크에 대한 연결 옵션은 무엇인가요?**

CloudSimple은 CloudSimple 지역 네트워크에 연결할 수 있는 다음과 같은 연결 옵션을 제공합니다. 여러 옵션을 동시에 사용할 수 있습니다.

* **온-프레미스 데이터 센터에서 CloudSimple 지역 네트워크로 ExpressRoute 연결**. 이 연결은 대기 시간이 낮은 고속 보안 프라이빗 연결로, Global Reach을 사용하여 온-프레미스 ExpressRoute 회로를 CloudSimple ExpressRoute 회로에 연결합니다. 연결 설정에 대한 지침은 [ExpressRoute를 사용하여 온-프레미스에서 CloudSimple로 연결](on-premises-connection.md)을 참조하세요.
* **Azure 가상 네트워크에서 CloudSimple 지역 네트워크로 ExpressRoute 연결**. 이 연결은 대기 시간이 낮은 고속 보안 프라이빗 연결로, 가상 네트워크 게이트웨이를 사용하여 Azure의 가상 네트워크를 CloudSimple ExpressRoute 회로에 연결합니다. 연결 설정에 대한 지침은 [ExpressRoute를 사용하여 CloudSimple 프라이빗 클라우드 환경을 Azure 가상 네트워크에 연결](azure-expressroute-connection.md)을 참조하세요.
* **온-프레미스 데이터 센터에서 CloudSimple 지역 네트워크로 사이트 간 VPN 연결**. 온-프레미스 VPN 디바이스에서 CloudSimple 프라이빗 클라우드 지역으로의 안전한 VPN(가상 사설망)입니다.  자세한 내용은 [CloudSimple 네트워크에서 VPN 게이트웨이 설정](vpn-gateway.md)을 참조하세요.

**프라이빗 클라우드에 연결하려면 어떻게 할까요?**

CloudSimple 포털에서 프라이빗 클라우드의 세부 정보를 볼 수 있습니다. 프라이빗 클라우드에 해당하는 vCenter에 연결하려면 먼저 사이트 간 VPN, 지점 및 사이트 간 VPN 또는 ExpressRoute를 사용하여 네트워크 연결이 설정되었는지 확인합니다. 그런 다음, Azure Portal에서 CloudSimple 포털을 시작하고 홈 페이지 또는 프라이빗 클라우드 세부 정보 페이지에서 **vSphere 클라이언트 시작** 을 클릭합니다.

**ExpressRoute 회로의 이점은 무엇인가요?**

Azure ExpressRoute 회로는 대기 시간이 낮은 고속 보안 연결입니다.  CloudSimple은 고객마다 지역별 전용 ExpressRoute 회로를 제공합니다.  이 회로를 사용하여 온-프레미스 또는 Azure 구독에서 보안 연결을 설정할 수 있습니다.

**CloudSimple에 연결하는 데 드는 네트워크 비용은 얼마나 되나요? CloudSimple과 Azure 간에 또는 지역 간에 송신 요금이 적용되나요?**

네트워크 송신에 대한 CloudSimple 요금은 없습니다.  Azure 표준 요금이 가상 네트워크 또는 온-프레미스 ExpressRoute 회로에서의 송신 트래픽에 적용됩니다.

## <a name="networking"></a>네트워킹

**프라이빗 클라우드에 사용할 수 있는 네트워킹 기능은 무엇인가요?**

VLAN과 해당 서브넷 및 방화벽 테이블을 프로비전하고 프라이빗 클라우드에서 실행되는 가상 머신에 매핑되는 공용 IP 주소를 할당할 수 있습니다. 네트워킹 기능에 대한 자세한 내용은 [VLAN 및 서브넷 개요](cloudsimple-vlans-subnets.md), [방화벽 테이블 개요](cloudsimple-firewall-tables.md) 및 [공용 IP 주소 개요](cloudsimple-public-ip-address.md)를 참조하세요.

**프라이빗 클라우드의 내 애플리케이션에 대해 다른 서브넷을 설정하려면 어떻게 할까요?**

CloudSimple 포털의 프라이빗 클라우드에서 VLAN을 만듭니다.  VLAN을 만든 후에는 VLAN을 사용하여 프라이빗 클라우드 vCenter에서 분산 포트 그룹을 만들고 분산 포트 그룹에 연결된 가상 머신을 만들 수 있습니다.  VLAN/서브넷에 대해 방화벽 테이블을 사용하도록 설정하고 네트워크 트래픽을 보호하는 방화벽 규칙을 정의할 수 있습니다.

**프라이빗 클라우드에서 사용할 수 있는 방화벽 설정은 무엇인가요?**

북-남 및 동-서 트래픽에 대한 규칙을 구성할 수 있습니다.  규칙은 방화벽 테이블에 정의됩니다.  방화벽 테이블은 프라이빗 클라우드의 VLAN에 연결할 수 있습니다.  자세한 내용은 [프라이빗 클라우드에 대한 방화벽 테이블 및 규칙 설정](firewall.md)을 참조하세요.

**프라이빗 클라우드 환경에서 VM에 공용 IP 주소를 할당할 수 있나요?**

CloudSimple 포털에서 새 공용 IP 주소를 할당하고 가상 머신 또는 어플라이언스의 개인 IP 주소와 연결할 수 있습니다.  또한 새 방화벽 규칙을 만들거나 기존 방화벽 규칙을 적용하여 포털에서 특정 포트 및 IP 주소의 트래픽을 허용할 수도 있습니다. 자세한 내용은 [프라이빗 클라우드 환경에 공용 IP 주소 할당](public-ips.md)을 참조하세요.

## <a name="security"></a>보안

**CloudSimple에서 내 보안 옵션은 무엇인가요?**

CloudSimple은 프라이빗 클라우드 환경을 보호하기 위한 다음과 같은 보안 기능을 제공합니다.

* **미사용 데이터 암호화**. 프라이빗 클라우드의 vSAN 스토리지에 있는 미사용 데이터를 암호화할 수 있습니다. vSAN은 Azure vNet 또는 온-프레미스 환경에 배포할 수 있는 외부 키 관리 서버를 지원합니다.  자세한 내용은 [CloudSimple 프라이빗 클라우드에 대한 vSAN 암호화 구성](vsan-encryption.md)을 참조하세요.
* **네트워크 보안**. 프라이빗 클라우드와 인터넷 간, 프라이빗 클라우드 및 온-프레미스 환경 간 또는 프라이빗 클라우드의 서브넷 내에서 적용되는 방화벽 규칙을 사용하여 네트워크 트래픽 흐름을 제어합니다.
* **안전한 프라이빗 연결**. 온-프레미스 네트워크와 Azure 구독 간에 안전한 프라이빗 연결을 설정합니다.

## <a name="compute"></a>컴퓨팅

**어떤 종류의 호스트를 사용할 수 있나요?**

CloudSimple은 다음과 같은 호스트 유형을 제공합니다.

* **CS28 노드:** CPU:2x 2.2GHz, 총 28개 코어, 48 HT.  RAM: 256GB.  스토리지: 1600GB NVMe 캐시, 5760GB 데이터(올-플래시). 네트워크: 4x25Gbe NIC
* **CS36 노드:** CPU 2x 2.3GHz, 총 36개 코어, 72 HT.  RAM: 512GB.  스토리지: 3200GB NVMe 캐시, 11520GB 데이터(올-플래시).  네트워크: 4x25Gbe NIC
* **CS36m 노드:** CPU 2x 2.3GHz, 총 36개 코어, 72 HT.  RAM: 576GB.  스토리지: 3200GB NVMe 캐시, 13360GB 데이터(올-플래시).  네트워크: 4x25Gbe NIC

**하드웨어 오류는 어떻게 처리되나요?**

모든 CloudSimple 인프라는 CloudSimple 플랫폼 및 서비스 운영 팀에서 지속적으로 모니터링합니다.  하드웨어 오류가 검색되면 프라이빗 클라우드에 새 노드가 추가되고 실패한 노드는 제거됩니다.

## <a name="storage"></a>스토리지

**프라이빗 클라우드에서 지원되는 스토리지 유형은 무엇인가요?**

CloudSimple은 모든 프라이빗 클라우드에서 올-플래시 VMware vSAN 스토리지를 제공합니다.  각 vSphere는 자체 vSAN 데이터 저장소를 사용하여 생성됩니다.  자세한 내용은 [프라이빗 클라우드 VMware 구성 요소 - vSAN 스토리지](vmware-components.md#vsan-storage)를 참조하세요.

**데이터 암호화가 지원되나요?**
예.  프라이빗 클라우드에서 vSAN 스토리지를 설정하면 온-프레미스 또는 Azure에 배포된 KMS(키 관리 서버)를 사용하여 vSAN에 저장된 데이터를 암호화할 수 있습니다.

**실패한 디스크는 어떻게 처리되나요?**

CloudSimple은 프라이빗 클라우드의 모든 하드웨어 구성 요소를 지속적으로 모니터링합니다.  디스크 오류가 감지되거나 디스크가 실패(추론 기반)로 식별되는 경우 새 노드가 자동으로 프라이빗 클라우드에 추가됩니다.  장애 또는 실패 디스크가 포함된 노드는 프라이빗 클라우드에서 제거됩니다.

## <a name="vmware"></a>VMware

**온-프레미스에서 애플리케이션 및 데이터의 대규모 업로드 또는 마이그레이션을 수행하려면 어떻게 할까요?**

CloudSimple은 기본 VMware vSphere 솔루션을 제공합니다.  대량 데이터 마이그레이션을 위한 모든 VMware 도구를 프라이빗 클라우드와 함께 사용할 수 있습니다.  표시되는 옵션은 다음과 같습니다.

* 데이터를 대량으로 마이그레이션하기 위한 VMware HCX
* 온-프레미스에서 CloudSimple로 Storage vMotion을 사용하여 데이터 콜드 마이그레이션

**VMware 도구를 내가 설치할 수 있나요?**

CloudSimple은 기본 VMware vSphere 솔루션을 제공합니다.  온-프레미스 vSphere 환경을 관리하는 데 사용되는 모든 VMware 도구를 CloudSimple에서 사용할 수 있습니다.  CloudSimple은 VMware 도구를 설치하기 위한 BYOL(사용자 라이선스 필요) 모델을 지원합니다.

**업데이트 및 업그레이드는 어떻게 관리되나요?**

CloudSimple은 중단 없고 원활한 방식으로 프라이빗 클라우드의 모든 인프라 구성 요소를 관리하고 업데이트합니다.  VMware 또는 인프라 공급업체에서 릴리스된 모든 업데이트 및 보안 패치는 CloudSimple로 한정되는 즉시 업데이트되도록 예약됩니다.

CloudSimple은 프라이빗 클라우드에 설치된 애플리케이션의 업그레이드 또는 업데이트를 수행하지 않습니다.

## <a name="azure-integration"></a>Azure 통합

**어떤 Azure 서비스가 지원되나요?**

CloudSimple은 Azure에서 구독에 대한 Azure ExpressRoute 연결을 제공합니다.  구독에서 실행 중인 모든 서비스는 프라이빗 클라우드에 연결할 수 있습니다.  다음은 이러한 템플릿의 예입니다.

* CloudSimple vCenter의 ID 원본으로서 **Azure Active Directory**
* 프라이빗 클라우드의 백업, 이미지 및 기타 데이터를 저장하기 위한 **Azure Storage**
* 퍼블릭 클라우드 및 프라이빗 클라우드에 걸쳐 있는 애플리케이션 아키텍처를 사용하는 **하이브리드 애플리케이션**.  예를 들어 Azure에서 프라이빗 클라우드의 애플리케이션 및 데이터베이스 서버에 액세스하는 웹 서버를 만들 수 있습니다.
* VMware 지원 로깅, 성능 메트릭 및 보안 관리에서 실행되는 워크로드를 위한 **Azure Monitor** 및 **Azure Security Center**

**VMware 테넌트를 Azure에 매핑하려면 어떻게 할까요?**

CloudSimple은 Azure Portal에서 프라이빗 클라우드의 VMware VM을 관리하는 고유한 기능을 제공합니다.  필요한 리소스 제약 조건으로 구성된 vCenter 리소스 풀을 전역 관리자가 구독에 매핑할 수 있습니다.  

**Azure에서 제공하는 라이선스 혜택은 무엇인가요?**

CloudSimple을 사용하면 Azure 하이브리드 사용 혜택을 활용하여 라이선스에 대해 최대 90%를 절감할 수 있습니다. 이 혜택을 통해 Microsoft 라이선스에 대한 투자를 보존하고 다른 클라우드 솔루션을 기준으로 TCO를 절감할 수 있습니다. Windows Server 2008 및 Microsoft SQL Server 2008에 대한 확장 보안 업데이트도 제공됩니다.  BYOL(사용자 라이선스 필요) 모델을 사용하면 Veeam 및 Zerto와 같은 일반적인 앱에 대한 비용을 낮게 유지할 수 있습니다.  
