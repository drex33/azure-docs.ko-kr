---
title: Azure Private Link를 사용하여 네트워크를 Azure Monitor에 연결
description: Azure Monitor Private Link 범위를 설정하여 네트워크를 Azure Monitor에 안전하게 연결합니다.
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: e175439cacb75fc50574f172d9e1e34cba4cdbd7
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426403"
---
# <a name="use-azure-private-link-to-connect-networks-to-azure-monitor"></a>Azure Private Link를 사용하여 네트워크를 Azure Monitor에 연결

[Azure Private Link](../../private-link/private-link-overview.md)를 사용하면 프라이빗 엔드포인트를 사용하여 Azure PaaS(Platform as a Service) 리소스를 가상 네트워크에 안전하게 연결할 수 있습니다. Azure Monitor는 동시에 작동하여 워크로드를 모니터링하는 상호 연결된 서로 다른 서비스의 모음입니다. Azure Monitor Private Link는 프라이빗 엔드포인트를 Azure Monitor 리소스 세트에 연결하여 모니터링 네트워크의 경계를 정의합니다. 이 세트를 AMPLS(Azure Monitor Private Link Scope)라고 합니다.


## <a name="advantages"></a>장점

Private Link를 사용하면 다음을 수행할 수 있습니다.

- 공용 네트워크 액세스를 개방하지 않고 Azure Monitor에 비공개로 연결
- 권한 있는 개인 네트워크를 통해서만 모니터링 데이터에 액세스할 수 있는지 확인
- 프라이빗 엔드포인트를 통해 연결되는 특정 Azure Monitor 리소스를 정의하여 개인 네트워크에서 데이터 반출 방지
- ExpressRoute 및 Private Link를 사용하여 온-프레미스 개인 네트워크를 Azure Monitor에 안전하게 연결
- Microsoft Azure 백본 네트워크 내에서 모든 트래픽 유지

