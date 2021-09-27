---
title: Azure의 연결 모니터 | Microsoft Docs
description: 연결 모니터를 사용하여 분산 환경에서 네트워크 통신을 모니터링하는 방법을 알아봅니다.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: ecb99e0add9700bce297f2dfc31e8220e02b0315
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128652968"
---
# <a name="network-connectivity-monitoring-with-connection-monitor"></a>연결 모니터를 사용하여 네트워크 연결 모니터링

> [!IMPORTANT]
> 2021년 7월 1일부터는 기존 작업 영역에 새 테스트를 추가하거나 네트워크 성능 모니터에서 새 작업 영역을 사용하도록 설정할 수 없습니다. 또한 연결 모니터(클래식)에서 새 연결 모니터를 추가할 수 없습니다. 2021년 7월 1일 이전에 만든 테스트 및 연결 모니터는 계속 사용할 수 있습니다. 현재 워크로드에 대한 서비스 중단을 최소화하려면 2024년 2월 29일 이전에 [네트워크 성능 모니터에서 테스트를 마이그레이션](migrate-to-connection-monitor-from-network-performance-monitor.md)하거나 [연결 모니터(클래식)에서 Azure Network Watcher의 새 연결 모니터로 마이그레이션](migrate-to-connection-monitor-from-connection-monitor-classic.md)합니다.

연결 모니터는 Azure Network Watcher에서 통합된 엔드투엔드 연결 모니터링을 제공합니다. 연결 모니터 기능은 하이브리드 및 Azure 클라우드 배포를 지원합니다. Network Watcher는 Azure 배포에 대한 연결 관련 메트릭을 모니터링하고 진단하고 볼 수 있는 도구를 제공합니다.

연결 모니터의 일부 사용 사례는 다음과 같습니다.

- 프런트 엔드 웹 서버 VM이 다중 계층 애플리케이션에서 데이터베이스 서버 VM과 통신합니다. 두 VM 간의 네트워크 연결을 확인하려 합니다.
- 미국 동부 지역의 VM에서 미국 중부 지역의 VM을 ping하고 지역 간 네트워크 대기 시간을 비교하려 합니다.
- 시애틀, 워싱턴 및 애슈번, 버지니아에 여러 온-프레미스 사무실 사이트가 있습니다. 사무실 사이트는 Microsoft 365 URL에 연결됩니다. Microsoft 365 URL 사용자에 대해 시애틀과 애슈번 간의 대기 시간을 비교합니다.
- 하이브리드 애플리케이션에 Azure Storage 엔드포인트에 대한 연결이 필요합니다. 온-프레미스 사이트와 Azure 애플리케이션이 동일한 Azure Storage 엔드포인트에 연결됩니다. 온-프레미스 사이트의 대기 시간을 Azure 애플리케이션의 대기 시간과 비교하려 합니다.
- 온-프레미스 설정과 클라우드 애플리케이션을 호스트하는 Azure VM 간의 연결을 확인하려 합니다.

