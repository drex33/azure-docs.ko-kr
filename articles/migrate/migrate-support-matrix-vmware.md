---
title: Azure Migrate의 VMware 서버 검색 지원
description: VMware 환경에서 서버에 대한 Azure Migrate 검색 및 평가 지원에 관해 알아봅니다.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: dedb05df1713238a6271af4dfd7b9cf1695d0bc7
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122567715"
---
# <a name="support-matrix-for-vmware-discovery"></a>VMware 검색을 위한 지원 매트릭스 

이 문서에서는 [Azure Migrate: 검색 및 평가](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) 도구를 사용하여 Azure로 마이그레이션하기 위해 VMware 환경에서 서버를 검색하고 평가하는 경우의 필수 조건과 지원 요구 사항을 간략하게 설명합니다.

서버를 평가하려면 먼저 Azure Migrate 프로젝트를 만듭니다. Azure Migrate: 검색 및 평가 도구가 프로젝트에 자동으로 추가됩니다. 그런 다음, Azure Migrate 어플라이언스를 배포합니다. 어플라이언스는 지속적으로 온-프레미스 서버를 검색하고 구성 및 성능 메타데이터를 Azure에 보냅니다. 검색이 완료되면 검색된 서버를 그룹으로 수집하고 그룹별로 평가를 실행합니다.

VMware 서버를 Azure로 마이그레이션할 계획인 경우 [마이그레이션 지원 매트릭스](migrate-support-matrix-vmware-migration.md)를 검토합니다.

## <a name="limitations"></a>제한 사항

