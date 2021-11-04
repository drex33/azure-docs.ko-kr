---
title: Azure VM에서 웹앱 프로파일링 - Application Insights Profiler
description: Application Insights Profiler를 사용하여 Azure VM에서 웹앱 프로파일링
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 11/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 1317bc86b2f4283475b1cc819d24c1ac6475a486
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131045686"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Application Insights Profiler를 사용하여 Azure Virtual Machine 또는 가상 머신 확장 집합에서 실행되는 웹앱 프로파일링

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

또한 다음과 같은 서비스에서 Azure Application Insights Profiler를 배포할 수도 있습니다.
* [Azure App Service](./profiler.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](?toc=%2fazure%2fazure-monitor%2ftoc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>가상 머신 또는 가상 머신 확장 집합에서 Profiler 배포
이 문서에서는 Azure VM(Virtual Machine) 또는 Azure Virtual Machine Scale Set에서 실행되는 Application Insights Profiler를 가져오는 방법을 보여 줍니다. Profiler는 VM용 Azure Diagnostics 확장과 함께 설치됩니다. Profiler를 실행하도록 확장을 구성하고 애플리케이션에 Application Insights SDK를 빌드합니다.

1. Application Insights SDK를 [ASP.NET 애플리케이션](./asp-net.md)에 추가합니다.

   요청에 대한 프로필을 보려면 Application Insights에 요청 원격 분석을 전송해야 합니다.

1. VM에 Azure Diagnostics 확장을 설치합니다. 전체 Resource Manager 템플릿 예제를 보려면 다음을 참조하세요.  
   * [가상 머신](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
   * [가상 머신 크기 집합](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
     중요한 부분은 WadCfg에서 ApplicationInsightsProfilerSink입니다. Profiler가 iKey에 데이터를 보내도록 Azure Diagnostics를 구성하려면 이 섹션에 다른 싱크를 추가합니다.
    
     ```json
     "SinksConfig": {
       "Sink": [
         {
           "name": "ApplicationInsightsSink",
           "ApplicationInsights": "85f73556-b1ba-46de-9534-606e08c6120f"
         },
         {
           "name": "MyApplicationInsightsProfilerSink",
           "ApplicationInsightsProfiler": "85f73556-b1ba-46de-9534-606e08c6120f"
         }
       ]
     },
     ```

1. 수정된 환경 배포 정의를 배포합니다.  

   수정 사항을 적용하면 일반적으로 PowerShell cmdlet 또는 Visual Studio를 통한 전체 템플릿 배포 또는 클라우드 서비스 기반 게시가 진행됩니다.  

   Azure Diagnostics 확장만 수정하는 기존 가상 머신에 대한 대안으로 다음과 같은 PowerShell 명령을 사용할 수 있습니다. 앞에서 언급한 ProfilerSink를 Get-AzVMDiagnosticsExtension 명령에서 반환하는 구성에 추가합니다. 그런 다음, 업데이트된 구성을 Set-AzVMDiagnosticsExtension 명령에 전달합니다.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. 원하는 애플리케이션이 [IIS](https://www.microsoft.com/web/downloads/platform.aspx)를 통해 실행 중인 경우라면 `IIS Http Tracing` Windows 기능을 활성화합니다.

   1. 환경에 대한 원격 액세스를 설정한 후, [Windows 기능 추가](/iis/configuration/system.webserver/tracing/) 창을 사용합니다. 또는 관리자 권한으로 PowerShell에서 다음 명령을 실행합니다.  

      ```powershell
      Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
      ```
  
   1. 원격 액세스 설정에 문제가 있을 경우에는 [Azure CLI](/cli/azure/get-started-with-azure-cli)를 사용하여 다음 명령을 실행할 수 있습니다.  

      ```azurecli
      az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
      ```

1. 애플리케이션을 배포합니다.

## <a name="set-profiler-sink-using-azure-resource-explorer"></a>Azure Resource Explorer를 사용하여 Profiler 싱크 설정

아직 포털에서 Application Insights Profiler 싱크를 설정하는 방법은 없습니다. 위에서 설명한 대로 PowerShell을 사용 하는 대신 Azure Resource Explorer를 사용 하 여 싱크를 설정할 수 있습니다. 하지만 VM을 다시 배포하는 경우 싱크가 손실됩니다. 이 설정을 유지하려면 VM을 배포할 때 사용하는 구성을 업데이트해야 합니다.

1. 가상 머신용으로 설치된 확장을 보고 Microsoft Azure Diagnostics 확장이 설치되었는지 확인합니다.  

    ![WAD 확장이 설치되었는지 확인][wadextension]

2. VM에 대한 VM Diagnostics 확장을 찾습니다. [https://resources.azure.com](https://resources.azure.com)으로 이동합니다. 리소스 그룹, Microsoft.Compute virtualMachines, 가상 머신 이름, 확장을 확장합니다.  

    ![Azure Resource Explorer에서 WAD 구성으로 이동합니다.][azureresourceexplorer]

3. Application Insights Profiler 싱크를 WadCfg 아래의 SinksConfig 노드에 추가합니다. 아직 SinksConfig 섹션이 없는 경우 하나 추가해야 할 수도 있습니다. 설정에 적절한 Application Insights iKey를 지정해야 합니다. 오른쪽 위 모서리에서 탐색기 모드를 읽기/쓰기로 전환하고 파란색 ‘편집’ 단추를 눌러야 합니다.

    ![Application Insights Profiler 싱크 추가][resourceexplorersinksconfig]

4. 구성 편집이 완료되면 ‘Put’(배치)을 누릅니다. 성공적으로 배치되면 화면 중간에 녹색 확인 표시가 나타납니다.

    ![배치 요청을 보내 변경 내용 적용][resourceexplorerput]






## <a name="can-profiler-run-on-on-premises-servers"></a>온-프레미스 서버에서 Profiler를 실행할 수 있나요?
온-프레미스 서버에 대한 Application Insights Profiler를 지원하는 계획이 없습니다.

## <a name="next-steps"></a>다음 단계

- 애플리케이션에 대한 트래픽을 생성합니다(예: [가용성 테스트](monitor-web-app-availability.md) 시작). 그런 다음, 추적을 10~15분 동안 기다려서 Application Insights 인스턴스로 보내기 시작합니다.
- Azure Portal에서 [Profiler 추적](profiler-overview.md?toc=/azure/azure-monitor/toc.json)을 참조하세요.
- Profiler 문제 해결 지원을 받으려면 [Profiler 문제 해결](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)을 참조하세요.

[azureresourceexplorer]: ./media/profiler-vm/azure-resource-explorer.png
[resourceexplorerput]: ./media/profiler-vm/resource-explorer-put.png
[resourceexplorersinksconfig]: ./media/profiler-vm/resource-explorer-sinks-config.png
[wadextension]: ./media/profiler-vm/wad-extension.png

