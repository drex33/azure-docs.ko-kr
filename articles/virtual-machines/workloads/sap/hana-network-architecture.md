---
title: Azure(대규모 인스턴스)에서 SAP HANA 네트워크 아키텍처 | Microsoft Docs
description: Azure(대규모 인스턴스)에서 SAP HANA를 배포하기 위한 네트워크 아키텍처에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/21/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5add931f71dfdb5034e614b3d6c3ddc8703293a2
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114461575"
---
# <a name="sap-hana-large-instances-network-architecture"></a>SAP HANA(대규모 인스턴스) 네트워크 아키텍처

이 문서에서는 Azure의 SAP HANA(대규모 인스턴스)(BareMetal Infrastructure라고도 함)를 배포하기 위한 네트워크 아키텍처를 살펴보겠습니다. 

Azure 네트워크 서비스의 아키텍처는 SAP 애플리케이션을 HANA 대규모 인스턴스에 성공적으로 배포하는 핵심 구성 요소입니다. 일반적으로 Azure(대규모 인스턴스) 배포의 SAP HANA에는 더 큰 SAP 환경이 있습니다. 여기에는 다양한 크기의 데이터베이스, CPU 리소스 사용 및 메모리 사용을 가진 여러 SAP 솔루션이 포함될 수 있습니다. 

일부 IT 시스템이 아직 Azure에 없을 가능성이 있습니다. SAP 환경도 하이브리드일 수 있습니다. DBMS(데이터베이스 관리 시스템) 및 SAP 애플리케이션은 NetWeaver, S/4HANA 및 SAP HANA를 혼합하여 사용할 수 있습니다. SAP 애플리케이션은 다른 DBMS를 사용할 수도 있습니다.

Azure는 DBMS, NetWeaver 및 S/4HANA 시스템을 실행할 수 있는 다양한 서비스를 제공합니다. Azure를 온-프레미스 소프트웨어 배포에 대한 가상 데이터 센터처럼 보이도록 하는 네트워크 기술을 제공합니다. Azure 네트워크 기능은 다음과 같습니다. 

