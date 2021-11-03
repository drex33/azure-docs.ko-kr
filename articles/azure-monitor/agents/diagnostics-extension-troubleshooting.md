---
title: Azure Diagnostics 확장 문제 해결
description: Azure Virtual Machines, Service Fabric 또는 Cloud Services에서 Azure 진단을 사용할 때 문제를 해결합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/08/2019
ms.openlocfilehash: 30715eee331547fe3747ff121797bb3e0939380f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131012305"
---
# <a name="azure-diagnostics-troubleshooting"></a>Azure Diagnostics 문제 해결
이 문서에서는 Azure Diagnostics 사용과 관련된 문제 해결 정보를 설명합니다. Azure 진단에 대한 자세한 내용은 [Azure Diagnostics 개요](diagnostics-extension-overview.md)를 참조하세요.

## <a name="logical-components"></a>논리적 구성 요소
**진단 플러그 인 시작 관리자(DiagnosticsPluginLauncher.exe)**: Azure Diagnostics 확장을 시작합니다. 진입점 프로세스로 사용됩니다.

**진단 플러그 인(DiagnosticsPlugin.exe)**: 모니터링 에이전트의 수명을 구성, 시작 및 관리합니다. 시작 관리자가 시작하는 주요 프로세스입니다.

**모니터링 에이전트(MonAgent\*.exe 프로세스)**: 진단 데이터를 모니터링, 수집 및 전송합니다.

## <a name="logartifact-paths"></a>로그/아티팩트 경로
다음은 중요한 몇 가지 로그 및 아티팩트에 대한 경로입니다. 문서의 나머지 부분에서 이 정보를 참조합니다.

### <a name="azure-cloud-services"></a>Azure Cloud Services
| 아티팩트 | 경로 |
| --- | --- |
| **Azure Diagnostics 구성 파일** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\Config.txt |
| **로그 파일** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\ |
| **진단 데이터에 대한 로컬 저장소** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Tables |
| **모니터링 에이전트 구성 파일** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Azure Diagnostics 확장 패키지** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version> |
| **로그 컬렉션 유틸리티 경로** | %SystemDrive%\Packages\GuestAgent\ |
| **MonAgentHost 로그 파일** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

### <a name="virtual-machines"></a>가상 머신
| 아티팩트 | 경로 |
| --- | --- |
| **Azure Diagnostics 구성 파일** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\RuntimeSettings |
| **로그 파일** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\ |
| **진단 데이터에 대한 로컬 저장소** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Tables |
| **모니터링 에이전트 구성 파일** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MaConfig.xml |
| **상태 파일** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\Status |
| **Azure Diagnostics 확장 패키지** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>|
| **로그 컬렉션 유틸리티 경로** | C:\WindowsAzure\Logs\WaAppAgent.log |
| **MonAgentHost 로그 파일** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

## <a name="metric-data-doesnt-appear-in-the-azure-portal"></a>Azure Portal에 메트릭 데이터가 나타나지 않음
Azure Diagnostics는 Azure Portal에 표시할 수 있는 메트릭 데이터를 제공합니다. 포털에 데이터를 표시하는 데 문제가 있는 경우 해당하는 메트릭 레코드가 있는지 Azure Diagnostics 스토리지 계정의 WADMetrics\* 테이블을 확인하고, [리소스 공급자](../../azure-resource-manager/management/resource-providers-and-types.md) Microsoft.Insights를 등록했는지 확인합니다.

여기서 테이블의 **PartitionKey** 는 리소스 ID, 가상 머신 또는 가상 머신 확장 집합입니다. **RowKey** 는 메트릭 이름(일명 성능 카운터 이름)입니다.

리소스 ID가 올바르지 않으면 **진단** **구성** > **메트릭** > **리소스 ID** 가 올바르게 설정되었는지 확인합니다.

