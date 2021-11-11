---
title: Azure Firewall 프리미엄 기능
description: Azure Firewall 프리미엄은 Azure 가상 네트워크 리소스를 보호하는 클라우드 기반의 관리 네트워크 보안 서비스입니다.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 11/10/2021
ms.author: victorh
ms.custom: references_regions
ms.openlocfilehash: d00bfe9e9ed43ed1eafc65d61718de6cb4ecf04e
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179850"
---
# <a name="azure-firewall-premium-features"></a>Azure Firewall 프리미엄 기능

:::image type="content" source="media/premium-features/icsa-cert-firewall-small.png" alt-text="ICSA 인증 로고" border="false"::::::image type="content" source="media/premium-features/pci-logo.png" alt-text="PCI 인증 로고" border="false":::

Azure Firewall Premium 결제 및 의료 산업과 같이 매우 민감하고 규제된 환경의 요구를 충족하는 고급 위협 방지 기능을 제공합니다. 

조직은 IDPS 및 TLS 검사와 같은 Premium SKU(재고 유지 단위) 기능을 활용하여 맬웨어 및 바이러스가 횡적 및 수평 방향으로 네트워크 간에 확산되지 않도록 방지할 수 있습니다. IDPS 및 TLS 검사의 향상된 성능 요구를 충족하기 위해 Azure Firewall Premium 보다 강력한 가상 머신 SKU를 사용합니다. 표준 SKU와 마찬가지로 Premium SKU는 최대 30Gbps까지 원활하게 확장하고 가용성 영역과 통합하여 99.99%의 SLA(서비스 수준 계약)를 지원할 수 있습니다. Premium SKU는 PCI DSS(Payment Card Industry Data Security Standard) 환경 요구 사항을 준수합니다.

:::image type="content" source="media/premium-features/premium-overview.png" alt-text="Azure Firewall 프리미엄 개요 다이어그램":::

Azure Firewall 프리미엄에는 다음과 같은 기능이 포함되어 있습니다.

- **TLS 검사** - 아웃바운드 트래픽을 해독하고, 데이터를 처리한 다음 데이터를 암호화하고 대상에 보냅니다.
- **IDPS** - 네트워크 침입 감지 및 방지 시스템(IDPS)을 사용하면 악의적인 활동의 네트워크 활동을 모니터링 하고, 이 활동에 대한 정보를 기록하고, 보고하고, 필요 시 차단할 수 있습니다.
- **URL 필터링** - 전체 URL을 고려하도록 Azure Firewall의 FQDN 필터링 기능을 확장합니다. 예를 들어 `www.contoso.com`이 아닌 `www.contoso.com/a/c`입니다.
- **웹 범주** - 관리자는 도박 웹 사이트, 소셜 미디어 웹 사이트 등의 웹 사이트 범주에 대한 사용자 액세스를 허용하거나 거부할 수 있습니다.

## <a name="tls-inspection"></a>TLS 조사

Azure Firewall 프리미엄은 아웃바운드와 동-서 TLS 연결을 종료합니다. 인바운드 TLS 검사는 엔드투엔드 암호화를 허용하는 [Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)에서 지원됩니다. Azure Firewall은 필수 부가 가치 보안 기능을 수행하고 원래 대상으로 전송되는 트래픽을 다시 암호화합니다.

> [!TIP]
> TLS 1.0과 1.1은 사용되지 않으며 지원되지 않습니다. TLS 1.0과 1.1 버전의 TLS/SSL(Secure Sockets Layer)은 취약한 것으로 나타났으며, 여전히 이전 버전과 호환되지만 사용하지 않는 것이 좋습니다. 최대한 빨리 TLS 1.2로 마이그레이션합니다.

Azure Firewall 프리미엄 중간 CA 인증서 요구 사항에 대한 자세한 내용은 [Azure Firewall 프리미엄 인증서](premium-certificates.md)를 참조하세요.

## <a name="idps"></a>IDPS

네트워크 IDPS(침입 감지 및 방지 시스템)를 사용하면 네트워크에서 악의적인 활동을 모니터링하고, 이 활동에 대한 정보를 기록하고, 보고하고, 필요할 때 차단할 수 있습니다. 

