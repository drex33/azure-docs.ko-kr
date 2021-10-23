---
title: Azure의 라우팅 기본 설정
description: 라우팅 기본 설정을 사용하여 Azure와 인터넷 간의 트래픽 라우팅 방법을 선택하는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: allensu
ms.openlocfilehash: 73bb9748fa13b6b5209bbbcd8c4332c28249aecc
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130233945"
---
# <a name="what-is-routing-preference"></a>라우팅 기본 설정이란?

Azure 라우팅 기본 설정을 사용하면 Azure와 인터넷 간의 트래픽 라우팅 방법을 선택할 수 있습니다. Microsoft 네트워크를 통해 또는 ISP 네트워크(공용 인터넷)를 통해 트래픽을 라우팅하도록 선택할 수 있습니다. 이러한 옵션을 각각 *콜드 포테이토 라우팅* 및 *핫 포테이토 라우팅* 이라고도 합니다. 송신 데이터 전송 가격은 라우팅 선택에 따라 달라집니다. 공용 IP 주소를 만드는 동안 라우팅 옵션을 선택할 수 있습니다. 공용 IP 주소는 가상 머신, 가상 머신 확장 집합, 인터넷 연결 부하 분산 장치 등의 리소스와 연결될 수 있습니다. 또한 Blob, 파일, 웹, Azure DataLake와 같은 Azure 스토리지 리소스에 대한 라우팅 기본 설정도 설정할 수 있습니다. 기본적으로 트래픽은 모든 Azure 서비스에 대해 Microsoft 글로벌 네트워크를 통해 라우팅됩니다.

## <a name="routing-via-microsoft-global-network"></a>Microsoft 글로벌 네트워크를 통한 라우팅

*Microsoft 글로벌 네트워크* 를 통해 트래픽을 라우팅하면 165 에지 POP(Point of Presence) 이상으로 160,000마일이 넘는 파이버 범위까지 확장된 지구상에서 가장 큰 네트워크 중 하나를 통해 트래픽이 전달됩니다. 이 네트워크는 매우 높은 안정성과 가용성을 보장하기 위해 여러 중복 파이버 경로를 사용하여 적절히 프로비저닝되어 있습니다. 트래픽 엔지니어링은 트래픽에 대해 짧은 대기 시간 경로를 선택하고 프리미엄 네트워크 성능을 제공하는 소프트웨어 정의 WAN 컨트롤러를 통해 관리됩니다.

![Microsoft 글로벌 네트워크를 통한 라우팅](./media/routing-preference-overview/route-via-microsoft-global-network.png)

**수신 트래픽:** 글로벌 BGP 애니캐스트 알림은 수신 트래픽이 사용자에게 가장 가까운 Microsoft 네트워크에 진입되도록 합니다. 예를 들어 싱가포르의 사용자가 미국 시카고에서 호스트되는 Azure 리소스에 액세스하는 경우, 싱가포르 Edge POP의 Microsoft 글로벌 네트워크로 트래픽은 들어간 다음, Microsoft 네트워크에서 시카고에 호스트되는 서비스로 이동합니다.

**송신 트래픽:** 송신 트래픽도 동일한 원칙을 따릅니다. 트래픽은 Microsoft 글로벌 네트워크에서 대부분 이동하고 사용자에게 가장 가까운 곳에서 끝납니다. 예를 들어 Azure 시카고의 트래픽의 목적지가 싱가포르의 사용자인 경우, 트래픽은 시카고의 Microsoft 네트워크에서 싱가포르로 이동하고 싱가포르 Edge POP의 Microsoft 네트워크에서 종료됩니다.

수신 및 송신 트래픽은 모두 Microsoft 글로벌 네트워크에서 대부분 이동합니다. 이를 *콜드 포테이토 라우팅* 라고도 합니다.


## <a name="routing-over-public-internet-isp-network"></a>공용 인터넷(ISP 네트워크)을 통한 라우팅

새 라우팅 옵션으로 *인터넷 라우팅* 을 선택하면 Microsoft 글로벌 네트워크에서의 이동을 최소화하고 전송 ISP 네트워크를 사용하여 트래픽을 라우팅합니다. 이 비용 최적화된 라우팅 옵션은 다른 클라우드 공급자와 비슷한 네트워크 성능을 제공합니다.

![공용 인터넷을 통한 라우팅](./media/routing-preference-overview/route-via-isp-network.png)

**수신 트래픽:** 수신 경로는 트래픽이 호스트된 서비스 지역에 가장 가까운 Microsoft 네트워크로 진입하는 *핫 포테이토 라우팅* 을 사용합니다. 예를 들어 싱가포르의 사용자가 시카고에서 호스트되는 Azure 리소스에 액세스하는 경우, 트래픽은 공용 인터넷을 통해 이동하고 시카고의 Microsoft 글로벌 네트워크에 진입합니다.

**송신 트래픽:** 송신 트래픽도 동일한 원칙을 따릅니다. 트래픽은 서비스가 호스트되는 동일한 지역에 있는 Microsoft 네트워크에서 종료됩니다. 예를 들어 Azure 시카고의 서비스 트래픽의 목적지가 싱가포르의 사용자인 경우, 트래픽은 시카고의 Microsoft 네트워크를 나가서 싱가포르의 공용 인터넷을 통해 이동합니다.

## <a name="supported-services"></a>지원되는 서비스

라우팅 기본 설정을 "Microsoft 글로벌 네트워크"로 선택한 공용 IP는 모든 Azure 서비스와 연결할 수 있습니다. 그러나 라우팅 기본 설정을 **인터넷** 으로 선택한 공용 IP는 다음 Azure 리소스와 연결할 수 있습니다.

* 가상 머신
* 가상 머신 크기 집합
* AKS(Azure Kubernetes Service)
* 인터넷 연결 부하 분산 장치
* Application Gateway
* Azure Firewall

스토리지의 경우 기본 엔드포인트는 항상 **Microsoft 글로벌 네트워크** 를 사용합니다. 트래픽 라우팅에 대한 선택으로 **인터넷** 을 사용하여 보조 엔드포인트를 사용하도록 설정할 수 있습니다. 지원되는 스토리지 서비스는 다음과 같습니다.

* Blob
* 파일
* 웹
* Azure DataLake

## <a name="pricing"></a>가격 책정
두 옵션 간의 가격 차이는 인터넷 송신 데이터 전송 가격 책정에 반영됩니다. **Microsoft 글로벌 네트워크** 를 통한 라우팅의 데이터 전송 가격은 현재 인터넷 송신 가격과 같습니다. 최신 가격 책정 정보는 [Azure 대역폭 가격 책정 페이지](https://azure.microsoft.com/pricing/details/bandwidth/)를 참조하세요.

## <a name="limitations"></a>제한 사항


* 라우팅 기본 설정은 공용 IP 주소의 영역 중복 표준 SKU와만 호환됩니다. 공용 IP 주소의 기본 SKU는 지원되지 않습니다.
* 현재 라우팅 기본 설정은 IPv4 공용 IP 주소만 지원합니다. IPv6 공용 IP 주소는 지원되지 않습니다.


## <a name="next-steps"></a>다음 단계

* [인터넷을 통해 Microsoft Azure 서비스에 대한 연결을 최적화하는 방법 동영상을 확인해 보세요](https://www.youtube.com/watch?v=j6A_Mbpuh6s&list=PLLasX02E8BPA5V-waZPcelhg9l3IkeUQo&index=12). 
* [Azure PowerShell을 사용하여 VM에 대한 라우팅 기본 설정 구성](./configure-routing-preference-virtual-machine-powershell.md)
* [Azure CLI를 사용하여 VM에 대한 라우팅 기본 설정 구성](./configure-routing-preference-virtual-machine-cli.md)