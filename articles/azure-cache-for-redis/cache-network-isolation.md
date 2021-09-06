---
title: Azure Cache for Redis 네트워크 격리 옵션
description: 이 문서에서는 사용자의 요구에 맞는 최고의 네트워크 격리 솔루션을 찾는 방법을 알아봅니다. Azure Private Link, Azure VNet(Virtual Network) 삽입 및 Azure Firewall 규칙에 대한 기본 사항을 활용하여 장점과 제한 사항을 알아봅니다.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 61865a38cfa96dae35a02b25a1f5025438e91816
ms.sourcegitcommit: 351279883100285f935d3ca9562e9a99d3744cbd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2021
ms.locfileid: "112379747"
---
# <a name="azure-cache-for-redis-network-isolation-options"></a>Azure Cache for Redis 네트워크 격리 옵션

이 문서에서는 사용자의 요구에 맞는 최고의 네트워크 격리 솔루션을 찾는 방법을 알아봅니다. Azure Private Link, Azure VNet(Virtual Network) 삽입 및 Azure Firewall 규칙의 기본 사항에 대해 설명합니다. 각각의 장점과 제한 사항을 설명합니다.  

## <a name="azure-private-link"></a>Azure Private Link

Azure Private Link는 가상 네트워크에서 Azure PaaS 서비스로 비공개 연결을 제공합니다. 이는 네트워크 구조를 간소화하고 Azure에서 엔드포인트 간의 연결을 보호합니다. 또한 공용 인터넷에 대한 데이터 노출을 방지하여 연결을 보호합니다.

### <a name="advantages"></a>장점

* 기본, 표준, 프리미엄 Azure Cache for Redis 인스턴스에서 지원됩니다.
* [Azure Private Link](../private-link/private-link-overview.md)를 사용하면 프라이빗 엔드포인트를 통해 가상 네트워크에서 Azure Cache 인스턴스에 연결할 수 있습니다. 엔드포인트는 가상 네트워크 내에 있는 서브넷에 개인 IP 주소를 할당합니다. 이 개인 링크를 통해 캐시 인스턴스를 VNet 내에서 공개적으로 사용할 수 있습니다.  
* 프라이빗 엔드포인트를 만든 후에는 `publicNetworkAccess` 플래그를 통해 공용 네트워크에 대한 액세스를 제한할 수 있습니다. 이 플래그는 기본값으로 `Disabled`로 설정되며, 이 경우 프라이빗 링크 액세스만 허용됩니다. 패치 요청을 사용하여 `Enabled` 또는 `Disabled`로 값을 설정할 수 있습니다. 자세한 내용은 [Azure Private Link를 사용하는 Azure Cache for Redis(cache-private-link.md)]를 참조하세요.
* 모든 외부 캐시 종속성은 VNet의 NSG 규칙에 영향을 주지 않습니다.

### <a name="limitations"></a>제한 사항

* 프라이빗 엔드포인트에 NSG(네트워크 보안 그룹)를 사용할 수 없습니다. 그러나 서브넷에 기타 리소스가 있는 경우 NSG 적용이 해당 리소스에 적용됩니다.
* 현재 포털 콘솔 지원과 방화벽 스토리지 계정에 대한 지속성은 지원되지 않습니다. 
* 클러스터형 캐시에 연결하려면 `publicNetworkAccess` 를 `Disabled`로 설정해야 하며 프라이빗 엔드포인트 연결은 하나만 가능합니다.

> [!NOTE]
> 캐시 인스턴스에 프라이빗 엔드포인트를 추가하는 경우 DNS로 인해 모든 Redis 트래픽이 프라이빗 엔드포인트로 이동합니다.
> 이전 방화벽 규칙이 이전에 조정되었는지 확인합니다.  

## <a name="azure-virtual-network-injection"></a>Azure Virtual Network 추가

VNet은 Azure에서 개인 네트워크의 기본 구성 요소입니다. VNet을 사용하면 다수의 Azure 리소스가 상호 간, 인터넷 및 온-프레미스 네트워크와 안전하게 통신할 수 있습니다. VNet은 사용자 고유의 데이터 센터에서 작동하는 기존 네트워크와 같습니다. 그러나 VNet에는 Azure 인프라, 확장, 가용성 및 격리의 이점도 있습니다.

### <a name="advantages"></a>장점

* Azure Cache for Redis 인스턴스가 VNet으로 구성된 경우 공개적으로 주소를 지정할 수 없습니다. VNet 내의 가상 머신과 애플리케이션에서만 액세스할 수 있습니다.  
* VNet을 제한된 NSG 정책과 결합하면 데이터 반출의 위험을 줄일 수 있습니다.
* VNet 배포는 Azure Cache for Redis에 대해 향상된 보안/격리 기능을 제공합니다. 서브넷, 액세스 제어 정책 및 기타 기능으로 액세스를 추가 제한합니다.
* 지역 복제가 지원됩니다.

### <a name="limitations"></a>제한 사항

* VNet 삽입 캐시는 프리미엄 Azure Cache for Redis에서만 사용할 수 있습니다.
* VNet 삽입 캐시를 사용하는 경우 CRL/PKI, AKV, Azure Storage, Azure Monitor 등의 종속성을 캐시하도록 VNet을 변경해야 합니다.  

## <a name="azure-firewall-rules"></a>Azure Firewall 규칙

[Azure Firewall](../firewall/overview.md)은 VNet 리소스를 보호하는 관리형 클라우드 기반 네트워크 보안 서비스입니다. 고가용성 및 무제한 클라우드 확장성이 기본 제공되는 서비스 형태의 완전한 상태 저장 방화벽입니다. 구독 및 가상 네트워크 전반에 걸쳐 애플리케이션 및 네트워크 연결 정책을 중앙에서 만들고, 적용하고 기록할 수 있습니다.  

### <a name="advantages"></a>장점

* 방화벽 규칙이 구성되면 지정된 IP 주소 범위의 클라이언트 연결만 캐시에 연결할 수 있습니다. 방화벽 규칙이 구성된 경우에도 Azure Cache for Redis 모니터링 시스템의 연결은 항상 허용됩니다. 사용자가 정의한 NSG 규칙 또한 허용됩니다.  

### <a name="limitations"></a>제한 사항

* 방화벽 규칙은 VNet 삽입 캐시에서 사용할 수 있지만, 프라이빗 엔드포인트에서는 현재 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [프리미엄 Azure Cache for Redis 인스턴스에 대한 VNet 삽입 캐시](cache-how-to-premium-vnet.md)를 구성하는 방법을 알아봅니다.
* [모든 Azure Cache for Redis 계층에 대해 방화벽 규칙](cache-configure.md#firewall)을 구성하는 방법을 알아봅니다.
* [모든 Azure Cache for Redis 계층에 대해 프라이빗 엔드포인트를 구성](cache-private-link.md)하는 방법을 알아봅니다.
