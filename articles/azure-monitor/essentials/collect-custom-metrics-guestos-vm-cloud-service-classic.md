---
title: Azure Monitor 메트릭 데이터베이스에 클래식 Cloud Services 메트릭 보내기
description: Azure 클래식 Cloud Services에 대한 게스트 OS 성능 메트릭을 Azure Monitor 메트릭 저장소에 보내는 프로세스에 대해 설명합니다.
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7be1aa846ee53edf0c23594d89af59e3c7f7aa32
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131051518"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-services"></a>Azure Monitor 메트릭 저장소의 클래식 Cloud Services에 게스트 OS 메트릭 보내기 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor [진단 확장](../agents/diagnostics-extension-overview.md)을 사용하여 가상 머신, 클라우드 서비스 또는 Service Fabric 클러스터의 일부로 실행되는 게스트 OS(게스트 운영 체제)에서 메트릭과 로그를 수집할 수 있습니다. 이 확장은 [여러 다른 위치](../data-platform.md?toc=%2fazure%2fazure-monitor%2ftoc.json)에 원격 분석을 보낼 수 있습니다.

이 문서에서는 Azure 클래식 Cloud Services에 대한 게스트 OS 성능 메트릭을 Azure Monitor 메트릭 저장소에 보내는 프로세스에 대해 설명합니다. 진단 버전 1.11부터 표준 플랫폼 메트릭이 이미 수집된 Azure Monitor 메트릭 저장소에 메트릭을 직접 기록할 수 있습니다. 

이 위치에 메트릭을 저장하면 플랫폼 메트릭의 경우와 동일한 작업에 액세스할 수 있습니다. 작업에는 실시간에 가까운 경고, 차트 작성, 라우팅, REST API에서 액세스 등이 포함됩니다.  과거에는 진단 확장을 Azure Monitor 데이터 저장소가 아니라 Azure Storage에 기록했습니다.  

이 문서에서 설명하는 프로세스는 Azure Cloud Services의 성능 카운터에서만 작동합니다. 다른 사용자 지정 메트릭에서는 작동하지 않습니다. 

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독의 [서비스 관리자 또는 공동 관리자](../../cost-management-billing/manage/add-change-subscription-administrator.md)여야 합니다. 

- 구독이 [Microsoft.Insights](../../azure-resource-manager/management/resource-providers-and-types.md)에 등록되어야 합니다. 

- [Azure PowerShell](/powershell/azure) 또는 [Azure Cloud Shell](../../cloud-shell/overview.md)이 설치되어 있어야 합니다.