특정 메트릭에 대한 데이터가 없는 경우 **진단 구성** > **PerformanceCounter** 에 메트릭(성능 카운터)이 포함되어 있는지 확인합니다. 다음 카운터를 기본적으로 활성화합니다.
- \Processor(_Total)\% 프로세서 시간
- \Memory\Available Bytes
- \ASP.NET Applications(__Total__)\Requests/Sec
- \ASP.NET Applications(__Total__)\Errors Total/Sec
- \ASP.NET\대기 중인 요청
- \ASP.NET\거부된 요청
- \Processor(w3wp)\% Processor Time
- \Process(w3wp)\Private Bytes
- \Process(WaIISHost)\% Processor Time
- \Process(WaIISHost)\Private Bytes
- \Process(WaWorkerHost)\% Processor Time
- \Process(WaWorkerHost)\Private Bytes
- \Memory\Page Faults/sec
- \.NET CLR Memory(_Global_)\% Time in GC
- \LogicalDisk(C:)\Disk Write Bytes/sec
- \LogicalDisk(C:)\Disk Read Bytes/sec
- \LogicalDisk(D:)\Disk Write Bytes/sec
- \LogicalDisk(D:)\Disk Read Bytes/sec

구성이 올바르게 설정되었지만 여전히 메트릭 데이터를 볼 수 없는 경우 다음 지침을 사용하여 문제를 해결해 보세요.

## <a name="azure-diagnostics-is-not-starting"></a>Azure Diagnostics가 시작되지 않음
Azure Diagnostics가 시작하지 못한 이유에 대한 자세한 내용은 앞서 제공한 로그 파일 위치에서 **DiagnosticsPluginLauncher.log** 및 **DiagnosticsPlugin.log** 파일을 참조하세요.

이러한 로그가 `Monitoring Agent not reporting success after launch`를 표시하는 경우 MonAgentHost.exe를 시작하지 못한 것을 의미합니다. 이전 섹션의 `MonAgentHost log file`에 대해 지정된 위치에서 해당 로그를 찾습니다.

