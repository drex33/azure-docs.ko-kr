---
title: Azure Firewall 표준 기능
description: Azure Firewall 기능에 대해 알아보기
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 07/30/2021
ms.author: victorh
ms.openlocfilehash: 0c9b871197085a6a220482c3b9d1d35f25d5b427
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132136749"
---
# <a name="azure-firewall-standard-features"></a>Azure Firewall 표준 기능

[Azure Firewall](overview.md) 표준은 Azure Virtual Network 리소스를 보호하는 관리형 클라우드 기반 네트워크 보안 서비스입니다.

:::image type="content" source="media/features/firewall-standard.png" alt-text="Azure Firewall 표준 기능":::

Azure Firewall에는 다음과 같은 기능이 포함되어 있습니다.

- 기본 제공되는 고가용성
- 가용성 영역
- 무제한 클라우드 확장성
- 애플리케이션 FQDN 필터링 규칙
- 네트워크 트래픽 필터링 규칙
- FQDN 태그
- 서비스 태그
- 위협 인텔리전스
- DNS 프록시
- 사용자 지정 DNS
- 네트워크 규칙의 FQDN
- 강제 Tunnel 모드에서 공용 IP 주소가 없는 배포
- 아웃바운드 SNAT 지원
- 인바운드 DNAT 지원
- 여러 공용 IP 주소
- Azure Monitor 로깅
- 강제 터널링
- 웹 범주
- 인증

## <a name="built-in-high-availability"></a>기본 제공되는 고가용성

고가용성이 기본 제공되므로 부하 분산 장치가 추가로 필요하지 않으며 아무것도 구성할 필요가 없습니다.

## <a name="availability-zones"></a>가용성 영역

Azure Firewall은 가용성을 높이기 위해 여러 가용 영역에 걸쳐 배포하는 동안 구성할 수 있습니다. 가용성 영역을 사용하면 가용성이 작동 시간 99.99%로 증가합니다. 자세한 내용은 Azure Firewall [SLA(서비스 수준 약정)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/)를 참조하세요. 99.99% 작동 시간 SLA는 둘 이상의 가용성 영역을 선택하는 경우 제공됩니다.

서비스 표준 99.95% SLA를 사용하여 근접성을 이유로 특정 영역에 Azure Firewall을 연결할 수도 있습니다.

