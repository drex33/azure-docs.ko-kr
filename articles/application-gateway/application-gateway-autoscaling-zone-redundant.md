---
title: 자동 크기 조정 및 영역 중복 Application Gateway v2
description: 이 문서에서는 자동 크기 조정 및 영역 중복 기능을 포함하는 Azure Application Standard_v2 및 WAF_v2 SKU를 소개합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: victorh
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: 17e50b602bcab7dd3a40e6823bea44eb63603b9d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132313093"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>자동 크기 조정 및 영역 중복 Application Gateway v2 

Application Gateway는 Standard_v2 SKU에서 사용할 수 있습니다. WAF(웹 애플리케이션 방화벽)는 WAF_v2 SKU에서 사용할 수 있습니다. V2 SKU는 성능 향상을 제공하고 자동 크기 조정, 영역 중복성 및 정적 VIP 지원과 같은 중요한 새 기능에 대한 지원을 추가적으로 제공합니다. Standard 및 WAF SKU의 기존 기능은 새 v2 SKU에서 계속 지원되며, [비교](#differences-from-v1-sku) 섹션에 몇 가지 예외가 나와 있습니다.

새 v2 SKU에는 다음과 같은 향상된 기능이 포함되어 있습니다.

- **자동 스케일링**: 자동 스케일링 SKU의 Application Gateway 또는 WAF 배포는 트래픽 부하 패턴의 변화에 따라 스케일 아웃하거나 스케일 인할 수 있습니다. 또한 자동 크기 조정을 사용하면 프로비전 시 배포 크기 또는 인스턴스 수를 선택할 필요가 없습니다. 이 SKU는 진정한 탄력성을 제공합니다. Standard_v2 및 WAF_v2 SKU에서 Application Gateway는 고정 용량(자동 크기 조정이 해제됨) 및 자동 크기 조정이 설정된 모드에서 모두 작동할 수 있습니다. 고정 용량 모드는 워크로드가 일관적이고 예측 가능한 시나리오에 유용합니다. 자동 크기 조정 모드는 애플리케이션 트래픽이 자주 변하는 애플리케이션에 유용합니다.
- **영역 중복**: Application Gateway 또는 WAF 배포 하나가 여러 가용성 영역을 포함할 수 있으므로 Traffic Manager를 사용하여 각 영역에 별도의 Application Gateway 인스턴스를 프로비저닝할 필요가 없습니다. Application Gateway 인스턴스가 배포되는 단일 영역 또는 여러 영역을 선택할 수 있기 때문에 영역 장애 복원력이 보장됩니다. 애플리케이션에 대한 백 엔드 풀을 가용성 영역 전반에 유사하게 배포할 수 있습니다.

  영역 중복성은 Azure 영역을 사용할 수 있는 경우에만 지원됩니다. 다른 지역에서는 다른 모든 기능이 지원됩니다. 자세한 내용은 [Azure의 지역 및 가용성 영역](../availability-zones/az-overview.md)을 참조하세요.
- **정적 VIP**: Application Gateway v2 SKU는 정적 VIP 유형만 독점적으로 지원합니다. 이렇게 하면 Application Gateway와 연결된 VIP가 다시 시작된 후에도 배포의 수명 주기 동안 변경되지 않습니다.  V1에는 정적 VIP가 없으므로 Application Gateway를 통해 App Services로 라우팅하는 도메인 이름의 IP 주소 대신, Application Gateway URL을 사용해야 합니다.
- **헤더 다시 쓰기**: Application Gateway를 사용하면 v2 SKU를 사용하여 HTTP 요청 및 응답 헤더를 추가, 제거 또는 업데이트할 수 있습니다. 자세한 내용은 [Application Gateway로 HTTP 헤더 다시 쓰기](./rewrite-http-headers-url.md)를 참조하세요.
- **Key Vault 통합**: Application Gateway v2는 HTTPS 지원 수신기에 연결된 서버 인증서에 대해 Key Vault와의 통합을 지원합니다. 자세한 내용은 [Key Vault 인증서를 사용한 TLS 종료](key-vault-certs.md)를 참조하세요.
- **Azure Kubernetes Service 수신 컨트롤러**: Application Gateway v2 수신 컨트롤러는 Azure Application Gateway를 AKS 클러스터라고도 하는 AKS(Azure Kubernetes Service)의 수신으로 사용할 수 있습니다. 자세한 내용은 [Application Gateway 수신 컨트롤러란?](ingress-controller-overview.md)을 참조하세요.
- **성능 향상**: V2 SKU는 표준/WAF SKU와 비교할 때 최대 5배 더 나은 TLS 오프로드 성능을 제공합니다.
- **신속한 빠른 배포 및 업데이트 시간** v2 SKU는 표준/WAF SKU와 비교할 때 보다 빠른 배포 및 업데이트 시간을 제공합니다. 여기에는 WAF 구성 변경 내용도 포함됩니다.

![자동 스케일링 영역의 다이어그램입니다.](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>지원되는 지역

Standard_v2 및 WAF_v2 SKU는 미국 중북부, 미국 중남부, 미국 서부, 미국 서부 2, 미국 동부, 미국 동부 2, 미국 중부, 북유럽, 서유럽, 동남 아시아, 프랑스 중부, 영국 서부, 일본 동부, 일본 서부, 오스트레일리아 동부, 오스트레일리아 남동부, 브라질 남부, 캐나다 중부, 캐나다 동부, 동아시아  한국 중부, 한국 남부, 영국 남부, 인도 중부, 인도 서부, 인도 남부, 인도 서부 Jio 인도 서부, 노르웨이 동부, 스위스 북부, 아랍에미리트 북부, 남부 Arica 북부, 독일 중서부.

## <a name="pricing"></a>가격 책정

v2 SKU를 사용하는 경우 가격 책정 모델은 사용량에 따라 결정되며 더 이상 인스턴스 수나 크기와 관련되지 않습니다. v2 SKU 가격에는 다음과 같은 두 가지 구성 요소가 있습니다.

- **고정 가격** - Standard_v2 또는 WAF_v2 게이트웨이를 프로비저닝하는 시간별(또는 부분 시간) 가격입니다. 0개 이상의 추가 최소 인스턴스는 항상 고정 가격에 포함된 서비스의 고가용성을 보장합니다.
- **용량 단위 가격** - 고정 비용 외에 청구되는 소비 기반 비용입니다. 용량 단위 요금은 시간별로 또는 부분 시간별로 계산됩니다. 용량 단위의 세 가지 차원은 컴퓨팅 단위, 영구 연결 및 처리량입니다. 컴퓨팅 단위는 소비된 프로세서 용량의 척도입니다. 컴퓨팅 단위에 영향을 주는 요소는 TLS 연결/초, URL 다시 쓰기 계산 및 WAF 규칙 처리입니다. 영구 연결은 지정된 청구 간격으로 Application Gateway에 설정된 TCP 연결의 척도입니다. 처리량은 지정된 청구 간격으로 시스템에서 처리되는 평균 메가비트/초입니다.  청구는 예약 인스턴스 수를 초과하는 경우에 대해 용량 단위 수준에서 수행됩니다.

각 용량 단위는 최대 1개의 컴퓨팅 단위, 2,500개의 영구 연결 또는 2.22Mbps의 처리량으로 구성됩니다.

더 자세히 알아보려면 [가격 책정 이해](understanding-pricing.md)를 참조하세요.

## <a name="scaling-application-gateway-and-waf-v2"></a>Application Gateway 및 WAF v2 크기 조정

Application Gateway 및 WAF는 다음과 같은 두 가지 모드로 확장되도록 구성할 수 있습니다.

- **자동 크기 조정** - 자동 크기 조정을 사용하면 Application Gateway 및 WAF v2 SKU는 애플리케이션 트래픽 요구 사항에 따라 확장 또는 축소됩니다. 이 모드는 애플리케이션에 더 나은 탄력성을 제공하고 Application Gateway 크기 또는 인스턴스 수를 추측할 필요를 없애줍니다. 또한 이 모드를 사용하면 예상된 최대 트래픽 부하를 위해 게이트웨이가 프로비저닝된 최대 용량에서 실행될 필요가 없으므로 비용을 절감할 수 있습니다. 최소 및 최대(선택적) 인스턴스 수를 지정해야 합니다. 최소 용량을 사용하면 트래픽이 없는 경우에도 Application Gateway 및 WAF v2가 지정된 최소 인스턴스 수 아래로 떨어지지 않습니다. 각 인스턴스는 대략 10개의 추가 예약 용량 단위와 동일합니다. 0은 예약된 용량을 의미하지 않으며 완전히 자동으로 조정되어 지정됩니다. 필요에 따라 최대 인스턴스 수를 지정하여 Application Gateway가 지정된 인스턴스 수를 초과하지 않도록 할 수도 있습니다. 게이트웨이에서 제공하는 트래픽 양에 대해서만 요금이 청구됩니다. 인스턴스 수의 범위는 0~125일 수 있습니다. 이 수를 지정하지 않으면 최대 인스턴스 수의 기본값은 20입니다.
- **수동** - 게이트웨이가 자동으로 크기 조정되지 않는 수동 모드를 선택할 수 있습니다. 이 모드에서는 Application Gateway 또는 WAF가 처리할 수 있는 것보다 많은 트래픽이 있는 경우 트래픽이 손실될 수 있습니다. 수동 모드에서는 반드시 인스턴스 수를 지정해야 합니다. 인스턴스 수는 1개에서 125개까지 다양할 수 있습니다.

## <a name="autoscaling-and-high-availability"></a>자동 크기 조정 및 고가용성

Azure Application Gateway는 고가용성이 유지되는 방식으로 배포됩니다. 이 서비스는 구성된 대로(자동 크기 조정을 사용하지 않도록 설정한 경우) 또는 애플리케이션 로드에 필요한 대로(자동 크기 조정을 사용하도록 설정한 경우) 생성되는 여러 인스턴스로 구성됩니다. 사용자의 관점에서 볼 때 항상 개별 인스턴스가 표시되는 것은 아니며 Application Gateway 서비스가 전체적으로 표시됩니다. 특정 인스턴스에 문제가 발생하고 작동하지 않는 경우 Azure Application Gateway는 새 인스턴스를 투명하게 만듭니다.

0개의 최소 인스턴스로 자동 크기 조정을 구성하는 경우에도 이 서비스는 항상 사용 가능한 상태로 유지되며 항상 고정 가격에 포함됩니다.

그러나 새 인스턴스를 만드는 데 다소 시간이 걸릴 수 있습니다(약 6~7분). 따라서 이 가동 중지 시간을 단축하려는 경우가 아니라면 가용성 영역 지원을 통해 최소 인스턴스 수를 2로 구성하는 것이 적절할 수 있습니다. 이러한 방식으로 정상적인 상황에서 Azure Application Gateway 내에 두 개 이상의 인스턴스가 유지되므로, 이중 하나에 문제가 발생하는 경우 새 인스턴스가 생성되는 동안 다른 인스턴스가 트래픽을 처리할 수 있습니다. Azure Application Gateway 인스턴스는 약 10개의 용량 단위를 지원할 수 있으므로, 일반적으로 보유한 트래픽 양에 따라 최소 인스턴스 자동 크기 조정 설정을 2보다 큰 값으로 구성하는 것이 좋습니다.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>v1 SKU와 v2 SKU 간의 기능 비교

다음 표는 각 SKU에서 사용할 수 있는 기능을 비교해서 보여 줍니다.

| 기능                                           | v1 SKU   | v2 SKU   |
| ------------------------------------------------- | -------- | -------- |
| 자동 확장                                       |          | &#x2713; |
| 영역 중복                                   |          | &#x2713; |
| 정적 VIP                                        |          | &#x2713; |
| AKS(Azure Kubernetes Service) 수신 컨트롤러 |          | &#x2713; |
| Azure Key Vault 통합                       |          | &#x2713; |
| HTTP 헤더 다시 쓰기                           |          | &#x2713; |
| URL 기반 라우팅                                 | &#x2713; | &#x2713; |
| 다중 사이트 호스팅                             | &#x2713; | &#x2713; |
| 트래픽 리디렉션                               | &#x2713; | &#x2713; |
| WAF(웹 애플리케이션 방화벽)                    | &#x2713; | &#x2713; |
| WAF 사용자 지정 규칙                                  |          | &#x2713; |
| TLS(전송 계층 보안)/SSL(Secure Sockets Layer) 종료            | &#x2713; | &#x2713; |
| 종단 간 TLS 암호화                         | &#x2713; | &#x2713; |
| 세션 선호도                                  | &#x2713; | &#x2713; |
| 사용자 지정 오류 페이지                                | &#x2713; | &#x2713; |
| WebSocket 지원                                 | &#x2713; | &#x2713; |
| HTTP/2 지원                                    | &#x2713; | &#x2713; |
| 연결 드레이닝                               | &#x2713; | &#x2713; |

> [!NOTE]
> 자동 크기 조정 v2 SKU는 이제 [기본 상태 프로브](application-gateway-probe-overview.md#default-health-probe)를 지원하여 백 엔드 풀에 있는 모든 리소스의 상태를 자동으로 모니터링하고 비정상으로 간주되는 백 엔드 멤버를 강조 표시합니다. 기본 상태 프로브는 사용자 지정 프로브 구성이 없는 백 엔드에 대해 자동으로 구성됩니다. 자세한 내용은 [Application Gateway의 상태 프로브](application-gateway-probe-overview.md)를 참조하세요.

## <a name="differences-from-v1-sku"></a>v1 SKU와의 차이점

이 섹션에서는 v1 SKU와 다른 v2 SKU의 기능 및 제한 사항에 대해 설명합니다.

|차이점|세부 정보|
|--|--|
|인증 인증서|지원되지 않습니다.<br>자세한 내용은 [Application Gateway의 엔드투엔드 TLS 개요](ssl-overview.md#end-to-end-tls-with-the-v2-sku)를 참조하세요.|
|동일한 서브넷에서 Standard_v2와 표준 애플리케이션 게이트웨이 혼합|지원되지 않음|
|Application Gateway 서브넷의 UDR(사용자 정의 경로)|지원됨(특정 시나리오). 미리 보기 상태.<br> 지원되는 시나리오에 대한 자세한 내용은 [Application Gateway 구성 개요](configuration-infrastructure.md#supported-user-defined-routes)를 참조하세요.|
|인바운드 포트 범위에 대한 NSG| - 65200 ~ 65535(Standard_v2 SKU)<br>- 65503 ~ 65534(Standard SKU)<br>자세한 내용은 [FAQ](application-gateway-faq.yml#are-network-security-groups-supported-on-the-application-gateway-subnet)을 참조하세요.|
|Azure 진단의 성능 로그|지원되지 않습니다.<br>Azure 메트릭을 사용해야 합니다.|
|결제|청구는 2019년 7월 1일에 시작하도록 예약됩니다.|
|FIPS 모드|현재는 지원되지 않습니다.|
|ILB 전용 모드|현재는 지원되지 않습니다. 공용 및 ILB 모드가 함께 지원됩니다.|
|네트워크 감시자 통합|지원되지 않습니다.|
|Microsoft Defender for Cloud 통합|아직 사용할 수 없습니다.

## <a name="migrate-from-v1-to-v2"></a>v1에서 v2로 마이그레이션

Azure PowerShell 스크립트는 v1 Application Gateway/WAF에서 v2 자동 크기 조정 SKU로 마이그레이션하는 데 유용한 PowerShell 갤러리에서 사용할 수 있습니다. 이 스크립트는 v1 게이트웨이에서 구성을 복사하는 데 유용합니다. 트래픽 마이그레이션은 여전히 사용자의 책임입니다. 자세한 내용은 [v1에서 v2로 Azure Application Gateway 마이그레이션](migrate-v1-v2.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [빠른 시작: Azure Application Gateway를 통해 웹 트래픽 보내기 - Azure Portal](quick-create-portal.md)
- [Azure PowerShell을 사용하여 예약된 가상 IP 주소로 자동 크기 조정, 영역 중복 애플리케이션 게이트웨이 만들기](tutorial-autoscale-ps.md)
- [Application Gateway](overview.md)에 대해 자세히 알아보세요.
- [Azure Firewall](../firewall/overview.md)에 대해 자세히 알아보세요.
