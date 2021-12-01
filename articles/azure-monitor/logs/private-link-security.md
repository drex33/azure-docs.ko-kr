---
title: Azure Private Link를 사용하여 네트워크를 Azure Monitor에 연결
description: Azure Monitor Private Link 범위를 설정하여 네트워크를 Azure Monitor에 안전하게 연결합니다.
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 967a1cd40ca288c4211bb996c5d0f00ad51fcc79
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133367552"
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
Azure Monitor 개인 링크는 전용 끝점을 Azure Monitor 리소스 Log Analytics 작업 영역 및 Application Insights 리소스 집합에 연결 합니다. 이 세트를 AMPLS(Azure Monitor Private Link Scope)라고 합니다.

![기본 리소스 토폴로지 다이어그램](./media/private-link-security/private-link-basic-topology.png)

* 개인 Ip 사용-VNet의 개인 끝점은 이러한 끝점의 공용 Ip를 사용 하는 대신 네트워크 풀에서 개인 Ip를 통해 Azure Monitor 끝점에 연결할 수 있습니다. 이렇게 하면 불필요한 아웃 바운드 트래픽까지 VNet을 열지 않고도 Azure Monitor 리소스를 계속 사용할 수 있습니다. 
* Azure 백본에서 실행 되는 Azure Monitor 리소스에 대 한 개인 끝점의 트래픽은 Microsoft Azure 백본을 통해 공용 네트워크로 라우팅되지 않습니다.
* 연결할 수 있는 Azure Monitor 리소스 제어-개인 링크 리소스에만 트래픽을 허용 하거나 개인 링크 및 개인 링크 (AMPLS) 리소스 모두에 대 한 트래픽을 허용 하는 기본 액세스 모드로 Azure Monitor 개인 링크 범위를 구성 합니다.
* Azure Monitor 리소스에 대 한 네트워크 액세스 제어-공용 네트워크에서 트래픽을 허용 하거나 차단 하도록 각 작업 영역 또는 구성 요소를 구성 합니다. 수집 및 쿼리 요청에 대해 다른 설정을 적용할 수 있습니다.


### <a name="azure-monitor-private-links-rely-on-your-dns"></a>Azure Monitor 개인 링크가 DNS를 사용 합니다.
개인 링크 연결을 설정 하는 경우 개인 링크를 통해 트래픽을 전송 하기 위해 DNS 영역 Azure Monitor 끝점을 개인 Ip에 매핑합니다. Azure Monitor는 리소스 관련 끝점과 공유 전역/지역 끝점을 모두 사용 하 여 AMPLS의 작업 영역 및 구성 요소에 연결 합니다. 

> [!WARNING]
> Azure Monitor는 리소스와 관련이 없는 끝점을 의미 하는 공유 끝점을 사용 하기 때문에 단일 리소스에 대 한 개인 링크를 설정 하면 **모든 리소스** 에 대 한 트래픽 영향을 받는 DNS 구성이 변경 됩니다. 즉, 모든 작업 영역 또는 구성 요소에 대 한 트래픽은 단일 개인 링크 설정에 의해 영향을 받습니다. 자세한 내용은 아래를 참조 하세요.