가용성 영역에 배포되는 방화벽에 대한 추가 비용은 없습니다. 그러나 가용성 영역과 관련된 인바운드 및 아웃바운드 데이터 전송에는 추가 비용이 발생합니다. 자세한 내용은 [대역폭 가격 세부 정보](https://azure.microsoft.com/pricing/details/bandwidth/)를 참조하세요.

Azure Firewall 가용성 영역은 가용 영역을 지원하는 지역에서 사용할 수 있습니다. 자세한 내용은 [Azure에서 가용성 영역을 지원하는 지역](../availability-zones/az-region.md)을 참조하세요.

> [!NOTE]
> 가용성 영역은 배포 중에만 구성할 수 있습니다. 가용성 영역을 포함하도록 기존 방화벽을 구성할 수는 없습니다.

가용성 영역에 대한 자세한 내용은 [Azure의 지역 및 가용성 영역](../availability-zones/az-overview.md)을 참조하세요.

## <a name="unrestricted-cloud-scalability"></a>무제한 클라우드 확장성

Azure Firewall은 변화하는 네트워크 트래픽 흐름을 수용하기 위해 필요한 만큼 스케일 아웃이 가능하기 때문에 최대 트래픽에 대한 예산을 마련할 필요가 없습니다.

## <a name="application-fqdn-filtering-rules"></a>애플리케이션 FQDN 필터링 규칙

아웃바운드 HTTP/S 트래픽 또는 Azure SQL 트래픽을 와일드 카드를 포함한 FQDN(정규화된 도메인 이름) 목록으로 제한할 수 있습니다. 이 기능에는 TLS 종료가 필요하지 않습니다.

## <a name="network-traffic-filtering-rules"></a>네트워크 트래픽 필터링 규칙

원본 및 대상 IP 주소, 포트 및 프로토콜별로 네트워크 필터링 규칙을 중앙에서 만들거나 *허용* 하거나 *거부* 할 수 있습니다. Azure Firewall은 상태를 완전히 저장하기 때문에 다양한 유형의 연결에서 올바른 패킷을 구별할 수 있습니다. 여러 구독 및 가상 네트워크 전반에 규칙이 적용되고 기록됩니다.

Azure Firewall은 레이어 3 및 레이어 4 네트워크 프로토콜의 상태 저장 필터링을 지원합니다. 레이어 3 IP 프로토콜은 네트워크 규칙에서 **모두** 프로토콜을 선택하고 포트에 **\*** 와일드카드를 선택하여 필터링할 수 있습니다.

## <a name="fqdn-tags"></a>FQDN 태그

[FQDN 태그](fqdn-tags.md)를 활용하면 방화벽을 통해 잘 알려진 Azure 서비스 네트워크 트래픽을 쉽게 허용할 수 있습니다. 방화벽을 통해 Windows 업데이트 네트워크 트래픽을 허용하려는 경우를 예로 들어 보겠습니다. 이렇게 하려면 애플리케이션 규칙을 만들고 Windows 업데이트 태그를 포함합니다. 그러면 Windows 업데이트의 네트워크 트래픽이 방화벽을 통해 이동할 수 있습니다.

## <a name="service-tags"></a>서비스 태그

[서비스 태그](service-tags.md)는 보안 규칙 생성에 대한 복잡성을 최소화할 수 있는 IP 주소 접두사의 그룹을 나타냅니다. 사용자 고유의 서비스 태그를 직접 만들 수 없거나 태그 내에 포함할 IP 주소를 지정할 수 없습니다. Microsoft에서는 서비스 태그에서 압축한 주소 접두사를 관리하고 주소를 변경하는 대로 서비스 태그를 자동으로 업데이트합니다.

## <a name="threat-intelligence"></a>위협 인텔리전스

방화벽에서 알려진 악성 IP 주소 및 도메인과 주고받는 트래픽을 경고하고 거부할 수 있도록 하기 위해 [위협 인텔리전스](threat-intel.md) 기반 필터링을 사용하도록 설정할 수 있습니다. IP 주소 및 도메인은 Microsoft 위협 인텔리전스 피드에서 제공됩니다.

## <a name="dns-proxy"></a>DNS 프록시

DNS 프록시를 사용하도록 설정하면 Azure Firewall Virtual Network DNS 쿼리를 처리하고 원하는 DNS 서버로 전달할 수 있습니다. 이 기능은 중요하며 네트워크 규칙에서 신뢰할 수 있는 FQDN 필터링이 필요합니다. Azure Firewall 및 방화벽 정책 설정에서 DNS 프록시를 사용하도록 설정할 수 있습니다. DNS 프록시에 대한 자세한 내용은 [DNS 설정 Azure Firewall 참조하세요.](dns-settings.md)

## <a name="custom-dns"></a>사용자 지정 DNS

사용자 지정 DNS를 사용하면 Azure Firewall 자체 DNS 서버를 사용하도록 구성하는 동시에 방화벽 아웃바운드 의존성을 Azure DNS 통해 확인할 수 있습니다. Azure Firewall 및 방화벽 정책 DNS 설정에서 단일 DNS 서버 또는 여러 서버를 구성할 수 있습니다. 사용자 지정 DNS에 대한 자세한 내용은 [DNS 설정 Azure Firewall 참조하세요.](dns-settings.md)

Azure Firewall Azure 프라이빗 DNS 사용하여 이름을 확인할 수도 있습니다. Azure Firewall 있는 가상 네트워크는 Azure 프라이빗 영역에 연결되어야 합니다. 자세한 내용은 Private Link Azure Firewall [DNS 전달자로 사용을](https://github.com/adstuart/azure-privatelink-dns-azurefirewall)참조하세요.

## <a name="fqdn-in-network-rules"></a>네트워크 규칙의 FQDN

Azure Firewall 및 방화벽 정책의 DNS 확인에 따라 네트워크 규칙에서 FQDN(정규화된 도메인 이름)을 사용할 수 있습니다. 

규칙 컬렉션에서 지정된 FQDN은 방화벽 DNS 설정에 따라 IP 주소로 변환됩니다. 이 기능을 사용하면 모든 TCP/UDP 프로토콜(NTP, SSH, RDP 등)에서 FQDN을 사용하여 아웃바운드 트래픽을 필터링할 수 있습니다. 이 기능은 DNS 확인에 기반하기 때문에 DNS 프록시를 사용하도록 설정하여 이름 확인이 보호된 가상 머신 및 방화벽과 일치하도록 하는 것이 좋습니다.

## <a name="deploy-azure-firewall-without-public-ip-address-in-forced-tunnel-mode"></a>강제 Tunnel 모드에서 공용 IP 주소 없이 Azure Firewall 배포

Azure Firewall 서비스에는 운영 목적으로 공용 IP 주소가 필요합니다. 보안이 보장되지만 일부 배포에서는 공용 IP 주소를 인터넷에 직접 노출하지 않는 것이 좋습니다. 

이러한 경우 강제 Tunnel 모드에서 Azure Firewall 배포할 수 있습니다. 이 구성은 Azure Firewall 해당 작업에 사용하는 관리 NIC를 만듭니다. 테넌트 데이터 경로 네트워크는 공용 IP 주소 없이 구성할 수 있으며 인터넷 트래픽은 다른 방화벽으로 강제 터널되거나 완전히 차단될 수 있습니다.

강제 Tunnel 모드는 런타임에 구성할 수 없습니다. 방화벽을 다시 배포하거나 중지 및 시작 기능을 사용하여 강제 Tunnel 모드에서 기존 Azure Firewall 다시 구성할 수 있습니다. Secure Hubs에 배포된 방화벽은 항상 강제 Tunnel 모드로 배포됩니다.

## <a name="outbound-snat-support"></a>아웃바운드 SNAT 지원

모든 아웃바운드 가상 네트워크 트래픽 IP 주소는 Azure Firewall 공용 IP로 변환됩니다(원본 네트워크 주소 변환). 가상 네트워크에서 발생하는 트래픽을 식별하여 원격 인터넷 대상으로 허용할 수 있습니다. [IANA RFC 1918](https://tools.ietf.org/html/rfc1918)에 따라 대상 IP가 개인 IP 범위인 경우 Azure Firewall은 SNAT를 지원하지 않습니다. 

조직에서 개인 네트워크에 대해 공용 IP 주소 범위를 사용하면 Azure Firewall은 AzureFirewallSubnet의 방화벽 개인 IP 주소 중 하나에 트래픽을 SNAT합니다. 공용 IP 주소 범위를 SNAT하지 **않도록** Azure 방화벽을 구성할 수 있습니다. 자세한 내용은 [Azure Firewall SNAT 개인 IP 주소 범위](snat-private-range.md)를 참조하세요.

Azure Firewall 메트릭에서 SNAT 포트 사용률을 모니터링할 수 있습니다. 자세한 내용은 [방화벽 로그 및 메트릭 설명서](logs-and-metrics.md#metrics)에서 SNAT 포트 사용률에 대한 권장 사항을 참조하세요.

## <a name="inbound-dnat-support"></a>인바운드 DNAT 지원

방화벽 공용 IP 주소로의 인바운드 인터넷 네트워크 트래픽이 변환되고(Destination Network Address Translation), 가상 네트워크의 개인 IP 주소로 필터링됩니다.

## <a name="multiple-public-ip-addresses"></a>여러 공용 IP 주소

방화벽에 [여러 공용 IP 주소](deploy-multi-public-ip-powershell.md)(최대 250개)를 연결할 수 있습니다.

따라서 다음과 같은 시나리오가 가능합니다.

- **DNAT** - 여러 표준 포트 인스턴스를 백 엔드 서버로 변환할 수 있습니다. 예를 들어 공용 IP 주소가 2개인 경우 두 IP 주소 모두에 대해 TCP 포트 3389(RDP)를 변환할 수 있습니다.
- **SNAT** - 아웃바운드 SNAT 연결에 추가 포트를 사용할 수 있기 때문에 SNAT 포트가 고갈될 가능성이 줄어듭니다. 이때 Azure Firewall은 연결에 사용할 원본 공용 IP 주소를 임의로 선택합니다. 네트워크에 다운스트림 필터링이 있는 경우, 방화벽과 연결된 모든 공용 IP 주소를 허용해야 합니다. [공용 IP 주소 접두사](../virtual-network/ip-services/public-ip-address-prefix.md)를 사용하여 이 구성을 단순화하세요.

## <a name="azure-monitor-logging"></a>Azure Monitor 로깅

모든 이벤트가 Azure Monitor와 통합되기 때문에 스토리지 계정에 로그를 보관하고 Event Hub에 이벤트를 스트리밍하거나 Azure Monitor 로그에 보낼 수 있습니다. Azure Monitor 로그 샘플은 [Azure Firewall에 대한 Azure Monitor 로그](./firewall-workbook.md)를 참조하세요.

자세한 내용은 [자습서: Azure Firewall 로그 및 메트릭 모니터링](./firewall-diagnostics.md)을 참조하세요. 

Azure Firewall 통합 문서는 Azure Firewall 데이터를 분석할 수 있도록 유연한 캔버스를 제공합니다. 이를 사용하여 Azure Portal 내에서 풍부한 시각적 보고서를 만들 수 있습니다. 자세한 내용은 [Azure Firewall 통합 문서를 사용하여 로그 모니터링](firewall-workbook.md)을 참조하세요.

## <a name="forced-tunneling"></a>강제 터널링

모든 인터넷 바인딩된 트래픽을 인터넷으로 직접 이동하는 대신 지정된 다음 홉으로 라우팅하도록 Azure Firewall을 구성할 수 있습니다. 예를 들어 온-프레미스 에지 방화벽이나 기타 NVA(네트워크 가상 어플라이언스)를 통해 네트워크 트래픽을 인터넷에 전달하기 전에 처리할 수 있습니다. 자세한 내용은 [Azure Firewall 강제 터널링](forced-tunneling.md)을 참조하세요.

## <a name="web-categories"></a>웹 범주

웹 범주를 사용하면 관리자가 도박 웹 사이트, 소셜 미디어 웹 사이트 등의 웹 사이트 범주에 대한 사용자 액세스를 허용하거나 거부할 수 있습니다. 웹 범주는 Azure Firewall 표준에 포함되어 있지만, Azure Firewall 프리미엄에서 더 세밀하게 조정됩니다. FQDN을 기반으로 범주가 일치하는지 확인하는 표준 SKU의 웹 범주 기능과 달리, 프리미엄 SKU에서는 전체 URL에 따라 HTTP와 HTTPS 트래픽의 범주가 일치하는지 확인합니다. Azure Firewall 프리미엄에 대한 자세한 내용은 [Azure Firewall 프리미엄 기능](premium-features.md)을 참조하세요.

예를 들어 Azure Firewall에서 `www.google.com/news`의 HTTPS 요청을 가로채면 다음과 같이 분류되어야 합니다. 

- Firewall 표준 – FQDN 파트만 검사하므로 `www.google.com`은 *검색 엔진* 으로 분류됩니다. 

- Firewall 프리미엄 – 전체 URL을 검사하므로 `www.google.com/news`는 *뉴스* 로 분류됩니다.

범주는 심각도를 기반으로 **책임**, **고대역폭**, **비즈니스 사용**, **생산성 손실**, **일반 탐색**, **분류되지 않음** 으로 구성됩니다.

### <a name="category-exceptions"></a>범주 예외

웹 범주 규칙에 대한 예외를 만들 수 있습니다. 규칙 컬렉션 그룹 내에서 우선 순위가 높은 별도의 허용 또는 거부 규칙 컬렉션을 만듭니다. 예를 들어 우선 순위가 100인 `www.linkedin.com`을 허용하는 규칙 컬렉션과 우선 순위가 200인 **소셜 네트워킹** 을 거부하는 규칙 컬렉션을 구성할 수 있습니다. 그러면 미리 정의된 **소셜 네트워킹** 웹 범주의 예외가 생성됩니다.



## <a name="certifications"></a>인증서

Azure Firewall은 PCI(결제 카드 산업), SOC(서비스 조직 컨트롤), ISO(국제 표준화 기구) 및 ICSA Labs 규격을 준수합니다. 자세한 내용은 [Azure Firewall 규정 준수 인증](compliance-certifications.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Firewall 프리미엄 기능](premium-features.md)