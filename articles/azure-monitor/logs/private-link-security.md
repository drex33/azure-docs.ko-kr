---
title: Azure Private Link를 사용하여 네트워크를 Azure Monitor에 연결
description: Azure Monitor Private Link 범위를 설정하여 네트워크를 Azure Monitor에 안전하게 연결합니다.
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: bdd47962b56324f9832070b13644b5489ee38989
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566370"
---
# <a name="use-azure-private-link-to-connect-networks-to-azure-monitor"></a>Azure Private Link를 사용하여 네트워크를 Azure Monitor에 연결

[Azure Private Link](../../private-link/private-link-overview.md)를 사용하면 프라이빗 엔드포인트를 사용하여 Azure PaaS(Platform as a Service) 서비스를 가상 네트워크에 안전하게 연결할 수 있습니다. 대부분의 서비스에서 각 리소스에 대해 엔드포인트를 설정하기만 하면 됩니다. 그러나 Azure Monitor는 동시에 작동하여 워크로드를 모니터링하는 상호 연결된 서로 다른 서비스의 모음입니다. 

## <a name="advantages"></a>장점

Private Link를 사용하면 다음을 수행할 수 있습니다.

- 공용 네트워크 액세스를 개방하지 않고 Azure Monitor에 비공개로 연결
- 권한 있는 개인 네트워크를 통해서만 모니터링 데이터에 액세스할 수 있는지 확인
- 프라이빗 엔드포인트를 통해 연결되는 특정 Azure Monitor 리소스를 정의하여 개인 네트워크에서 데이터 반출 방지
- ExpressRoute 및 Private Link를 사용하여 온-프레미스 개인 네트워크를 Azure Monitor에 안전하게 연결
- Microsoft Azure 백본 네트워크 내에서 모든 트래픽 유지

자세한 내용은 [Private Link의 주요 이점](../../private-link/private-link-overview.md#key-benefits)을 참조하세요.

## <a name="how-it-works"></a>작동 방법

Azure Monitor 프라이빗 링크 범위 (AMPLS)는 엔드포인트 (및 포함된 VNet)을 하나 이상의 Azure Monitor 리소스-Log Analytics 작업 영역 및 Application Insights 구성 요소-에 연결합니다.

![기본 리소스 토폴로지 다이어그램](./media/private-link-security/private-link-basic-topology.png)

* VNet의 프라이빗 엔드포인트는 이러한 엔드포인트의 공용 IP를 사용하는 대신 네트워크 풀에서 프라이빗 IP를 통해 Azure Monitor 엔드포인트에 도착할 수 있습니다. 이렇게 하면 불필요한 아웃 바운드 트래픽까지 VNet을 열지 않고도 Azure Monitor 리소스를 계속 사용할 수 있습니다. 
* 프라이빗 엔드포인트에서 Azure Monitor 리소스에 대한 트래픽은 Microsoft Azure 백본을 지나며, 공용 네트워크로 라우팅되지 않습니다. 
* 공용 네트워크에서 수집 및 쿼리를 허용하거나 거부하도록 각 작업 영역 또는 컴포넌트를 구성할 수 있습니다. 리소스 수준의 보호를 제공하므로 특정 리소스에 대한 트래픽을 제어할 수 있습니다.

> [!NOTE]
> 단일 Azure Monitor 리소스는 여러 AMPLS에 속할 수 있지만, 단일 VNet을 둘 이상의 AMPLS에 연결할 수는 없습니다. 

### <a name="azure-monitor-private-links-and-your-dns-its-all-or-nothing"></a>Azure Monitor Private Link와 DNS 중 양자택일의 문제입니다.
일부 Azure Monitor 서비스는 글로벌 엔드포인트 사용합니다. 즉, 작업 영역/컴포넌트를 대상으로 하는 요청을 처리합니다. Private Link 연결을 설정하면 Private Link를 통해 트래픽을 전송하기 위해 DNS가 업데이트되어 Azure Monitor 엔드포인트를 개인 IP에 매핑합니다. 전역 엔드포인트와 관련하여 단일 리소스에도 Private Link를 설정하면 모든 리소스에 대한 트래픽에 영향을 줍니다. 즉, 특정 구성 요소나 작업 영역에 대해서만 Private Link 연결을 만들 수 없습니다.

#### <a name="global-endpoints"></a>전역 엔드포인트
가장 중요한 점은 아래 전역 엔드포인트에 대한 트래픽이 Private Link를 통해 전송된다는 점입니다.
* 모든 Application Insights 엔드포인트 - Application Insights 엔드포인트에 대해 수집, 라이브 메트릭, 프로파일러, 디버거 등을 처리하는 엔드포인트가 전역입니다.
* 쿼리 엔드포인트 - Application Insights와 Log Analytics 리소스 모두에 대한 쿼리를 처리하는 엔드포인트가 전역입니다.

실제로 모든 Application Insights 트래픽이 Private Link로 전송되고 Application Insights 및 Log Analytics 리소스에 대한 모든 쿼리가 Private Link로 전송된다는 의미입니다.

AMPLS에 추가되지 않은 Application Insights 리소스에 대한 트래픽은 Private Link 유효성 검사를 통과하지 못하고 실패합니다.

![양자택일 동작의 다이어그램](./media/private-link-security/all-or-nothing.png)

#### <a name="resource-specific-endpoints"></a>리소스별 엔드포인트
쿼리 엔드포인트를 제외한 모든 Log Analytics 엔드포인트가 작업 영역별로 다릅니다. 따라서 특정 Log Analytics 작업 영역에 대한 Private Link를 만들면 다른 작업 영역에 대한 수집 또는 다른 트래픽에 영향을 주지 않으며 퍼블릭 Log Analytics 엔드포인트를 계속 사용합니다. 그러나 모든 쿼리는 Private Link를 통해 전송됩니다.

### <a name="azure-monitor-private-link-applies-to-all-networks-that-share-the-same-dns"></a>Azure Monitor Private Link는 동일한 DNS를 공유하는 모든 네트워크에 적용됩니다.
일부 네트워크는 여러 VNet이나 다른 연결된 네트워크로 구성됩니다. 이러한 네트워크가 동일한 DNS를 공유하는 경우 해당 네트워크에 대한 Private Link를 설정하면 DNS가 업데이트되고 모든 네트워크의 트래픽에 영향을 줍니다. 특히 위에서 설명한 “양자택일” 동작으로 인해 주의해야 합니다.

![복합 Vnet의 DNS 재정의 다이어그램](./media/private-link-security/dns-overrides-multiple-vnets.png)

위의 다이어그램에서 VNet 10.0.1.x는 먼저 AMPLS1에 연결하고, Azure Monitor 전역 엔드포인트를 해당 범위에서 IP에 매핑합니다. 이후에 VNet 10.0.2.x는 AMPLS2에 연결하고, 범위에서 IP를 사용하여 *동일한 글로벌 엔드포인트* 의 DNS 매핑을 재정의합니다. 이러한 Vnet는 피어링되지 않으므로, 첫 번째 VNet은 이제 이러한 엔드포인트에 도달하지 못합니다.


## <a name="next-steps"></a>다음 단계
- [Private Link 설정 디자인](private-link-design.md)
- [Private Link 구성](private-link-configure.md) 방법 알아보기

<h3><a id="connect-to-a-private-endpoint"></a></h3>
