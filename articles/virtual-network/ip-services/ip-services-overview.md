---
title: Azure Virtual Network IP 서비스란?
description: Azure Virtual Network IP 서비스의 개요입니다. IP 서비스가 작동하는 방식과 Azure에서 IP 리소스를 사용하는 방법을 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subService: ip-services
ms.topic: overview
ms.date: 10/01/2021
ms.custom: template-overview
ms.openlocfilehash: 7b9c1eb8e1e0a067da2f0ed29762ef14ce365690
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130257693"
---
# <a name="what-is-azure-virtual-network-ip-services"></a>Azure Virtual Network IP 서비스란?

IP 서비스는 Azure Virtual Network에서 통신할 수 있도록 하는 IP 주소 관련 서비스의 컬렉션입니다. 공용 및 개인 IP 주소는 Azure에서 리소스 간 통신에 사용됩니다. 리소스 통신은 프라이빗 Azure Virtual Network와 퍼블릭 인터넷에서 발생할 수 있습니다.

IP 서비스는 다음으로 구성됩니다.

* 퍼블릿 IP 주소

* 공용 IP 주소 접두사

* 개인 IP 주소

* 라우팅 기본 설정

* 무제한 라우팅 기본 설정

## <a name="public-ip-addresses"></a>퍼블릿 IP 주소

공용 IP는 인터넷 리소스에서 Azure의 리소스에 대한 인바운드 통신에 사용됩니다. IPv4 또는 IPv6 주소를 사용하여 공용 IP 주소를 만들 수 있습니다. IPv4 및 IPv6 주소를 사용하여 이중 스택 배포도 만들 수 있습니다. 공용 IP 주소는 **표준** 및 **기본** SKU로 제공됩니다. 공용 IP 주소는 고정되거나 동적으로 할당될 수 있습니다.

공용 IP 주소는 자체 속성이 있는 리소스입니다. 공용 IP 주소와 연결할 수 있는 일부 리소스는 다음과 같습니다.

* 가상 머신 네트워크 인터페이스
* 인터넷 연결 부하 분산 장치
* 가상 네트워크 게이트웨이(VPN/ER)
* NAT 게이트웨이
* 애플리케이션 게이트웨이
* Azure Firewall
* 베스천 호스트

공용 IP 주소에 관한 자세한 내용은 [공용 IP 주소](./public-ip-addresses.md)와 [Azure 공용 IP 주소 만들기, 변경 또는 삭제](./virtual-network-public-ip-address.md)를 참조하세요.

## <a name="public-ip-address-prefixes"></a>공용 IP 주소 접두사

공용 IP 접두사는 Azure에서 예약된 IP 주소 범위입니다. 공용 IP 주소 접두사는 IPv4 또는 IPv6 주소로 구성됩니다.  가용성 영역이 있는 지역에서는 공용 IP 주소 접두사는 영역 중복으로 만들거나 특정 가용성 영역에 연결할 수 있습니다. 공용 IP 주소 접두사를 만든 후 공용 IP 주소를 만들 수 있습니다.

다음 공용 IP 접두사 크기를 사용할 수 있습니다.

-  /28(IPv4) 또는 /124(IPv6) = 16개 주소
-  /29(IPv4) 또는 /125(IPv6) = 8개 주소
-  /30(IPv4) 또는 /126(IPv6) = 4개 주소
-  /31(IPv4) 또는 /127(IPv6) = 2개 주소

접두사 크기는 CIDR(Classless Inter-Domain Routing) 마스크 크기로 지정됩니다.