연결 모니터는 다음 두 가지 기능 즉, Network Watcher [연결 모니터(클래식)](./network-watcher-monitoring-overview.md#monitor-communication-between-a-virtual-machine-and-an-endpoint) 기능과 NPM(네트워크 성능 모니터) [서비스 연결 모니터](../azure-monitor/insights/network-performance-monitor-service-connectivity.md), [ExpressRoute 모니터링](../expressroute/how-to-npm.md), [성능 모니터링](../azure-monitor/insights/network-performance-monitor-performance-monitor.md) 기능의 장점을 결합합니다.

연결 모니터의 몇 가지 이점은 다음과 같습니다.

* Azure 및 하이브리드 모니터링 요구 사항에 맞는 통합된 직관적인 환경
* 지역 간, 작업 영역 간 연결 모니터링
* 높은 검색 빈도 및 네트워크 성능에 대한 가시성 향상
* 하이브리드 배포에 대한 더 빠른 경고
* HTTP, TCP 및 ICMP를 기반으로 하는 연결 확인 지원 
* Azure 및 비 Azure 테스트 설정 모두에 대한 메트릭 및 로그 분석 지원

![연결 모니터가 Azure VM, Azure가 아닌 호스트, 엔드포인트 및 데이터 스토리지 위치와 상호 작용하는 방식을 보여주는 다이어그램](./media/connection-monitor-2-preview/hero-graphic.png)

연결 모니터를 사용하여 모니터링을 시작하려면 다음 단계를 수행합니다. 

1. 모니터링 에이전트를 설치합니다.
1. 구독에서 Network Watcher를 사용하도록 설정합니다.
1. 연결 모니터를 만듭니다.
1. 데이터 분석 및 경고를 설정합니다.
1. 네트워크의 문제를 진단합니다.

다음 섹션에서는 이러한 단계에 대한 세부 정보를 제공합니다.

## <a name="install-monitoring-agents"></a>모니터링 에이전트 설치

연결 모니터는 간단한 실행 파일을 사용하여 연결 확인을 실행합니다. Azure 환경과 온-프레미스 환경 모두에서 연결 확인을 지원합니다. 사용하는 실행 파일은 VM이 Azure와 온-프레미스 중 어디에서 호스트되는지에 따라 달라집니다.

### <a name="agents-for-azure-virtual-machines"></a>Azure 가상 머신용 에이전트

연결 모니터가 Azure VM을 모니터링 원본으로 인식하도록 하려면 Network Watcher 에이전트 가상 머신 확장을 VM에 설치합니다. 이 확장은 *Network Watcher 확장* 이라고도 합니다. Azure 가상 머신에는 엔드투엔드 모니터링 및 기타 고급 기능을 트리거하기 위한 확장이 필요합니다. 

Network Watcher 확장은 [VM을 만들](./connection-monitor.md#create-the-first-vm)때 설치할 수 있습니다. [Linux](../virtual-machines/extensions/network-watcher-linux.md) 및 [Windows](../virtual-machines/extensions/network-watcher-windows.md)용 Network Watcher 확장을 별도로 설치, 구성하고 문제를 해결할 수도 있습니다.

NSG(네트워크 보안 그룹) 또는 방화벽에 대한 규칙은 원본과 대상 간의 통신을 차단할 수 있습니다. 연결 모니터는 이런 문제를 감지하고 토폴로지에 진단 메시지로 표시합니다. 연결 모니터링이 가능하도록 설정하려면 NSG 및 방화벽 규칙이 원본과 대상 간에 TCP 또는 ICMP를 통한 패킷을 허용하는지 확인합니다.

### <a name="agents-for-on-premises-machines"></a>온-프레미스 컴퓨터용 에이전트

연결 모니터가 온-프레미스 컴퓨터를 모니터링 원본으로 인식하도록 하려면 컴퓨터에 Log Analytics 에이전트를 설치합니다.  그런 다음, [네트워크 성능 모니터 솔루션](/azure/azure-monitor/insights/network-performance-monitor#configure-the-solution)을 사용하도록 설정합니다. 이러한 에이전트는 Log Analytics 작업 영역에 연결되므로 에이전트가 모니터링을 시작하려면 먼저 작업 영역 ID 및 기본 키를 설정해야 합니다.

Windows 머신에 Log Analytics 에이전트를 설치하려면 [Windows에 Log Analytics 에이전트 설치](../azure-monitor/agents/agent-windows.md)를 참조하세요.

경로에 방화벽 또는 NVA(네트워크 가상 어플라이언스)가 포함된 경우에는 대상에 연결할 수 있는지 확인합니다.

Windows 컴퓨터의 경우, 포트를 열려면 매개 변수 없이 PowerShell 창에서 관리자 권한으로 [EnableRules.ps1](https://aka.ms/npmpowershellscript) PowerShell 스크립트를 실행합니다.

Linux 컴퓨터의 경우 사용할 portNumber를 수동으로 변경해야 합니다. 
* 다음 경로로 이동: /var/opt/microsoft/omsagent/npm_state. 
* 파일 열기: npmdregistry
* 포트 번호 값 변경: ```“PortNumber:<port of your choice>”```

 사용되는 포트 번호는 작업 영역에서 사용되는 모든 에이전트에서 동일해야 합니다. 

스크립트를 통해 솔루션에 필요한 레지스트리 키가 만들어집니다. 또한 에이전트가 서로 TCP 연결을 만들 수 있도록 Windows 방화벽 규칙이 만들어집니다. 스크립트로 만들어진 레지스트리 키는 디버그 로그와 로그 파일의 경로를 기록할지 여부를 지정합니다. 스크립트는 통신에 사용되는 에이전트 TCP 포트도 정의합니다. 이러한 키 값은 스크립트에 의해 자동으로 설정됩니다. 이 키는 수동으로 변경하지 마십시오. 기본적으로 열리는 포트는 8084입니다. 스크립트에 매개 변수 portNumber를 지정하여 사용자 지정 포트를 사용할 수 있습니다. 스크립트가 실행되는 모든 컴퓨터에서 동일한 포트를 사용하십시오. Log Analytics 에이전트의 네트워크 요구 사항에 대해 [자세히 알아보세요](../azure-monitor/agents/log-analytics-agent.md#network-requirements).

스크립트는 Windows 방화벽만 로컬로 구성합니다. 네트워크 방화벽이 있는 경우 네트워크 성능 모니터에서 사용하는 TCP 포트를 대상으로 하는 트래픽을 허용하는지 확인해야 합니다.

LA Windows 에이전트는 다중 작업 영역 및 System Center Operations Manager 관리 그룹으로 데이터를 보낼 수 있습니다. Linux 에이전트는 작업 영역 또는 관리 그룹 중 하나만 단일 대상으로 보낼 수 있습니다.

#### <a name="enable-network-performance-monitor-solution-for-on-premise-machines"></a>온-프레미스 컴퓨터에 대 한 네트워크 성능 모니터 솔루션 사용 

온-프레미스 컴퓨터에 대 한 네트워크 성능 모니터 솔루션을 사용 하도록 설정 하려면 다음 단계를 수행 합니다. 

1. Azure Portal 홈 페이지에서로 이동 Network Watcher
2. 왼쪽의 모니터링 섹션에서 네트워크 성능 모니터을 선택 합니다. 
3. 포털에서 NPM 솔루션을 사용 하도록 설정 하 고 구독 별로 필터링 된 작업 영역 목록이 표시 됩니다. 
4. 새 작업 영역에 NPM 솔루션을 추가 하려면 포털의 왼쪽 위에서 "+ 추가 NPM"를 클릭 합니다. 
5. 솔루션을 사용 하도록 설정할 구독 및 작업 영역을 선택 하 고 만들기를 클릭 합니다.
6. 솔루션을 사용 하도록 설정한 후 포털에서 작업 영역을 표시 하는 데 몇 분 정도 걸립니다.

:::image type="content" source="./media/connection-monitor/network-performance-monitor-solution-enable.png" alt-text="연결 모니터에서 NPM 솔루션을 추가 하는 방법을 보여 주는 스크린샷" lightbox="./media/connection-monitor/network-performance-monitor-solution-enable.png":::

LA 에이전트와 달리 NPM 솔루션은 단일 LA 작업 영역에 데이터를 보내도록 구성 될 수 있습니다.

## <a name="enable-network-watcher-on-your-subscription"></a>구독에서 Network Watcher를 사용하도록 설정

가상 네트워크가 있는 모든 구독은 Network Watcher에서 사용하도록 설정됩니다. 구독에서 가상 네트워크를 만들 때 가상 네트워크의 지역 및 구독에서 Network Watcher가 자동으로 사용하도록 설정됩니다. 이렇게 자동으로 사용하도록 설정해도 리소스에 영향을 주거나 요금이 발생하지 않습니다. 구독에서 Network Watcher가 명시적으로 사용하지 않도록 설정되어 있는지 확인합니다. 

[해당 지역](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher&regions=all)에서 Network Watcher를 사용할 수 있는지 확인합니다. 자세한 내용은 [Network Watcher를 사용하도록 설정](./network-watcher-create.md)을 참조하세요.

## <a name="create-a-connection-monitor"></a>연결 모니터 만들기 

연결 모니터는 일정한 간격으로 통신을 모니터링합니다. 연결 가능성 및 대기 시간이 변경되면 알려줍니다. 원본 에이전트와 대상 엔드포인트 간의 현재 및 과거 네트워크 토폴로지를 확인할 수도 있습니다.

원본은 모니터링 에이전트가 설치된 온-프레미스 컴퓨터 또는 Azure VM일 수 있습니다. 대상 엔드포인트는 Microsoft 365 URL, Dynamics 365 URL, 사용자 지정 URL, Azure VM 리소스 ID, IPv4, IPv6, FQDN 또는 임의의 도메인 이름일 수 있습니다.

### <a name="access-connection-monitor"></a>연결 모니터에 액세스

1. Azure Portal 홈 페이지에서 **Network Watcher** 로 이동합니다.
1. 왼쪽의 **모니터링** 섹션에서 **연결 모니터** 를 선택합니다.
1. 연결 모니터에서 만든 모든 연결 모니터가 표시됩니다. 연결 모니터의 클래식 환경에서 만든 연결 모니터를 보려면 **연결 모니터** 탭으로 이동합니다.
    
  :::image type="content" source="./media/connection-monitor-2-preview/cm-resource-view.png" alt-text="연결 모니터에서 만든 연결 모니터를 보여주는 스크린샷" lightbox="./media/connection-monitor-2-preview/cm-resource-view.png":::

### <a name="create-a-connection-monitor"></a>연결 모니터 만들기

연결 모니터에서 만든 연결 모니터에서는 온-프레미스 컴퓨터와 Azure VM을 모두 원본으로 추가할 수 있습니다. 이러한 연결 모니터는 엔드포인트에 대한 연결을 모니터링할 수도 있습니다. 엔드포인트는 Azure 또는 다른 URL 또는 IP에 있을 수 있습니다.

연결 모니터에는 다음 엔터티가 포함됩니다.

* **연결 모니터 리소스** – 지역별 Azure 리소스입니다. 다음 엔터티는 모두 연결 모니터 리소스의 속성입니다.
* **엔드포인트** – 연결 확인에 참여하는 원본 또는 대상입니다. 엔드포인트의 예로는 Azure VM, 온-프레미스 에이전트, URL 및 IP가 있습니다.
* **테스트 구성** – 테스트를 위한 프로토콜별 구성입니다. 선택한 프로토콜에 따라 포트, 임계값, 테스트 빈도 및 기타 매개 변수를 정의할 수 있습니다.
* **테스트 그룹** – 원본 엔드포인트, 대상 엔드포인트 및 테스트 구성을 포함하는 그룹입니다. 연결 모니터는 둘 이상의 테스트 그룹을 포함할 수 있습니다.
* **테스트** – 원본 엔드포인트, 대상 엔드포인트 및 테스트 구성의 조합입니다. 테스트는 모니터링 데이터를 사용할 수 있는 가장 세분화된 수준입니다. 모니터링 데이터에는 실패한 검사 비율과 RTT(왕복 시간)가 포함됩니다.

 ![테스트 그룹과 테스트 간의 관계를 정의하는 연결 모니터를 보여주는 다이어그램](./media/connection-monitor-2-preview/cm-tg-2.png)

연결 모니터는 [Azure Portal](./connection-monitor-create-using-portal.md), [ARMClient](./connection-monitor-create-using-template.md) 또는 [PowerShell](connection-monitor-create-using-powershell.md)을 사용하여 만들 수 있습니다.

테스트 그룹에 추가하는 모든 원본, 대상 및 테스트 구성은 개별 테스트로 분류됩니다. 원본 및 대상을 분류하는 방법의 예는 다음과 같습니다.

* 테스트 그룹: TG1
* 원본: 3개(A, B, C)
* 대상: 2개(D, E)
* 테스트 구성: 2개(Config 1, Config 2)
* 만든 총 테스트: 12개

| 테스트 번호 | 원본 | 대상 | 테스트 구성 |
| --- | --- | --- | --- |
| 1 | A | D | Config 1 |
| 2 | A | D | Config 2 |
| 3 | A | E | Config 1 |
| 4 | A | E | Config 2 |
| 5 | b | D | Config 1 |
| 6 | b | D | Config 2 |
| 7 | b | E | Config 1 |
| 8 | b | E | Config 2 |
| 9 | C | D | Config 1 |
| 10 | C | D | Config 2 |
| 11 | C | E | Config 1 |
| 12 | C | E | Config 2 |

### <a name="scale-limits"></a> 확장 한도

연결 모니터의 규모 한도는 다음과 같습니다.

* 지역별 구독당 최대 연결 모니터 수: 100개
* 연결 모니터당 최대 테스트 그룹 수: 20개
* 연결 모니터당 최대 원본 및 대상 수: 100개
* 연결 모니터당 최대 테스트 구성 수: 20개

## <a name="analyze-monitoring-data-and-set-alerts"></a>모니터링 데이터 분석 및 경고 설정

연결 모니터를 만든 후 원본은 테스트 구성에 따라 대상에 대한 연결을 확인합니다.

### <a name="checks-in-a-test"></a>테스트의 검사

테스트 구성에서 선택한 프로토콜을 기반으로 연결 모니터는 원본-대상 쌍에 대한 일련의 검사를 실행합니다. 선택한 테스트 빈도에 따라 검사가 실행됩니다.

HTTP를 사용하는 경우 서비스는 유효한 응답 코드를 반환한 HTTP 응답 수를 계산합니다. PowerShell 및 CLI를 사용하여 유효한 응답 코드를 설정할 수 있습니다. 결과에 따라 실패한 검사의 백분율이 결정됩니다. RTT를 계산하기 위해 서비스는 HTTP 호출과 응답 사이의 시간을 측정합니다.

TCP 또는 ICMP를 사용하는 경우 서비스는 패킷 손실 비율을 계산하여 실패한 검사의 백분율을 확인합니다. RTT를 계산하기 위해 서비스는 보낸 패킷에 대해 승인(ACK)을 받는 데 걸린 시간을 측정합니다. 네트워크 테스트에 traceroute 데이터를 사용하도록 설정한 경우, 온-프레미스 네트워크에 대한 홉 단위 손실 및 대기 시간을 볼 수 있습니다.

### <a name="states-of-a-test"></a>테스트의 상태

검사가 반환하는 데이터를 기반으로 테스트는 다음과 같은 상태를 가질 수 있습니다.

* **성공** – 실패한 검사의 백분율 및 RTT의 실제 값이 지정된 임계값 내에 있지 않습니다.
* **실패** – 실패한 검사의 백분율 또는 RTT의 실제 값이 지정된 임계값을 초과했습니다. 임계값을 지정하지 않으면 실패한 검사의 백분율이 100일 때 테스트는 실패 상태가 됩니다.
* **경고** – 
     * 임계값이 지정되고 연결 모니터에서 임계값의 80%를 초과하는 검사 실패율이 관찰되면 테스트는 경고로 표시됩니다.
     * 지정된 임계값이 없으면 연결 모니터는 임계값을 자동으로 할당합니다. 이 임계값을 초과하면 테스트 상태가 경고로 변경됩니다. TCP 또는 ICMP 테스트에서 왕복 시간에 대한 임계값은 750밀리초입니다. 실패한 검사 백분율의 임계값은 10%입니다. 
* **결정할 수 없음** – Log Analytics 작업 영역에 데이터가 없습니다.  메트릭을 확인하세요. 
* **실행 중 아님** – 테스트 그룹을 사용하지 않도록 설정하여 비활성화했습니다.  

### <a name="data-collection-analysis-and-alerts"></a>데이터 수집, 분석 및 경고

연결 모니터가 수집하는 데이터는 Log Analytics 작업 영역에 저장됩니다. 이 작업 영역은 연결 모니터를 만들 때 직접 설정합니다. 

모니터링 데이터는 Azure Monitor 메트릭에도 제공됩니다. Log Analytics를 사용하여 원하는 기간 동안 모니터링 데이터를 유지할 수 있습니다. Azure Monitor는 기본적으로 30일 동안만 메트릭을 저장합니다. 

[데이터에 대한 메트릭 기반 경고를 설정](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/)할 수 있습니다.

#### <a name="monitoring-dashboards"></a>모니터링 대시보드

모니터링 대시보드에는 구독, 지역, 타임스탬프, 원본 및 대상 유형에 액세스할 수 있는 연결 모니터의 목록이 표시됩니다.

Network Watcher에서 연결 모니터로 이동하면 다음과 같은 방법으로 데이터를 볼 수 있습니다.

* **연결 모니터** – 구독, 지역, 타임스탬프, 원본 및 대상 유형에 대해 만들어진 모든 연결 모니터의 목록입니다. 이 보기는 기본값입니다.
* **테스트 그룹** – 구독, 지역, 타임스탬프, 원본 및 대상 유형에 대해 만들어진 모든 테스트 그룹의 목록입니다. 이러한 테스트 그룹은 연결 모니터로 필터링되지 않습니다.
* **테스트** – 구독, 지역, 타임스탬프, 원본 및 대상 유형에 대해 실행되는 모든 테스트의 목록입니다. 이러한 테스트는 연결 모니터 또는 테스트 그룹으로 필터링되지 않습니다.

다음 그림에서 3개 데이터 보기가 화살표 1로 표시됩니다.

대시보드에서 각 연결 모니터를 펼쳐서 테스트 그룹을 볼 수 있습니다. 그런 다음, 각 테스트 그룹을 펼쳐서 테스트 그룹에서 실행되는 테스트를 볼 수 있습니다. 

다음을 기준으로 목록을 필터링할 수 있습니다.

* **최상위 필터** – 텍스트, 엔터티 형식(연결 모니터, 테스트 그룹 또는 테스트) 타임스탬프 및 범위를 기준으로 목록을 검색합니다. 범위에는 구독, 지역, 원본 및 대상 유형이 포함됩니다. 다음 그림의 상자 1을 참조하세요.
* **상태 기반 필터** – 연결 모니터, 테스트 그룹 또는 테스트의 상태별로 필터링합니다. 다음 그림의 상자 2를 참조하세요.
* **경고 기반 필터** -연결 모니터 리소스에서 발생한 경고를 기준으로 필터링합니다. 다음 그림의 상자 3을 참조하세요.

  :::image type="content" source="./media/connection-monitor-2-preview/cm-view.png" alt-text="연결 모니터에서 연결 모니터, 테스트 그룹 및 테스트의 보기를 필터링하는 방법을 보여주는 스크린샷 " lightbox="./media/connection-monitor-2-preview/cm-view.png":::
    
예를 들어 원본 IP가 10.192.64.56인 경우 연결 모니터에서 모든 테스트를 보려면 다음을 수행합니다.
1. 보기를 **테스트** 로 변경합니다.
1. 검색 필드에 *10.192.64.56* 을 입력합니다.
1. 최상위 필터의 **범위** 에서 **원본** 을 선택합니다.

원본 IP가 10.192.64.56인 경우 연결 모니터에서 실패한 테스트만 표시하려면 다음을 수행합니다.
1. 보기를 **테스트** 로 변경합니다.
1. 상태 기반 필터에 대해 **실패** 를 선택합니다.
1. 검색 필드에 *10.192.64.56* 을 입력합니다.
1. 최상위 필터의 **범위** 에서 **원본** 을 선택합니다.

대상이 outlook.office365.com인 경우 연결 모니터에서 실패한 테스트만 표시하려면 다음을 수행합니다.
1. 보기를 **테스트** 로 변경합니다.
1. 상태 기반 필터에 대해 **실패** 를 선택합니다.
1. 검색 필드에 *office.live.com* 을 입력합니다.
1. 최상위 필터의 **범위** 에서 **대상** 을 선택합니다.
  
  :::image type="content" source="./media/connection-monitor-2-preview/tests-view.png" alt-text="Outlook.Office365.com 대상에 대해 실패한 테스트만 표시하도록 필터링된 보기를 보여주는 스크린샷" lightbox="./media/connection-monitor-2-preview/tests-view.png":::

연결 모니터 또는 테스트 그룹 또는 테스트의 실패 이유를 알려면 reason이라는 열을 클릭합니다.  그러면 어떤 임계값(검사 실패율(%) 또는 RTT)을 위반했는지와 관련 진단 메시지를 알려줍니다.
  
  :::image type="content" source="./media/connection-monitor-2-preview/cm-reason-of-failure.png" alt-text="연결 모니터, 테스트 또는 테스트 그룹의 실패 이유를 보여주는 스크린샷" lightbox="./media/connection-monitor-2-preview/cm-reason-of-failure.png":::
    
연결 모니터에 대한 실패한 검사의 백분율 및 RTT 추세를 보려면 다음을 수행합니다.
1. 조사할 연결 모니터를 선택합니다.

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing.png" alt-text="테스트 그룹별로 표시되는 연결 모니터에 대한 메트릭을 보여주는 스크린샷" lightbox="./media/connection-monitor-2-preview/cm-drill-landing.png":::

1. 다음 섹션이 표시됩니다.  
    1. 기본 정보 - 선택한 연결 모니터의 리소스 관련 속성 
    1. 요약 - 
        1. 연결 모니터의 모든 테스트에 대한 실패한 검사 백분율 및 RTT에 대해 집계된 추세선입니다. 특정 시간을 설정하면 세부 정보를 볼 수 있습니다.
        1. RTT 또는 실패한 검사 백분율을 기반으로 테스트 그룹, 원본 및 대상 전체에서 상위 5개입니다. 
    1. 테스트 그룹, 원본, 대상 및 테스트 구성에 대한 탭 - 연결 모니터의 테스트 그룹, 원본 또는 대상을 나열합니다. 테스트 실패를 확인하고 RTT를 집계하고 실패한 % 값을 확인합니다.  시간을 뒤로 돌려 데이터를 볼 수도 있습니다. 
    1. 문제 - 연결 모니터의 각 테스트에 대한 홉 수준 문제입니다. 

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing-2.png" alt-text="테스트 그룹 파트 2에 의해 표시되는 연결 모니터에 대한 메트릭을 보여주는 스크린샷" lightbox="./media/connection-monitor-2-preview/cm-drill-landing-2.png":::

1. 이 문서의 설명에 따라 Azure Automation Hybrid Runbook Worker를 제거할 수 있습니다.
    * 모든 테스트 보기를 클릭하여 연결 모니터의 모든 테스트를 봅니다.
    * 모든 테스트 그룹, 테스트 구성, 원본 및 대상 보기를 클릭하여 각각에 대한 세부 정보를 봅니다. 
    * 테스트 그룹, 테스트 구성, 원본 또는 대상을 선택하여 엔터티의 모든 테스트를 봅니다.

1. 모든 테스트 보기 뷰에서 다음을 수행할 수 있습니다.
    * 테스트를 선택하고 비교를 클릭합니다.
    
    :::image type="content" source="./media/connection-monitor-2-preview/cm-compare-test.png" alt-text="2개 테스트의 비교를 보여주는 스크린샷" lightbox="./media/connection-monitor-2-preview/cm-compare-test.png":::
    
    * 클러스터를 사용하여 VNET, 서브넷과 같은 복합 리소스를 자식 리소스로 확장
    * 토폴로지를 클릭하여 테스트에 대한 토폴로지를 표시합니다.

테스트 그룹의 실패한 검사 백분율 및 RTT 추세를 보려면 다음을 수행합니다.
1. 조사할 테스트 그룹을 선택합니다. 
1. 연결 모니터와 유사하게 표시됩니다(테스트 그룹, 원본, 대상 및 테스트 구성에 대한 기본 정보, 요약 테이블). 연결 모니터에서와 같이 탐색합니다.

테스트의 실패한 검사 백분율 및 RTT 추세를 보려면 다음을 수행합니다.
1. 조사할 테스트를 선택합니다. 검사 실패율(%) 및 왕복 시간에 대한 네트워크 토폴로지와 엔드투엔드 추세 차트가 표시됩니다. 식별된 문제를 보려면 토폴로지에서 경로에 있는 홉을 선택합니다. 이러한 홉은 Azure 리소스입니다. 이 기능은 현재 온-프레미스 네트워크에서 사용할 수 없습니다.

  :::image type="content" source="./media/connection-monitor-2-preview/cm-test-topology.png" alt-text="테스트의 토폴로지 보기를 보여주는 스크린샷" lightbox="./media/connection-monitor-2-preview/cm-test-topology.png":::

#### <a name="log-queries-in-log-analytics"></a>Log Analytics의 로그 쿼리

Log Analytics를 사용하여 모니터링 데이터의 사용자 지정 보기를 만들 수 있습니다. UI에 표시되는 모든 데이터는 Log Analytics에서 가져온 것입니다. 리포지토리의 데이터를 대화형으로 분석할 수 있습니다. 에이전트 상태 또는 Log Analytics 기반으로 하는 다른 솔루션의 데이터에 상관 관계를 지정합니다. 데이터를 Excel 또는 Power BI로 내보내거나 공유 가능한 링크를 만듭니다.

#### <a name="network-topology-in-connection-monitor"></a>연결 모니터의 네트워크 토폴로지 

일반적으로 연결 모니터 토폴로지는 기본적으로 원본에서 대상으로 모든 홉을 가져오는 에이전트에서 수행되는 추적 경로 명령의 결과를 사용하여 빌드됩니다.
그러나 원본 또는 대상이 Azure 경계에 있는 경우 고유 작업 2개의 결과를 병합하여 토폴로지가 빌드됩니다.
첫 번째는 확실히 추적 경로 명령의 결과입니다. 두 번째는 Azure 경계 내의 (고객) 네트워크 구성을 기반으로 논리 경로를 식별하는 내부 명령(NW의 Next Hop Diagnostics 도구와 매우 유사)의 결과입니다. 후자는 논리적이고 전자는 일반적으로 Azure 경계에서 홉을 식별하지 않으므로 병합된 결과의 대다수 홉(대부분 Azure 경계의 모든 홉)에는 대기 시간 값이 있습니다.

#### <a name="metrics-in-azure-monitor"></a>Azure Monitor의 메트릭

연결 모니터 환경 전에 생성된 연결 모니터에서는 4가지 메트릭인 % 프로브 실패, AverageRoundtripMs, ChecksFailedPercent, RoundTripTimeMs를 모두 사용할 수 있습니다. 연결 모니터 환경에서 생성된 연결 모니터에서는 ChecksFailedPercent, RoundTripTimeMs, Test Result 메트릭에 대해서만 데이터를 사용할 수 있습니다.

메트릭은 모니터링 빈도에 따라 내보내지고 특정 시간에 연결 모니터의 측면을 설명합니다. 연결 모니터 메트릭에는 SourceName, DestinationName, TestConfiguration, TestGroup 등 여러 차원이 있습니다. 이러한 차원을 사용하여 특정 데이터 세트를 시각화할 수 있으며 경고를 정의하는 동안 동일한 대상을 지정할 수도 있습니다.
현재 Azure 메트릭은 1분의 최소 단위를 허용합니다. 빈도가 1분 미만이면 집계된 결과가 표시됩니다.

  :::image type="content" source="./media/connection-monitor-2-preview/monitor-metrics.png" alt-text="연결 모니터의 메트릭을 보여주는 스크린샷" lightbox="./media/connection-monitor-2-preview/monitor-metrics.png":::

메트릭을 사용하는 경우 리소스 종류를 Microsoft.Network/networkWatchers/connectionMonitors로 설정합니다.

| 메트릭 | 표시 이름 | 단위 | 집계 유형 | Description | 차원 |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent(클래식) | 프로브 실패 %(클래식) | 백분율 | 평균 | 실패한 연결 모니터링 프로브의 백분율(%)<br>이 메트릭은 연결 모니터 클래식에만 사용할 수 있습니다  | 차원 없음 |
| AverageRoundtripMs(클래식) | 평균 왕복 시간(ms)(클래식) | 밀리초 | 평균 | 원본과 대상 간에 전송된 연결 모니터링 프로브의 평균 네트워크 RTT<br>이 메트릭은 연결 모니터 클래식에만 사용할 수 있습니다 |             차원 없음 |
| ChecksFailedPercent | 검사 실패율(%) | 백분율 | 평균 | 테스트에 대해 실패한 검사 비율 | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>프로토콜 <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>지역 <br>SourceIP <br>DestinationIP <br>SourceSubnet <br>DestinationSubnet |
| RoundTripTimeMs | 왕복 시간(ms) | 밀리초 | 평균 | 원본 및 대상 간에 전송되는 검사에 대한 RTT. 이 값은 평균이 아닙니다. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>프로토콜 <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>지역 <br>SourceIP <br>DestinationIP <br>SourceSubnet <br>DestinationSubnet |
| TestResult | 테스트 결과 | 개수 | 평균 | 연결 모니터 테스트 결과 <br>결과 값의 해석은 다음과 같습니다. <br>0- 미확정 <br>1- 합격 <br>2- 경고 <br>3- 실패| SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>프로토콜 <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>SourceIP <br>DestinationIP <br>SourceSubnet <br>DestinationSubnet |

#### <a name="metric-based-alerts-for-connection-monitor"></a>연결 모니터에 대한 메트릭 기반 경고

연결 모니터에 대한 메트릭 경고는 아래 방법을 사용하여 만들 수 있습니다. 

1. 연결 모니터에서 [Azure Portal을 사용하여](connection-monitor-preview-create-using-portal.md#) 연결 모니터를 만드는 동안 
1. 연결 모니터의 대시보드에서 "경고 구성"을 사용하여 
1. Azure Monitor에서 - Azure Monitor에서 경고를 만들려면: 
    1. 연결 모니터에서 만든 연결 모니터 리소스를 선택합니다.
    1. **메트릭** 이 연결 모니터의 신호 유형으로 표시되는지 확인합니다.
    1. **조건 추가** 에서 **신호 이름** 에 대해 **ChecksFailedPercent** 또는 **RoundTripTimeMs** 를 선택합니다.
    1. **신호 유형** 에 **메트릭** 을 선택합니다. 예를 들어, **ChecksFailedPercent** 를 선택합니다.
    1. 메트릭의 모든 차원이 나열됩니다. 차원 이름 및 차원 값을 선택합니다. 예를 들어 **원본 주소** 를 선택한 후 연결 모니터에 원본의 IP 주소를 입력합니다.
    1. **경고 논리** 에 다음 세부 정보를 입력합니다.
        * **조건 형식**: **정적**
        * **조건** 및 **임계값**
        * **집계 세분성 및 평가 빈도**: 연결 모니터는 1분마다 데이터를 업데이트합니다.
    1. **작업** 에서 작업 그룹을 선택합니다.
    1. 경고 세부 정보를 제공합니다.
    1. 경고 규칙을 만듭니다.

  :::image type="content" source="./media/connection-monitor-2-preview/mdm-alerts.jpg" alt-text="Azure Monitor에서 규칙 만들기 영역을 보여주는 스크린샷. 원본 주소 및 원본 엔드포인트 이름이 강조 표시됨" lightbox="./media/connection-monitor-2-preview/mdm-alerts.jpg":::

## <a name="diagnose-issues-in-your-network"></a>네트워크에서 문제 진단

연결 모니터는 연결 모니터 및 네트워크의 문제를 진단하는 데 유용합니다. 하이브리드 네트워크의 문제는 이전에 설치한 Log Analytics 에이전트에 의해 검색됩니다. Azure의 문제는 Network Watcher 확장에 의해 검색됩니다. 

네트워크 토폴로지에서 Azure 네트워크의 문제를 볼 수 있습니다.

원본이 온-프레미스 VM인 네트워크의 경우 다음 문제를 감지할 수 있습니다.

* 요청 시간이 초과되었습니다.
* 엔드포인트가 DNS에서 확인되지 않습니다(임시 또는 영구). URL이 잘못되었습니다.
* 호스트를 찾을 수 없습니다.
* 원본이 대상에 연결할 수 없습니다. ICMP를 통해 대상에 연결할 수 없습니다.
* 인증서 관련 문제: 
    * 에이전트를 인증하려면 클라이언트 인증서가 필요합니다. 
    * 인증서 재배치 목록에 액세스할 수 없습니다. 
    * 엔드포인트의 호스트 이름이 인증서의 주체 또는 주체 대체 이름과 일치하지 않습니다. 
    * 원본 로컬 컴퓨터의 신뢰할 수 있는 인증 기관 저장소에 루트 인증서가 없습니다. 
    * SSL 인증서가 만료되었거나 유효하지 않거나 해지되었거나 호환되지 않습니다.

원본이 Azure VM인 네트워크의 경우 다음 문제를 감지할 수 있습니다.

* 에이전트 문제:
    * 에이전트가 중지되었습니다.
    * DNS 확인에 실패했습니다.
    * 대상 포트에서 수신 대기하는 애플리케이션 또는 수신기가 없습니다.
    * 소켓을 열 수 없습니다.
* VM 상태 문제: 
    * 시작 중
    * 중지 중
    * 중지됨
    * 할당 취소 중
    * 할당 취소됨
    * 다시 부팅 중
    * 할당되지 않음
* ARP 테이블 항목이 없습니다.
* 로컬 방화벽 문제 또는 NSG 규칙으로 인해 트래픽이 차단되었습니다.
* 가상 네트워크 게이트웨이 문제: 
    * 경로가 누락되었습니다.
    * 두 게이트웨이 간의 터널이 끊어졌거나 누락되었습니다.
    * 터널에서 두 번째 게이트웨이를 찾지 못했습니다.
    * 피어링 정보를 찾을 수 없습니다.
> [!NOTE]
> 연결된 게이트웨이가 2개 있고 그 중 하나가 원본 엔드포인트와 동일한 지역에 없는 경우, CM은 토폴로지 보기에 대해 '경로를 알 수 없음'으로 식별합니다. 연결은 영향을 받지 않습니다. 알려진 문제이며 수정이 진행 중입니다. 
* Microsoft Edge에서 경로가 누락되었습니다.
* 시스템 경로 또는 UDR 때문에 트래픽이 중지되었습니다.
* 게이트웨이 연결에서 BGP를 사용하도록 설정되지 않았습니다.
* 부하 분산 장치에서 DIP 프로브가 다운되었습니다.

## <a name="comparison-between-azures-connectivity-monitoring-support"></a>Azure의 연결 모니터링 지원 비교 

가동 중지 시간 없이 한 번의 클릭으로 네트워크 성능 모니터 및 연결 모니터(클래식)에서 새롭게 향상된 연결 모니터로 테스트를 마이그레이션할 수 있습니다.
 
마이그레이션은 다음과 같은 결과를 생성하는 데 도움이 됩니다.

* 에이전트 및 방화벽 설정이 그대로 작동합니다. 변경할 필요가 없습니다. 
* 기존 연결 모니터는 연결 모니터 > 테스트 그룹 > 테스트 형식에 매핑됩니다. **편집** 을 선택하여 새 연결 모니터의 속성 보기 및 수정을 할 수 있고, 연결 모니터를 변경할 템플릿을 다운로드할 수 있으며, Azure Resource Manager를 통해 템플릿을 제출할 수 있습니다. 
* Network Watcher 확장을 포함하는 Azure 가상 머신은 작업 영역과 메트릭 모두에 데이터를 보냅니다. 연결 모니터는 이전 메트릭(ProbesFailedPercent 및 AverageRoundtripMs) 대신 새 메트릭(ChecksFailedPercent 및 RoundTripTimeMs)을 통해 데이터를 사용할 수 있습니다. 이전 메트릭은 ProbesFailedPercent -> ChecksFailedPercent 및 AverageRoundtripMs -> RoundTripTimeMs로 새 메트릭으로 마이그레이션됩니다.
* 데이터 모니터링:
   * **경고**: 새 메트릭으로 자동으로 마이그레이션됩니다.
   * **대시보드 및 통합**: 메트릭 집합을 수동으로 편집해야 합니다. 
   
네트워크 성능 모니터 및 연결 모니터(클래식)에서 연결 모니터로 마이그레이션해야 하는 몇 가지 이유가 있습니다. 다음은 Azure의 연결 모니터가 네트워크 성능 모니터 및 연결 모니터(클래식)에 대해 수행하는 방식을 보여 주는 몇 가지 사용 사례입니다. 

 | 특징  | 네트워크 성능 모니터 | 연결 모니터(클래식) | 연결 모니터 |
 | -------  | --------------------------- | -------------------------- | ------------------ | 
 | Azure 및 하이브리드 모니터링을 위한 통합 환경 | 사용할 수 없음 | 사용할 수 없음 | 사용 가능 |
 | 구독 간, 지역 간, 작업 영역 간 모니터링 | 구독 간, 지역 간 모니터링을 허용하지만 작업 영역 간 모니터링을 허용하지 않음 | 사용할 수 없음 | 구독 간, 작업 영역 간 모니터링을 허용함, Azure 에이전트에는 지역 경계가 있음  |
 | 중앙 집중식 작업 영역 지원 |  사용할 수 없음 | 사용할 수 없음   | 사용 가능 |
 | 여러 원본이 여러 대상을 ping할 수 있음 | 성능 모니터링을 사용하면 여러 원본이 여러 대상을 ping할 수 있고, 서비스 연결 모니터링을 사용하면 여러 원본이 단일 서비스/URL을 ping할 수 있으며, Express Route를 사용하면 여러 원본이 여러 대상을 ping할 수 있음 | 사용할 수 없음 | 사용 가능 |
 | 온-프레미스, 인터넷 홉, Azure 전반에 걸친 통합 토폴로지 | 사용할 수 없음 | 사용할 수 없음 | 사용 가능 |
 | HTTP 상태 코드 검사 | 사용할 수 없음  | 사용할 수 없음 | 사용 가능 |
 | 연결 진단 | 사용할 수 없음 | 사용 가능 | 사용 가능 |
 | 복합 리소스 - VNET, 서브넷, 온-프레미스 사용자 지정 네트워크 | 성능 모니터링은 서브넷, 온-프레미스 네트워크, 논리 네트워크 그룹을 지원하며, 서비스 연결 모니터링, Express Route는 온-프레미스 및 Azure 에이전트만 지원 | 사용할 수 없음 | 사용 가능 |
 | 연결 메트릭 및 차원 측정값 |   사용할 수 없음 | 손실, 대기 시간, RTT | 사용 가능 |
 | 자동화 – PS/CLI/Terraform | 사용할 수 없음 | 사용 가능 | 사용 가능 |
 | Linux 지원 | 성능 모니터링은 Linux를 지원하며, 서비스 연결 모니터 및 Express Route는 Linux를 지원하지 않음 | 사용 가능 | 사용 가능 |
 | 퍼블릭, 정부, Mooncake, Air-Gapped 클라우드 지원 | 사용 가능 | 사용 가능 | 사용 가능|


## <a name="faq"></a>FAQ

### <a name="are-classic-vms-supported"></a>클래식 VM이 지원되나요?
아니요, 연결 모니터는 클래식 VM을 지원하지 않습니다. 클래식 리소스가 [사용되지 않으므로](../virtual-machines/classic-vm-deprecation.md) 클래식에서 Azure Resource Manager로 IaaS 리소스를 마이그레이션하는 것이 좋습니다. [마이그레이션 방법](../virtual-machines/migration-classic-resource-manager-overview.md)을 이해하려면 이 문서를 참조하세요.

### <a name="my-topology-is-not-decorated-or-my-hops-have-missing-information"></a>토폴로지가 데코레이트되지 않았거나 홉에 누락된 정보가 있나요?
비 Azure부터 Azure까지, 토폴로지는 대상 Azure 리소스 및 연결 모니터 리소스가 동일한 지역에 있는 경우에만 데코레이트할 수 있습니다. 

### <a name="my-connection-monitor-creation-is-failing-with-error-we-dont-allow-creating-different-endpoints-for-the-same-vm"></a>“동일한 VM에 대해 다른 엔드포인트를 만들 수 없습니다”라는 오류가 표시되면서 연결 모니터 만들기가 실패합니다.
동일한 연결 모니터에서 서로 다른 구성으로 동일한 Azure VM을 사용할 수 없습니다. 예를 들어, 동일한 연결 모니터에서 필터가 있는 상태 및 필터가 없는 상태로 동일한 VM을 사용하는 것은 지원되지 않습니다.

### <a name="the-test-failure-reason-is-nothing-to-display"></a>테스트 실패 이유가 “표시할 내용 없음”입니다.
연결 모니터 대시보드에 표시되는 문제는 토폴로지 검색 또는 홉 탐색 중에 발견됩니다. % 손실 또는 RTT에 대해 설정된 임계값이 위반되었지만 홉에 문제가 없는 경우가 있을 수 있습니다.

### <a name="while-migrating-existing-connection-monitor-classic-to-connection-monitor-the-external-endpoint-tests-are-being-migrated-with-tcp-protocol-only"></a>기존 연결 모니터(클래식)를 연결 모니터로 마이그레이션하는 동안 외부 엔드포인트 테스트는 TCP 프로토콜을 통해서만 마이그레이션되나요? 
연결 모니터(클래식)에는 프로토콜 선택 항목이 없습니다. 따라서 고객은 연결 모니터(클래식)에서 HTTP 프로토콜을 사용하여 외부 엔드포인트에 대한 연결을 지정할 수 없었습니다.
모든 테스트는 연결 모니터(클래식)에 TCP 프로토콜만 있으므로, 마이그레이션 시 연결 모니터 테스트에서 TCP 구성을 만듭니다. 

## <a name="next-steps"></a>다음 단계
    
   * [Azure Portal을 사용하여 연결 모니터를 만드는 방법](./connection-monitor-create-using-portal.md) 알아보기  
   * [ARMClient를 사용하여 연결 모니터를 만드는 방법](./connection-monitor-create-using-template.md) 알아보기