Azure Firewall 프리미엄은 네트워크 트래픽의 바이트 시퀀스 또는 맬웨어에 사용되는 알려진 악성 명령 시퀀스와 같은 특정 패턴을 검색하여 공격을 신속하게 검색할 수 있도록 서명 기반 IDPS를 제공합니다. IDPS 서명은 애플리케이션 및 네트워크 수준 트래픽(계층 4-7)에 모두 적용되고 완전히 관리되며 지속적으로 업데이트됩니다. IDPS는 인바운드 트래픽, 스포크-스포크 트래픽(동-서) 및 아웃바운드 트래픽에 적용할 수 있습니다.

Azure Firewall 서명/규칙 세트에는 다음이 포함됩니다.
- 실제 맬웨어, 명령과 제어, 익스플로잇 키트 및 기존 예방 방법에서 누락된 실제 악성 활동에 관한 지문을 강조합니다.
- 50개 이상의 범주에서 58,000개 이상의 규칙
    - 범주에는 맬웨어 명령 및 제어, 피싱, 트로이 목마, 봇넷, 정보 이벤트, 악용, 취약성, SCADA 네트워크 프로토콜, 익스플로잇 키트 활동 등이 포함됩니다.
- 20 ~ 40개 이상의 새 규칙이 매일 릴리스됩니다.
- 최신 맬웨어 샌드박스 및 전역 센서 네트워크 피드백 루프를 사용하여 가양성 등급을 낮춥니다.

IDPS를 사용하면 암호화되지 않은 트래픽에 대한 모든 포트와 프로토콜에서 공격을 검색할 수 있습니다. 그러나 HTTPS 트래픽을 검사해야 하는 경우 Azure Firewall은 TLS 검사 기능을 사용하여 트래픽을 암호 해독하고 악의적인 활동을 더 잘 검색할 수 있습니다.  

IDPS 바이패스 목록을 사용하면 바이패스 목록에 지정된 IP 주소, 범위 및 서브넷에 대한 트래픽을 필터링하지 않을 수 있습니다.

IDPS 모드가 **경고** 로 설정되어 있지만 연결된 트래픽을 포함하여 차단하려는 특정 서명이 하나 이상 있는 경우에도 서명 규칙을 사용할 수 있습니다. 이 경우 TLS 검사 모드를 **거부** 로 설정하여 새 서명 규칙을 추가할 수 있습니다.


## <a name="url-filtering"></a>URL 필터링

URL 필터링은 Azure Firewall의 FQDN 필터링 기능을 확장하여 전체 URL을 고려합니다. 예를 들어 `www.contoso.com`이 아닌 `www.contoso.com/a/c`입니다.  

URL 필터링은 HTTP와 HTTPS 트래픽 모두에 적용할 수 있습니다. HTTPS 트래픽을 검사할 때 Azure Firewall 프리미엄에서 TLS 검사 기능을 사용하여 트래픽의 암호를 해독하고 대상 URL을 추출하여 액세스가 허용되는지 유효성을 검사할 수 있습니다. TLS 검사를 수행하려면 애플리케이션 규칙 수준에서 옵트인해야 합니다. 사용하도록 설정되면 HTTPS를 사용하여 필터링하는 데 URL을 사용할 수 있습니다. 

## <a name="web-categories"></a>웹 범주

웹 범주를 사용하면 관리자가 도박 웹 사이트, 소셜 미디어 웹 사이트 등의 웹 사이트 범주에 대한 사용자 액세스를 허용하거나 거부할 수 있습니다. 웹 범주는 Azure Firewall 표준에도 포함되지만, Azure Firewall 프리미엄에서 더 세밀하게 조정됩니다. FQDN을 기반으로 범주가 일치하는지 확인하는 표준 SKU의 웹 범주 기능과 달리, 프리미엄 SKU에서는 전체 URL에 따라 HTTP와 HTTPS 트래픽의 범주가 일치하는지 확인합니다. 

예를 들어 Azure Firewall에서 `www.google.com/news`의 HTTPS 요청을 가로채면 다음과 같이 분류되어야 합니다. 

- Firewall 표준 – FQDN 파트만 검사하므로 `www.google.com`은 *검색 엔진* 으로 분류됩니다. 

- Firewall 프리미엄 – 전체 URL을 검사하므로 `www.google.com/news`는 *뉴스* 로 분류됩니다.

범주는 심각도를 기반으로 **책임**, **고대역폭**, **비즈니스 사용**, **생산성 손실**, **일반 탐색**, **분류되지 않음** 으로 구성됩니다. 웹 범주에 대한 자세한 설명은 [Azure Firewall 웹 범주](web-categories.md)를 참조하세요.