구독에서 만든 접두사 수에 대한 제한은 없습니다. 만든 범위의 수는 구독에 허용할 수 있는 정적 공용 IP 주소 수를 초과할 수 없습니다. 자세한 내용은 [Azure 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조하세요.

공용 IP 주소 접두사에 관한 자세한 내용은 [공용 IP 주소 접두사](./public-ip-address-prefix.md)와 [Azure 공용 IP 주소 접두사 만들기, 변경 또는 삭제](./manage-public-ip-address-prefix.md)를 참조하세요.

## <a name="private-ip-addresses"></a>개인 IP 주소

개인 IP를 사용하면 Azure의 리소스 간 통신이 가능합니다. Azure는 리소스가 있는 가상 네트워크 서브넷의 주소 범위에서 리소스에 개인 IP 주소를 할당합니다. Azure의 개인 IP 주소는 고정되거나 동적으로 할당됩니다.

개인 IP 주소를 연결할 수 있는 일부 리소스는 다음과 같습니다.

* 가상 머신
* 내부 부하 분산 장치
* 애플리케이션 게이트웨이
* 프라이빗 엔드포인트

개인 IP 주소에 관한 자세한 내용은 [개인 IP 주소](./private-ip-addresses.md)를 참조하세요.

## <a name="routing-preference"></a>라우팅 기본 설정

Azure 라우팅 기본 설정을 사용하면 Azure와 인터넷 간의 트래픽 라우팅 방법을 선택할 수 있습니다. Microsoft 네트워크를 통해 또는 ISP 네트워크(공용 인터넷)를 통해 트래픽을 라우팅하도록 선택할 수 있습니다. 공용 IP 주소를 만드는 동안 라우팅 옵션을 선택할 수 있습니다. 기본적으로 트래픽은 모든 Azure 서비스에 대해 Microsoft 글로벌 네트워크를 통해 라우팅됩니다. 

라우팅 기본 설정 선택 항목은 다음과 같습니다.

* **Microsoft 네트워크** - 수신 및 송신 트래픽은 모두 Microsoft 글로벌 네트워크에서 대량으로 이동합니다. 이 라우팅을 ‘콜드 포테이토’ 라우팅이라고도 합니다.

* **퍼블릭 인터넷(ISP 네트워크)** - 새 라우팅 옵션으로 인터넷 라우팅을 선택하면 Microsoft 글로벌 네트워크에서 이동을 최소화하고 전송 ISP 네트워크를 사용하여 트래픽을 라우팅합니다. 이 라우팅을 ‘핫 포테이토’ 라우팅이라고도 합니다.

라우팅 기본 설정에 관한 자세한 내용은 [라우팅 기본 설정이란?](./routing-preference-overview.md)을 참조하세요.

## <a name="routing-preference-unmetered"></a>무제한 라우팅 기본 설정

무제한 라우팅 기본 설정은 Azure에서 고객의 원본 콘텐츠를 호스트하는 CDN(Content Delivery Network) 공급자가 사용할 수 있습니다. 이 서비스를 사용하면 CDN 공급자는 다양한 위치에서 Microsoft 글로벌 네트워크 에지 라우터와 직접 피어링 연결을 설정할 수 있습니다.

Azure의 원본에서 CDN 공급자에게 송신하는 네트워크 트래픽을 이용하여 직접 연결의 이점을 경험할 수 있습니다.

* 이러한 직접 링크를 통해 라우팅되는 Azure 리소스에서 송신되는 트래픽에 대한 데이터 전송 청구 금액은 무료입니다.

* CDN 공급자와 Azure 원본 간의 직접 연결은 사이에 홉이 없기 때문에 최적의 성능을 제공합니다. 이 연결은 원본에서 데이터를 자주 가져오는 CDN 워크로드에 유용합니다.

무제한 라우팅 기본 설정에 관한 자세한 내용은 [무제한 라우팅 기본 설정이란?](./routing-preference-unmetered.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

IP 서비스 리소스 만들기를 시작합니다.

- [Azure Portal을 사용하여 공용 IP 주소를 만듭니다](./create-public-ip-portal.md).
- [Azure Portal을 사용하여 공용 IP 주소 접두사를 만듭니다](./create-public-ip-prefix-portal.md).
- [Azure Portal을 사용하여 VM의 개인 IP 주소를 구성합니다](./virtual-networks-static-private-ip-arm-pportal.md).
- [Azure Portal을 사용하여 공용 IP 주소의 라우팅 기본 설정을 구성합니다](./routing-preference-portal.md).