로그 파일의 마지막 줄에는 종료 코드가 포함됩니다.

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
**음수** 종료 코드가 있으면 [참조 섹션](#references)의 [종료 코드 테이블](#azure-diagnostics-plugin-exit-codes)을 참조하세요.

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>진단 데이터가 Azure Storage에 기록되지 않음
나타나는 데이터가 없는지 아니면 일부 데이터가 나타나는지 결정합니다.

### <a name="diagnostics-infrastructure-logs"></a>진단 인프라 로그
진단은 모든 오류를 진단 인프라 로그에 기록합니다. [구성에서 진단 인프라 로그 캡처](#how-to-check-diagnostics-extension-configuration)를 활성화했는지 확인합니다. 그런 다음 구성한 스토리지 계정의 `DiagnosticInfrastructureLogsTable` 테이블에 나타나는 관련 오류를 빨리 확인할 수 있습니다.

### <a name="no-data-is-appearing"></a>데이터가 나타나지 않음
이벤트 데이터가 전혀 나타나지 않는 가장 일반적인 이유는 스토리지 계정 정보가 잘못 정의된 것입니다.

해결 방법: 진단 구성을 수정하고 진단을 다시 설치합니다.

스토리지 계정이 올바르게 구성되었으면 컴퓨터에 원격 액세스하고 *DiagnosticsPlugin.exe* 및 *MonAgentCore.exe* 가 실행되고 있는지 확인합니다. 실행 중이 아니면 [Azure Diagnostics가 시작되지 않음](#azure-diagnostics-is-not-starting)의 단계를 따릅니다.

프로세스가 실행되고 있으면 [데이터가 로컬로 캡처되고 있습니까?](#is-data-getting-captured-locally)로 이동하고 여기서 지침을 따릅니다.

그래도 문제가 해결되지 않으면 다음을 수행해 보세요.

1. 에이전트 제거
2. C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics 디렉터리 제거
3. 에이전트 다시 설치

### <a name="part-of-the-data-is-missing"></a>일부 데이터가 누락됨
전부가 아니라 일부 데이터만 가져온다는 것은 데이터 수집/전송 파이프라인은 올바르게 설정되었음을 의미합니다. 문제의 범위를 좁히려면 여기서 하위 섹션을 따릅니다.

#### <a name="is-the-collection-configured"></a>컬렉션이 구성되어 있습니까?
진단 구성에는 수집할 데이터의 특정 형식에 대한 지침이 있습니다. [구성을 검토](#how-to-check-diagnostics-extension-configuration)하여 컬렉션에 대해 구성한 데이터만 찾고 있는지 확인합니다.

#### <a name="is-the-host-generating-data"></a>호스트에서 데이터를 생성하고 있습니까?
- **성능 카운터**: perfmon을 열고 카운터를 확인합니다.

- **추적 로그:** VM에 원격으로 액세스하고 앱의 구성 파일에 TextWriterTraceListener를 추가합니다.  https://msdn.microsoft.com/library/sk36c28t.aspx 문서를 참조하여 텍스트 수신기를 설정합니다.  `<trace>` 요소에 `<trace autoflush="true">`가 있는지 확인합니다.<br />
생성된 추적 로그가 표시되지 않으면 누락된 추적 로그에 대한 자세한 정보를 참조하세요.

- **ETW 추적**: VM에 원격 액세스하고 PerfView를 설치합니다.  PerfView에서 **파일** > **사용자 명령** > **Listen etwprovder1** > **etwprovider2** 등을 차례로 실행합니다. **Listen** 명령은 대/소문자를 구분하며, 쉼표로 구분된 ETW 공급자 목록 사이에는 공백이 없어야 합니다. 명령을 실행하지 못하는 경우 Perfview 도구의 오른쪽 아래에 있는 **로그** 단추를 선택하여 실행하려고 시도한 내용과 그 결과를 확인할 수 있습니다.  입력이 올바르다고 가정하면 새 창이 나타납니다. 몇 초 후 ETW 추적 보기를 시작합니다.

- **이벤트 로그**: VM에 원격 액세스합니다. `Event Viewer`를 연 다음 이벤트가 있는지 확인합니다.

#### <a name="is-data-getting-captured-locally"></a>데이터가 로컬로 캡처되고 있습니까?
다음으로 데이터가 로컬에서 캡처되고 있는지 확인합니다.
데이터는 진단 데이터에 대한 로컬 저장소의 `*.tsf` 파일에 로컬로 저장됩니다. 다른 종류의 로그는 서로 다른 `.tsf` 파일에 수집됩니다. 이름은 Azure Storage의 테이블 이름과 비슷합니다.

예를 들어 `Performance Counters`는 `PerformanceCountersTable.tsf`에 수집됩니다. 이벤트 로그가 `WindowsEventLogsTable.tsf`에 수집됩니다. [로컬 로그 추출](#local-log-extraction) 섹션의 지침을 사용하여 로컬 수집 파일을 열고 디스크에 수집되는지 확인합니다.

로컬로 수집된 로그가 표시되지 않고 호스트에서 데이터를 생성하고 있음을 이미 확인한 경우 구성 문제가 있을 가능성이 높습니다. 구성 설정을 주의 깊게 검토합니다.

또한 MonitoringAgent MaConfig.xml에 대해 생성된 구성을 검토합니다. 관련 로그 소스를 설명하는 섹션이 있는지 확인합니다. 그런 다음 진단 구성과 구성 모니터링 에이전트 간의 변환에서 손실이 없는지 확인합니다.

#### <a name="is-data-getting-transferred"></a>데이터가 전송되고 있습니까?
데이터가 로컬에서 캡처되고 있지만 여전히 스토리지 계정에 표시되지 않음을 확인한 경우 다음 단계를 수행합니다.

- 올바른 스토리지 계정을 제공하고 지정된 스토리지 계정에 대해 키를 롤오버하지 않았는지 확인합니다. Azure Cloud Services의 경우 사람들이 `useDevelopmentStorage=true`를 업데이트하지 않는 경우가 종종 있습니다.

- 제공된 스토리지 계정이 올바른지 확인합니다. 구성 요소가 공용 스토리지 엔드포인트에 연결하지 못하게 하는 네트워크 제한 사항이 없는지 확인합니다. 이 작업을 수행하는 한 가지 방법은 컴퓨터에 원격 액세스한 다음 동일한 스토리지 계정에 직접 작성하는 것입니다.

- 마지막으로, 모니터링 에이전트에서 오류를 보고하고 있는지 확인할 수 있습니다. 모니터링 에이전트는 진단 데이터의 로컬 저장소에 있는 `maeventtable.tsf`에 로그를 기록합니다. 이 파일을 열려면 [로컬 로그 추출](#local-log-extraction) 섹션의 지침을 따릅니다. 그런 다음 스토리지에 기록하는 로컬 파일 읽기 실패를 나타내는 `errors`가 있는지 여부를 결정합니다.

### <a name="capturing-and-archiving-logs"></a>로그 캡처 및 보관
지원 문의를 생각하고 있는 경우, 요청받을 수 있는 첫 번째 작업은 자신의 머신에서 로그를 수집하는 것입니다. 사용자가 직접 수행하면 시간을 절약할 수 있습니다. 로그 수집 유틸리티 경로에서 `CollectGuestLogs.exe` 유틸리티를 실행합니다. 모든 관련 Azure 로그를 포함한 .zip 파일을 동일한 폴더에 생성합니다.

## <a name="diagnostics-data-tables-not-found"></a>진단 데이터 테이블을 찾을 수 없음
ETW 이벤트를 보유하는 Azure Storage의 테이블 이름은 다음 코드를 사용하여 지정됩니다.

```csharp
        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;
```

예를 들면 다음과 같습니다.

```xml
        <EtwEventSourceProviderConfiguration provider="prov1">
          <Event id="1" />
          <Event id="2" eventDestination="dest1" />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider="prov2">
          <DefaultEvents eventDestination="dest2" />
        </EtwEventSourceProviderConfiguration>
```
```JSON
"EtwEventSourceProviderConfiguration": [
    {
        "provider": "prov1",
        "Event": [
            {
                "id": 1
            },
            {
                "id": 2,
                "eventDestination": "dest1"
            }
        ],
        "DefaultEvents": {
            "eventDestination": "DefaultEventDestination",
            "sinks": ""
        }
    },
    {
        "provider": "prov2",
        "DefaultEvents": {
            "eventDestination": "dest2"
        }
    }
]
```
이 코드는 테이블 네 개를 생성합니다.

| 이벤트 | 테이블 이름 |
| --- | --- |
| provider="prov1" &lt;Event id="1" /&gt; |WADEvent+MD5("prov1")+"1" |
| provider="prov1" &lt;Event id="2" eventDestination="dest1" /&gt; |WADdest1 |
| provider="prov1" &lt;DefaultEvents /&gt; |WADDefault+MD5("prov1") |
| provider="prov2" &lt;DefaultEvents eventDestination="dest2" /&gt; |WADdest2 |

## <a name="references"></a>참조

### <a name="how-to-check-diagnostics-extension-configuration"></a>진단 확장 구성을 확인하는 방법
확장 구성을 확인하는 가장 쉬운 방법은 [Azure Resource Explorer](https://resources.azure.com)로 이동한 다음, Azure Diagnostics 확장(IaaSDiagnostics / PaaDiagnostics)이 있는 가상 머신 또는 클라우드 서비스로 이동하는 것입니다.

또는 원격 데스크톱을 머신에 연결하고 로그 아티팩트 경로 섹션에서 설명하는 Azure Diagnostics 구성 파일을 살펴보는 것입니다.

두 경우 모두 **Microsoft.Azure.Diagnostics** 를 검색한 다음 **xmlCfg** 또는 **WadCfg** 필드를 검색합니다.

가상 머신에서 검색하는데 **WadCfg** 필드가 있으면 구성이 JSON 형식임을 의미합니다. **xmlCfg** 필드가 있는 경우 구성은 XML 형식이고 base64로 인코딩됩니다. 진단에서 로드한 XML을 보려면 [디코딩](https://www.bing.com/search?q=base64+decoder)해야 합니다.

Cloud Service 역할의 경우 디스크에서 구성을 선택하면 데이터가 base64로 인코딩되므로 진단에서 로드한 XML을 보려면 [디코딩](https://www.bing.com/search?q=base64+decoder)해야 합니다.

### <a name="azure-diagnostics-plugin-exit-codes"></a>Azure Diagnostics 플러그 인 종료 코드
플러그 인은 다음 종료 코드를 반환합니다.

| 종료 코드 | 설명 |
| --- | --- |
| 0 |성공했습니다. |
| -1 |일반 오류. |
| -2 |rcf 파일을 로드할 수 없습니다.<p>이는 게스트 에이전트 플러그 인 시작 관리자를 VM에서 올바르지 않게 수동으로 호출할 때에만 발생하는 내부 오류입니다. |
| -3 |진단 구성 파일을 로드할 수 없습니다.<p><p>해결 방법: 구성 파일이 스키마 유효성 검사를 통과하지 못한 것이 원인입니다. 해결 방법은 스키마를 준수하는 구성 파일을 제공하는 것입니다. |
| -4 |진단에서 이미 로컬 리소스 디렉터리를 사용하고 있는 모니터링 에이전트의 또 다른 인스턴스입니다.<p><p>해결 방법: **LocalResourceDirectory** 를 참조하세요. |
| -6 |게스트 에이전트 플러그 인 시작 관리자가 잘못된 명령줄로 진단을 실행하려고 했습니다.<p><p>이는 게스트 에이전트 플러그 인 시작 관리자를 VM에서 올바르지 않게 수동으로 호출할 때에만 발생하는 내부 오류입니다. |
| -10 |진단 플러그 인이 처리되지 않은 예외와 함께 종료되었습니다. |
| -11 |게스트 에이전트는 모니터링 에이전트를 시작하고 모니터링하는 일을 담당하는 프로세스를 만들 수 없습니다.<p><p>해결 방법: 새 프로세스를 실행할 수 있을 만큼 시스템 리소스가 충분한지 확인합니다.<p> |
| -101 |진단 플러그 인을 호출할 때 인수가 잘못되었습니다.<p><p>이는 게스트 에이전트 플러그 인 시작 관리자를 VM에서 올바르지 않게 수동으로 호출할 때에만 발생하는 내부 오류입니다. |
| -102 |플러그인 프로세스 자체를 초기화할 수 없습니다.<p><p>해결 방법: 새 프로세스를 실행할 수 있을 만큼 시스템 리소스가 충분한지 확인합니다. |
| -103 |플러그인 프로세스 자체를 초기화할 수 없습니다. 특히 로거 개체를 만들 수 없습니다.<p><p>해결 방법: 새 프로세스를 실행할 수 있을 만큼 시스템 리소스가 충분한지 확인합니다. |
| -104 |게스트 에이전트가 제공하는 rcf 파일을 로드할 수 없습니다.<p><p>이는 게스트 에이전트 플러그 인 시작 관리자를 VM에서 올바르지 않게 수동으로 호출할 때에만 발생하는 내부 오류입니다. |
| -105 |진단 플러그 인이 진단 구성 파일을 열 수 없습니다.<p><p>이는 진단 플러그 인을 VM에서 올바르지 않게 수동으로 호출할 때에만 발생하는 내부 오류입니다. |
| -106 |진단 구성 파일을 읽을 수 없습니다.<p><p>구성 파일이 스키마 유효성 검사를 통과하지 못한 것이 원인입니다. <br><br>해결 방법: 스키마를 준수하는 구성 파일을 제공합니다. 자세한 내용은 [진단 확장 구성을 확인하는 방법](#how-to-check-diagnostics-extension-configuration)을 참조하세요. |
| -107 |모니터링 에이전트로의 리소스 디렉터리 전달이 유효하지 않습니다.<p><p>이는 모니터링 에이전트를 VM에서 올바르지 않게 수동으로 호출할 때에만 발생하는 내부 오류입니다.</p> |
| -108 |진단 구성 파일을 모니터링 에이전트 구성 파일로 변환할 수 없습니다.<p><p>이는 진단 플러그 인을 유효하지 않은 구성 파일과 함께 수동으로 호출할 때에만 발생하는 내부 오류입니다. |
| -110 |일반 진단 구성 오류.<p><p>이는 진단 플러그 인을 유효하지 않은 구성 파일과 함께 수동으로 호출할 때에만 발생하는 내부 오류입니다. |
| -111 |모니터링 에이전트를 시작할 수 없습니다.<p><p>해결 방법: 사용 가능한 시스템 리소스가 충분한지 확인합니다. |
| -112 |일반 오류 |

### <a name="local-log-extraction"></a>로컬 로그 추출
모니터링 에이전트는 로그 및 아티팩트를 `.tsf` 파일로 수집합니다. `.tsf` 파일은 읽을 수 없지만 다음과 같이 `.csv`로 변환할 수 있습니다:

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
`<relevantLogFile>.csv`라는 새 파일이 해당 `.tsf` 파일과 동일한 경로에 만들어집니다.

> [!NOTE]
> 이 유틸리티는 기본 tsf 파일(예: PerformanceCountersTable.tsf)에 대해서만 실행해야 합니다. 그러면 첨부된 파일(예: PerformanceCountersTables_\*\*001.tsf, PerformanceCountersTables_\*\*002.tsf 등)이 자동으로 처리됩니다.

### <a name="more-about-missing-trace-logs"></a>누락된 추적 로그에 대한 자세한 정보

> [!NOTE]
> 다음 정보는 주로 IaaS VM에서 실행되는 애플리케이션에 DiagnosticsMonitorTraceListener를 구성하지 않은 경우에만 Azure Cloud Services에 적용됩니다.

- **DiagnosticMonitorTraceListener** 가 web.config 또는 app.config에서 구성되었는지 확인합니다. 클라우드 서비스 프로젝트에서는 기본적으로 구성되어 있습니다. 그러나 일부 고객은 추적 문이 진단에서 수집되지 않도록 이 부분을 주석화합니다.

- 로그가 **OnStart** 또는 **Run** 메서드로부터 쓰여지지 않는 경우 **DiagnosticMonitorTraceListener** 가 app.config에 있는지 확인합니다. 이는 기본적으로 web.config에 있지만 이는 w3wp.exe 내에서 실행되는 코드에만 적용됩니다. 그러므로 WaIISHost.exe에서 실행하는 추적을 캡처하려면 이것을 app.config에 넣어야 합니다.

- **Diagnostics.Debug.WriteXXX** 대신 **Diagnostics.Trace.TraceXXX** 를 사용하고 있는지 확인합니다. Debug(디버그) 문은 릴리스 빌드에서 제거됩니다.

- 컴파일된 코드에 **Diagnostics.Trace 줄** 이 실제로 있는지 확인합니다(Reflector, ildasm 또는 ILSpy를 통해 확인). TRACE 조건부 컴파일 기호를 사용하지 않으면 **Diagnostics.Trace** 명령이 컴파일된 이진 파일에서 제거됩니다. 이는 msbuild를 사용하여 프로젝트를 빌드할 때 발생하는 일반적인 문제입니다.

## <a name="known-issues-and-mitigations"></a>알려진 문제 및 완화 방법
다음은 알려진 문제 및 완화 방법을 보여 주는 목록입니다.

**1. .NET 4.5 종속성**

Windows Azure Diagnostics 확장은 .NET 4.5 프레임워크 이상에 대한 런타임 종속성을 포함합니다. 이 섹션을 쓰는 시점에서 Azure Cloud Services용으로 프로비전된 모든 컴퓨터와 Azure Virtual Machine 기반의 모든 공인 이미지에는 .NET 4.5 이상이 설치되어 있습니다.

.NET 4.5 이상이 없는 머신에서 Windows Azure Diagnostics 확장의 실행을 시도하는 상황은 여전히 발생할 수 있습니다. 이 문제는 오래된 이미지 또는 스냅샷에서 컴퓨터를 만들거나 사용자 지정 디스크를 가져올 때 발생합니다.

**DiagnosticsPluginLauncher.exe** 를 실행할 때 일반적으로 **255** 종료 코드로 매니페스트되지만, 다음과 같이 처리되지 않은 예외로 인해 실패가 발생합니다.
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**완화 방법:** 컴퓨터에 .NET 4.5 이상을 설치합니다.

**2. 성능 카운터 데이터가 스토리지에서 사용할 수 있지만 포털에서 표시되지 않음**

가상 머신의 포털 환경에는 기본적으로 특정 성능 카운터가 표시됩니다. 성능 카운터가 표시되지 않더라도 스토리지에서 사용할 수 있기 때문에 데이터가 생성되고 있음을 알 수 있는 경우 다음을 확인합니다.

- 스토리지의 데이터에 영어 카운터 이름이 있는지 여부. 카운터 이름이 영어가 아닌 경우 포털 메트릭 차트에서 해당 이름을 인식할 수 없습니다. **완화 방법**: 시스템 계정에 대한 컴퓨터의 언어를 영어로 변경합니다. 이렇게 하려면 **제어판** > **영역** > **관리** > **복사 설정** 을 선택합니다. 그런 다음 사용자 지정 언어가 시스템 계정에 적용되지 않도록 **시작 화면 및 시스템 계정** 을 선택 취소합니다.

- 성능 카운터 이름에 와일드카드(\*)를 사용하는 경우 성능 카운터가 Azure Storage 싱크에 전송될 때 포털에서 구성된 카운터와 수집된 카운터 간의 상관관계를 지정할 수 없습니다. **완화**: 와일드카드를 사용하고 포털에서 (\*)를 확장하도록 하려면 성능 카운터를 Azure Monitor 싱크로 라우팅합니다.