자세한 내용은 [Private Link의 주요 이점](../../private-link/private-link-overview.md#key-benefits)을 참조하세요.

## <a name="how-it-works"></a>작동 방법

### <a name="overview"></a>개요
Azure Monitor Private Link Scope는 프라이빗 엔드포인트(및 해당 엔드포인트가 포함된 VNet)를 하나 이상의 Azure Monitor 리소스인 Log Analytics 작업 영역, Application Insights 구성 요소에 연결합니다.

![기본 리소스 토폴로지 다이어그램](./media/private-link-security/private-link-basic-topology.png)

* Azure Monitor 개인 링크는 전용 끝점을 Azure Monitor 리소스 Log Analytics 작업 영역 및 Application Insights 리소스 집합에 연결 합니다. 이 세트를 AMPLS(Azure Monitor Private Link Scope)라고 합니다.
* VNet의 프라이빗 엔드포인트는 이러한 엔드포인트의 공용 IP를 사용하는 대신 네트워크 풀에서 프라이빗 IP를 통해 Azure Monitor 엔드포인트에 도착할 수 있습니다. 이렇게 하면 불필요한 아웃 바운드 트래픽까지 VNet을 열지 않고도 Azure Monitor 리소스를 계속 사용할 수 있습니다. 
* 프라이빗 엔드포인트에서 Azure Monitor 리소스에 대한 트래픽은 Microsoft Azure 백본을 지나며, 공용 네트워크로 라우팅되지 않습니다.
* Azure Monitor 개인 링크 범위 (또는 it에 연결 하는 특정 네트워크)를 사용 하 여 개인 링크 리소스에만 트래픽을 허용 하거나 개인 링크 리소스와 개인 링크를 사용 하지 않는 리소스 (AMPLS의 리소스) 모두에 대 한 트래픽을 허용 하도록 구성할 수 있습니다.
* 공용 네트워크에서 수집 및 쿼리를 허용하거나 거부하도록 각 작업 영역 또는 컴포넌트를 구성할 수 있습니다. 리소스 수준의 보호를 제공하므로 특정 리소스에 대한 트래픽을 제어할 수 있습니다.

> [!NOTE]
> VNet은 Private Link를 통해 연결할 수 있는 최대 50개의 리소스를 나열하는 단일 AMPLS에만 연결할 수 있습니다.

### <a name="azure-monitor-private-link-relies-on-your-dns"></a>Azure Monitor Private Link가 DNS를 사용함
Private Link 연결을 설정하면 Private Link를 통해 트래픽을 전송하기 위해 Azure Monitor 엔드포인트를 개인 IP에 매핑하도록 DNS 영역이 설정됩니다. Azure Monitor는 리소스별 엔드포인트와 여러 작업 영역/구성 요소에 대한 트래픽을 처리하는 지역 또는 전역 엔드포인트를 둘 다 사용합니다. 지역 및 전역 엔드포인트와 관련하여 단일 리소스에도 Private Link를 설정하면 **모든** 리소스에 대한 트래픽을 제어하는 DNS 매핑에 영향을 줍니다. 즉, 모든 작업 영역이나 구성 요소에 대한 트래픽이 단일 Private Link 설정의 영향을 받을 수 있습니다.

#### <a name="global-endpoints"></a>전역 엔드포인트
가장 중요한 점은 아래 전역 엔드포인트에 대한 트래픽이 Private Link를 통해 전송된다는 점입니다.
* 모든 Application Insights 엔드포인트 - Application Insights 엔드포인트에 대해 수집, 라이브 메트릭, 프로파일러, 디버거 등을 처리하는 엔드포인트가 전역입니다.
* 쿼리 엔드포인트 - Application Insights와 Log Analytics 리소스 모두에 대한 쿼리를 처리하는 엔드포인트가 전역입니다.

실제로 모든 Application Insights 트래픽이 Private Link로 전송되고 Application Insights 및 Log Analytics 리소스에 대한 모든 쿼리가 Private Link로 전송된다는 의미입니다.

AMPLS에 추가되지 않은 Application Insights 리소스에 대한 트래픽은 Private Link 유효성 검사를 통과하지 못하고 실패합니다.

#### <a name="resource-specific-endpoints"></a>리소스별 엔드포인트
쿼리 엔드포인트를 제외한 모든 Log Analytics 엔드포인트가 작업 영역별로 다릅니다. 따라서 특정 Log Analytics 작업 영역에 대한 Private Link를 만들면 다른 작업 영역에 대한 수집에 영향을 주지 않으며 퍼블릭 엔드포인트를 계속 사용합니다.


> [!NOTE]
> 동일한 DNS를 공유하는 모든 네트워크에 대해 단일 AMPLS만 만듭니다. AMPLS 리소스를 여러 개 만들면 DNS 영역에서 서로를 재정의 하 고 기존 환경을 중단 하 게 Azure Monitor 됩니다.

### <a name="private-link-access-modes-private-only-vs-open"></a>Private Link 액세스 모드: 프라이빗 전용 및 공개
[Azure Monitor 개인 링크](#azure-monitor-private-link-relies-on-your-dns)에 설명 된 대로 dns를 사용 하 여 동일한 dns를 공유 하는 모든 네트워크에 대해 단일 AMPLS 리소스를 만들어야 합니다. 따라서 단일 전역 또는 지역 DNS를 사용하는 조직에는 실제로 모든 전역 또는 지역 네트워크에서 모든 Azure Monitor 리소스에 대한 트래픽을 관리하기 위한 단일 Private Link가 있습니다.

2021년 9월 이전에 만든 Private Link의 경우 다음을 의미합니다. 
* 로그 수집은 AMPLS의 리소스에 대해서만 작동합니다. 구독이나 테넌트에 관계없이 다른 모든 리소스에 대한 수집이 거부됩니다(동일한 DNS를 공유하는 모든 네트워크에서).
* 쿼리에는 더 공개적인 동작이 있으므로 쿼리 요청이 AMPLS에 없는 리소스에도 도달할 수 있습니다. 여기서 의도는 AMPLS에 없는 리소스에 대한 고객 쿼리 중단을 방지하고 리소스 중심 쿼리가 전체 결과 집합을 반환하도록 허용하는 것이었습니다.

그러나 이 동작은 일부 고객에게는 너무 제한적이고(AMPLS에 없는 리소스에 대한 수집을 중단하기 때문에), 다른 고객에게는 너무 허용적이며(AMPLS에 없는 리소스 쿼리를 허용하기 때문에), 일반적으로 혼동되는 것으로 확인되었습니다.

따라서 2021년 9월부터 만들어진 Private Link에는 Private Link가 네트워크 트래픽에 미치는 영향을 명시적으로 설정하는 새로운 필수 AMPLS 설정이 있습니다. 새 AMPLS 리소스를 만들 때 이제는 수집과 쿼리에 대해 개별적으로 원하는 액세스 모드를 선택해야 합니다. 
* 프라이빗 전용 모드 - Private Link 리소스에 대한 트래픽만 허용합니다.
* 공개 모드 - Private Link를 사용하여 AMPLS의 리소스와 통신하지만 트래픽도 다른 리소스로 계속 진행할 수 있습니다. 자세한 내용은 [Private Links가 네트워크에 적용되는 방식 제어](./private-link-design.md#control-how-private-links-apply-to-your-networks)를 참조하세요.

> [!NOTE]
> Log Analytics 수집은 리소스 관련 끝점을 사용 합니다. 따라서 AMPLS 액세스 모드를 따르지 않습니다. AMPLS의 작업 영역에 대 한 수집은 개인 링크를 통해 전송 되 고, AMPLS에 없는 작업 영역에 대 한 수집은 기본 공용 끝점을 사용 합니다. 수집 요청이 AMPLS에서 리소스에 액세스할 수 없도록 하려면 공용 끝점에 대 한 네트워크 액세스를 차단 합니다.

## <a name="next-steps"></a>다음 단계
- [Private Link 설정 디자인](private-link-design.md)
- [Private Link 구성](private-link-configure.md) 방법 알아보기

<h3><a id="connect-to-a-private-endpoint"></a></h3>
