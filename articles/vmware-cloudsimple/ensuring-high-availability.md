---
title: Azure에서 VMware를 실행하는 경우 애플리케이션 고가용성 보장
description: CloudSimple 프라이빗 클라우드에서 실행되는 애플리케이션에 관한 일반적인 애플리케이션 오류 시나리오를 해결하기 위한 CloudSimple 고가용성 기능을 설명합니다.
author: suzizuber
ms.author: v-szuber
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3ef9c852370052f5613d7395c7518c6a3f15b8d0
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129614691"
---
# <a name="ensure-application-high-availability-when-running-in-vmware-on-azure"></a>Azure에서 VMware를 실행하는 경우 애플리케이션 고가용성 보장

CloudSimple 솔루션은 Azure 환경에서 VMware를 실행하는 애플리케이션에 고가용성을 제공합니다. 다음 표에서는 오류 시나리오 및 관련된 고가용성 기능을 보여 줍니다.

|  오류 시나리오  |  애플리케이션 보호 여부  |  플랫폼 HA 기능  |  VMware HA 기능  |  Azure HA 기능  |
|----------------------------------------|------------------------|-------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------|
|  디스크 실패  |  YES  |  실패한 노드를 빨리 대체  |  [vSAN 기본 스토리지 정책 정보](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-C228168F-6807-4C2A-9D74-E584CAF49A2A.html)  |  |
|  팬 오류  |  YES  |  중복 팬, 실패한 노드를 빨리 대체  |  |  |
|  NIC 오류  |  YES  |  중복 NIC, 실패한 노드를 빨리 대체  |  |  |
|  호스트 전원 오류  |  YES  |  중복 전원 공급 장치  |  |  |
|  ESXi 호스트 오류  |  YES  |  실패한 노드를 빨리 대체  |  [VMware vSphere 고가용성](https://www.vmware.com/products/vsphere/high-availability.html)  |  |
|  VM 오류  |  YES  |  [부하 분산 장치](load-balancers.md)  |  [VMware vSphere 고가용성](https://www.vmware.com/products/vsphere/high-availability.html)  |  상태 비저장 VMware VM을 위한 Azure Load Balancer  |
|  리프 스위치 포트 오류  |  YES  |  중복 NIC  |  |  |
|  리프 전환 오류  |  YES  |  중복 리프 스위치  |  |  |
|  랙 오류  |  YES  |  배치 그룹  |  |  |
|  온-프레미스 DC를 위한 네트워크 연결  |  YES  |  중복 네트워킹 서비스  |  |  중복 ER 회로  |
|  Azure를 위한 네트워크 연결  |  YES  |  |  |  중복 ER 회로  |
|  데이터 센터 오류  |  YES  |  |  |  가용성 영역  |
|  지역 오류  |  YES  |  |  |  Azure 지역  |

Azure VMware Solution by CloudSimple은 다음과 같은 고가용성 기능을 제공합니다.

## <a name="fast-replacement-of-failed-node"></a>실패한 노드를 빨리 대체

CloudSimple 컨트롤 플레인 소프트웨어는 VMware 클러스터의 상태를 지속해서 모니터링하고 ESXi 노드가 실패할 때를 탐지합니다. 그런 다음 즉시 사용 가능한 노드 풀에서 영향을 받은 VMware 클러스터에 새 ESXi 호스트를 자동으로 추가하고 실패한 노드를 클러스터에서 제외합니다. 이 기능을 사용하면 VMware 클러스터의 예비 용량이 빠르게 복원되어 vSAN 및 VMware HA에서 제공하는 클러스터의 복원력이 복원됩니다.

## <a name="placement-groups"></a>배치 그룹

프라이빗 클라우드를 만드는 사용자는 선택한 지역 내에서 Azure 지역 및 배치 그룹을 선택할 수 있습니다. 배치 그룹은 여러 랙에 분산되지만 동일한 스파인 네트워크 세그먼트 내에 분산된 노드 세트입니다. 동일한 배치 그룹 내의 노드는 최대 두 개의 추가 스위치 홉을 사용하여 서로 연결할 수 있습니다. 배치 그룹은 항상 단일 Azure 가용성 영역 내에 있으며 여러 랙에 걸쳐 있습니다. CloudSimple 컨트롤 플레인은 최상의 노력을 기울여 프라이빗 클라우드의 노드를 여러 랙에 배포합니다. 다른 배치 그룹의 노드는 서로 다른 랙에 배치됩니다.

## <a name="availability-zones"></a>가용성 영역

가용성 영역은 데이터 센터 오류에서 애플리케이션 및 데이터를 보호하는 고가용성 기능입니다. 가용성 영역은 Azure 지역 내에서 특별한 물리적 위치입니다. 각 영역은 독립된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성됩니다. 각 지역에는 하나의 가용성 영역이 있습니다. 자세한 내용은 [Azure에서 가용성 영역이란?](../availability-zones/az-overview.md)을 참조하세요.

## <a name="redundant-azure-expressroute-circuits"></a>중복 Azure ExpressRoute 회로

ExpressRoute를 사용하는 Azure vNet을 위한 데이터 센터 연결은 고가용성의 네트워크 연결 링크를 제공하는 중복 회로를 가집니다.

## <a name="redundant-networking-services"></a>중복 네트워킹 서비스

프라이빗 클라우드를 위한 모든 CloudSimple 네트워킹 서비스(VLAN, 방화벽, 공용 IP 주소, 인터넷, VPN 포함)는 가용성이 뛰어나고 서비스 SLA를 지원할 수 있도록 설계되었습니다.

## <a name="azure-layer-7-load-balancer-for-stateless-vmware-vms"></a>상태 비저장 VMware VM을 위한 Azure 레이어 7 Load Balancer

사용자는 VMware 환경에서 실행 중인 상태 비저장 웹 계층 VM 앞에 Azure 레이어 7 Load Balancer를 배치하여 웹 계층의 고가용성을 획득할 수 있습니다.

## <a name="azure-regions"></a>Azure 지역

Azure 지역은 대기 시간이 정의된 경계 내에 배포되고 전용 지역의 대기 시간이 짧은 네트워크를 통해 연결되는 데이터 센터 세트입니다. 자세한 내용은 [Azure 지역](https://azure.microsoft.com/global-infrastructure/regions)을 참조하세요.
