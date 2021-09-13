---
title: Private Link 설정 설계
description: Private Link 설정 설계
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 08/01/2021
ms.openlocfilehash: d6d5b5bf1cba2ebb2def30b15f3a70dea91e5ff7
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123272604"
---
# <a name="design-your-private-link-setup"></a>Private Link 설정 설계

Azure Monitor Private Link를 설정하기 전에 네트워크 토폴로지와 특히 DNS 라우팅 토폴로지를 고려합니다.
[작동 방식에서](./private-link-security.md#how-it-works) 설명한 대로 Private Link 설정하면 모든 Azure Monitor 리소스에 대한 트래픽에 영향을 미칩니다. Application Insights 리소스의 경우 특히 그렇습니다. 또한 프라이빗 엔드포인트에 연결된 네트워크뿐만 아니라 동일한 DNS를 공유하는 다른 모든 네트워크에도 영향을 미칩니다.

가장 간단하고 안전한 방법은 다음과 같습니다.
1. 단일 프라이빗 엔드포인트 및 단일 AMPLS를 사용하여 단일 Private Link 연결을 만듭니다. 네트워크가 피어된 경우 공유(또는 허브) VNet에 Private Link 연결을 만듭니다.
2. *모든* Azure Monitor 리소스(로그 분석 작업 공간 및 Application Insights 구성 요소)를 AMPLS에 연결합니다.
3. 네트워크 송신 트래픽을 최대한 차단합니다.


## <a name="plan-by-network-topology"></a>네트워크 토폴로지에 관한 계획

### <a name="guiding-principle-avoid-dns-overrides-by-using-a-single-ampls"></a>기본 원칙: 단일 AMPLS를 사용하여 DNS 재정의 방지
일부 네트워크는 여러 VNet이나 다른 연결된 네트워크로 구성됩니다. 이러한 네트워크가 동일한 DNS를 공유하는 경우 해당 네트워크에 대한 Private Link를 설정하면 DNS가 업데이트되고 모든 네트워크의 트래픽에 영향을 줍니다.

아래 다이어그램에서 VNet 10.0.1.x는 먼저 AMPLS1에 연결하고, Azure Monitor 전역 엔드포인트를 해당 범위에서 IP에 매핑합니다. 이후에 VNet 10.0.2.x는 AMPLS2에 연결하고, 범위에서 IP를 사용하여 **동일한 글로벌 엔드포인트** 의 DNS 매핑을 재정의합니다. 이러한 Vnet는 피어링되지 않으므로, 첫 번째 VNet은 이제 이러한 엔드포인트에 도달하지 못합니다.

이 충돌을 방지하려면 DNS당 단일 AMPLS 개체만 만듭니다.

![복합 Vnet의 DNS 재정의 다이어그램](./media/private-link-security/dns-overrides-multiple-vnets.png)


### <a name="hub-and-spoke-networks"></a>허브 및 스포크 네트워크
허브 및 스포크 네트워크는 각 스포크 VNet이 아닌 허브(기본) 네트워크에 설정된 단일 Private Link 연결을 사용해야 합니다. 

![Hub-및-spoke-단일-PE](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

> [!NOTE]
> 예를 들자면 각 VNet이 제한된 모니터링 리소스 집합에 액세스할 수 있도록 하는 등 spoke Vnet 별도의 프라이빗 링크를 만들 수 있습니다. 이러한 경우에는 각 VNet에 대한 전용 프라이빗 엔드포인트 및 AMPLS를 만들 수 있지만, **DNS 재정의를 방지하기 위해 동일한 DNS 영역을 공유하지 않는지 확인해야 합니다**.

### <a name="peered-networks"></a>피어된 네트워크
네트워크 피어링이 허브-스포크 이외의 다양한 토폴로지에서 사용됩니다. 이러한 네트워크는 서로의 IP 주소에 연결할 수 있으며 대부분 동일한 DNS를 공유할 수 있습니다. 이 경우 한 번 더 다른 네트워크에서 액세스할 수 있는 네트워크에 단일 Private Link를 만드는 것이 좋습니다. 궁극적으로 DNS의 마지막 한 세트만 적용되므로 여러 프라이빗 엔드포인트와 AMPLS 개체를 만들지 마세요.

### <a name="isolated-networks"></a>격리된 네트워크
네트워크가 피어되지 않은 경우 **Private Links를 사용하려면 DNS도 분리해야 합니다**. 작업이 완료된 후 각 네트워크에 대해 별도의 프라이빗 엔드포인트와 별도의 AMPLS 개체를 만듭니다. AMPLS 개체는 동일한 작업 영역/구성 요소 또는 다른 작업 영역/구성 요소에 연결할 수 있습니다.

### <a name="testing-locally-edit-your-machines-hosts-file-instead-of-the-dns"></a>현지 테스트는 DNS 대신 컴퓨터의 호스트 파일 편집합니다. 
네트워크의 다른 클라이언트에 영향을 주지 않고 로컬로 Private Link를 테스트하려면 프라이빗 엔드포인트를 만들 때 DNS를 업데이트하지 않아야 합니다. 대신 Private Link 엔드포인트에 요청을 보내도록 머신에서 호스트 파일을 편집합니다.
* Private Link 설정하지만 프라이빗 엔드포인트에 연결할 때 DNS와 자동 통합하지 **않도록** 선택합니다(5b단계).
* 컴퓨터의 호스트 파일에서 관련 엔드포인트를 구성합니다. 매핑이 필요한 Azure Monitor 엔드포인트를 검토하려면 [엔드포인트의 DNS 설정 검토를 참조하세요](./private-link-configure.md#reviewing-your-endpoints-dns-settings).

프로덕션 환경에는 이 방법을 권장하지 않습니다.

## <a name="control-how-private-links-apply-to-your-networks"></a>Private Link가 네트워크에 적용되는 방식 제어
Private Link 액세스 모드(2021년 8월에 도입됨)를 사용하면 Private Link가 네트워크 트래픽에 영향을 미치는 방식을 제어할 수 있습니다. 이 설정은 AMPLS 개체(연결된 모든 네트워크에 영향을 주도록) 또는 연결된 특정 네트워크에 적용될 수 있습니다.

적절한 액세스 모드를 선택하면 네트워크 트래픽에 해로운 영향을 미칩니다. 이러한 모드는 각각 다음과 같이 개별적으로 수집과 쿼리에 대해 설정할 수 있습니다.

* 프라이빗 전용 - VNet이 Private Link 리소스(AMPLS의 리소스)에만 도달할 수 있습니다. 이것이 데이터 반출을 방지하는 가장 안전한 작업 모드입니다. 이를 위해 AMPLS에서 Azure Monitor 리소스에 대한 트래픽이 차단됩니다.
![AMPLS 프라이빗 전용 액세스 모드의 다이어그램](./media/private-link-security/ampls-private-only-access-mode.png)
* 공개 - VNet이 Private Link 리소스와 AMPLS에 없는 리소스에 둘 다 도달할 수 있습니다([공용 네트워크의 트래픽을 수락](./private-link-design.md#control-network-access-to-your-resources)하는 경우). 공개 액세스 모드는 데이터 반출을 방지하지는 않지만 여전히 Private Link의 다른 이점을 제공합니다. Private Link 리소스에 대한 트래픽은 프라이빗 엔드포인트를 통해 전송되고, 유효성이 검사되고, Microsoft 백본을 통해 전송됩니다. 공개 모드는 혼합 작업 모드(공개적으로 일부 리소스에 액세스하고 Private Link를 통해 다른 리소스에 액세스) 또는 점진적 온보딩 프로세스 중에 유용합니다.
![AMPLS 공개 액세스 모드의 다이어그램](./media/private-link-security/ampls-open-access-mode.png) 액세스 모드는 수집과 쿼리에 대해 개별적으로 설정됩니다. 예를 들어, 수집에 대해 프라이빗 전용 모드를 설정하고 쿼리에 대해 공개 모드를 설정할 수 있습니다.

> [!NOTE]
> 액세스 모드를 선택할 때 주의 적용: 프라이빗 전용 액세스 모드를 사용하면 구독이나 테넌트에 관계없이 동일한 DNS를 공유하는 모든 네트워크에서 AMPLS에 없는 리소스에 대한 트래픽이 차단됩니다. 모든 Azure Monitor 리소스를 AMPLS에 추가할 수 없는 경우 공개 모드를 사용하고 선택 리소스를 AMPLS에 추가하는 것이 좋습니다. 모든 Azure Monitor 리소스를 AMPLS에 추가한 후에만 보안을 최대화하기 위해 프라이빗 전용 모드로 전환합니다.

### <a name="setting-access-modes-for-specific-networks"></a>특정 네트워크에 대한 액세스 모드 설정
AMPLS 리소스에 설정된 액세스 모드는 모든 네트워크에 영향을 주지만 특정 네트워크에 대해 해당 설정을 재정의할 수 있습니다.

다음 다이어그램에서 VNet1은 공개 모드를 사용하고 VNet2는 프라이빗 전용 모드를 사용합니다. 따라서 VNet1의 요청은 Private Link를 통해 Workspace1 및 Component2에 도달하고, Component3은 Private Link를 통해 도달할 수 없습니다([공용 네트워크의 트래픽을 허용](./private-link-design.md#control-network-access-to-your-resources)하는 경우). 그러나 VNet2 요청은 Component3에 도달할 수 없습니다. 
![혼합 액세스 모드의 다이어그램](./media/private-link-security/ampls-mixed-access-modes.png)


## <a name="consider-ampls-limits"></a>AMPLS 제한 고려
AMPLS의 제한 개체는 다음과 같습니다.
* VNet은 1개의 AMPLS 개체 **하나만** 연결할 수 있습니다. 즉, AMPLS 개체는 VNet이 액세스할 수 있어야 하는 모든 Azure Monitor 리소스에 대한 액세스를 제공해야 합니다.
* 한 AMPLS 개체는 최대 50개의 Azure Monitor 리소스에 연결할 수 있습니다.
* Azure Monitor 리소스(작업 영역 또는 Application Insights 구성 요소)는 최대 5개의 AMPLS에 연결할 수 있습니다.
* 한 AMPLS 개체는 최대 10개의 프라이빗 엔드포인트에 연결할 수 있습니다.

아래 다이어그램에서 다음을 수행합니다.
* 각 VNet은 **1개의** AMPLS 개체에만 연결합니다.
* AMPLS A는 세 개에서 50개의 가능한 Azure Monitor 리소스 연결을 사용하여 두 개의 작업 영역 및 하나의 애플리케이션 정보 구성 요소에 연결합니다.
* Workspace2는 다섯 개의 가능한 AMPLS 연결 중 두 가지를 사용하여 AMPLS A 및 AMPLS B에 연결합니다.
* AMPLS B는 열 개 중 두 개의 프라이빗 엔드포인트를 사용하여 두 Vnet(VNet2 및 VNet3)의 프라이빗 엔드포인트로 연결됩니다.

![AMPLS 제한 다이어그램](./media/private-link-security/ampls-limits.png)


## <a name="control-network-access-to-your-resources"></a>리소스에 대한 네트워크 액세스 제어
Log Analytics 작업 영역 또는 Application Insights 구성 요소를 다음으로 설정할 수 있습니다.
* 공용 네트워크(리소스 AMPLS에 연결되지 않은 네트워크)의 수집을 허용하거나 차단합니다.
* 공용 네트워크(리소스 AMPLS에 연결되지 않은 네트워크)의 쿼리를 허용하거나 차단합니다.

이러한 세분성을 통해 작업 영역별로 필요에 따라 액세스를 설정할 수 있습니다. 예를 들어, 연결된 Private Link 네트워크(특정 VNet을 의미함)를 통해서만 데이터 수집을 허용하지만 공용 및 개인의 모든 네트워크에서 쿼리를 허용하도록 선택할 수 있습니다. 

공용 네트워크의 쿼리를 차단하는 것은 연결된 AMPLS 외부의 클라이언트(머신, SDK 등)가 리소스의 데이터를 쿼리할 수 없다는 것을 의미합니다. 해당 데이터에는 로그, 메트릭, 라이브 메트릭 스트림이 포함됩니다. 공용 네트워크의 쿼리를 차단하면 통합 문서, 대시보드, Azure Portal의 Insights, Azure Portal 외부에서 실행되는 쿼리 등 이러한 쿼리를 실행하는 모든 환경에 영향을 줍니다.

### <a name="exceptions"></a>예외

#### <a name="diagnostic-logs"></a>진단 로그
[진단 설정](../essentials/diagnostic-settings.md)을 통해 작업 영역에 업로드된 로그 및 메트릭은 안전한 프라이빗 Microsoft 채널을 통해 이동하며 이러한 설정으로 제어되지 않습니다.

#### <a name="azure-resource-manager"></a>Azure Resource Manager
위에서 설명한 액세스 제한은 리소스의 데이터에 적용됩니다. 이러한 액세스 설정의 켜기 또는 끄기를 포함한 구성 변경은 Azure Resource Manager에서 관리합니다. 이 설정을 제어하려면 적절한 역할, 권한, 네트워크 제어 및 감사를 통해 리소스에 대한 액세스를 제한해야 합니다. 자세한 내용은 [Azure Monitor 역할, 권한 및 보안](../roles-permissions-security.md)을 참조하세요.

> [!NOTE]
> ARM(Azure Resource Management) API를 통해 전송된 쿼리는 Azure Monitor Private Link를 사용할 수 없습니다. 이 쿼리는 대상 리소스가 공용 네트워크의 쿼리를 허용하는 경우에만 진행할 수 있습니다(네트워크 격리 블레이드 또는 [CLI를 사용](./private-link-configure.md#set-resource-access-flags)하여 설정).
>
> ARM API를 통해 쿼리를 실행하는 것으로 알려진 환경은 다음과 같습니다.
> * Sentinel
> * LogicApp 커넥터
> * 업데이트 관리 솔루션
> * 변경 내용 추적 솔루션
> * VM 인사이트
> * 컨테이너 인사이트
> * Log Analytics의 작업 영역 요약 블레이드(솔루션 대시보드 표시)

## <a name="application-insights-considerations"></a>Application Insight 고려 사항
* 모니터링되는 워크로드를 호스팅하는 리소스를 ㅖPrivate Link에 추가해야 합니다. 예를 들어, [Azure 웹 앱용 프라이빗 엔드포인트 사용](../../app-service/networking/private-endpoint.md)을 참조하십시오.
* 포털이 아닌 사용 환경은 모니터링되는 워크로드를 포함하는 프라이빗 링크 VNET에서도 실행해야 합니다.
* 프로파일러 및 디버거에 대한 Private Links를 지원하려면 [고유한 스토리지 계정을 제공해야](../app/profiler-bring-your-own-storage.md) 합니다. 

> [!NOTE]
> 작업 영역 기반 Application Insights를 완벽하게 보호하려면 Application Insights 리소스 및 기본 Log Analytics 작업 영역에 대한 액세스를 모두 잠가야 합니다.

## <a name="log-analytics-considerations"></a>로그 분석 비용에 대한 고려 사항

### <a name="log-analytics-solution-packs-download"></a>Log Analytics 솔루션 팩 다운로드
Log Analytics 에이전트는 전역 저장소 계정에 액세스하여 솔루션 팩을 다운로드해야 합니다. 2021년 4월 19일 또는 그 이후(또는 Azure 소버린 클라우드에서 2021년 6월 시작)에 생성된 Private Link 설정은 Private Link를 통해 에이전트의 솔루션 팩 저장소에 연결할 수 있습니다. ‘Blob.core.windows.net’용으로 만든 새 DNS 영역을 통해 이기능을 수행할 수 있습니다.

프라이빗 링크 설정이 2021년 4월 19일 이전에 생성된 경우에는 프라이빗 링크를 통해 솔루션 팩 저장소에 연결되지 않습니다. 이를 처리하려면 다음 중 하나를 수행할 수 있습니다.
* AMPLS와 연결된 프라이빗 엔드포인트를 다시 만듭니다.
* 방화벽 허용 목록에 다음 규칙을 추가하여 에이전트가 공용 엔드포인트를 통해 저장소 계정에 연결할 수 있도록 허용합니다.

    | 클라우드 환경 | 에이전트 리소스 | 포트 | Direction |
    |:--|:--|:--|:--|
    |Azure 공용     | scadvisorcontent.blob.core.windows.net         | 443 | 아웃바운드
    |Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  아웃바운드
    |Azure China 21Vianet      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | 아웃바운드

### <a name="collecting-custom-logs-and-iis-log-over-private-link"></a>Private Link를 통한 사용자 지정 로그 및 IIS 로그 수집
스토리지 계정은 사용자 지정 로그 수집 프로세스에서 사용됩니다. 기본적으로 서비스 관리형 스토리지 계정이 사용됩니다. 그러나 프라이빗 링크에서 사용자 지정 로그를 수집하려면 사용자 고유의 스토리지 계정을 사용하여 Log Analytics 작업 영역에 연결해야 합니다. [명령줄](/cli/azure/monitor/log-analytics/workspace/linked-storage)을 사용하여 이러한 계정을 설정하는 방법에 대한 자세한 내용을 참조하세요.

사용자 고유의 스토리지 계정을 가져오는 방법에 대한 자세한 내용은 [로그 수집에 고객이 소유한 스토리지 계정 사용](private-storage.md)을 참조하세요.

### <a name="automation"></a>Automation
Automation 계정이 필요한 Log Analytics 솔루션(예: 업데이트 관리, 변경 내용 추적 또는 인벤토리)을 사용하는 경우 Automation 계정의 Private Link도 만들어야 합니다. 자세한 내용은 [Azure Private Link를 사용하여 네트워크를 Azure Automation에 안전하게 연결](../../automation/how-to/private-link-security.md)을 참조하세요.

> [!NOTE]
> 일부 제품 및 Azure Portal 경험은 Azure Resource Manager를 통해 데이터를 쿼리하므로 Private Link 설정이 Resource Manager에도 적용되지 않는 한 Private Link를 통해 데이터를 쿼리할 수 없습니다. 이를 해결 하려면 [리소스에 대한 네트워크 액세스 제어](./private-link-design.md#control-network-access-to-your-resources) (수집은 개인 링크 네트워크로 제한 됨)에 설명된 대로 공용 네트워크의 쿼리를 허용 하도록 리소스를 구성할 수 있습니다.
Azure Resource Manager를 통해 다음 제품을 확인하고 쿼리 작업 영역을 경험합니다.
> * LogicApp 커넥터
> * 업데이트 관리 솔루션
> * 변경 내용 추적 솔루션
> * 포털의 작업 영역 요약 블레이드 (솔루션 대시보드 표시)
> * VM 인사이트
> * 컨테이너 인사이트

## <a name="requirements"></a>요구 사항
### <a name="agents"></a>에이전트
로그 분석 작업 영역에 대한 안전한 원격 분석 수집을 사용하도록 설정하려면 최신 버전의 Windows 및 Linux 에이전트를 사용해야 합니다. 이전 버전에서는 프라이빗 네트워크에서 모니터링 데이터를 업로드할 수 없습니다.

**Log Analytics Windows 에이전트**

Log Analytics 에이전트 버전 10.20.18038.0 이상을 사용합니다.

**Log Analytics Linux 에이전트**

에이전트 버전 1.12.25 이상을 사용합니다. 할 수 없는 경우 VM에서 다음 명령을 실행합니다.

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```
### <a name="azure-portal"></a>Azure portal
Application Insights 및 Log Analytics와 같은 Azure Monitor 포털 환경을 사용하려면 개인 네트워크에서 Azure Portal 및 Azure Monitor 확장에 액세스할 수 있어야 합니다. **AzureActiveDirectory**, **AzureResourceManager**, AzureFrontDoor.FirstParty 및 **AzureFrontdoor.Frontend** [서비스 태그를](../../firewall/service-tags.md) 네트워크 보안 그룹에 추가합니다.

### <a name="programmatic-access"></a>프로그래밍 방식 액세스
프라이빗 네트워크의 Azure Monitor에서 REST API, [CLI](/cli/azure/monitor) 또는 PowerShell을 사용하려면 **AzureActiveDirectory** 및 **AzureResourceManager** [서비스 태그](../../virtual-network/service-tags-overview.md)를 방화벽에 추가합니다.

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>콘텐츠 배달 네트워크에서 Application Insights SDK 다운로드
브라우저에서 CDN으로부터 코드를 다운로드하려고 시도하지 않도록 스크립트에서 JavaScript 코드를 추가로 줍니다. 예제는 [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup)에서 제공됩니다.

### <a name="browser-dns-settings"></a>브라우저 DNS 설정
프라이빗 링크를 통해 Azure Monitor 리소스에 연결하는 경우 이러한 리소스에 대한 트래픽은 네트워크에 구성된 프라이빗 엔드포인트를 통해 이동해야 합니다. 프라이빗 엔드포인트를 사용하도록 설정하려면 [프라이빗 엔드포인트에 연결](./private-link-configure.md#connect-to-a-private-endpoint)에 설명된 대로 DNS 설정을 업데이트합니다. 일부 브라우저는 사용자가 설정하는 대신 자체 DNS 설정을 사용합니다. 브라우저는 Azure Monitor 공용 엔드포인트에 연결을 시도하고 프라이빗 링크를 완전히 우회할 수 있습니다. 브라우저 설정이 이전 DNS 설정을 재정의하거나 캐시하지 않는지 확인합니다. 

### <a name="querying-limitation-externaldata-operator"></a>쿼리 제한: externaldata 연산자
[ `externaldata` 운영자](/azure/data-explorer/kusto/query/externaldata-operator?pivots=azuremonitor)는 프라이빗 링크에 대해 지원되지 않지만 스토리지 계정에서 데이터를 읽으므로, 스토리지에 대해 개인적으로 액세스되는 걸 보장하지는 않습니다.

## <a name="next-steps"></a>다음 단계
- [Private Link를 구성하는 방법](private-link-configure.md)을 참조하세요.
- [개인 스토리지](private-storage.md)에 대한 자세한 정보
- [Private Link 자동화](../../automation/how-to/private-link-security.md)에 대해 자세히 알아보기: