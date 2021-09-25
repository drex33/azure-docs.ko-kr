---
title: Azure Migrate에서 에이전트 기반 종속성 분석 설정
description: 이 문서에서는 Azure Migrate에서 에이전트 기반 종속성 분석을 설정하는 방법에 대해 설명합니다.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: 757a10473c2f6776ef3a84310b42209ddd12ed45
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128643653"
---
# <a name="set-up-dependency-visualization"></a>종속성 시각화 설정

이 문서에서는 Azure Migrate: 검색 및 평가에서 에이전트 기반 종속성 분석을 설정하는 방법에 대해 설명합니다. [종속성 분석](concepts-dependency-visualization.md)을 사용하면 평가하고 Azure로 마이그레이션하려는 서버 간의 종속성을 파악하고 이해하는 데 도움이 됩니다.

## <a name="before-you-start"></a>시작하기 전에

- 다음에 대한 에이전트 기반 종속성 분석의 지원 및 배포 요구 사항을 검토합니다.
    - [VMware 환경의 서버](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agent-based)
    - [물리적 서버](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)
    - [Hyper-V 환경의 서버](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements)
- 다음을 확인합니다.
    - Azure Migrate 프로젝트가 있습니다. 프로젝트가 없으면 지금 [만듭니다](./create-manage-projects.md).
    - Azure Migrate: 검색 및 평가 도구를 프로젝트에 [추가](how-to-assess.md)했는지 확인합니다.
    - 온-프레미스 서버를 검색하도록 [Azure Migrate 어플라이언스](migrate-appliance.md)를 설정합니다. 어플라이언스는 온-프레미스 서버를 검색하고 메타데이터 및 성능 데이터를 Azure Migrate: 검색 및 평가로 보냅니다. 다음에 대해 어플라이언스를 설정합니다.
        - [VMware 환경의 서버](how-to-set-up-appliance-vmware.md)
        - [Hyper-V 환경의 서버](how-to-set-up-appliance-hyper-v.md)
        - [물리적 서버](how-to-set-up-appliance-physical.md)
- 종속성 시각화를 사용하려면 [Log Analytics 작업 영역](../azure-monitor/logs/manage-access.md)을 Azure Migrate 프로젝트에 연결합니다.
    - Azure Migrate 어플라이언스를 설정하고 Azure Migrate 프로젝트에서 서버를 검색한 후에만 작업 영역을 연결할 수 있습니다.
    - Azure Migrate 프로젝트를 포함하는 구독에 작업 영역이 있는지 확인합니다.
    - 작업 영역은 미국 동부, 동남 아시아 또는 서유럽 지역에 있어야 합니다. 다른 지역의 작업 영역은 프로젝트에 연결할 수 없습니다.
    - 작업 영역은 [서비스 맵 지원되는](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions) 지역에 있어야 합니다.
    - 신규 또는 기존 Log Analytics 작업 영역을 Azure Migrate 프로젝트와 연결할 수 있습니다.
    - 서버에 대한 종속성 시각화를 처음 설정할 때 작업 영역을 연결합니다. Azure Migrate 프로젝트에 대한 작업 영역은 추가된 후 수정할 수 없습니다.
    - Log Analytics에서 Azure Migrate와 연결된 작업 영역에는 마이그레이션 프로젝트 키와 프로젝트 이름이 태그로 지정됩니다.

## <a name="associate-a-workspace"></a>작업 영역 연결

