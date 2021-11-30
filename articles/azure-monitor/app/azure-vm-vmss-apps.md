---
title: Azure VM에서 성능 모니터링 - Azure Application Insights
description: Azure VM 및 Azure Virtual Machine Scale Sets에 대한 애플리케이션 성능 모니터링. 차트 부하 및 응답 시간, 종속성 정보 및 성능에 대한 경고를 설정합니다.
ms.topic: conceptual
ms.date: 08/26/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b16d2e5ef686dea419c3155af99bcdc63250cc7
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133266581"
---
# <a name="deploy-the-azure-monitor-application-insights-agent-on-azure-virtual-machines-and-azure-virtual-machine-scale-sets"></a>Azure 가상 머신 및 Azure 가상 머신 확장 집합에 Azure Monitor Application Insights 에이전트 배포

이제 [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) 및 [Azure Virtual Machine Scale Sets](../../virtual-machine-scale-sets/index.yml)에서 실행되는 .NET 또는 Java 기반 웹 애플리케이션에 대한 모니터링을 그 어느 때보다 쉽게 사용하도록 설정할 수 있습니다. 코드를 수정하지 않고 Application Insights를 사용하여 모든 이점을 얻을 수 있습니다.

이 문서에서는 Application Insights 에이전트를 사용하여 Application Insights 모니터링을 사용하도록 설정하는 과정을 안내하고 대규모 배포 프로세스를 자동화하기 위한 예비 지침을 제공합니다.
> [!IMPORTANT]
> Azure VM 및 VMSS에서 실행되는 **Java** 기반 애플리케이션은 일반적으로 사용 가능한 **[Application Insights Java 3.0 에이전트](./java-in-process-agent.md)** 로 모니터링됩니다.

> [!IMPORTANT]
> **Azure VM 및 VMSS** 에서 실행되는 ASP.NET 및 ASP.NET Core 애플리케이션용 Azure Application Insights 에이전트는 현재 공개 미리 보기로 제공됩니다. **온-프레미스** 에서 실행되는 ASP.NET 애플리케이션을 모니터링하려면 일반 공급되고 완전히 지원되는 [온-프레미스 서버용 Azure Application Insights 에이전트](./status-monitor-v2-overview.md)를 사용하세요.
> Azure VM 및 VMSS에 대한 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다. 일부 기능은 지원되지 않을 수 있으며 일부 기능은 제한된 기능을 가질 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="enable-application-insights"></a>Application Insights 사용

Azure Virtual Machines 및 Azure Virtual Machine Scale Sets 호스팅된 애플리케이션에 대해 애플리케이션 모니터링을 사용하도록 설정하는 방법에는 두 가지가 있습니다.

### <a name="auto-instrumentation-via-application-insights-agent"></a>Application Insights 에이전트를 통한 자동 계측

* 이 방법은 사용하기 가장 쉽고 고급 구성이 필요하지 않습니다. 이 방법을 “런타임“ 모니터링이라고도 합니다.

* Azure Virtual Machines 및 Azure Virtual Machine Scale Sets의 경우 이 모니터링 수준을 최소한으로 설정하는 것이 좋습니다. 그런 다음 특정 시나리오에 따라 수동 계측이 필요한지 여부를 평가할 수 있습니다.

> [!NOTE]
> 자동 계측은 현재 ASP.NET, ASP.NET Core IIS에서 호스트되는 애플리케이션 및 Java에 대해서만 사용할 수 있습니다. SDK를 사용하여 Azure 가상 머신 및 가상 머신 확장 집합에서 호스트되는 Node.js 및 Python 애플리케이션을 계측할 수 있습니다.


