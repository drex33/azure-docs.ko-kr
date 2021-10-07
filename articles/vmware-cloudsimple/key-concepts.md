---
title: Azure VMware Solution by CloudSimple 관리를 위한 주요 개념
titleSuffix: Azure VMware Solution by CloudSimple
description: Azure VMware Solution by CloudSimple 관리를 위한 주요 개념을 설명합니다.
author: suzizuber
ms.author: v-szuber
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e48df490137357a376f0d546b7d3c16165205d09
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620111"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-by-cloudsimple"></a>Azure VMware Solution by CloudSimple 관리를 위한 주요 개념

Azure VMware Solutions by CloudSimple을 관리하려면 다음과 같은 개념을 이해해야 합니다.

* Azure VMware Solutions by CloudSimple로 표시되는 CloudSimple 서비스 - Service
* Azure VMware Solutions by CloudSimple로 표시되는 CloudSimple 노드 - 노드
* CloudSimple 프라이빗 클라우드
* 서비스 네트워킹
* Azure VMware Solutions by CloudSimple로 표시되는 CloudSimple 가상 머신 - 가상 머신

## <a name="cloudsimple-service"></a>CloudSimple 서비스

CloudSimple 서비스를 사용하면 Azure Portal에서 VMware Solutions by CloudSimple과 연결된 모든 리소스를 만들고 관리할 수 있습니다. 서비스를 사용하려는 모든 지역에 서비스 리소스를 만듭니다.

[CloudSimple 서비스](cloudsimple-service.md)에 대해 자세히 알아보세요.

## <a name="cloudsimple-node"></a>CloudSimple 노드

CloudSimple 노드는 VMware ESXi 하이퍼바이저가 배포되는 전용 운영 체제 미설치 하이퍼컨버지드 컴퓨팅 및 스토리지 호스트입니다. 그런 다음, 이 노드는 VMware vSphere, vCenter, vSAN 및 NSX 플랫폼에 통합됩니다. CloudSimple 네트워킹 서비스 및 에지 네트워킹 서비스도 사용할 수 있습니다. 각 노드는 [CloudSimple 프라이빗 클라우드](cloudsimple-private-cloud.md)를 만들기 위해 프로비전할 수 있는 컴퓨팅 및 스토리지 용량 단위로 사용됩니다. CloudSimple 서비스를 사용할 수 있는 지역에서 노드를 프로비전하거나 예약합니다.

[CloudSimple 노드](cloudsimple-node.md)에 대해 자세히 알아보세요.

## <a name="cloudsimple-private-cloud"></a>CloudSimple 프라이빗 클라우드

CloudSimple 프라이빗 클라우드는 자체 관리 도메인의 vCenter 서버에서 관리하는 격리된 VMware 스택 환경입니다. VMware 스택에는 ESXi 호스트, vSphere, vCenter, vSAN 및 NSX가 포함됩니다. 스택은 전용 노드(전용 및 격리된 운영 체제 미설치 하드웨어)에서 실행되며 사용자는 vCenter 및 NSX 관리자를 포함하는 네이티브 VMware 도구를 통해 사용합니다. 전용 노드는 Azure 위치에 배포되고 Azure에서 관리됩니다. 각 프라이빗 클라우드는 VLAN 및 서브넷 및 방화벽 테이블과 같은 네트워킹 서비스를 사용하여 분할하고 보호할 수 있습니다. 온-프레미스 환경 및 Azure 네트워크에 대한 연결은 보안, 프라이빗 VPN 및 Azure ExpressRoute 연결을 사용하여 생성됩니다.

[CloudSimple 프라이빗 클라우드](cloudsimple-private-cloud.md)에 대해 자세히 알아보세요.

## <a name="service-networking"></a>서비스 네트워킹

CloudSimple 서비스는 CloudSimple 서비스가 배포되는 지역마다 네트워크를 제공합니다. 네트워크는 기본적으로 라우팅이 활성화된 단일 TCP 계층 3 주소 공간입니다. 이 지역에서 만든 모든 프라이빗 클라우드 및 서브넷은 추가 구성 없이 서로 통신합니다. VLAN을 사용하여 vCenter에서 분산 포트 그룹을 만듭니다. 다음 네트워크 기능을 사용하여 프라이빗 클라우드에서 워크로드 리소스를 구성하고 보호할 수 있습니다.

* [VLAN 및 서브넷](cloudsimple-vlans-subnets.md)
* [방화벽 테이블](cloudsimple-firewall-tables.md)
* [VPN 게이트웨이](cloudsimple-vpn-gateways.md)
* [공용 IP](cloudsimple-public-ip-address.md)
* [Azure 네트워크 연결](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>CloudSimple 가상 머신

CloudSimple 서비스를 사용하여 Azure Portal에서 VMware 가상 머신을 관리할 수 있습니다. vSphere 환경에서 하나 이상의 클러스터 또는 리소스 풀을 서비스가 만들어진 구독에 매핑할 수 있습니다.

다음에 대해 자세히 알아보세요.

* [CloudSimple 가상 머신](cloudsimple-virtual-machines.md)
* [Azure 구독 매핑](./azure-subscription-mapping.md)