공유 끝점을 사용 하는 것은 동일한 DNS를 공유 하는 모든 네트워크에 단일 AMPLS를 사용 해야 함을 의미 합니다. AMPLS 리소스를 여러 개 만들면 DNS 영역에서 서로를 재정의 하 고 기존 환경을 중단 하 게 Azure Monitor 됩니다. 자세히 알아보려면 [네트워크 토폴로지에서 계획](./private-link-design.md#plan-by-network-topology) 을 참조 하세요.


#### <a name="shared-global-and-regional-endpoints"></a>공유 전역 및 지역 끝점
단일 리소스에 대해서도 개인 링크를 구성 하는 경우 할당 된 개인 Ip를 통해 아래 끝점에 대 한 트래픽이 전송 됩니다.

* 모든 Application Insights 엔드포인트 - Application Insights 엔드포인트에 대해 수집, 라이브 메트릭, 프로파일러, 디버거 등을 처리하는 엔드포인트가 전역입니다.
* 쿼리 엔드포인트 - Application Insights와 Log Analytics 리소스 모두에 대한 쿼리를 처리하는 엔드포인트가 전역입니다.


> [!IMPORTANT]
> 개인 링크를 만들면 AMPLS의 리소스 뿐만 아니라 **모든** 모니터링 리소스에 대 한 트래픽에 영향을 줍니다. 효과적으로 모든 쿼리 요청 뿐만 아니라 Application Insights 구성 요소에 대 한 수집이 개인 ip를 통과 하도록 합니다. 그러나 이러한 모든 요청에는 개인 링크 유효성 검사가 적용 되는 것을 의미 하지는 않습니다.</br>
> AMPLS에 추가 되지 않은 리소스는 AMPLS 액세스 모드가 ' Open ' 인 경우에만 연결할 수 있으며, 대상 리소스는 공용 네트워크의 트래픽을 허용 합니다. 개인 IP를 사용 하는 동안 **개인 링크 유효성 검사는 AMPLS에 없는 리소스에는 적용** 되지 않습니다. 자세히 알아보려면 [개인 링크 액세스 모드](#private-link-access-modes-private-only-vs-open) 를 참조 하세요.

#### <a name="resource-specific-endpoints"></a>리소스별 엔드포인트
Log Analytics 끝점은 앞에서 설명한 쿼리 엔드포인트를 제외 하 고 작업 영역 별로 다릅니다. 결과적으로, 특정 Log Analytics 작업 영역을 AMPLS에 추가 하면 개인 링크를 통해이 작업 영역에 수집 요청을 보내고, 다른 작업 영역에 대 한 수집은 계속 해 서 공용 끝점을 사용 합니다.

> [!IMPORTANT]
> 2021 년 12 월 1 일부 터 개인 끝점 DNS 구성에서는 동일한 지역의 모든 작업 영역에 대해 단일 개인 IP 주소를 할당 하는 끝점 압축 메커니즘을 사용 합니다. 그러면 지원 되는 규모 (최대 300 개의 작업 영역 및 AMPLS 당 1000 구성 요소)가 개선 되 고 네트워크의 IP 풀에서 가져온 총 Ip 수가 줄어듭니다.  


### <a name="private-link-access-modes-private-only-vs-open"></a>Private Link 액세스 모드: 프라이빗 전용 및 공개
[Azure Monitor 개인 링크](#azure-monitor-private-links-rely-on-your-dns)에 설명 된 대로 dns를 사용 하 여 동일한 dns를 공유 하는 모든 네트워크에 대해 단일 AMPLS 리소스를 만들어야 합니다. 따라서 단일 전역 또는 지역 DNS를 사용하는 조직에는 실제로 모든 전역 또는 지역 네트워크에서 모든 Azure Monitor 리소스에 대한 트래픽을 관리하기 위한 단일 Private Link가 있습니다.

2021년 9월 이전에 만든 Private Link의 경우 다음을 의미합니다. 
* 로그 수집은 AMPLS의 리소스에 대해서만 작동합니다. 구독이나 테넌트에 관계없이 다른 모든 리소스에 대한 수집이 거부됩니다(동일한 DNS를 공유하는 모든 네트워크에서).
* 쿼리에는 더 공개적인 동작이 있으므로 쿼리 요청이 AMPLS에 없는 리소스에도 도달할 수 있습니다. 여기서 의도는 AMPLS에 없는 리소스에 대한 고객 쿼리 중단을 방지하고 리소스 중심 쿼리가 전체 결과 집합을 반환하도록 허용하는 것이었습니다.

그러나이 동작은 AMPLS에 없는 리소스에 대 한 수집을 중단 하기 때문에 일부 고객에 대해 너무 제한적이 고 다른 사용자에 게는 허용 되지 않습니다 (AMPLS에서 리소스를 쿼리할 수 있으므로).

따라서 2021년 9월부터 만들어진 Private Link에는 Private Link가 네트워크 트래픽에 미치는 영향을 명시적으로 설정하는 새로운 필수 AMPLS 설정이 있습니다. 새 AMPLS 리소스를 만들 때 이제는 수집과 쿼리에 대해 개별적으로 원하는 액세스 모드를 선택해야 합니다. 
* 프라이빗 전용 모드 - Private Link 리소스에 대한 트래픽만 허용합니다.
* 공개 모드 - Private Link를 사용하여 AMPLS의 리소스와 통신하지만 트래픽도 다른 리소스로 계속 진행할 수 있습니다. 자세한 내용은 [Private Links가 네트워크에 적용되는 방식 제어](./private-link-design.md#control-how-private-links-apply-to-your-networks)를 참조하세요.

> [!NOTE]
> Log Analytics 수집은 리소스 관련 끝점을 사용 합니다. 따라서 AMPLS 액세스 모드를 따르지 않습니다. **Log Analytics 수집 요청에서 AMPLS의 작업 영역에 액세스할 수 없도록 하려면 AMPLS 액세스 모드에 관계 없이 공용 끝점에 대 한 트래픽을 차단 하도록 네트워크 방화벽을 설정** 합니다.

## <a name="next-steps"></a>다음 단계
- [Private Link 설정 디자인](private-link-design.md)
- [Private Link 구성](private-link-configure.md) 방법 알아보기
- 사용자 지정 로그 및 CMK (고객 관리 키)의 [개인 저장소](private-storage.md) 에 대해 알아보기
<h3><a id="connect-to-a-private-endpoint"></a></h3>