- Azure 가상 네트워크는 온-프레미스 네트워크 자산에 연결되는 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 회로에 연결됩니다.
- 온-프레미스를 Azure에 연결하는 ExpressRoute 회로(최소한 [1Gbps 이상](https://azure.microsoft.com/pricing/details/expressroute/)의 대역폭). 이 회로를 통해 온-프레미스 시스템과 VM(가상 머신)에서 실행되는 시스템 간의 데이터 전송에 적합한 대역폭이 허용됩니다. 또한 온-프레미스 사용자의 Azure 시스템에 연결할 수 있는 적절한 대역폭도 허용됩니다.
- 서로 통신할 수 있도록 가상 네트워크에 설치된 Azure의 모든 SAP 시스템.
- 온-프레미스에서 호스팅되는 Active Directory 및 DNS는 ExpressRoute를 통해 온-프레미스에서 Azure로 확장됩니다. Azure에서 완전히 실행될 수도 있습니다.

Azure 데이터 센터 네트워크 패브릭에 HANA 대규모 인스턴스를 통합할 때 Azure ExpressRoute 기술 또한 사용됩니다.


> [!NOTE] 
> 특정 Azure 지역의 HANA 대규모 인스턴스 스탬프에 있는 하나의 테넌트에는 하나의 Azure 구독만 연결할 수 있습니다. 역으로 단일 HANA 대규모 인스턴스 스탬프 테넌트는 하나의 Azure 구독에만 연결할 수 있습니다. 이 요구 사항은 Azure에서 청구 가능한 다른 개체와 일치합니다.

여러 Azure 지역에 Azure의 SAP HANA(대규모 인스턴스)가 배포된 경우 HANA 대규모 인스턴스 스탬프의 별도의 테넌트가 배포됩니다. 이러한 인스턴스가 동일한 SAP 환경의 일부인 경우 동일한 Azure 구독에서 둘 다 실행할 수 있습니다. 

> [!IMPORTANT] 
> Azure (대규모 인스턴스)의 SAP HANA에서는 Azure Resource Manager 배포 메서드만 지원됩니다.

## <a name="extra-virtual-network-information"></a>추가 가상 네트워크 정보

가상 네트워크를 ExpressRoute에 연결하려면 Azure ExpressRoute 게이트웨이를 생성해야 합니다. 자세한 내용은 [ExpressRoute에 대한 가상 네트워크 게이트웨이 정보](../../../expressroute/expressroute-about-virtual-network-gateways.md)를 참조하세요. 

Azure 외부의 인프라 또는 Azure 대규모 인스턴스 스탬프에 ExpressRoute와 함께 사용되는 Azure ExpressRoute 게이트웨이. Azure ExpressRoute 게이트웨이를 최대 4개의 ExpressRoute 회로에 연결할 수 있지만 이러한 연결이 서로 다른 MSEE(Microsoft Enterprise Edge Router)에서 발생하는 경우에만 가능합니다. 자세한 내용은 [SAP HANA on Azure(대규모 인스턴스) 인프라 및 연결](hana-overview-infrastructure-connectivity.md)을 참조하세요. 

> [!NOTE] 
> Express 경로 게이트웨이를 사용하여 달성할 수 있는 최대 처리량은 ExpressRoute 연결을 사용할 경우 10gbps입니다. 가상 네트워크에 있는 VM과 온-프레미스 시스템 간에 파일을 복사하는 경우(단일 복사 스트림으로), 다양한 게이트웨이 SKU의 전체 처리량을 달성할 수 없습니다. ExpressRoute 게이트웨이의 전체 대역폭을 활용하려면 여러 스트림을 사용하거나 서로 다른 파일을 단일 파일의 병렬 스트림으로 복사해야 합니다.


## <a name="networking-architecture-for-hana-large-instance"></a>HANA 대규모 인스턴스에 대한 네트워킹 아키텍처
HANA 대규모 인스턴스에 대한 네트워킹 아키텍처는 다음 네 가지 부분으로 구분할 수 있습니다.

- Azure에 대한 온-프레미스 네트워킹 및 ExpressRoute 연결. 이 부분은 고객의 도메인이며 ExpressRoute를 통해 Azure에 연결됩니다. 이 ExpressRoute 회로는 사용자가 모든 비용을 지불합니다. 대역폭은 온-프레미스 자산과 연결 중인 Azure 지역 간의 네트워크 트래픽을 처리할 수 있을 만큼 커야 합니다. 다음 그림의 오른쪽 아래 부분을 참조하세요.
- 앞서 설명한 것처럼 가상 네트워크를 사용하는 Azure 네트워크 서비스는 ExpressRoute 게이트웨이를 다시 추가해야 합니다. 이 부분의 경우 애플리케이션, 보안 및 규정 준수 요구 사항을 충족하는 적절한 디자인을 만들어야 합니다. 선택할 가상 네트워크 및 Azure 게이트웨이 SKU의 수를 고려하여 HANA 대규모 인스턴스를 사용할지 여부를 고려합니다. 그림의 오른쪽 위 부분을 참조하세요.
- Azure에 ExpressRoute를 통해 HANA 대규모 인스턴스 연결. 이 부분은 Microsoft에서 배포하고 처리합니다. HANA 대규모 인스턴스에 자산을 배포한 후 ExpressRoute 회로를 가상 네트워크에 연결하기 위한 일부 IP 주소 범위를 제공하기만 하면 됩니다. 자세한 내용은 [SAP HANA on Azure(대규모 인스턴스) 인프라 및 연결](hana-overview-infrastructure-connectivity.md)을 참조하세요. Azure 데이터 센터 네트워크 패브릭과 HANA 대규모 인스턴스 단위 간의 연결에 대한 추가 요금은 없습니다.
- HANA(대규모 인스턴스) 스탬프 내 네트워킹. 대부분 사용자에게 투명합니다.

![SAP HANA on Azure(대규모 인스턴스) 및 온-프레미스에 연결된 가상 네트워크](./media/hana-overview-architecture/image1-architecture.png)

Hana 대규모 인스턴스를 사용하더라도 다음 두 가지 요구 사항은 여전히 유효합니다.
- 온-프레미스 자산은 ExpressRoute를 통해 Azure에 연결해야 합니다.
- VM을 실행하는 하나 이상의 가상 네트워크가 필요합니다. 이러한 VM은 HANA 대규모 인스턴스에서 호스팅되는 HANA 인스턴스에 연결하는 애플리케이션 계층을 호스팅합니다.

Azure에서 SAP 배포의 차이점은 다음과 같습니다.

- 테넌트의 HANA 대규모 인스턴스 장치는 또 다른 ExpressRoute 회로를 통해 가상 네트워크에 연결됩니다. 온-프레미스에서 Azure 가상 네트워크로의 ExpressRoute 회로 및 Azure 가상 네트워크와 HANA 대규모 인스턴스 간의 회로는 동일한 라우터를 공유하지 않습니다. 로드 조건은 별도로 유지됩니다.
- SAP 애플리케이션 계층과 HANA 대규모 인스턴스 간의 워크로드 프로필은 다른 특성을 가지고 있습니다. SAP HANA는 애플리케이션 계층으로의 데이터 전송(결과 집합)과 같은 많은 작은 요청과 버스트를 생성합니다.
- SAP 애플리케이션 아키텍처는 온-프레미스와 Azure 간에 데이터가 교환되는 일반적인 시나리오보다 네트워크 대기 시간에 더 민감합니다.
- Azure ExpressRoute 게이트웨이에는 적어도 두 개의 ExpressRoute 연결이 있습니다. 온-프레미스에서 연결된 회로 및 HANA 대규모 인스턴스에서 연결된 회로 이 구성은 ExpressRoute 게이트웨이에 연결할 다른 MSEE의 회로 두 개를 위한 공간만 남깁니다. 이 제한은 ExpressRoute FastPath의 사용과는 무관합니다. 연결된 모든 회로는 ExpressRoute 게이트웨이의 수신 데이터에 대한 최대 대역폭을 공유합니다.

HANA 대규모 인스턴스 수정 버전 3을 통해 VM과 HANA 대규모 인스턴스 장치 간에 발생하는 네트워크 대기 시간이 일반적인 VM 간 네트워크 왕복 지연 시간보다 길어질 수 있습니다. Azure 지역에 따라 값은 [SAP Note #1100926 - FAQ: 네트워크 성능](https://launchpad.support.sap.com/#/notes/1100926/E)에서 평균 미만으로 분류된 0.7ms 왕복 대기 시간을 초과할 수 있습니다. Azure 지역 및 Azure VM과 HANA 대규모 인스턴스 간의 네트워크 왕복 대기 시간을 측정하는 도구에 따라 대기 시간은 최대 2밀리초가 될 수 있습니다. 그런데도 고객은 SAP HANA 기반 프로덕션 SAP 애플리케이션을 SAP HANA 대규모 인스턴스에 성공적으로 배포합니다. Azure HANA 대규모 인스턴스에서 자신의 비즈니스 프로세스를 철저하게 테스트해야 합니다. Express Route FastPath라는 새로운 기능을 통해 Azure의 HANA 대규모 인스턴스 및 애플리케이션 계층 VM 간의 네트워크 대기 시간을 크게 줄일 수 있습니다(아래 참조). 

HANA 대규모 인스턴스 스탬프의 버전 4는 HANA 대규모 인스턴스 스탬프에 근접하여 배포된 Azure VM 간의 네트워크 대기 시간을 개선합니다. Azure ExpressRoute FastPath가 구성된 경우 대기 시간은 [SAP Note #1100926 - FAQ: 네트워크 성능](https://launchpad.support.sap.com/#/notes/1100926/E)에 설명된 평균 또는 평균 이상의 분류를 충족합니다(아래 참조). 

버전 4의 HANA 대규모 인스턴스 근처에 Azure VM을 배포하려면 [Azure 근접 배치 그룹](../../co-location.md)을 적용해야 합니다. 근접 배치 그룹을 사용하여 버전 4에서 호스팅하는 HANA 대규모 인스턴스와 동일한 Azure 데이터 센터에서 SAP 애플리케이션 계층을 찾을 수 있습니다. 자세한 내용은 [SAP 애플리케이션에서 최적의 네트워크 대기 시간을 위한 Azure 근접 배치 그룹](sap-proximity-placement-scenarios.md)을 참조하세요.

VM과 HANA 대규모 인스턴스 간에 결정적인 네트워크 대기 시간을 제공하려면 ExpressRoute 게이트웨이 SKU 사용이 중요합니다. 온-프레미스와 VM 간의 트래픽 패턴과는 달리, VM과 HANA 대규모 인스턴스 간의 트래픽 패턴은 작지만 높은 버스트의 요청 및 데이터 볼륨을 개발할 수 있습니다. 이러한 버스트를 잘 처리하려면 UltraPerformance 게이트웨이 SKU를 사용하는 것이 좋습니다. HANA 대규모 인스턴스 Sku의 Type II 클래스의 경우 UltraPerformance 게이트웨이 SKU를 ExpressRoute 게이트웨이로 사용해야 합니다.

> [!IMPORTANT] 
> SAP 애플리케이션과 데이터베이스 계층 간의 전체 네트워크 트래픽을 고려할 때, SAP HANA on Azure(대규모 인스턴스) 연결에는 가상 네트워크용 HighPerformance 또는 UltraPerformance 게이트웨이 SKU만 지원됩니다. HANA Large Instance Type II SKU의 경우 Ultra Performance 게이트웨이 SKU만 ExpressRoute 게이트웨이로 지원됩니다. ExpressRoute FastPath를 사용하는 경우 예외가 적용됩니다(아래 참조).

### <a name="expressroute-fastpath"></a>ExpressRoute FastPath
2019년 5월에 ExpressRoute FastPath를 릴리스했습니다. FastPath는 HANA 대규모 인스턴스와 SAP 애플리케이션 VM을 호스팅하는 Azure 가상 네트워크 간의 대기 시간을 줄입니다. FastPath를 사용하면 VM과 HANA 대규모 인스턴스 간의 데이터 흐름이 ExpressRoute 게이트웨이를 통해 라우팅되지 않습니다. Azure 가상 네트워크의 서브넷에 할당된 VM이 전용 엔터프라이즈 에지 라우터와 직접 통신합니다. 

> [!IMPORTANT] 
> ExpressRoute FastPath를 사용하려면 SAP 애플리케이션 VM을 실행하는 서브넷이 HANA 대규모 인스턴스에 연결된 동일한 Azure 가상 네트워크에 있어야 합니다. HANA 대규모 인스턴스 장치에 연결된 Azure 가상 네트워크와 피어링된 Azure 가상 네트워크에 위치한 VM은 ExpressRoute FastPath 기능을 사용할 수 없습니다. 따라서 ExpressRoute 회로가 허브 가상 네트워크에 연결되어 있고 SAP 애플리케이션 계층(스포크)이 포함된 가상 네트워크에 연결되어 있는 경우 ExpressRoute FastPath에 의한 최적화는 작동하지 않습니다. ExpressRoute FastPath는 현재 UDR(사용자 정의 라우팅 규칙)도 지원하지 않습니다. 자세한 내용은 [ExpressRoute 가상 네트워크 게이트웨이 및 FastPath](../../../expressroute/expressroute-about-virtual-network-gateways.md)를 참조하세요. 


ExpressRoute FastPath를 구성하는 방법에 대한 자세한 내용은 [가상 네트워크를 HANA 대규모 인스턴스에 연결](./hana-connect-vnet-express-route.md)을 참조하세요.    

> [!NOTE]
> ExpressRoute FastPath를 사용하려면 UltraPerformance ExpressRoute 게이트웨이가 필요합니다.


## <a name="single-sap-system"></a>단일 SAP 시스템

앞에서 보여 준 온-프레미스 인프라는 ExpressRoute를 통해 Azure에 연결됩니다. ExpressRoute 회로는 MSEE에 연결됩니다. 자세한 내용은 [ExpressRoute 기술 개요](../../../expressroute/expressroute-introduction.md)를 참조하세요. 경로가 설정되면 Azure 백본에 연결됩니다.

> [!NOTE] 
> Azure에서 SAP 자산을 실행하려면 SAP 자산에서 Azure 지역과 가장 가까운 엔터프라이즈 에지 라우터에 연결합니다. Azure 대규모 인스턴스 스탬프는 전용 엔터프라이즈 에지 라우터를 통해 연결되어 Azure IaaS 및 HANA 대규모 인스턴스 스탬프에서 VM 간의 네트워크 대기 시간을 최소화합니다.

SAP 애플리케이션 인스턴스를 호스팅하는 VM 용 ExpressRoute 게이트웨이는 온-프레미스 네트워크에 연결되는 하나의 ExpressRoute 회로에 연결됩니다. 동일한 가상 네트워크가 별도의 엔터프라이즈 에지 라우터에 연결됩니다. 해당 에지 라우터는 대규모 인스턴스 스탬프 연결 전용입니다. 다시 말하지만, FastPath를 사용하면 HANA 대규모 인스턴스에서 SAP 애플리케이션 계층 VM으로의 데이터 흐름이 ExpressRoute 게이트웨이를 통해 라우팅되지 않습니다. 이 구성은 네트워크 왕복 대기 시간을 줄입니다.

이 시스템은 단일 SAP 시스템의 간단한 예입니다. SAP 애플리케이션 계층은 Azure에서 호스팅됩니다. SAP HANA 데이터베이스는 SAP HANA on Azure(대규모 인스턴스)에서 실행됩니다. 2Gbps 또는 10Gbps 처리량의 ExpressRoute 게이트웨이 대역폭에서는 병목 현상이 나타나지 않는다고 가정합니다.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>여러 SAP 시스템 또는 큰 SAP 시스템

SAP HANA(대규모 인스턴스)에 연결하기 위해 여러 SAP 시스템 또는 대형 SAP 시스템을 배포하는 경우, ExpressRoute 게이트웨이의 처리량으로 인해 병목 현상이 발생할 수 있습니다. 이 경우 애플리케이션 계층을 여러 가상 네트워크로 분할합니다. 프로덕션 및 비프로덕션 시스템을 서로 다른 Azure 가상 네트워크에서 격리하려는 경우 애플리케이션 계층을 분할할 수도 있습니다. 

다음과 같은 경우 HANA 대규모 인스턴스에 연결하는 특수 가상 네트워크를 만들 수 있습니다.

- HANA 대규모 인스턴스의 HANA 인스턴스에서 NFS 공유를 호스팅하는 Azure의 VM으로 백업을 직접 수행합니다.
- 큰 백업 또는 HANA 대규모 인스턴스의 기타 파일을 Azure에서 관리하는 디스크 공간으로 복사합니다.

별도의 가상 네트워크를 사용하여 HANA 대규모 인스턴스와 Azure 간에 데이터를 대량으로 전송하기 위해 스토리지를 관리하는 VM을 호스팅합니다. 이 정렬은 SAP 애플리케이션 계층을 실행하는 VM을 제공하는 ExpressRoute 게이트웨이에서 HANA 대규모 인스턴스에서 Azure로의 대용량 파일 또는 데이터 전송을 방지합니다. 

보다 확장 가능한 네트워크 아키텍처의 경우:

- 규모가 큰 단일 SAP 애플리케이션 레이어에는 여러 가상 네트워크를 사용합니다.
- 동일한 가상 네트워크 하에서 별도의 서브넷에 있는 이러한 SAP 시스템을 결합하는 것과 비교하여 배포된 각 SAP 시스템에 대해 별도의 가상 네트워크를 하나씩 배포합니다.

    다음 다이어그램은 Azure(대규모 인스턴스)의 SAP HANA에 대한 보다 확장 가능한 네트워킹 아키텍처를 보여 줍니다.

![여러 가상 네트워크를 통해 SAP 애플리케이션 계층 배포](./media/hana-overview-architecture/image4-networking-architecture.png)

규칙 및 제한 사항에 따라, 서로 다른 SAP 시스템의 VM을 호스팅하는 여러 가상 네트워크 간에 적용하려는 경우 해당 가상 네트워크를 피어링해야 합니다. 가상 네트워크 피어링에 대한 자세한 내용은 [가상 네트워크 피어링](../../../virtual-network/virtual-network-peering-overview.md)을 참조하세요.


## <a name="routing-in-azure"></a>Azure에서 라우팅

Azure의 SAP HANA(대규모 인스턴스)의 네트워크 라우팅에 대해 고려해야 할 세 가지 중요한 사항은 다음과 같습니다.

- Azure(Large Instance)의 SAP HANA는 VM 및 전용 ExpressRoute 연결을 통해서만 액세스할 수 있으며, 온-프레미스에서 직접 액세스 할 수 없습니다. Microsoft에서 제공하는 것처럼 온-프레미스에서 HANA 대규모 인스턴스 장치에 직접적으로 즉시 액세스할 수 없습니다. 전이적 라우팅 제한은 SAP HANA 대규모 인스턴스에 사용되는 현재 Azure 네트워크 아키텍처로 인해 발생합니다. 온-프레미스에서 실행되는 SAP 솔루션 관리자와 같이 직접 액세스가 필요한 관리 클라이언트 및 애플리케이션 일부는 SAP HANA 데이터베이스에 연결할 수 없습니다. 예외는 [HANA 대규모 인스턴스로 직접 라우팅](#direct-routing-to-hana-large-instances) 섹션을 참조하세요.

- 재해 복구를 위해 두 개의 다른 Azure 지역에 배포된 HANA 대규모 인스턴스 장치가 있는 경우 과거 동일한 임시 라우팅 제한이 적용됩니다. 즉, 한 지역(예: 미국 서부)에 있는 HANA 대규모 인스턴스의 IP 주소는 다른 지역(예: 미국 동부)에 배포된 HANA 대규모 인스턴스로 라우팅되지 않습니다. 이 제한은 지역 간에 또는 HANA 대규모 인스턴스를 가상 네트워크에 연결하는 ExpressRoute 회로 연결 간의 Azure 네트워크 피어링을 사용하는 것과는 독립적입니다. 그래픽 표현은 [여러 지역에서 HANA 대규모 인스턴스 장치 사용](#use-hana-large-instance-units-in-multiple-regions) 섹션의 그림을 참조하세요. 배포된 아키텍처와 함께 제공되는 이 제한은 HANA 시스템 복제를 재해 복구로 즉시 사용하지 못하게 합니다. 최근 변경 사항은 [여러 지역에서 HANA 대규모 인스턴스 장치 사용](#use-hana-large-instance-units-in-multiple-regions)을 참조하세요. 

- Azure의 SAP HANA(대규모 인스턴스)에는 HANA 대규모 인스턴스 배포를 요청할 때 제출한 서버 IP 풀 주소 범위에서 할당된 IP 주소가 있습니다. 자세한 내용은 [SAP HANA on Azure(대규모 인스턴스) 인프라 및 연결](hana-overview-infrastructure-connectivity.md)을 참조하세요. 이 IP 주소는 Azure 구독 및 Azure 가상 네트워크를 HANA 대규모 인스턴스에 연결하는 회로를 통해 액세스할 수 있습니다. 해당 서버 IP 풀 주소 범위 이외의 할당된 IP 주소는 하드웨어 장치에 직접 할당됩니다. 이 솔루션의 첫 번째 배포의 경우처럼 더 이상 NAT(네트워크 주소 변환)를 통해 할당되지 *않습니다*. 

### <a name="direct-routing-to-hana-large-instances"></a>HANA Large Instances로 직접 라우팅

기본적으로 전이적 라우팅은 다음과 같은 시나리오에서 작동하지 않습니다.

- HANA Large Instance 장치와 온-프레미스 배포 사이.

- 다른 지역에 배포된 HANA 대규모 인스턴스 장치 사이.

이러한 시나리오에서 전이적 라우팅을 활성화하는 세 가지 방법은 다음과 같습니다.

- 데이터를 라우팅하는 역방향 프록시를 사용합니다. 예를 들어 가상 방화벽/트래픽 라우팅 솔루션으로 HANA 대규모 인스턴스와 온-프레미스에 연결되어 Azure 가상 네트워크에 배포된 F5 BIG-IP, NGINX with TrafficManager.
- Linux VM에서 IPTables 규칙을 사용하여 온-프레미스 위치와 HANA 대규모 인스턴스 장치 간 또는 다른 지역의 HANA 대규모 인스턴스 장치 간에 라우팅을 사용하도록 설정합니다. IPTable을 실행하는 VM은 HANA 대규모 인스턴스 및 온-프레미스 네트워크에 연결하는 Azure 가상 네트워크에 배포되어야 합니다. VM의 네트워크 처리량이 예상 네트워크 트래픽에 충분하도록 VM의 크기를 적절하게 조정해야 합니다. VM 네트워크 대역폭에 대한 자세한 내용은 [Azure의 Linux 가상 머신 크기](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)문서를 참조하세요.
- [Azure Firewall](https://azure.microsoft.com/services/azure-firewall/)은 온-프레미스와 HANA Large instance 장치 간의 직접 트래픽을 허용하는 또 다른 솔루션입니다. 

이러한 솔루션의 모든 트래픽은 Azure 가상 네트워크를 통해 라우팅됩니다. 따라서 트래픽은 사용되는 소프트 어플라이언스 또는 Azure 네트워크 보안 그룹에 의해 제한될 수도 있습니다. 이러한 방식으로 온-프레미스의 특정 IP 주소 또는 IP 주소 범위를 차단하거나 HANA 대규모 인스턴스에 대한 액세스를 명시적으로 허용할 수 있습니다. 

> [!NOTE]  
> Microsoft에서는 타사 네트워크 어플라이언스 또는 IPTables와 관련된 사용자 지정 솔루션에 대한 구현 및 지원을 제공하지 않습니다. 지원은 사용되는 구성 요소의 공급업체 또는 통합자에서 제공해야 합니다. 

#### <a name="express-route-global-reach"></a>Express Route Global Reach
Microsoft는 [ExpressRoute GlobalRete](../../../expressroute/expressroute-global-reach.md)라는 새 기능을 도입했습니다. Global Reach는 다음 두 가지 시나리오에서 HANA 대규모 Instance에 사용할 수 있습니다.

- 온-프레미스에서 여러 지역에 배포된 HANA 대규모 인스턴스 장치에 직접 액세스를 사용합니다.
- 여러 지역에 배포된 HANA 대규모 인스턴스 장치 간에 직접 통신을 사용합니다.


##### <a name="direct-access-from-on-premises"></a>온-프레미스에서 직접 액세스
Global Reach가 제공되는 Azure 지역에서는 ExpressRoute 회로에 대해 Global Reach 사용을 요청할 수 있습니다. 해당 회로는 온-프레미스 네트워크를 HANA 대규모 인스턴스에 연결하는 Azure 가상 네트워크에 연결합니다. ExpressRoute 회로의 온-프레미스 측에 비용이 발생합니다. 자세한 내용은 [Global Reach 추가 기능](https://azure.microsoft.com/pricing/details/expressroute/)에 대한 가격 책정을 참조하세요. HANA 대규모 인스턴스를 Azure에 연결하는 회로에 대해 추가 비용을 지불하지 않습니다. 

> [!IMPORTANT]  
> Global Reach를 사용하여 HANA 대규모 인스턴스 단위와 온-프레미스 자산 간의 직접 액세스를 사용하는 경우 네트워크 데이터 및 제어 흐름은 **Azure 가상 네트워크를 통해 라우팅되지 않습니다**. 대신 네트워크 데이터 및 제어 흐름이 Microsoft 엔터프라이즈 교환 라우터 간에 직접 라우팅됩니다. 따라서 NSG 또는 ASG 규칙 또는 Azure 가상 네트워크에 배포된 모든 유형의 방화벽, NVA 또는 프록시는 작업을 수행하지 않습니다. **ExpressRoute Global Reach를 사용하여 온-프레미스에서 HANA 대규모 인스턴스 장치로 직접 액세스할 수 있는 경우 먼저 HANA 대규모 인스턴스 장치에 액세스할 수 있는 권한을 정의해야 합니다.** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>다른 Azure 지역에서 HANA Large Instances 연결
유사하게 ExpressRoute Global Reach를 사용하여 서로 다른 지역에 배포된 두 개의 HANA 대규모 인스턴스 테넌트를 연결할 수도 있습니다. 격리는 HANA 대규모 인스턴스 테넌트가 두 지역의 Azure와 연결하기 위해 사용하는 ExpressRoute 회로입니다. 다른 지역에 배포된 두 개의 HANA 대규모 인스턴스 테넌트를 연결하는 데는 추가 요금이 없습니다. 

> [!IMPORTANT]  
> HANA 대규모 인스턴스 테넌트 간의 네트워크 트래픽 제어 흐름 및 데이터 흐름은 Azure 가상 네트워크를 통해 라우팅되지 않습니다. 따라서 Azure 기능 또는 NVA(네트워크 가상 어플라이언스)를 사용하여 HANA 대규모 인스턴스 테넌트 간에 통신 제한을 적용할 수 없습니다. 

ExpressRoute Global Reach를 사용하도록 설정하는 방법에 대한 자세한 내용은 [가상 네트워크를 HANA 대규모 인스턴스에 연결](./hana-connect-vnet-express-route.md)을 참조하세요.


## <a name="internet-connectivity-of-hana-large-instance"></a>HANA 대규모 인스턴스의 인터넷 연결
HANA 대규모 인스턴스에는 직접 인터넷 연결이 *없습니다*. 예를 들어 이 제한으로 인해 OS 공급업체에 OS 이미지를 직접 등록하는 기능이 제한될 수 있습니다. 로컬 SUSE Linux Enterprise Server 구독 관리 도구 서버 또는 Red Hat Enterprise Linux 구독 관리자를 사용해야 할 수도 있습니다.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>VM 및 HANA 대규모 인스턴스 간 데이터 암호화
HANA 대규모 인스턴스 및 VM 간에 전송되는 데이터는 암호화되지 않습니다. 그러나 HANA DBMS 쪽과 JDBC/ODBC 기반 애플리케이션 간의 교환을 위해서만 트래픽 암호화를 사용할 수 있습니다. 자세한 내용은 [SAP HANA와 JDBC/ODBC 클라이언트 간의 보안 통신](https://help.sap.com/viewer/102d9916bf77407ea3942fef93a47da8/1.0.11/en-US/dbd3d887bb571014bf05ca887f897b99.html)을 참조하세요.

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>여러 지역에서 HANA 대규모 인스턴스 장치 사용

재해 복구의 경우 여러 Azure 지역에 HANA 대규모 인스턴스 장치가 있어야 합니다. Azure [글로벌 Vnet 피어링](../../../virtual-network/virtual-network-peering-overview.md)만 사용하면 기본적으로 다른 지역의 HANA 대규모 인스턴스 테넌트 간에 전이적 라우팅이 작동하지 않습니다. 그러나 Global Reach는 다른 지역에 있는 HANA 대규모 인스턴스 장치 간의 통신을 엽니다. ExpressRoute Global Reach를 사용하는 이 시나리오는 다음을 가능하게 합니다.

 - 더 이상 프록시나 방화벽 없이 HANA 시스템 복제.
 - 서로 다른 지역의 HANA 대규모 인스턴스 장치 간에 백업을 복사하여 시스템 복사본 또는 시스템 새로 고침을 수행합니다.


![다른 Azure 지역의 Azure 대규모 인스턴스 스탬프에 연결된 가상 네트워크](./media/hana-overview-architecture/image8-multiple-regions.png)

앞의 그림은 두 지역의 가상 네트워크가 두 개의 ExpressRoute 회로에 연결되는 방법을 보여 줍니다. 이러한 회로는 두 Azure 지역(회색 선)에서 Azure(대규모 인스턴스)의 SAP HANA에 연결하는 데 사용됩니다. 이러한 두 개의 교차 연결에 대한 이유는 한쪽의 MSEE 중단을 방지하기 위함입니다. 두 Azure 지역에 있는 두 가상 네트워크 간의 통신 흐름은 두 개의 서로 다른 지역(파란색 점선)에 있는 두 가상 네트워크의 [글로벌 피어링](/archive/blogs/azureedu/how-to-setup-global-vnet-peering-in-azure)을 통해 처리되어야 합니다. 두꺼운 빨간색 선은 ExpressRoute Global Reach 연결을 설명합니다. 이 연결을 통해 서로 다른 지역에 있는 테넌트의 HANA 대규모 인스턴스 단위가 서로 통신할 수 있습니다. 

> [!IMPORTANT] 
> 여러 개의 ExpressRoute 회로를 사용한 경우 트래픽을 적절하게 라우팅할 수 있도록 선행 AS 경로 추가 및 로컬 기본 BGP 설정을 사용합니다.

## <a name="next-steps"></a>다음 단계

SAP HANA(대규모 인스턴스)의 스토리지 아키텍처에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [SAP HANA(대규모 인스턴스) 스토리지 아키텍처](hana-storage-architecture.md)