요구 사항 | 세부 정보
--- | ---
**프로젝트 제한** | Azure 구독에서 여러 Azure Migrate 프로젝트를 만들 수 있습니다.<br /><br /> 단일 [프로젝트](migrate-support-matrix.md#project)의 VMware 환경에서 최대 50,000개 서버를 검색하고 평가할 수 있습니다. 프로젝트에는 물리적 서버와 Hyper-V 환경의 서버를 평가 한도까지 포함할 수 있습니다.
**검색** | Azure Migrate 어플라이언스는 vCenter Server를 실행하는 서버에서 최대 10,000개 서버를 검색할 수 있습니다.
**평가** | 단일 그룹에 최대 35,000대의 서버를 추가할 수 있습니다.<br /><br /> 단일 평가에서 최대 35,000대의 서버를 평가할 수 있습니다.

[평가](concepts-assessment-calculation.md)에 대해 자세히 알아보세요.

## <a name="vmware-requirements"></a>VMware 요구 사항

VMware | 세부 정보
--- | ---
**vCenter Server** | 검색하고 평가하려는 서버는 vCenter Server 버전 7.0, 6.7, 6.5, 6.0 또는 5.5를 통해 관리해야 합니다.<br /><br /> 어플라이언스에서 ESXi 호스트 세부 정보를 제공하여 서버를 검색하는 기능은 현재 지원되지 않습니다. <br /><br /> IPv6 주소는 vCenter Server(서버 검색 및 평가용) 및 ESXi 호스트(서버 복제용)에 지원되지 않습니다.
**권한** | Azure Migrate: 검색 및 평가 도구에는 vCenter Server 읽기 전용 계정이 필요합니다.<br /><br /> 소프트웨어 인벤토리와 에이전트 없는 종속성 분석에 도구를 사용하려면 계정에 VMware VM의 게스트 작업 권한이 있어야 합니다.

## <a name="server-requirements"></a>서버 요구 사항

VMware | 세부 정보
--- | ---
**운영 체제** | 모든 Windows 및 Linux 운영 체제에 대해 마이그레이션을 평가할 수 있습니다.
**스토리지** | SCSI, IDE, SATA 기반 컨트롤러에 연결된 디스크가 지원됩니다.

## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 어플라이언스 요구 사항

Azure Migrate는 검색 및 평가를 위해 [Azure Migrate 어플라이언스](migrate-appliance.md)를 사용합니다. vCenter Server로 가져온 VMware OVA(Open Virtualization Appliance) 템플릿을 사용하거나 [PowerShell 스크립트](deploy-appliance-script.md)를 사용하여 VMware 환경에서 어플라이언스를 서버로 배포할 수 있습니다. [VMware에 대한 어플라이언스 요구 사항](migrate-appliance.md#appliance---vmware)에 관해 알아봅니다.

어플라이언스에 대한 추가 요구 사항은 다음과 같습니다.

- Azure Government에서 [스크립트](deploy-appliance-script-government.md)를 사용하여 어플라이언스를 배포해야 합니다.
- 어플라이언스는 [퍼블릭 클라우드](migrate-appliance.md#public-cloud-urls) 및 [정부 클라우드](migrate-appliance.md#government-cloud-urls)의 특정 URL에 액세스할 수 있어야 합니다.

## <a name="port-access-requirements"></a>포트 액세스 요구 사항

디바이스 | 연결
--- | ---
**Azure Migrate 어플라이언스** | 어플라이언스에 대한 원격 데스크톱 연결을 허용하기 위한 TCP 포트 3389에서 인바운드 연결<br /><br /> URL `https://<appliance-ip-or-name>:44368`을 사용하여 어플라이언스 관리 앱에 원격으로 액세스하기 위한 포트 44368의 인바운드 연결 <br /><br />Azure Migrate에 검색 및 성능 메타데이터를 보내기 위한 포트 443(HTTPS)의 아웃바운드 연결
**vCenter Server** | 어플라이언스에서 평가를 위한 구성 및 성능 메타데이터를 수집할 수 있도록 TCP 포트 443에서 인바운드 연결 <br /><br /> 어플라이언스는 기본적으로 포트 443의 vCenter에 연결됩니다. vCenter Server가 다른 포트에서 수신 대기하는 경우 검색을 설정할 때 포트를 수정할 수 있습니다.
**ESXi 호스트** | [소프트웨어 인벤토리 검색](how-to-discover-applications.md) 또는 [에이전트 없는 종속성 분석](concepts-dependency-visualization.md#agentless-analysis)의 경우 어플라이언스는 TCP 포트 443의 ESXi 호스트에 연결하여 서버의 소프트웨어 인벤토리와 종속성을 검색합니다.

## <a name="software-inventory-requirements"></a>소프트웨어 인벤토리 요구 사항

서버를 검색하는 것 외에도 Azure Migrate: 검색 및 평가는 서버에서 소프트웨어 인벤토리를 수행할 수 있습니다. 소프트웨어 인벤토리를 사용하면 온-프레미스 워크로드에 맞게 조정된 마이그레이션 경로를 파악하고 계획할 수 있습니다.

지원 | 세부 정보
--- | ---
**지원되는 서버** | 현재 VMware 환경에 있는 서버에서만 지원됩니다. 각 Azure Migrate 어플라이언스에서 최대 10,000대의 서버에서 소프트웨어 인벤토리를 수행할 수 있습니다.
**운영 체제** | 모든 Windows 및 Linux 버전을 실행하는 서버가 지원됩니다.
**VM 요구 사항** | 소프트웨어 인벤토리의 경우 서버에서 VMware Tools를 설치하고 실행해야 합니다. <br /><br /> VMware Tools 버전은 버전 10.2.1 이상이어야 합니다.<br /><br /> Windows 서버에는 PowerShell 버전 2.0 이상이 설치되어 있어야 합니다.
**검색** | 소프트웨어 인벤토리는 서버에 설치된 VMware Tools를 사용하여 vCenter Server에서 수행됩니다. 어플라이언스는 vSphere API를 통해 vCenter Server를 실행하는 서버에서 소프트웨어 인벤토리 정보를 수집합니다. 소프트웨어 인벤토리는 에이전트가 없습니다. 서버에 에이전트가 설치되어 있지 않으며 어플라이언스가 서버에 직접 연결되지 않습니다. 서버에 설치된 역할과 기능에 대한 세부 정보를 수집하려면 Windows 서버에서 WMI가 사용되도록 설정하고 사용할 수 있어야 합니다.
**vCenter Server 사용자 계정** | 소프트웨어 인벤토리를 위해 서버와 상호 작용하려면 평가에 사용하는 vCenter Server 읽기 전용 계정에 VMware VM의 게스트 작업 권한이 있어야 합니다.
**서버 액세스** | 소프트웨어 인벤토리를 위해 어플라이언스 구성 관리자에서 여러 도메인 자격 증명과 비도메인(Windows/Linux) 자격 증명을 추가할 수 있습니다.<br /><br /> Windows 서버의 게스트 사용자 계정과 모든 Linux 서버의 표준 사용자 계정(비 `sudo` 액세스)이 있어야 합니다.
**포트 액세스** | Azure Migrate 어플라이언스는 소프트웨어 인벤토리를 수행하려는 서버를 실행하는 ESXi 호스트의 TCP 포트 443에 연결할 수 있어야 합니다. vCenter Server를 실행하는 서버는 ESXi 호스트 연결을 반환하여 소프트웨어 인벤토리의 세부 정보를 포함하는 파일을 다운로드합니다.

## <a name="sql-server-instance-and-database-discovery-requirements"></a>SQL Server 인스턴스 및 데이터베이스 검색 요구 사항

[소프트웨어 인벤토리](how-to-discover-applications.md)는 SQL Server 인스턴스를 식별합니다. 어플라이언스는 해당 정보를 사용하여 어플라이언스 구성 관리자에서 제공되는 Windows 인증 또는 SQL Server 인증 자격 증명을 통해 각 SQL Server 인스턴스에 연결을 시도합니다. 어플라이언스는 네트워크에 대한 가시성이 확보된 SQL Server에만 연결할 수 있지만, 소프트웨어 인벤토리 자체에는 네트워크에 대한 가시성이 필요하지 않을 수 있습니다.

어플라이언스는 연결된 후 SQL Server 인스턴스와 데이터베이스의 구성 및 성능 데이터를 수집합니다. SQL Server 구성 데이터는 24시간마다 한 번 업데이트됩니다. 성능 데이터는 30초마다 캡처됩니다.

지원 | 세부 정보
--- | ---
**지원되는 서버** | 현재 VMware 환경에서 SQL Server를 실행하는 서버에서만 지원됩니다. 최대 300개의 SQL Server 인스턴스 또는 6,000개 SQL 데이터베이스 중 적은 값을 검색할 수 있습니다.
**Windows 서버** | Windows Server 2008 이상이 지원됩니다.
**Linux 서버** | 현재 지원되지 않습니다.
**인증 메커니즘** | Windows 및 SQL Server 인증이 모두 지원됩니다. 어플라이언스 구성 관리자에서 두 인증 유형의 자격 증명을 제공할 수 있습니다.
**SQL Server 액세스** | Azure Migrate에는 sysadmin 서버 역할의 멤버인 Windows 사용자 계정이 필요합니다.
**SQL Server 버전** | SQL Server 2008 이상이 지원됩니다.
**SQL Server 버전** | Enterprise, Standard, Developer, Express 버전이 지원됩니다.
**지원되는 SQL 구성** | 현재 독립 실행형 SQL Server 인스턴스와 해당 데이터베이스의 검색만 지원됩니다.<br /><br /> 장애 조치(failover) 클러스터 및 Always On 가용성 그룹 식별은 지원되지 않습니다.
**지원되는 SQL 서비스** | SQL Server 데이터베이스 엔진만 지원됩니다. <br /><br /> SSRS(SQL Server Reporting Services), SSIS(SQL Server Integration Services), SSAS(SQL Server Analysis Services) 검색은 지원되지 않습니다.

> [!NOTE]
> 기본적으로 Azure Migrate는 SQL 인스턴스에 연결하는 가장 안전한 방법을 사용합니다. 즉, Azure Migrate는 TrustServerCertificate 속성을 `true`로 설정하여 Azure Migrate 어플라이언스와 원본 SQL Server 인스턴스 간의 통신을 암호화합니다. 또한 전송 계층은 SSL을 사용하여 채널을 암호화하고 인증서 체인을 건너뛰어 신뢰 유효성을 검사합니다. 따라서 어플라이언스 서버는 인증서의 루트 인증 기관을 신뢰하도록 설정되어야 합니다. 
>
> 그러나 어플라이언스에서 **SQL Server 연결 속성 편집** 을 선택하여 연결 설정을 수정할 수 있습니다. 선택할 항목을 이해하는 방법에 대해 [자세히 알아보세요](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

## <a name="aspnet-web-apps-discovery-requirements"></a>ASP.NET 웹앱 검색 요구 사항

[소프트웨어 인벤토리](how-to-discover-applications.md)는 검색된 서버의 기존 웹 서버 역할을 식별합니다. 서버에서 웹 서버 역할을 사용한 것으로 확인되면 Azure Migrate는 서버에서 웹앱 검색을 수행합니다.
사용자는 어플라이언스에서 도메인과 비도메인 자격 증명을 모두 추가할 수 있습니다. 사용된 계정에 원본 서버에 대한 로컬 관리자 권한이 있는지 확인합니다. Azure Migrate는 각 서버에 자격 증명을 자동으로 매핑하므로 자격 증명을 수동으로 매핑하지 않아도 됩니다. 가장 중요한 점은 해당 자격 증명이 Microsoft로 전송되지 않고 원본 환경에서 실행되는 어플라이언스에 남아 있다는 것입니다.
어플라이언스가 연결되면 IIS 웹 서버 및 ASP.NET 웹앱에 대한 구성 데이터를 수집합니다. 웹앱 구성 데이터는 24시간마다 한 번 업데이트됩니다.

지원 | 세부 정보
--- | ---
**지원되는 서버** | 현재 VMware 환경에서 IIS를 실행하는 Windows 서버에서만 지원됩니다.
**Windows 서버** | Windows Server 2008 R2 이상이 지원됩니다.
**Linux 서버** | 현재 지원되지 않습니다.
**IIS 액세스** | 웹앱 검색에는 로컬 관리 사용자 계정이 필요합니다.
**IIS 버전** | IIS 7.5 이상이 지원됩니다.

> [!NOTE]
> 데이터는 미사용 및 전송 중에 상시 암호화됩니다.

## <a name="dependency-analysis-requirements-agentless"></a>종속성 분석 요구 사항(에이전트 없음)

[종속성 분석](concepts-dependency-visualization.md)을 사용하면 평가하여 Azure로 마이그레이션하려는 온-프레미스 서버 간 종속성을 파악할 수 있습니다. 다음 표에는 에이전트 없는 종속성 분석을 설정하기 위한 요구 사항이 요약되어 있습니다.

지원 | 세부 정보
--- | ---
**지원되는 서버** | 현재 VMware 환경에 있는 서버에서만 지원됩니다.
**Windows 서버** | Windows Server 2019<br />Windows Server 2016<br /> Windows Server 2012 R2<br /> Windows Server 2012<br /> Windows Server 2008 R2(64비트)<br />Microsoft Windows Server 2008(32비트)
**Linux 서버** | Red Hat Enterprise Linux 7, 6, 5<br /> Ubuntu Linux 16.04, 14.04<br /> Debian 8, 7<br /> Oracle Linux 7, 6<br /> CentOS 7, 6, 5<br /> SUSE Linux Enterprise Server 11 이상
**서버 요구 사항** | 분석하려는 서버에 VMware Tools(10.2.1 이상)를 설치하고 실행해야 합니다.<br /><br /> 서버에는 PowerShell 버전 2.0 이상이 설치되어 있어야 합니다.
**검색 방법** |  vCenter Server를 실행하는 서버에 설치된 VMware Tools를 사용하여 서버 간 종속성 정보를 수집합니다. Azure Migrate는 vSphere API를 사용하여 서버에서 정보를 수집합니다. 서버에 에이전트가 설치되어 있지 않으며 어플라이언스가 서버에 직접 연결되지 않습니다. WMI는 Windows 서버에서 사용으로 설정되고 사용 가능해야 합니다.
**vCenter 계정** | Azure Migrate에서 평가에 사용하는 읽기 전용 계정에는 VMware VM의 게스트 작업 권한이 있어야 합니다.
**Windows 서버 권한** |  서버에 대한 관리자 권한이 있는 사용자 계정(로컬 또는 도메인)입니다.
**Linux 계정** | 루트 사용자 계정 또는 /bin/netstat 및 /bin/ls 파일에 대한 다음 권한이 있는 계정입니다. <br />CAP_DAC_READ_SEARCH<br /> CAP_SYS_PTRACE<br /><br /> 다음 명령을 사용하여 해당 기능을 설정합니다.<br /><code>sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/ls<br /> sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/netstat</code>
**포트 액세스** | Azure Migrate 어플라이언스는 검색하려는 종속성이 있는 서버를 실행하는 ESXi 호스트의 TCP 포트 443에 연결할 수 있어야 합니다. vCenter Server를 실행하는 서버는 ESXi 호스트 연결을 반환하여 종속성 데이터를 포함하는 파일을 다운로드합니다.

## <a name="dependency-analysis-requirements-agent-based"></a>종속성 분석 요구 사항(에이전트 기반)

[종속성 분석](concepts-dependency-visualization.md)을 사용하면 평가하여 Azure로 마이그레이션하려는 온-프레미스 서버 간 종속성을 파악할 수 있습니다. 다음 표에는 에이전트 기반 종속성 분석을 설정하기 위한 요구 사항이 요약되어 있습니다.

요구 사항 | 세부 정보
--- | ---
**배포 전** | Azure Migrate: 검색 및 평가 도구를 프로젝트에 추가하여 준비합니다.<br /><br />온-프레미스 서버를 검색하도록 Azure Migrate 어플라이언스를 설정한 후 종속성 시각화를 배포합니다.<br /><br />[처음으로 프로젝트를 만드는](create-manage-projects.md) 방법을 알아봅니다.<br /> [기존 프로젝트에 검색 및 평가 도구를 추가](how-to-assess.md)하는 방법을 알아봅니다.<br /> [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md) 또는 물리적 서버의 평가를 위해 Azure Migrate 어플라이언스를 설정하는 방법을 알아봅니다.
**지원되는 서버** | 온-프레미스 환경의 모든 서버에 지원됩니다.
**Log Analytics** | Azure Migrate는 종속성 시각화에 대한 [Azure Monitor 로그](../azure-monitor/logs/log-query-overview.md)의 [서비스 맵](../azure-monitor/vm/service-map.md) 솔루션을 사용합니다.<br /><br /> 신규 또는 기존 Log Analytics 작업 영역을 프로젝트와 연결합니다. 프로젝트의 작업 영역은 추가된 후 수정할 수 없습니다. <br /><br /> 작업 영역은 프로젝트와 동일한 구독에 있어야 합니다.<br /><br /> 작업 영역은 미국 동부, 동남아시아 또는 서유럽 지역에 있어야 합니다. 다른 지역의 작업 영역은 프로젝트에 연결할 수 없습니다.<br /><br /> 작업 영역은 [서비스 맵이 지원되는 지역](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions)에 있어야 합니다.<br /><br /> Log Analytics에서 Azure Migrate와 연결된 작업 영역에는 프로젝트 키와 프로젝트 이름이 태그로 지정됩니다.
**필요한 에이전트** | 분석하려는 각 서버에서 다음 에이전트를 설치합니다.<br />- [MMA(Microsoft Monitoring Agent)](../azure-monitor/agents/agent-windows.md)<br />- [종속성 에이전트](../azure-monitor/agents/agents-overview.md#dependency-agent)<br /><br /> 온-프레미스 서버가 인터넷에 연결되어 있지 않으면 Log Analytics 게이트웨이를 다운로드하여 온-프레미스 서버에 설치합니다.<br /><br /> [종속성 에이전트](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) 및 [MMA](how-to-create-group-machine-dependencies.md#install-the-mma) 설치에 관해 자세히 알아봅니다.
**Log Analytics 작업 영역** | 작업 영역은 프로젝트와 동일한 구독에 있어야 합니다.<br /><br /> Azure Migrate는 현재 미국 동부, 동남아시아, 서유럽 지역에 있는 작업 영역을 지원합니다.<br /><br />  작업 영역은 [서비스 맵 지원되는](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions) 지역에 있어야 합니다.<br /><br /> 프로젝트의 작업 영역은 추가된 후 수정할 수 없습니다.
**비용** | 프로젝트와 Log Analytics 작업 영역을 연결한 날로부터 180일 동안은 서비스 맵 솔루션 요금이 부과되지 않습니다.<br /><br /> 180일이 지나면 표준 Log Analytics 요금이 적용됩니다.<br /><br /> 연결된 Log Analytics 작업 영역 내에서 서비스 맵 이외의 다른 솔루션을 사용하면 Log Analytics에 대한 [표준 요금](https://azure.microsoft.com/pricing/details/log-analytics/)이 발생합니다.<br /><br /> 프로젝트가 삭제될 때 작업 영역은 자동으로 삭제되지 않습니다. 프로젝트를 삭제한 후에는 서비스 맵을 무료로 사용할 수 없으며 Log Analytics 작업 영역의 유료 계층에 따라 노드마다 요금이 부과됩니다.<br /><br />Azure Migrate 일반 공급(2018년 2월 28일) 전에 만든 프로젝트가 있는 경우 서비스 맵 요금이 추가로 부과될 수도 있습니다. 180일 후에만 요금이 부과되도록 새 프로젝트를 만드는 것이 좋습니다. GA 이전에 만든 작업 영역은 계속 요금이 부과됩니다.
**관리** | 작업 영역에 에이전트를 등록할 때 프로젝트에서 제공하는 ID 및 키를 사용합니다.<br /><br /> Azure Migrate 외부에서 Log Analytics 작업 영역을 사용할 수 있습니다.<br /><br /> 연결된 프로젝트를 삭제하면 작업 영역이 자동으로 삭제되지 않습니다. [수동으로 삭제합니다](../azure-monitor/logs/manage-access.md).<br /><br /> 프로젝트를 삭제하지 않는 한, Azure Migrate가 만든 작업 영역을 삭제하지 마세요. 작업 영역을 삭제하면 종속성 시각화 기능이 정상적으로 작동하지 않습니다.
**인터넷 연결** | 서버가 인터넷에 연결되어 있지 않으면 Log Analytics 게이트웨이를 서버에 설치해야 합니다.
**Azure Government** | 에이전트 기반 종속성 분석은 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [평가 모범 사례](best-practices-assessment.md)를 검토합니다.
- [VMware 평가를 준비](./tutorial-discover-vmware.md)하는 방법을 알아봅니다.