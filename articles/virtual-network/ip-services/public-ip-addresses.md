---
title: Azure의 공용 IP 주소
titleSuffix: Azure Virtual Network
description: Azure의 공용 IP 주소에 대해 알아보기
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: conceptual
ms.date: 11/19/2021
ms.author: allensu
ms.openlocfilehash: c77385b5ecc4792276ef6ab41e10a82c0720d0dd
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132871569"
---
# <a name="public-ip-addresses"></a>공용 IP 주소

공용 IP 주소를 통해 인터넷 리소스가 Azure 리소스에 대한 인바운드와 통신할 수 있습니다. 공용 IP를 사용하면 Azure 리소스에서 인터넷 및 공용 Azure 서비스에 통신할 수 있습니다. 주소는 사용자가 할당 해제하지 않는 한 리소스에 전용됩니다. 공용 IP가 할당되지 않은 리소스는 아웃바운드로 통신할 수 있습니다. Azure는 사용 가능하며 리소스 전용이 아닌 IP 주소를 동적으로 할당합니다. Azure에서 아웃바운드 연결에 대한 자세한 내용은 [아웃바운드 연결 이해](../../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.

Azure 리소스 관리자에서 [공용 IP](virtual-network-public-ip-address.md) 주소는 고유 속성을 가진 리소스입니다. 공용 IP 리소스를 연결할 수 있는 리소스는 다음과 같습니다.

* 가상 머신 네트워크 인터페이스
* 인터넷 연결 부하 분산 장치
* 가상 네트워크 게이트웨이(VPN/ER)
* NAT 게이트웨이
* 애플리케이션 게이트웨이
* Azure Firewall
* 베스천 호스트

Virtual Machine Scale Sets의 경우 [공용 IP 접두사](public-ip-address-prefix.md)를 사용합니다.

## <a name="at-a-glance"></a>요약

다음 표에는 공용 IP를 리소스에 연결할 수 있는 속성과 할당 방법이 나와 있습니다. 현재 일부 리소스 종류에는 공용 IPv6 지원을 사용할 수 없습니다.

| 최상위 리소스 | IP 주소 연결 | 동적 IPv4 | 고정 IPv4 | 동적 IPv6 | 고정 IPv6 |
| --- | --- | --- | --- | --- | --- |
| 가상 머신 |네트워크 인터페이스 |예 | 예 | 예 | 예 |
| 인터넷 연결 부하 분산 장치 |프런트 엔드 구성 |예 | 예 | 예 |예 |
| 가상 네트워크 게이트웨이(VPN) |게이트웨이 IP 구성 |예(비 AZ만 해당) |예(AZ만 해당) | 아니요 |아니요 |
| 가상 네트워크 게이트웨이(ER) |게이트웨이 IP 구성 |Yes | 아니요 | 예(미리 보기) |No |
| NAT 게이트웨이 |게이트웨이 IP 구성 |아니요 |예 | 아니요 |아니요 |
| 프런트 엔드 |프런트 엔드 구성 |예(V1에만 해당) |예(V2에만 해당) | 아니요 | 아니요 |
| Azure Firewall | 프런트 엔드 구성 | 아니요 | 예 | 아니요 | 아니요 |
| 베스천 호스트 | 공용 IP 구성 | 아니요 | 예 | 아니요 | 아니요 |

## <a name="ip-address-version"></a>IP 주소 버전

IPv4 또는 IPv6 주소를 사용하여 공용 IP 주소를 만들 수 있습니다. IPv4 및 IPv6 주소를 사용하여 이중 스택 배포도 만들 수 있습니다.

## <a name="sku"></a>SKU

공용 IP 주소는 다음 SKU 중 하나로 만들어집니다.

| | Standard  | Basic |
| --- | --- | --- |
| 할당 방법| 정적 | IPv4의 경우: 동적 또는 정적; IPv6의 경우: 동적입니다.| 
| | 조정 가능한 인바운드 발생 흐름 유휴 시간 제한은 4-30분(기본값은 4분)으로, 고정 아웃바운드 발생 흐름 유휴 시간 제한은 4분으로 정합니다.|조정 가능한 인바운드 발생 흐름 유휴 시간 제한은 4-30분(기본값은 4분)으로, 고정 아웃바운드 발생 흐름 유휴 시간 제한은 4분으로 정합니다.|
| 보안 | 기본적으로 모델을 보호하고 프런트 엔드로 사용될 때 인바운드 트래픽에 대해 닫힙니다.  NSG(네트워크 [보안 그룹)를](../../virtual-network/network-security-groups-overview.md#network-security-groups) 사용하여 트래픽을 허용해야 합니다(예: 표준 SKU 공용 IP가 연결된 가상 머신의 NIC에서).| 기본적으로 엽니다.  네트워크 보안 그룹을 사용하는 것이 좋지만 인바운드 또는 아웃바운드 트래픽을 제한하는 것은 선택 사항입니다.| 
| [가용성 영역](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 지원됨. 표준 IP는 비영역, 영역 또는 영역 중복일 수 있습니다. **영역 중복 IP는 [3개의 가용성 영역이 있는 하위 지역](../../availability-zones/az-region.md)에서만 만들 수 있습니다.** 영역이 라이브 상태가 되기 전에 만든 IP는 영역 중복이 아닙니다. | 지원되지 않습니다. | 
| [라우팅 기본 설정](routing-preference-overview.md)| Azure와 인터넷 간에 트래픽을 라우팅하는 방법을 보다 세밀 하 게 제어할 수 있도록 지원 됩니다. | 지원되지 않습니다.| 
| 글로벌 계층 | [지역 간 부하 분산 장치](../../load-balancer/cross-region-overview.md)를 통해 지원 됩니다.| 지원되지 않습니다. |


> [!NOTE]
> 표준 SKU에 만든 후 기본 SKU IPv4 주소를 업그레이드할 수 있습니다.  SKU 업그레이드에 관한 자세한 내용은 [공용 IP 업그레이드](public-ip-upgrade-portal.md)를 참조하세요.

>[!IMPORTANT]
> Load Balancer 및 공용 IP 리소스에 대해 일치 하는 Sku가 필요 합니다. 기본 SKU 리소스와 표준 SKU 리소스를 혼합 하 여 사용할 수 없습니다. 독립 실행형 가상 머신, 가용성 집합 리소스의 가상 머신 또는 가상 머신 확장 집합 리소스를 두 SKU에 동시에 연결할 수 없습니다.  새 디자인에서는 표준 SKU 리소스를 사용하도록 고려해야 합니다.  자세한 내용은 [표준 Load Balancer](../../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 검토하세요.

## <a name="ip-address-assignment"></a>IP 주소 할당

표준 공용 IPv4, 기본 공용 IPv4, 표준 공용 IPv6 주소는 모두 **고정** 할당을 지원합니다.  리소스는 생성될 때 IP 주소에 할당됩니다. 이 IP 주소는 리소스가 삭제되면 해제됩니다.  

> [!NOTE]
> 할당 방법을 **고정** 으로 설정한 경우에도 공용 IP 주소 리소스에 할당된 실제 IP 주소를 지정할 수 없습니다. Azure는 리소스가 생성된 Azure 위치에서 사용 가능한 IP 주소 풀의 IP 주소를 할당됩니다.
>

정적 공용 IP 주소는 일반적으로 다음과 같은 시나리오에서 사용됩니다.

* Azure 리소스와 통신하도록 방화벽 규칙을 업데이트해야 하는 경우
* DNS 이름을 확인, IP 주소를 변경하려면 A 레코드를 업데이트해야 하는 경우
* Azure 리소스가 IP 기반 보안 모델을 사용하는 다른 앱 또는 서비스와 통신하는 경우
* IP 주소에 연결된 TLS/SSL 인증서를 사용하는 경우.

기본 공용 IPv4 및 IPv6 주소는 **동적** 할당을 지원합니다.  동적을 선택하는 경우 IP 주소는 생성 시 리소스에 제공되지 **않습니다**.  공용 IP 주소를 리소스에 연결할 때 IP가 할당됩니다. 리소스를 중지하거나 삭제하면 IP 주소가 해제됩니다.   예를 들어 **리소스 A** 라는 리소스에서 공용 IP 리소스를 해제합니다. **리소스 A** 는 공용 IP 리소스를 다시 할당하는 경우, 시작할 때 다른 IP를 수신합니다. 할당 방법을 **고정** 에서 **동적** 으로 변경하면 연결된 IP 주소가 모두 해제됩니다. 할당 방법이 **동적** 에서 **정적** 으로 변경 되는 경우 연결 된 모든 IP 주소는 변경 되지 않습니다. IP 주소를 동일하게 유지하려면 할당 방법을 **고정** 으로 설정합니다.

> [!NOTE]
> Azure는 각 Azure 클라우드 지역의 고유한 범위에서 공용 IP 주소를 할당합니다. Azure [공용](https://www.microsoft.com/download/details.aspx?id=56519), [US 정부](https://www.microsoft.com/download/details.aspx?id=57063), [중국](https://www.microsoft.com/download/details.aspx?id=57062) 및 [독일](https://www.microsoft.com/download/details.aspx?id=57064) 클라우드의 범위(접두사) 목록을 다운로드할 수 있습니다.
>

## <a name="dns-name-label"></a>DNS 이름 레이블

공용 IP 리소스에 대한 DNS 레이블을 지정하려면 이 옵션을 선택합니다. 이 기능은 IPv4 주소(32비트 A 레코드)와 IPv6 주소(128비트 AAAA 레코드)에서 모두 작동합니다.  이렇게 선택하면 Azure 관리형 DNS에 공용 IP를 위한 **domainnamelabel**.**location**.cloudapp.azure.com의 매핑이 생성됩니다. 

예를 들어 다음을 사용하여 공용 IP를 만듭니다.

* **domainnamelabel** 로서의 **contoso**
* **미국 서부** Azure **위치**

FQDN(정규화된 도메인 이름)인 **contoso.westus.cloudapp.azure.com** 은 리소스의 공용 IP로 확인됩니다.

> [!IMPORTANT]
> 생성된 각 도메인 이름은 Azure 위치 내에서 고유해야 합니다.  

공용 IP를 사용 하는 서비스에 사용자 지정 도메인을 사용 하는 경우 DNS 레코드에 대 한 [Azure DNS](../../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) 또는 외부 dns 공급자를 사용할 수 있습니다.

## <a name="other-public-ip-address-features"></a>기타 공용 IP 주소 기능

공용 IP 주소에 사용할 수 있는 기타 특성이 있습니다.  

* 전역 **계층** 에서는 지역 간 부하 분산 장치에 공용 IP 주소를 사용할 수 있습니다. 
* 인터넷 **라우팅 기본 설정** 옵션은 트래픽이 Microsoft 네트워크에서 소비하는 시간을 최소화하여 송신 데이터 전송 비용을 줄입니다.

> [!NOTE]
> 현재 **계층** 및 **라우팅 기본 설정** 기능은 표준 SKU IPv4 주소에만 사용할 수 있습니다.  또한 동일한 IP 주소에서 두 기능을 동시에 사용할 수는 없습니다.
>

[!INCLUDE [ephemeral-ip-note.md](../../../includes/ephemeral-ip-note.md)]

## <a name="limits"></a>제한 

IP 주소 지정에 적용되는 제한은 Azure에서 [네트워킹 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)의 전체 집합에 나열되어 있습니다. 제한은 지역별, 구독별로 적용됩니다. 비즈니스 요구 사항에 따라 기본 한도 이상으로 늘리려면 [고객 지원팀에 문의](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요.

## <a name="pricing"></a>가격 책정

공용 IP 주소에는 명목 요금이 부과됩니다. Azure의 IP 주소 가격 책정에 대한 자세한 내용은 [IP 주소 가격 책정](https://azure.microsoft.com/pricing/details/ip-addresses) 페이지를 참조하세요.

## <a name="limitations-for-ipv6"></a>IPv6에 대한 제한 사항

* IPv6가 사용하도록 설정된 가상 네트워크에서 VPN 게이트웨이를 직접 또는 “UseRemoteGateway”와 피어링하여 사용할 수 없습니다.
* 유휴 시간 제한인 4분이 경과하면 공용 IPv6 주소가 잠깁니다.
* Azure는 컨테이너에 대해 IPv6 통신을 지원하지 않습니다.
* IPv6 전용 가상 머신 또는 가상 머신 확장 집합을 사용할 수 없습니다. 각 NIC에 IPv4 IP 구성(이중 스택)이 하나 이상 포함되어야 합니다.
* 기존 IPv4 배포에 IPv6를 추가하는 경우 기존 리소스 탐색 링크를 포함하는 가상 네트워크에 IPv6 범위를 추가할 수 없습니다.
* Azure 공용 DNS에서 IPv6에 대한 정방향 DNS가 지원됩니다. 역방향 DNS는 지원되지 않습니다.
* 라우팅 기본 설정과 지역 간 부하 분산은 지원되지 않습니다.

Azure의 IPv6에 대한 자세한 내용은 [여기](ipv6-overview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Azure의 개인 IP 주소](private-ip-addresses.md)에 관한 자세한 정보
* [Azure 포털을 사용하여 고정 공용 IP를 사용하는 VM 배포](./virtual-network-deploy-static-pip-arm-portal.md)