#### <a name="aspnet--aspnet-core"></a>ASP.NET/ASP.NET Core

  * Application Insights 에이전트는 .NET SDK와 동일한 종속성 신호를 기본적으로 자동 수집합니다. 자세한 내용은 [종속성 자동 수집](./auto-collect-dependencies.md#net)을 참조하세요.
        
#### <a name="java"></a>Java
  * Java의 경우 **[Application Insights Java 3.0 에이전트](./java-in-process-agent.md)** 가 권장되는 접근 방식입니다. 가장 많이 사용되는 라이브러리 및 프레임워크, 로그 및 종속성은 다양한 [추가 구성](./java-standalone-config.md)과 함께 [자동 수집](./java-in-process-agent.md#autocollected-requests)됩니다.

### <a name="code-based-via-sdk"></a>SDK를 통한 코드 기반
    
#### <a name="aspnet--aspnet-core"></a>ASP.NET/ASP.NET Core
  * .NET 앱의 경우 이 접근 방식을 훨씬 더 다양하게 사용자 지정할 수 있지만 [Application Insights SDK NuGet 패키지에 대한 종속성을 추가](./asp-net.md)해야 합니다. 또한 이 방법은 최신 버전의 패키지에 대한 업데이트를 직접 관리해야 함을 의미합니다.

  * 에이전트 기반 모니터링을 사용하여 기본적으로 캡처되지 않는 이벤트/종속성을 추적하기 위해 사용자 지정 API 호출을 수행해야 하는 경우 이 방법을 사용해야 합니다. 자세한 내용은 [사용자 지정 이벤트 및 메트릭용 API 문서](./api-custom-events-metrics.md)를 확인하세요.

    > [!NOTE]
    > .NET 앱에만 해당 - 에이전트 기반 모니터링 및 수동 SDK 기반 계측이 모두 검색되는 경우 수동 계측 설정만 인식됩니다. 이를 통해 중복 데이터가 전송되는 것을 방지합니다. 이 기능에 관한 자세한 내용은 아래 [문제 해결 섹션](#troubleshooting)을 확인하세요.

#### <a name="java"></a>Java 

Java 애플리케이션을 위한 추가적인 사용자 지정 원격 분석이 필요한 경우 [사용 가능한](./java-in-process-agent.md#custom-telemetry) 항목을 확인하거나 [사용자 지정 차원](./java-standalone-config.md#custom-dimensions)을 추가하거나 [원격 분석 프로세서](./java-standalone-telemetry-processors.md)를 사용합니다. 

#### <a name="nodejs"></a>Node.js

Node.js 애플리케이션을 계측하려면 [SDK](./nodejs.md)를 사용합니다.

#### <a name="python"></a>Python

Python 앱을 모니터링하려면 [SDK](./opencensus-python.md)를 사용합니다.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machines-using-powershell"></a>PowerShell을 사용하여 Azure Virtual Machines에서 .NET 애플리케이션용 Application Insights 에이전트 관리

> [!NOTE]
> Application Insights 에이전트를 설치하기 전에 연결 문자열이 필요합니다. [새 Application Insights 리소스를 만들거나](./create-new-resource.md) 기존 애플리케이션 인사이트 리소스에서 연결 문자열을 복사합니다.

> [!NOTE]
> PowerShell을 처음 사용하시나요? [시작 가이드](/powershell/azure/get-started-azureps)를 확인하세요.

Azure Virtual Machines에 대한 확장으로 Application Insights 에이전트 설치 또는 업데이트
```powershell
$publicCfgJsonString = '
{
  "redfieldConfiguration": {
    "instrumentationKeyMap": {
      "filters": [
        {
          "appFilter": ".*",
          "machineFilter": ".*",
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          }
        }
      ]
    }
  }
}
';
$privateCfgJsonString = '{}';

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "<myVmLocation>" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

> [!NOTE]
> PowerShell 루프를 사용하여 여러 Virtual Machines의 확장으로 Application Insights 에이전트를 설치하거나 업데이트할 수 있습니다.

Azure Virtual Machine에서 Application Insights 에이전트 확장 제거
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Azure Virtual Machine에 대한 Application Insights 에이전트 확장 상태 쿼리
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

Azure Virtual Machine에 대해 설치된 확장 목록 가져오기
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```
포털의 [Azure Virtual Machine 블레이드](../../virtual-machines/extensions/overview.md)에서 설치된 확장을 볼 수도 있습니다.

> [!NOTE]
> Application Insights 에이전트 확장을 배포하는 데 사용한 연결 문자열과 연결된 Application Insights 리소스 내의 라이브 메트릭 스트림을 클릭하여 설치를 확인합니다. 여러 Virtual Machines에서 데이터를 보내는 경우 서버 이름 아래에서 대상 Azure Virtual Machines를 선택합니다. 데이터 흐름이 시작되는 데 최대 1분 정도 걸릴 수 있습니다.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machine-scale-sets-using-powershell"></a>PowerShell을 사용하여 Azure Virtual Machine Scale Sets에서 .NET 애플리케이션용 Application Insights 에이전트 관리

Azure Virtual Machine Scale Sets에 대한 확장으로 Application Insights 에이전트 설치 또는 업데이트
```powershell
$publicCfgHashtable =
@{
  "redfieldConfiguration"= @{
    "instrumentationKeyMap"= @{
      "filters"= @(
        @{
          "appFilter"= ".*";
          "machineFilter"= ".*";
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" # Application Insights connection string, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
          }
        }
      )
    }
  }
};
$privateCfgHashtable = @{};

$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoringWindows" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -TypeHandlerVersion "2.8" -Setting $publicCfgHashtable -ProtectedSetting $privateCfgHashtable

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Azure Virtual Machine Scale Sets에서 애플리케이션 모니터링 확장 제거
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Azure Virtual Machine Scale Sets에 대한 애플리케이션 모니터링 확장 상태 쿼리
```powershell
# Not supported by extensions framework
```

Azure Virtual Machine Scale Sets에 대해 설치된 확장 목록 가져오기
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>문제 해결

Azure Virtual Machines 및 Virtual Machine Scale Sets에서 실행되는 .NET 애플리케이션용 Application Insights 모니터링 에이전트 확장에 대한 문제 해결 팁을 확인하세요.

> [!NOTE]
> .NET Core, Node.js 및 Python 애플리케이션은 수동 SDK 기반 계측을 통해 Azure Virtual Machines 및 Azure Virtual Machine Scale Sets에서만 지원되므로 아래 단계는 이러한 시나리오에 적용되지 않습니다.

확장 실행 출력은 다음 디렉터리에 있는 파일에 기록됩니다.
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>다음 단계
* [Azure Virtual Machine Scale Sets에 애플리케이션을 배포](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md)하는 방법을 알아봅니다.
* 엔드포인트가 다운된 경우 알림을 받도록 [가용성 웹 테스트를 설정](monitor-web-app-availability.md)합니다.
