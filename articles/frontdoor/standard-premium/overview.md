---
title: Azure Front Door 표준/프리미엄| Microsoft Docs
description: 이 문서는 Azure Front Door 표준/프리미엄의 개요를 제공합니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: overview
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 9f565e3b02d3ff06457971dda8354759464fc41d
ms.sourcegitcommit: 11ca7ba5a017429c22a6b0bc02acb70b83a2984a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814419"
---
# <a name="what-is-azure-front-door-standardpremium-preview"></a>Azure Front Door 표준/프리미엄(미리 보기)이란?

> [!IMPORTANT]
> 이 설명서는 Azure Front Door 표준/프리미엄(미리 보기)용입니다. Azure Front Door에 대한 정보를 찾고 있나요? [Azure Front Door 문서](../front-door-overview.md)를 참조하세요.

Azure Front Door 표준/프리미엄은 빠르고 안정적이며 안전한 최신 클라우드 CDN으로, Microsoft 글로벌 경계 네트워크를 사용하며 인텔리전트 위협 보호 기능과 통합됩니다. Azure Front Door, Azure CDN(Content Delivery Network) 표준 및 Azure WAF(Web Application Firewall)의 기능을 단일 보안 클라우드 CDN 플랫폼으로 결합합니다.

Azure Front Door 표준/프리미엄을 사용하면 글로벌 소비자 및 엔터프라이즈 애플리케이션을 사용자와 가까운 네트워크 가장자리에서 글로벌 대상에게 도달하는 콘텐츠를 사용하여 안전하고 고성능의 맞춤형 최신 애플리케이션으로 변환할 수 있습니다. 또한 즉각적인 장애 조치(failover)를 통해 글로벌 HTTP 부하 분산의 이점을 누리면서 워밍업 없이 애플리케이션을 확장할 수 있습니다.

   :::image type="content" source="../media/overview/front-door-overview.png" alt-text="Azure Front Door 표준/프리미엄 아키텍처" lightbox="../media/overview/front-door-overview-expanded.png":::

Azure Front Door 표준/프리미엄은 Layer 7(HTTP/HTTPS 계층)에서 작동하며, TCP가 분할된 애니캐스트 프로토콜과 Microsoft의 글로벌 네트워크를 사용하여 글로벌 연결을 개선합니다. 규칙 집합을 사용하여 사용자 지정된 라우팅 방법에 따라 Azure Front Door가 클라이언트 요청을 가장 빠르고 가장 가용성이 높은 원본으로 라우팅하도록 할 수 있습니다. 애플리케이션 원본은 Azure의 내부 또는 외부에서 호스팅되는 모든 인터넷 연결 서비스입니다. Azure Front Door 표준/프리미엄은 다양한 애플리케이션 요구와 자동 장애 조치(failover) 시나리오에 맞는 트래픽 라우팅 방법 및 원본 상태 모니터링 옵션을 제공합니다. Traffic Manager와 유사한 Front Door는 전체 Azure 지역에 대한 오류를 포함한, 오류에 대해 복원력을 갖습니다.

또한 Azure Front Door는 통합된 Web Application Firewall 보호, 봇 보호, 기본 제공 계층 3/계층 4 DDoS(분산형 서비스 거부) 보호를 사용하여 에지에서 앱을 보호합니다. 또한 프라이빗 링크 서비스로 프라이빗 백 엔드를 보호합니다. Azure Front Door는 글로벌 규모에서 Microsoft의 최상의 보안을 제공합니다.  

>[!NOTE]
> Azure는 사용자 시나리오를 위한 완전히 관리되는 부하 분산 솔루션 모음을 제공합니다.
>
> * DNS 기반 글로벌 라우팅을 수행하고 HTTP/HTTPS 요청 또는 애플리케이션 계층 처리당 TLS(Transport Layer Security) 프로토콜 종료("SSL 오프로드")에 대한 요구 사항이 **없는** 경우 [Traffic Manager](../../traffic-manager/traffic-manager-overview.md)를 검토하세요.
> * 애플리케이션 계층에서 한 지역의 서버 간에 부하를 분산하려면 [Application Gateway](../../application-gateway/overview.md)를 검토하세요.
> * 네트워크 계층 부하 분산을 수행하려면 [Load Balancer](../../load-balancer/load-balancer-overview.md)를 검토하세요.
>
> 필요에 따라 엔드투엔드 시나리오에서 이러한 솔루션을 조합하여 이점을 얻을 수 있습니다.
> Azure 부하 분산 옵션 비교는 [Azure의 부하 분산 옵션 개요](/azure/architecture/guide/technology-choices/load-balancing-overview)를 참조하세요.

> [!IMPORTANT]
> Azure Front Door 표준/프리미엄은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="why-use-azure-front-door-standardpremium-preview"></a>Azure Front Door 표준/프리미엄(미리 보기)을 사용하는 이유는 무엇인가요?

Azure Front Door 표준/프리미엄은 기본 제공되는 턴키 보안 통합과 단순하고 예측 가능한 가격 모델을 통해 동적 및 정적 가속을 모두 충족하는 단일 통합 플랫폼을 제공합니다. Front Door를 사용하면 앱에 대한 글로벌 라우팅을 정의, 관리 및 모니터링할 수도 있습니다.

Azure Front Door 표준/프리미엄(미리 보기)에 포함된 주요 기능은 다음과 같습니다.

- **[분할 TCP](../front-door-routing-architecture.md#splittcp)** 기반 애니캐스트 프로토콜을 사용하여 애플리케이션 성능 가속화.

- 인텔리전트 **[상태 프로브](/azure/frontdoor/front-door-health-probes)** 모니터링 및 **[원본](concept-origin.md)** 간의 부하 분산.

- 유연한 도메인 유효성 검사로 고유한 **[사용자 지정 도메인](how-to-add-custom-domain.md)** 정의.

- **[WAF(Web Application Firewall)](../../web-application-firewall/afds/afds-overview.md)** 가 통합된 애플리케이션 보안.

- SSL 오프로드 및 통합된 **[인증서 관리](how-to-configure-https-custom-domain.md)** .

- **[프라이빗 링크](concept-private-link.md)** 를 사용하여 원본 보호.  

- **[규칙 집합](concept-rule-set.md)** 을 통해 사용자 지정할 수 있는 트래픽 라우팅 및 최적화.

- Front Door 및 보안 패턴 모두를 위한 올인원 대시 보드가 포함된 **[기본 제공 보고서](how-to-reports.md)** .

- Azure Monitoring과 통합되는 **[실시간 모니터링](how-to-monitor-metrics.md)** 및 경고.

- 각 Front Door 요청 및 실패한 상태 프로브에 대한 **[로깅](how-to-logs.md)**

- 엔드투엔드 IPv6 연결 및 HTTP/2 프로토콜 기본 지원.

## <a name="pricing"></a>가격 책정

Azure Front Door 표준/프리미엄에는 표준과 프리미엄의 두 가지 SKU가 있습니다. [계층 비교](tier-comparison.md)를 참조하세요. 가격 정보는 [Front Door 가격 책정](https://azure.microsoft.com/pricing/details/frontdoor/)을 참조하세요. 

## <a name="whats-new"></a>새로운 기능

RSS 피드를 구독하고 [Azure 업데이트](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Front%20Door) 페이지에서 최신 Azure Front Door 기능 업데이트를 확인합니다.

## <a name="next-steps"></a>다음 단계

* [Front Door를 만드는](create-front-door-portal.md) 방법을 알아봅니다.