- 클라우드 서비스는 [사용자 지정 메트릭을 지원하는 지역](./metrics-custom-overview.md#supported-regions)에 있어야 합니다.

## <a name="provision-a-cloud-service-and-storage-account"></a>클라우드 서비스 및 스토리지 계정 프로비전 

1. 클래식 클라우드 서비스를 만들고 배포합니다. 샘플 클래식 Cloud Services 애플리케이션 및 배포는 [Azure Cloud Services 및 ASP.NET 시작](../../cloud-services/cloud-services-dotnet-get-started.md)에서 확인할 수 있습니다. 

2. 기존 스토리지 계정을 사용하거나 새 스토리지 계정을 배포할 수 있습니다. 직접 만든 클래식 클라우드 서비스와 동일한 지역에 스토리지 계정이 있는 것이 가장 좋습니다. Azure Portal에서 **스토리지 계정** 리소스 블레이드로 이동한 다음, **키** 를 선택합니다. 스토리지 계정 이름과 스토리지 계정 키를 적어 둡니다. 이 정보는 이후 단계에서 필요합니다.

   ![Storage 계정 키](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/storage-keys.png)

## <a name="create-a-service-principal"></a>서비스 주체 만들기 

[포털을 사용하여 리소스에 액세스할 수 있는 Azure Active Directory 애플리케이션 및 서비스 주체 만들기](../../active-directory/develop/howto-create-service-principal-portal.md) 지침을 사용하여 Azure Active Directory 테넌트에 서비스 주체를 만듭니다. 이 프로세스를 진행하는 동안 다음 사항에 유의하세요. 

- 로그인 URL에 대해 임의 URL을 입력할 수 있습니다.  
- 이 앱에 대한 새 클라이언트 암호를 만듭니다.  
- 이후 단계에서 사용하기 위해 키와 클라이언트 ID를 저장합니다.  

메트릭을 내보내려는 리소스에 대한 *모니터링 메트릭 게시자* 권한을 이전 단계에서 만든 앱에 부여합니다. 앱을 사용하여 여러 리소스에 대한 사용자 지정 메트릭을 내보내려는 경우 리소스 그룹 또는 구독 수준에서 이러한 권한을 부여할 수 있습니다.  

> [!NOTE]
> 진단 확장에서는 서비스 주체를 사용하여 Azure Monitor에 대해 인증하고 클라우드 서비스에 대한 메트릭을 내보냅니다.

## <a name="author-diagnostics-extension-configuration"></a>진단 확장 구성 작성 

진단 확장 구성 파일을 준비합니다. 이 파일은 진단 확장에서 클라우드 서비스에 대해 수집해야 하는 로그와 성능 카운터를 지정합니다. 다음은 샘플 진단 구성 파일입니다.  

```xml
<?xml version="1.0" encoding="utf-8"?> 
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <WadCfg> 
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"> 
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" /> 
        <Directories scheduledTransferPeriod="PT1M"> 
          <IISLogs containerName="wad-iis-logfiles" /> 
          <FailedRequestLogs containerName="wad-failedrequestlogs" /> 
        </Directories> 
        <PerformanceCounters scheduledTransferPeriod="PT1M"> 
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Page Faults/sec" sampleRate="PT15S" /> 
        </PerformanceCounters> 
        <WindowsEventLog scheduledTransferPeriod="PT1M"> 
          <DataSource name="Application!*[System[(Level=1 or Level=2 or Level=3)]]" /> 
          <DataSource name="Windows Azure!*[System[(Level=1 or Level=2 or Level=3 or Level=4)]]" /> 
        </WindowsEventLog> 
        <CrashDumps> 
          <CrashDumpConfiguration processName="WaIISHost.exe" /> 
          <CrashDumpConfiguration processName="WaWorkerHost.exe" /> 
          <CrashDumpConfiguration processName="w3wp.exe" /> 
        </CrashDumps> 
        <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Error" /> 
      </DiagnosticMonitorConfiguration> 
      <SinksConfig> 
      </SinksConfig> 
    </WadCfg> 
    <StorageAccount /> 
  </PublicConfig> 
  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <StorageAccount name="" endpoint="" /> 
</PrivateConfig> 
  <IsEnabled>true</IsEnabled> 
</DiagnosticsConfiguration> 
```

진단 파일의 “SinksConfig” 섹션에서 새 Azure Monitor 싱크를 정의합니다. 

```xml
  <SinksConfig> 
    <Sink name="AzMonSink"> 
    <AzureMonitor> 
      <ResourceId>-Provide ClassicCloudService’s Resource ID-</ResourceId> 
      <Region>-Azure Region your Cloud Service is deployed in-</Region> 
    </AzureMonitor> 
    </Sink> 
  </SinksConfig> 
```

수집할 성능 카운터 목록이 있는 구성 파일 섹션에 Azure Monitor 싱크를 추가합니다. 이 항목은 지정한 모든 성능 카운터가 Azure Monitor에 메트릭으로 라우팅되도록 합니다. 필요에 따라 성능 카운터를 추가하거나 제거할 수 있습니다. 

```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
```

마지막으로, 프라이빗 구성에서 *Azure Monitor 계정* 섹션을 추가합니다. 앞에서 만든 서비스 주체 클라이언트 ID와 비밀을 입력합니다. 

```xml
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <StorageAccount name="" endpoint="" /> 
    <AzureMonitorAccount> 
      <ServicePrincipalMeta> 
        <PrincipalId>clientId from step 3</PrincipalId> 
        <Secret>client secret from step 3</Secret> 
      </ServicePrincipalMeta> 
    </AzureMonitorAccount> 
</PrivateConfig> 
```

이 진단 파일을 로컬로 저장합니다.  

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>클라우드 서비스에 진단 확장 배포 

PowerShell을 시작하고 Azure에 로그인합니다. 

```powershell
Login-AzAccount 
```

다음 명령을 사용하여 앞에서 만든 스토리지 계정의 세부 정보를 저장합니다. 

```powershell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```

마찬가지로, 다음 명령을 사용하여 진단 파일 경로를 변수에 설정합니다.

```powershell
$diagconfig = “<path of the Diagnostics configuration file with the Azure Monitor sink configured>” 
```

다음 명령을 사용하여 Azure Monitor 싱크가 구성되어 있는 진단 파일을 통해 진단 확장을 클라우드 서비스에 배포합니다.  

```powershell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```

> [!NOTE] 
> 여전히 진단 확장 설치 중에 스토리지 계정을 제공해야 합니다. 진단 구성 파일에 지정된 모든 로그 또는 성능 카운터는 지정한 스토리지 계정에 기록됩니다.  

## <a name="plot-metrics-in-the-azure-portal"></a>Azure Portal에서 메트릭 구성 

1. Azure Portal로 이동합니다. 

   ![모니터가 있는 Azure Portal과 선택된 메트릭을 차례로 보여 주는 스크린샷.](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/navigate-metrics.png)

2. 왼쪽 메뉴에서 **모니터** 를 선택합니다.

3. **모니터** 블레이드에서 **메트릭 미리 보기** 탭을 선택합니다.

4. 리소스 드롭다운 메뉴에서 클래식 클라우드 서비스를 선택합니다.

5. 네임스페이스 드롭다운 메뉴에서 **azure.vm.windows.guest** 를 선택합니다. 

6. 메트릭 드롭다운 메뉴에서 **Memory\Committed Bytes in Use** 를 선택합니다. 

차원 필터링 및 분할 기능을 사용하여 특정 역할 또는 역할 인스턴스에서 사용하는 총 메모리를 볼 수 있습니다. 

 ![메트릭 데이터를 보여 주는 스크린샷.](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/metrics-graph.png)

## <a name="next-steps"></a>다음 단계

- [사용자 지정 메트릭](./metrics-custom-overview.md)에 대해 자세히 알아보세요.