### <a name="web-category-logging"></a>웹 범주 로깅
애플리케이션 로그에서 **웹 범주** 를 기준으로 필터링된 트래픽을 볼 수 있습니다. **웹 범주** 필드는 방화벽 정책 애플리케이션 규칙에 명시적으로 구성된 경우에만 표시됩니다. 예를 들어 *검색 엔진* 을 명시적으로 거부하는 규칙이 없으며 사용자가 www.bing.com으로 이동하도록 요청하는 경우 웹 범주 메시지와 달리 기본 거부 메시지만 표시됩니다. 웹 범주가 명시적으로 구성되지 않았기 때문입니다.

### <a name="category-exceptions"></a>범주 예외

웹 범주 규칙에 대한 예외를 만들 수 있습니다. 규칙 컬렉션 그룹 내에서 우선 순위가 높은 별도의 허용 또는 거부 규칙 컬렉션을 만듭니다. 예를 들어 우선 순위가 100인 `www.linkedin.com`을 허용하는 규칙 컬렉션과 우선 순위가 200인 **소셜 네트워킹** 을 거부하는 규칙 컬렉션을 구성할 수 있습니다. 그러면 미리 정의된 **소셜 네트워킹** 웹 범주의 예외가 생성됩니다.

### <a name="web-category-search"></a>웹 범주 검색

웹 범주 확인 기능을 사용하여 지정된 FQDN 또는 URL이 어떤 **범주인지** 식별할 수 있습니다. 이를 사용하려면 **방화벽 정책 설정** **웹 범주** 탭을 선택합니다. 이 방법은 대상 트래픽에 대한 애플리케이션 규칙을 정의할 때 특히 유용합니다.

:::image type="content" source="media/premium-features/firewall-category-search.png" alt-text="방화벽 범주 검색 대화 상자":::

### <a name="category-change"></a>범주 변경

**방화벽 정책 설정** **의 웹 범주** 탭 아래에서 다음과 같은 경우 분류 변경을 요청할 수 있습니다. 

- FQDN 또는 URL은 다른 범주에 속해야 함 

   또는 

- 분류되지 않은 FQDN 또는 URL에 대해 제안된 범주가 있음 

 범주 변경 보고서를 제출하면 처리 요청을 받았음을 나타내는 토큰이 알림에 제공됩니다. 검색 표시줄에 토큰을 입력하여 요청이 진행 중인지, 거부되었는지 또는 승인되었는지 확인할 수 있습니다.  이렇게 하려면 토큰 ID를 저장해야 합니다.

:::image type="content" source="media/premium-features/firewall-category-change.png" alt-text="방화벽 범주 보고서 대화 상자":::

 ## <a name="supported-regions"></a>지원되는 지역

Azure Firewall 프리미엄은 다음 지역에서 지원됩니다.

- 오스트레일리아 중부(퍼블릭/오스트레일리아)
- 오스트레일리아 중부 2(퍼블릭/오스트레일리아)
- 오스트레일리아 동부(퍼블릭/오스트레일리아)
- 오스트레일리아 남동부(퍼블릭/오스트레일리아)
- 브라질 남부(퍼블릭/브라질)
- 브라질 남동부(퍼블릭/브라질)
- 캐나다 중부(퍼블릭/캐나다)
- 캐나다 동부(퍼블릭/캐나다)
- 인도 중부(퍼블릭/인도)
- 미국 중부(퍼블릭/미국)
- 미국 중부 EUAP(퍼블릭/카나리아(미국))
- 중국 북부 2(Mooncake /China)
- 중국 동부 2(문케이크/중국)
- 동아시아(퍼블릭/아시아 태평양)
- 미국 동부(퍼블릭/미국)
- 미국 동부 2(퍼블릭/미국)
- 프랑스 중부(퍼블릭/프랑스)
- 프랑스 남부(퍼블릭/프랑스)
- 독일 중서부(퍼블릭/독일)
- 일본 동부(퍼블릭/일본)
- 일본 서부(퍼블릭/일본)
- 한국 중부(퍼블릭/한국)
- 한국 남부(퍼블릭/한국)
- 미국 중북부(퍼블릭/미국)
- 북유럽(퍼블릭/유럽)
- 노르웨이 동부(퍼블릭/노르웨이)
- 남아프리카 공화국 북부(퍼블릭/남아프리카 공화국)
- 미국 중남부(퍼블릭/미국)
- 인도 남부(퍼블릭/인도)
- 동남아시아(퍼블릭/아시아 태평양)
- 스위스 북부(퍼블릭/스위스)
- 아랍에미리트 중부(퍼블릭/아랍에미리트)
- 아랍에미리트 북부(퍼블릭/아랍에미리트)
- 영국 남부(퍼블릭/영국)
- 영국 서부(퍼블릭/영국)
- USGov구호도어(페드/USGov)
- USGov 텍사스(텍사스/USGov)
- USGov 버지니아(페더리/USGov)
- 미국 중서부(퍼블릭/미국)
- 서유럽(퍼블릭/유럽)
- 인도 서부(퍼블릭/인도)
- 미국 서부(퍼블릭/미국)
- 미국 서부 2(퍼블릭/미국)
- 미국 서부 3(퍼블릭/미국)