1. 평가를 위해 서버를 검색한 후 **서버** > **Azure Migrate: 검색 및 평가** 에서 **개요** 를 클릭합니다.  
2. **Azure Migrate: 검색 및 평가** 에서 **기본 정보** 를 클릭합니다.
3. **OMS 작업 영역** 에서 **구성 필요** 를 클릭합니다.

     ![Log Analytics 작업 영역 구성](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. **OMS 작업 영역 구성** 에서 새 작업 영역을 만들지, 아니면 기존 작업 영역을 사용할지를 지정합니다.
    - 프로젝트 구독의 모든 작업 영역에서 기존 작업 영역을 선택할 수 있습니다.
    - 연결하려면 작업 영역에 대한 읽기 권한이 있어야 합니다.
5. 새 작업 영역을 만드는 경우 해당 위치를 선택합니다.

    ![새 작업 영역 추가](./media/how-to-create-group-machine-dependencies/workspace.png)

> [!Note]
> 프라이빗 엔드포인트 연결을 위해 OMS 작업 영역을 구성하는 [방법을 알아보세요](../azure-monitor/logs/private-link-security.md).  

## <a name="download-and-install-the-vm-agents"></a>VM 에이전트 다운로드 및 설치

분석할 각 서버에서 에이전트를 설치합니다.

> [!NOTE]
> System Center Operations Manager 2012 R2 이상에서 모니터링하는 서버의 경우 MMA 에이전트를 설치할 필요가 없습니다. 서비스 맵은 Operations Manager와 통합됩니다. 통합 지침을 [따릅니다](../azure-monitor/vm/service-map-scom.md#prerequisites).

1. **Azure Migrate: 검색 및 평가** 에서 **검색된 서버** 를 클릭합니다.
2. 종속성 시각화를 사용하여 분석하려는 각 서버에 대해 **종속성** 열에서 **에이전트 설치 필요** 를 클릭합니다.
3. **종속성** 페이지에서 Windows 또는 LINUX용 MMA 및 종속성 에이전트를 다운로드합니다.
4. **MMA 에이전트 구성** 에서 작업 영역 ID 및 키를 복사합니다. MMA 에이전트를 설치할 때 필요합니다.

    ![에이전트 설치](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>MMA 설치

분석하려는 각 Windows 또는 Linux 서버에 MMA를 설치합니다.

### <a name="install-mma-on-a-windows-server"></a>Windows 서버에 MMA 설치

Windows 서버에 에이전트를 설치하려면

1. 다운로드한 에이전트를 두 번 클릭합니다.
2. **Welcome** 페이지에서 **다음** 을 클릭합니다. **사용 조건** 페이지에서 **동의함** 을 클릭하여 라이선스에 동의합니다.
3. **대상 폴더** 에서 기본 설치 폴더를 유지하거나 수정하고 **다음** 을 클릭합니다.
4. **에이전트 설치 옵션** 에서 **Azure Log Analytics** > **다음** 을 차례로 선택합니다.
5. **추가** 를 클릭하여 새로운 Log Analytics 작업 영역을 추가합니다. 포털에서 복사한 작업 영역 ID와 키를 붙여넣습니다. **다음** 을 클릭합니다.

명령줄에서 또는 Configuration Manager나 Intigua와 같은 자동화된 방법을 사용하여 에이전트를 설치할 수 있습니다.
- 이 방법을 사용하여 MMA 에이전트를 설치하는 방법을 [자세히 알아보세요](../azure-monitor/agents/log-analytics-agent.md#installation-options).
- MMA 에이전트는 이 [스크립트](https://github.com/brianbar-MSFT/Install-MMA)를 사용하여 설치할 수도 있습니다.
- MMA에서 지원하는 Windows 운영 체제를 [자세히 확인](../azure-monitor/agents/agents-overview.md#supported-operating-systems)해 보세요.

### <a name="install-mma-on-a-linux-server"></a>Linux 서버에 MMA 설치

Linux 서버에 MMA를 설치하려면

1. scp/sftp를 사용하여 Linux 컴퓨터로 해당 번들(x86 또는 x64)을 전송합니다.

2. --install 인수를 사용하여 번들을 설치합니다.

   `sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>`

MMA에서 지원하는 Linux 운영 체제 목록을 [자세히 확인](../azure-monitor/agents/agents-overview.md#supported-operating-systems)해 보세요. 

## <a name="install-the-dependency-agent"></a>종속성 에이전트 설치

1. Windows 서버에 종속성 에이전트를 설치하려면 설치 파일을 두 번 누르고 마법사를 따릅니다.

2. Linux 서버에 종속성 에이전트를 설치하려면 다음 명령을 사용하여 루트로 설치합니다.

   `sh InstallDependencyAgent-Linux64.bin`

- 스크립트를 사용하여 종속성 에이전트를 설치하는 방법에 대해 [자세히 알아봅니다](../azure-monitor/vm/vminsights-enable-hybrid.md#dependency-agent).
- 종속성 에이전트에서 지원하는 운영 체제에 대해 [자세히 알아보세요](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems).


## <a name="create-a-group-using-dependency-visualization"></a>종속성 시각화를 사용하여 그룹 만들기

이제 평가를 위해 그룹을 만듭니다. 


> [!NOTE]
> 종속성을 시각화하려는 그룹에는 10개 이하의 서버만 포함되어 있어야 합니다. 서버를 10개 이상 보유한 경우 더 작은 그룹으로 분할합니다.

1. **Azure Migrate: 검색 및 평가** 에서 **검색된 서버** 를 클릭합니다.
2. **종속성** 열에서 검토하려는 각 서버에 대한 **종속성 보기** 를 클릭합니다.
3. 종속성 맵에서 다음을 확인할 수 있습니다.
    - 서버와 주고 받는 인바운드(클라이언트) 및 아웃바운드(서버) TCP 연결
    - 종속성 에이전트가 설치되어 있지 않은 종속 서버는 포트 번호별로 그룹화됩니다.
    - 종속성 에이전트가 설치된 종속성 서버는 별도의 상자에 표시됩니다.
    - 서버 내에서 실행되는 프로세스입니다. 각 서버 상자를 확장하여 프로세스를 확인합니다.
    - 서버 속성(FQDN, 운영 체제, MAC 주소 포함) 각 서버 상자를 클릭하여 세부 정보를 확인합니다.

4. 시간 범위 레이블의 시간 범위를 클릭하여 여러 시간 범위의 종속성을 살펴볼 수 있습니다.
    - 범위는 기본적으로 1시간입니다. 
    - 시간 범위를 수정하거나 시작 및 종료 날짜와 기간을 지정할 수 있습니다.
    - 시간 범위는 최대 1시간까지 가능합니다. 더 긴 범위가 필요한 경우 Azure Monitor를 사용하여 더 긴 기간 동안의 종속성 데이터를 쿼리합니다.

5. 그룹화하려는 종속 서버를 식별한 후 Ctrl 키를 누른 상태로 맵에서 여러 서버를 클릭하여 선택하고 **컴퓨터 그룹화** 를 클릭합니다.
6. 그룹 이름을 지정합니다.
7. Azure Migrate에서 종속 서버가 검색되는지 확인합니다.

    - Azure Migrate: 검색 및 평가를 통해 종속 서버가 검색되지 않는 경우 그룹에 추가할 수 없습니다.
    - 서버를 추가하려면 검색을 다시 실행하고 서버가 검색되었는지 확인합니다.

8. 이 그룹에 대한 평가를 만들려면 그룹에 대한 새 평가를 만드는 확인란을 선택합니다.
8. **확인** 을 클릭하여 그룹을 저장합니다.

그룹을 만든 후에는 그룹의 모든 서버에 에이전트를 설치한 다음, 전체 그룹에 대한 종속성을 시각화하는 것이 좋습니다.

## <a name="query-dependency-data-in-azure-monitor"></a>Azure Monitor에서 종속성 데이터 쿼리

Azure Migrate 프로젝트와 연결된 Log Analytics 작업 영역에서 서비스 맵을 통해 캡처한 종속성 데이터를 쿼리할 수 있습니다. Log Analytics는 Azure Monitor 로그 쿼리를 작성하고 실행하는 데 사용됩니다.

- Log Analytics에서 서비스 맵 데이터를 검색하는 [방법을 알아봅니다](../azure-monitor/vm/service-map.md#log-analytics-records).
- [Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)에서 로그 쿼리를 작성하는 방법에 대한 [개요를 확인합니다](../azure-monitor/logs/get-started-queries.md).

다음과 같이 종속성 데이터에 대한 쿼리를 실행합니다.

1. 에이전트를 설치한 후 포털로 이동하고 **개요** 를 클릭합니다.
2. **Azure Migrate: 검색 및 평가** 에서 **개요** 를 클릭합니다. 아래쪽 화살표를 클릭하여 **기본 정보** 를 확장합니다.
3. **OMS 작업 영역** 에서 작업 영역 이름을 클릭합니다.
3. Log Analytics 작업 영역 페이지에서 **일반**, **로그** 를 차례로 클릭합니다.
4. 쿼리를 작성하고 **실행** 을 클릭합니다.

### <a name="sample-queries"></a>샘플 쿼리

다음은 종속성 데이터를 추출하는 데 사용할 수 있는 몇 가지 샘플 쿼리입니다.

- 선호하는 데이터 요소를 추출하도록 쿼리를 수정할 수 있습니다.
- 종속성 데이터 레코드의 전체 목록을 [검토](../azure-monitor/vm/service-map.md#log-analytics-records)합니다.
- 추가 샘플 쿼리를 [검토](../azure-monitor/vm/service-map.md#sample-log-searches)합니다.

#### <a name="sample-review-inbound-connections"></a>샘플: 인바운드 연결 검토

서버 세트에 대한 인바운드 연결을 검토합니다.

- 연결 메트릭(VMConnection) 표에 포함된 레코드는 물리적 네트워크 연결을 나타내지 않습니다.
- 여러 물리적 네트워크 연결은 논리적 연결로 그룹화됩니다.
- 물리적 네트워크 연결 데이터를 VMConnection에 집계하는 방법에 대해 [자세히 알아봅니다](../azure-monitor/vm/service-map.md#connections).

```
// the servers of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="sample-summarize-sent-and-received-data"></a>샘플: 송수신된 데이터 요약

이 샘플은 서버 세트 간의 인바운드 연결에서 송수신된 데이터의 양을 요약합니다.

```
// the servers of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

## <a name="next-steps"></a>다음 단계

그룹에 대한 [평가를 만듭니다](how-to-create-assessment.md).
