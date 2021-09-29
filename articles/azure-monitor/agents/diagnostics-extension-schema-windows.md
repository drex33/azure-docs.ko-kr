---
title: Windows 진단 확장 스키마
description: Azure Monitor의 Windows 진단 확장 (WAD)에 대 한 구성 스키마 참조입니다.
ms.topic: reference
author: bwren
ms.author: bwren
ms.date: 01/20/2020
ms.openlocfilehash: f0a2fed2a7dc2fae62005d1f00fdb688a4f52ebe
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129232744"
---
# <a name="windows-diagnostics-extension-schema"></a>Windows 진단 확장 스키마
Azure 진단 확장은 게스트 운영 체제에서 모니터링 데이터를 수집 하 고 Azure compute 리소스의 작업을 수집 하는 Azure Monitor의 에이전트입니다. 이 문서에서는 Windows 가상 컴퓨터 및 기타 계산 리소스에서 진단 확장을 구성 하는 데 사용 되는 스키마에 대해 자세히 설명 합니다.

> [!NOTE]
> 이 문서의 스키마는 버전 1.3 이상 (Azure SDK 2.4 이상)에 유효 합니다. 최신 구성 섹션은 추가된 버전에 표시하도록 주석 처리되었습니다. 스키마의 버전 1.0 및 1.2이 보관 되었으며 더 이상 사용할 수 없습니다. 

## <a name="public-configuration-file-schema"></a>공용 구성 파일 스키마

다음 PowerShell 명령을 실행하여 공용 구성 파일 스키마 정의를 다운로드합니다.  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  