## <a name="known-issues"></a>알려진 문제

Azure Firewall 프리미엄에는 다음과 같이 알려진 문제가 있습니다.


|문제  |Description  |완화 방법  |
|---------|---------|---------|
|ESNI는 HTTPS에서 FQDN 해결을 위해 지원됩니다.|암호화된 SNI는 HTTPS 핸드셰이크에서 지원되지 않습니다.|오늘날 Firefox만 사용자 지정 구성을 통해 ESNI를 지원합니다. 제안된 해결 방법은 이 기능을 사용하지 않도록 설정하는 것입니다.|
|클라이언트 인증서(TLS)|클라이언트 인증서는 클라이언트와 서버 간에 상호 ID 신뢰를 구축하는 데 사용합니다. 클라이언트 인증서는 TLS 협상 중에 사용합니다. Azure Firewall은 서버와의 연결을 재협상하고 클라이언트 인증서의 프라이빗 키에 액세스할 수 없습니다.|없음|
|QUIC/HTTP3|QUIC는 HTTP의 새 주 버전입니다. 80(PLAN)과 443(SSL)을 통한 UDP 기반 프로토콜입니다. FQDN/URL/TLS 검사는 지원되지 않습니다.|UDP 80/443을 네트워크 규칙으로 전달하도록 구성합니다.|
신뢰할 수 없는 고객 서명 인증서|고객이 서명한 인증서는 인트라넷 기반 웹 서버에서 수신되면 방화벽에서 신뢰되지 않습니다.|수정 사항을 조사하고 있습니다.
|HTTP에 IDPS를 사용하는 경고에 잘못된 원본 IP 주소가 있습니다(TLS 검사 없음).|일반 텍스트 HTTP 트래픽이 사용 중이고 IDPS에서 새 경고를 발생시키고 대상이 공용 IP 주소인 경우 표시된 원본 IP 주소가 잘못되었습니다(내부 IP 주소가 원래 IP 주소 대신 표시).|수정 사항을 조사하고 있습니다.|
|인증서 전파|CA 인증서가 방화벽에 적용된 후 인증서가 적용되는 데 5-10분 정도 걸릴 수 있습니다.|수정 사항을 조사하고 있습니다.|
|TLS 1.3 지원|TLS 1.3은 부분적으로 지원됩니다. 클라이언트에서 방화벽으로의 TLS 터널은 TLS 1.2를 기반으로 하고, 방화벽에서 외부 웹 서버로의 TLS 터널은 TLS 1.3을 기반으로 합니다.|업데이트는 조사 중입니다.|
|KeyVault 프라이빗 엔드포인트|KeyVault는 네트워크 공개를 제한하기 위해 프라이빗 엔드포인트 액세스를 지원합니다. 신뢰할 수 있는 Azure 서비스는 [KeyVault 문서](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services)에 설명된 대로 예외가 구성된 경우 이 제한을 바이패스할 수 있습니다. Azure Firewall은 현재 신뢰할 수 있는 서비스로 나열되지 않으며 Key Vault에 액세스할 수 없습니다.|수정 사항을 조사하고 있습니다.|

## <a name="next-steps"></a>다음 단계

- [Azure Firewall 프리미엄 인증서에 관해 알아보기](premium-certificates.md)
- [Azure Firewall 프리미엄 배포 및 구성](premium-deploy.md)
- [Azure Firewall 프리미엄으로 마이그레이션](premium-migrate.md)