## <a name="common-attribute-types"></a>일반 특성 유형  
 **scheduledTransferPeriod** 특성이 몇 가지 요소에 나타납니다. 스토리지에 예약된 전송 사이의 간격은 가장 가까운 시간(분)으로 반올림됩니다. 값은 [XML "기간 데이터 형식"](https://www.w3schools.com/xml/schema_dtypes_date.asp)입니다.


## <a name="diagnosticsconfiguration-element"></a>DiagnosticsConfiguration 요소  
 *Tree: Root - DiagnosticsConfiguration*

버전 1.3에 추가되었습니다.  

진단 구성 파일의 최상위 요소입니다.  

**특성** xmlns - 진단 구성 파일에 대한 XML 네임스페이스는 다음과 같습니다.  
`http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration`


|자식 요소|Description|  
|--------------------|-----------------|  
|**PublicConfig**|필수 사항입니다. 이 페이지의 다른 곳에 있는 설명을 참조하세요.|  
|**PrivateConfig**|(선택 사항) 이 페이지의 다른 곳에 있는 설명을 참조하세요.|  
|**IsEnabled**|Boolean입니다. 이 페이지의 다른 곳에 있는 설명을 참조하세요.|  

## <a name="publicconfig-element"></a>PublicConfig 요소  
 *Tree: Root - DiagnosticsConfiguration - PublicConfig*

 공용 진단 구성을 설명합니다.  

|자식 요소|Description|  
|--------------------|-----------------|  
|**WadCfg**|필수 사항입니다. 이 페이지의 다른 곳에 있는 설명을 참조하세요.|  
|**StorageAccount**|데이터를 저장할 Azure Storage 계정의 이름입니다. Set-AzureServiceDiagnosticsExtension cmdlet을 실행할 때 매개 변수로 지정할 수도 있습니다.|  
|**StorageType**|*Table*, *Blob* 또는 *TableAndBlob* 일 수 있습니다. Table이 기본값입니다. TableAndBlob을 선택하면 진단 데이터는 각 형식당 한 번씩, 두 번 기록됩니다.|  
|**LocalResourceDirectory**|모니터링 에이전트가 이벤트 데이터를 저장하는 가상 컴퓨터의 디렉터리입니다. 설정되어 있지 않은 경우 기본 디렉터리가 사용됩니다.<br /><br /> 작업자/웹 역할의 경우: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Virtual Machine의 경우: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> 필수 특성은 다음과 같습니다.<br /><br /> - **경로** - Azure Diagnostics에서 사용되는 시스템의 디렉터리입니다.<br /><br /> - **expandEnvironment** - 환경 변수가 경로 이름에서 확장되는지 여부를 제어합니다.|  

## <a name="wadcfg-element"></a>WadCFG 요소  
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG*

 수집할 원격 분석 데이터를 식별 및 구성합니다.  


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration 요소
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration*

 필수

|특성|설명|  
|----------------|-----------------|  
| **overallQuotaInMB** | Azure Diagnostics로 수집된 진단 데이터의 다양한 형식에서 사용될 수 있는 로컬 디스크 공간의 최대 크기입니다. 기본 설정은 4096MB입니다.<br />
|**useProxyServer** | IE 설정에서 설정한 대로 프록시 서버 설정을 사용하도록 Azure Diagnostics를 구성합니다.|
|**sinks** | 1.5에 추가되었습니다. (선택 사항) 또한 싱크를 지원하는 모든 자식 요소에 대한 진단 데이터를 보낼 싱크 위치도 가리킵니다. 싱크 예제는 Application Insights 또는 Event Hubs입니다. 참고 Event Hubs에 업로드 된 이벤트를 리소스 ID를 갖도록 하려면 *메트릭* 요소 아래에 *resourceId* 속성을 추가 해야 합니다. |  


<br /> <br />

|자식 요소|Description|  
|--------------------|-----------------|  
|**CrashDumps**|이 페이지의 다른 곳에 있는 설명을 참조하세요.|  
|**DiagnosticInfrastructureLogs**|Azure Diagnostics에 의해 생성된 로그의 컬렉션을 사용하도록 설정합니다. 진단 인프라 로그는 진단 시스템 자체의 문제 해결에 유용합니다. 선택적 특성은 다음과 같습니다.<br /><br /> - **scheduledTransferLogLevelFilter** - 수집된 로그의 최소 심각도 수준을 구성합니다.<br /><br /> - **scheduledTransferPeriod** -가장 가까운 시간 (분)으로 반올림 된 저장소에 대 한 예약 된 전송 사이의 간격입니다. 값은 [XML "기간 데이터 형식"](https://www.w3schools.com/xml/schema_dtypes_date.asp)입니다. |  
|**Directories**|이 페이지의 다른 곳에 있는 설명을 참조하세요.|  
|**EtwProviders**|이 페이지의 다른 곳에 있는 설명을 참조하세요.|  
|**메트릭**|이 페이지의 다른 곳에 있는 설명을 참조하세요.|  
|**PerformanceCounters**|이 페이지의 다른 곳에 있는 설명을 참조하세요.|  
|**WindowsEventLog**|이 페이지의 다른 곳에 있는 설명을 참조하세요.|
|**DockerSources**|이 페이지의 다른 곳에 있는 설명을 참조하세요. |



## <a name="crashdumps-element"></a>CrashDumps 요소  
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - CrashDumps*

 크래시 덤프의 수집을 사용하도록 설정합니다.  

|특성|설명|  
|----------------|-----------------|  
|**containerName**|(선택 사항) 크래시 덤프를 저장하는 데 사용할 Azure Storage 계정의 blob 컨테이너의 이름입니다.|  
|**crashDumpType**|(선택 사항)  최소 또는 전체 크래시 덤프를 수집하도록 Azure Diagnostics를 구성합니다.|  
|**directoryQuotaPercentage**|(선택 사항)  VM에서 크래시 덤프에 대해 예약될 **overallQuotaInMB** 의 비율을 구성합니다.|  

|자식 요소|Description|  
|--------------------|-----------------|  
|**CrashDumpConfiguration**|필수 사항입니다. 각 프로세스에 대한 구성 값을 정의합니다.<br /><br /> 다음과 같은 특성도 필요합니다.<br /><br /> **processName** - Azure Diagnostics에서 크래시 덤프를 수집하도록 할 프로세스의 이름입니다.|  

## <a name="directories-element"></a>Directories 요소
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration -  Directories*

 디렉터리, IIS 실패한 액세스 요청 로그 및/또는 IIS 로그의 콘텐츠 수집을 활성화합니다.  

 선택적 **scheduledTransferPeriod** 특성입니다. 이전 설명을 참조하세요.  

|자식 요소|Description|  
|--------------------|-----------------|  
|**IISLogs**|이 요소를 구성에 포함하면 IIS 로그의 컬렉션이 활성화됩니다.<br /><br /> **containerName** - IIS를 저장하는 데 사용할 Azure Storage 계정의 blob 컨테이너의 이름입니다.|   
|**FailedRequestLogs**|이 요소를 구성에 포함하면 IIS 사이트 또는 애플리케이션에 실패 한 요청에 대한 로그 컬렉션이 활성화합니다. 또한 **Web.config** 의 **system.WebServer** 에 있는 추적 옵션도 사용하도록 설정해야 합니다.|  
|**DataSources**|모니터링할 디렉터리의 목록입니다.|




## <a name="datasources-element"></a>DataSources 요소  
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Directories - DataSources*

 모니터링할 디렉터리의 목록입니다.  

|자식 요소|Description|  
|--------------------|-----------------|  
|**DirectoryConfiguration**|필수 사항입니다. 필수 특성:<br /><br /> **containerName** - 로그 파일을 저장하는 데 사용할 Azure Storage 계정의 blob 컨테이너의 이름입니다.|  





## <a name="directoryconfiguration-element"></a>DirectoryConfiguration 요소  
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Directories - DataSources - DirectoryConfiguration*

 **Absolute** 또는 **LocalResource** 요소 중 하나를 포함하되, 모두 포함할 수는 없습니다.  

|자식 요소|Description|  
|--------------------|-----------------|  
|**절대**|모니터링할 디렉터리의 절대 경로입니다. 다음과 같은 특성이 필요합니다.<br /><br /> - **Path** - 모니터링할 디렉터리의 절대 경로입니다.<br /><br /> - **expandEnvironment** - 경로의 환경 변수가 확장되어 있는지 여부를 구성합니다.|  
|**LocalResource**|모니터링할 로컬 리소스의 상대 경로입니다. 필수 특성은 다음과 같습니다.<br /><br /> - **Name** - 모니터링할 디렉터리를 포함하는 로컬 리소스<br /><br /> - **relativePath** -모니터링할 디렉터리를 포함하는 이름의 상대 경로|  



## <a name="etwproviders-element"></a>EtwProviders 요소  
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders*

 EventSource의 ETW 이벤트 및/또는 공급자를 기반으로 하는 ETW 매니페스트의 컬렉션을 구성합니다.  

|자식 요소|Description|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|[EventSource 클래스](/dotnet/api/system.diagnostics.tracing.eventsource)에서 생성된 이벤트 컬렉션을 구성합니다. 필수 특성:<br /><br /> **provider** - EventSource 이벤트의 클래스 이름입니다.<br /><br /> 선택적 특성은 다음과 같습니다.<br /><br /> - **scheduledTransferLogLevelFilter** - 스토리지 계정으로 전송할 최소 심각도 수준입니다.<br /><br /> - **scheduledTransferPeriod** - 스토리지에 예약된 전송 사이의 간격이 가장 가까운 분으로 반올림됩니다. 값은 [XML "기간 데이터 형식"](https://www.w3schools.com/xml/schema_dtypes_date.asp)입니다. |  
|**EtwManifestProviderConfiguration**|필수 특성:<br /><br /> **provider** - 이벤트 공급자의 GUID<br /><br /> 선택적 특성은 다음과 같습니다.<br /><br /> - **scheduledTransferLogLevelFilter** - 스토리지 계정으로 전송할 최소 심각도 수준입니다.<br /><br /> - **scheduledTransferPeriod** - 스토리지에 예약된 전송 사이의 간격이 가장 가까운 분으로 반올림됩니다. 값은 [XML "기간 데이터 형식"](https://www.w3schools.com/xml/schema_dtypes_date.asp)입니다. |  



## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration 요소  
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders- EtwEventSourceProviderConfiguration*

 [EventSource 클래스](/dotnet/api/system.diagnostics.tracing.eventsource)에서 생성된 이벤트 컬렉션을 구성합니다.  

|자식 요소|Description|  
|--------------------|-----------------|  
|**DefaultEvents**|선택적 특성:<br/><br/> **eventDestination** - 이벤트를 저장할 테이블의 이름|  
|**이벤트**|필수 특성:<br /><br /> **id** - 이벤트의 ID입니다.<br /><br /> 선택적 특성:<br /><br /> **eventDestination** - 이벤트를 저장할 테이블의 이름|  



## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration 요소  
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders - EtwManifestProviderConfiguration*

|자식 요소|Description|  
|--------------------|-----------------|  
|**DefaultEvents**|선택적 특성:<br /><br /> **eventDestination** - 이벤트를 저장할 테이블의 이름|  
|**이벤트**|필수 특성:<br /><br /> **id** - 이벤트의 ID입니다.<br /><br /> 선택적 특성:<br /><br /> **eventDestination** - 이벤트를 저장할 테이블의 이름|  



## <a name="metrics-element"></a>Metrics 요소  
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Metrics*

 빠른 쿼리를 위해 최적화된 성능 카운터 테이블을 생성할 수 있습니다. **PerformanceCounters** 요소에 정의되어 있는 각 성능 카운터는 성능 카운터 테이블에 추가된 메트릭 테이블에 저장되어 있습니다.  

 **resourceId** 특성이 필요합니다.  Azure Diagnostics를 배포하는 가상 머신 또는 Virtual Machine Scale Set의 리소스 ID입니다. [Azure Portal](https://portal.azure.com)에서 **resourceID** 를 가져옵니다. 리소스 그룹 **찾아보기**  ->    ->  **<\> 이름을** 선택합니다. **속성** 타일을 클릭하고 **ID** 필드에서 값을 복사합니다.  이 resourceID 속성은 사용자 지정 메트릭을 보내고 Event Hubs 전송된 데이터에 resourceID 속성을 추가하는 데 사용됩니다. Event Hubs 이벤트를 업로드하여 리소스 ID를 갖도록 하려면 *Metrics* 요소 아래에 *resourceId* 속성을 추가해야 합니다.

|자식 요소|Description|  
|--------------------|-----------------|  
|**MetricAggregation**|필수 특성:<br /><br /> **scheduledTransferPeriod** - 스토리지에 예약된 전송 사이의 간격으로 가장 가까운 시간(분)으로 반올림됩니다. 값은 [XML "기간 데이터 형식"](https://www.w3schools.com/xml/schema_dtypes_date.asp)입니다. |  



## <a name="performancecounters-element"></a>PerformanceCounters 요소  
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - PerformanceCounters*

 성능 카운터의 컬렉션을 활성화합니다.  

 선택적 특성:  

 선택적 **scheduledTransferPeriod** 특성입니다. 이전 설명을 참조하세요.

|자식 요소|Description|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|다음과 같은 특성이 필요합니다.<br /><br /> - **counterSpecifier** - 성능 카운터의 이름입니다. 예들 들어 `\Processor(_Total)\% Processor Time`입니다. 호스트에서 성능 카운터의 목록을 가져오려면 명령 `typeperf`를 실행합니다.<br /><br /> - **sampleRate** - 카운터가 샘플링되는 주기입니다.<br /><br /> 선택적 특성:<br /><br /> **unit** - 카운터의 측정 단위입니다. 값은 [UnitType 클래스에서](/dotnet/api/microsoft.azure.management.sql.models.unittype) 사용할 수 있습니다. |
|**sinks** | 1.5에 추가되었습니다. (선택 사항) 또한 진단 데이터를 보내는 싱크 위치를 가리킵니다. 예를 들어 Azure Monitor 또는 Event Hubs입니다. Event Hubs 이벤트를 업로드하여 리소스 ID를 갖도록 하려면 *Metrics* 요소 아래에 *resourceId* 속성을 추가해야 합니다.|    




## <a name="windowseventlog-element"></a>WindowsEventLog 요소
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - WindowsEventLog*

 Windows 이벤트 로그의 컬렉션을 활성화합니다.  

 선택적 **scheduledTransferPeriod** 특성입니다. 이전 설명을 참조하세요.  

|자식 요소|Description|  
|-------------------|-----------------|  
|**DataSource**|수집할 Windows 이벤트 로그입니다. 필수 특성:<br /><br /> **name** - 수집할 Windows 이벤트를 설명하는 XPath 쿼리입니다. 예를 들면 다음과 같습니다.<br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> 모든 이벤트를 수집하려면 “*”를 지정합니다. |
|**sinks** | 1.5에 추가되었습니다. (선택 사항) 또한 싱크를 지원하는 모든 자식 요소에 대한 진단 데이터를 보낼 싱크 위치도 가리킵니다. 싱크 예제는 Application Insights 또는 Event Hubs입니다.|  


## <a name="logs-element"></a>Logs 요소  
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Logs*

 버전 1.0 및 1.1에서 제공됩니다. 1.2에는 제공되지 않습니다. 1.3에서 다시 추가됩니다.  

 기본 Azure 로그의 버퍼 구성을 정의합니다.  

|attribute|Type|Description|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**unsignedInt**|(선택 사항) 지정된 데이터에 사용할 수 있는 파일 시스템 스토리지의 최대 크기를 지정합니다.<br /><br /> 기본값은 0입니다.|  
|**scheduledTransferLogLevelFilter**|**string**|(선택 사항) 전송되는 로그 항목에 대한 최소 심각도 수준을 지정합니다. 기본값은 **Undefined** 로, 모든 로그를 전송합니다. 정보가 적은 순서대로 사용 가능한 다른 값을 나열하면 다음과 같습니다. **자세한 정보**, **정보**, **경고**, **오류**, **중요**|  
|**scheduledTransferPeriod**|**duration**|(선택 사항) 예약된 데이터 전송 사이의 간격(가장 가까운 시간(분)으로 반올림)을 지정합니다.<br /><br /> 기본값은 PT0S입니다.|  
|**sinks** |**string**| 1.5에 추가되었습니다. (선택 사항) 또한 진단 데이터를 보내는 싱크 위치를 가리킵니다. Application Insights 또는 Event Hubs를 예로 들 수 있습니다. Event Hubs 이벤트를 업로드하여 리소스 ID를 갖도록 하려면 *Metrics* 요소 아래에 *resourceId* 속성을 추가해야 합니다.|  

## <a name="dockersources"></a>DockerSources
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - DockerSources*

 1.9에 추가되었습니다.

|요소 이름|설명|  
|------------------|-----------------|  
|**통계**|Docker 컨테이너에 대한 통계를 수집하도록 시스템에 지시|  

## <a name="sinksconfig-element"></a>SinksConfig 요소  
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig*

 진단 데이터를 보낼 위치와 그러한 위치와 관련된 구성의 목록 입니다.  

|요소 이름|설명|  
|------------------|-----------------|  
|**싱크**|이 페이지의 다른 곳에 있는 설명을 참조하세요.|  

## <a name="sink-element"></a>싱크 요소
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink*

 버전 1.5에 추가되었습니다.  

 진단 데이터를 보낼 위치를 정의합니다. 예를 들어 Application Insights 서비스입니다.  

|attribute|Type|Description|  
|---------------|----------|-----------------|  
|**name**|문자열|sinkname을 식별하는 문자열입니다.|  

|요소|유형|Description|  
|-------------|----------|-----------------|  
|**Application Insights**|string|데이터를 Application Insights로 전송하는 경우에만 사용됩니다. 액세스 권한이 있는 활성 Application Insights 계정에 대한 계측 키를 포함합니다.|  
|**채널**|string|스트림하는 각 추가 필터링에 대한|  

## <a name="channels-element"></a>Channels 요소  
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink - Channels*

 버전 1.5에 추가되었습니다.  

 싱크를 통해 전달되는 로그 데이터의 스트림에 대한 필터를 정의합니다.  

|요소|유형|Description|  
|-------------|----------|-----------------|  
|**채널**|string|이 페이지의 다른 곳에 있는 설명을 참조하세요.|  

## <a name="channel-element"></a>채널 요소
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink - Channels - Channel*

 버전 1.5에 추가되었습니다.  

 진단 데이터를 보낼 위치를 정의합니다. 예를 들어 Application Insights 서비스입니다.  

|특성|형식|Description|  
|----------------|----------|-----------------|  
|**logLevel**|**string**|전송되는 로그 항목에 대한 최소 심각도 수준을 지정합니다. 기본값은 **Undefined** 로, 모든 로그를 전송합니다. 정보가 적은 순서대로 사용 가능한 다른 값을 나열하면 다음과 같습니다. **자세한 정보**, **정보**, **경고**, **오류**, **중요**|  
|**name**|**string**|참조 하는 채널의 고유 이름|  


## <a name="privateconfig-element"></a>PrivateConfig 요소
 *Tree: Root - DiagnosticsConfiguration - PrivateConfig*

 버전 1.3에 추가되었습니다.  

 옵션  

 스토리지 계정(이름, 키 및 엔드포인트)의 프라이빗 정보를 저장합니다. 이 정보는 가상 컴퓨터에 전송되지만 여기에서 검색할 수 없습니다.  

|자식 요소|Description|  
|--------------------|-----------------|  
|**StorageAccount**|사용할 스토리지 계정입니다. 다음과 같은 특성이 필요<br /><br /> - **이름** - 스토리지 계정의 이름입니다.<br /><br /> - **키** - 스토리지 계정의 키입니다.<br /><br /> - **엔드포인트** - 스토리지 계정에 액세스하는 엔드포인트입니다. <br /><br /> -**sasToken** (추가 된 1.8.1)-개인 구성에서 저장소 계정 키 대신 SAS 토큰을 지정할 수 있습니다. 제공 된 경우 저장소 계정 키가 무시 됩니다. <br />SAS 토큰에 대한 요구 사항: <br />- 계정 SAS 토큰만 지원합니다. <br />- *b*, *t* 서비스 형식이 필요합니다. <br /> - *a*, *c*, *u*, *w* 권한이 필요합니다. <br /> - *c*, *o* 리소스 형식이 필요합니다. <br /> - HTTPS 프로토콜만 지원합니다. <br /> - 시작 및 만료 시간이 유효해야 합니다.|  


## <a name="isenabled-element"></a>IsEnabled 요소  
 *Tree: Root - DiagnosticsConfiguration - IsEnabled*

 Boolean입니다. `true`를 사용하여 진단을 사용하도록 설정하거나 `false`를 사용하여 진단을 사용하지 않도록 설정합니다.

## <a name="example-configuration"></a>구성 예
 다음은 JSON 및 XML 모두에 표시 되는 Windows 진단 확장에 대 한 전체 샘플 구성입니다.

 
### <a name="json"></a>JSON

*Publicconfig* 및 *privateconfig.json* 는 대부분의 JSON 사용 사례에서 서로 다른 변수로 전달 되기 때문에 구분 됩니다. 이러한 경우 리소스 관리자 템플릿, PowerShell 및 Visual Studio 포함 됩니다.

> [!NOTE]
> 공용 구성 Azure Monitor 싱크 정의에는 *resourceId* 와 *region* 이라는 두 가지 속성이 있습니다. 두 속성은 클래식 VM 및 클래식 클라우드 서비스에만 필요합니다. *Region* 속성은 다른 리소스에 사용 하면 안 됩니다. *RESOURCEID* 속성은 ARM vm에서 Event Hubs에 업로드 된 로그의 resourceid 필드를 채우는 데 사용 됩니다.

```json
"PublicConfig" {
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 10000,
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "AzureMonitorSink",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT1M",
                        "unit": "percent"
                    }
                ]
            },
            "Directories": {
                "scheduledTransferPeriod": "PT5M",
                "IISLogs": {
                    "containerName": "iislogs"
                },
                "FailedRequestLogs": {
                    "containerName": "iisfailed"
                },
                "DataSources": [
                    {
                        "containerName": "mynewprocess",
                        "Absolute": {
                            "path": "C:\\MyNewProcess",
                            "expandEnvironment": false
                        }
                    },
                    {
                        "containerName": "badapp",
                        "Absolute": {
                            "path": "%SYSTEMDRIVE%\\BadApp",
                            "expandEnvironment": true
                        }
                    },
                    {
                        "containerName": "goodapp",
                        "LocalResource": {
                            "relativePath": "..\\PeanutButter",
                            "name": "Skippy"
                        }
                    }
                ]
            },
            "EtwProviders": {
                "sinks": "",
                "EtwEventSourceProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT5M",
                        "provider": "MyProviderClass",
                        "Event": [
                            {
                                "id": 0
                            },
                            {
                                "id": 1,
                                "eventDestination": "errorTable"
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ],
                "EtwManifestProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT2M",
                        "scheduledTransferLogLevelFilter": "Information",
                        "provider": "5974b00b-84c2-44bc-9e58-3a2451b4e3ad",
                        "Event": [
                            {
                                "id": 0
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT5M",
                "DataSource": [
                    {
                        "name": "System!*[System[Provider[@Name='Microsoft Antimalware']]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Verbose",
                "sinks": "ApplicationInsights.AppLogs"
            },
            "CrashDumps": {
                "directoryQuotaPercentage": 30,
                "dumpType": "Mini",
                "containerName": "wad-crashdumps",
                "CrashDumpConfiguration": [
                    {
                        "processName": "mynewprocess.exe"
                    },
                    {
                        "processName": "badapp.exe"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "AzureMonitorSink",
                    "AzureMonitor":
                    {
                        "ResourceId": "{insert resourceId if a classic VM or cloud service, else property not needed}",
                        "Region": "{insert Azure region of resource if a classic VM or cloud service, else property not needed}"
                    }
                },
                {
                    "name": "ApplicationInsights",
                    "ApplicationInsights": "{Insert InstrumentationKey}",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "Errors"
                            },
                            {
                                "logLevel": "Verbose",
                                "name": "AppLogs"
                            }
                        ]
                    }
                },
                {
                    "name": "EventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryEventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryStorageAccount",
                    "StorageAccount": {
                        "name": "secondarydiagstorageaccount",
                        "endpoint": "https://core.windows.net"
                    }
                }
            ]
        }
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

> [!NOTE]
> 개인 구성 Azure Monitor 싱크 정의에는 *Principalid* 와 *Secret* 의 두 속성이 있습니다. 두 속성은 클래식 VM 및 클래식 클라우드 서비스에만 필요합니다. 이러한 속성은 다른 리소스에 사용 하면 안 됩니다.


```json
"PrivateConfig" {
    "storageAccountName": "diagstorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "EventHub": {
        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    },
    "AzureMonitorAccount": {
        "ServicePrincipalMeta": {
            "PrincipalId": "{Insert service principal client Id}",
            "Secret": "{Insert service principal client secret}"
        }
    },
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "key": "{base64 encoded key}",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    },
    "SecondaryEventHubs": {
        "EventHub": [
            {
                "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                "SharedAccessKeyName": "SendRule",
                "SharedAccessKey": "{base64 encoded key}"
            }
        ]
    }
}

```

### <a name="xml"></a>XML

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">   
  <PublicConfig>  
    <WadCfg>  
      <DiagnosticMonitorConfiguration overallQuotaInMB="10000">  

        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzureMonitorSink">  
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
        </PerformanceCounters>  

        <Directories scheduledTransferPeriod="PT5M">  
          <IISLogs containerName="iislogs" />  
          <FailedRequestLogs containerName="iisfailed" />  

          <DataSources>  
            <DirectoryConfiguration containerName="mynewprocess">  
              <Absolute path="C:\MyNewProcess" expandEnvironment="false" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="badapp">  
              <Absolute path="%SYSTEMDRIVE%\BadApp" expandEnvironment="true" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="goodapp">  
              <LocalResource name="Skippy" relativePath="..\PeanutButter"/>  
            </DirectoryConfiguration>  
          </DataSources>  

        </Directories>  

        <EtwProviders>  
          <EtwEventSourceProviderConfiguration   
                       provider="MyProviderClass"   
                       scheduledTransferPeriod="PT5M">  
            <Event id="0"/>  
            <Event id="1" eventDestination="errorTable"/>  
            <DefaultEvents />  
          </EtwEventSourceProviderConfiguration>  
          <EtwManifestProviderConfiguration provider="5974b00b-84c2-44bc-9e58-3a2451b4e3ad" scheduledTransferLogLevelFilter="Information" scheduledTransferPeriod="PT2M">  
            <Event id="0"/>  
            <DefaultEvents eventDestination="defaultTable"/>  
          </EtwManifestProviderConfiguration>  
        </EtwProviders>  

        <WindowsEventLog scheduledTransferPeriod="PT5M">  
          <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>  
          <DataSource name="System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]" />  
          <DataSource name="System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]" />  
        </WindowsEventLog>  

        <Logs  bufferQuotaInMB="1024"   
             scheduledTransferPeriod="PT1M"   
             scheduledTransferLogLevelFilter="Verbose"   
             sinks="ApplicationInsights.AppLogs"/>  <!-- sinks attribute added in 1.5 -->  

        <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
          <CrashDumpConfiguration processName="mynewprocess.exe" />  
          <CrashDumpConfiguration processName="badapp.exe"/>  
        </CrashDumps>  

        <DockerSources> <!-- Added in 1.9 -->
          <Stats enabled="true" sampleRate="PT1M" scheduledTransferPeriod="PT1M" />
        </DockerSources>

      </DiagnosticMonitorConfiguration>  

      <SinksConfig>   <!-- Added in 1.5 -->  
        <Sink name="AzureMonitorSink">
            <AzureMonitor> <!-- Added in 1.11 -->
                <resourceId>{insert resourceId}</ResourceId> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs-->
                <Region>{insert Azure region of resource}</Region> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs -->
            </AzureMonitor>
        </Sink>
        <Sink name="ApplicationInsights">   
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>   
          <Channels>   
            <Channel logLevel="Error" name="Errors"  />   
            <Channel logLevel="Verbose" name="AppLogs"  />   
          </Channels>   
        </Sink>   
        <Sink name="EventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryEventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryStorageAccount"> <!-- Added in 1.7 -->
          <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" />
        </Sink>
   </SinksConfig>

  </WadCfg>  

  <StorageAccount>diagstorageaccount</StorageAccount>
  <StorageType>TableAndBlob</StorageType> <!-- Added in 1.8 -->  
  </PublicConfig>  

  <PrivateConfig>  <!-- Added in 1.3 -->  
    <StorageAccount name="" key="" endpoint="" sasToken="{sas token}"  />  <!-- sasToken in Private config added in 1.8.1 -->  
    <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />

    <AzureMonitorAccount>
        <ServicePrincipalMeta> <!-- Added in 1.11; only needed for classic VMs and Classic cloud services -->
            <PrincipalId>{Insert service principal clientId}</PrincipalId>
            <Secret>{Insert service principal client secret}</Secret>
        </ServicePrincipalMeta>
    </AzureMonitorAccount>

    <SecondaryStorageAccounts>
       <StorageAccount name="secondarydiagstorageaccount" key="{base64 encoded key}" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>

    <SecondaryEventHubs>
       <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
    </SecondaryEventHubs>

  </PrivateConfig>  
  <IsEnabled>true</IsEnabled>  
</DiagnosticsConfiguration>  

```  
> [!NOTE]
> 공용 구성 Azure Monitor 싱크 정의에는 resourceId 및 region이라는 두 가지 속성이 있습니다. 두 속성은 클래식 VM 및 클래식 클라우드 서비스에만 필요합니다. Resource Manager 가상 머신 또는 가상 머신 확장 집합에는 이 속성을 사용하면 안 됩니다.
> 보안 주체 ID 및 암호를 전달하는, Azure Monitor 싱크에 대한 추가 프라이빗 구성 요소도 있습니다. 이 요소는 클래식 VM 및 클래식 클라우드 서비스에만 필요합니다. Resource Manager VM 및 VMSS의 경우 프라이빗 구성 요소에서 Azure Monitor 정의를 제외할 수 있습니다.
>
