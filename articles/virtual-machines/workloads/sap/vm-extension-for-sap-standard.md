---
title: SAP 솔루션용 Azure VM 확장의 표준 버전 | Microsoft Docs
description: SAP용 Std VM 확장을 배포하는 방법을 알아봅니다.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: OliverDoll
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/22/2021
ms.author: oldoll
ms.openlocfilehash: 8e723e39364207e1075d66eeafcdf12b74e16624
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130261389"
---
# <a name="standard-version-of-azure-vm-extension-for-sap-solutions"></a>SAP 솔루션용 Azure VM 확장의 표준 버전
[new-extension]:vm-extension-for-sap-new.md (SAP 솔루션용 Azure VM 확장의 새 버전)
[configure-windows]:vm-extension-for-sap-standard.md#cb095b34-51c9-41f3-aeed-30a16072a1dc (PowerShell을 통해 SAP 솔루션용 Azure VM 확장 구성)
[configure-linux]:vm-extension-for-sap-standard.md#c691e304-3524-4bfd-8612-992d5715a689 (Azure CLI SAP 솔루션용 Azure VM 확장 구성)
[configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d 
[troubleshoot-windows]:vm-extension-for-sap-standard.md#f566b77e-5427-451b-b4ca-6b93114d51e8 (Windows 문제 해결)
[troubleshoot-linux]:vm-extension-for-sap-standard.md#a4dae567-e7fd-4d4b-8279-510b8e5fae4a (Linux 문제 해결)
[healthcheck]:vm-extension-for-sap-standard.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (SAP용 Azure 확장 구성 관련 상태 검사)
[deployment-guide-4.1]:vm-extension-for-sap-standard.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Azure PowerShell cmdlet 배포)
[deploy-cli]:vm-extension-for-sap-standard.md#1ded9453-1330-442a-86ea-e0fd8ae8cab3 (Azure CLI 배포)
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[azure-cli-2]:/cli/azure/install-azure-cli
[msdn-set-Azvmaemextension]:/powershell/module/az.compute/set-azvmaemextension
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[readiness-check]:vm-extension-for-sap-standard.md#6b2561a0-b476-4579-847d-6c68117eb26e (준비 검사)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (SAP 리소스)
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[deployment-guide-contact-support]:vm-extension-for-sap-standard.md#3ba34cfc-c9bb-4648-9c3c-88e8b9130ca2 (SAP용 Azure 확장 문제 해결 - 지원에 문의)
[deployment-guide-run-the-script]:vm-extension-for-sap-standard.md#0d2847ad-865d-4a4c-a405-f9b7baaa00c7 (SAP용 Azure 확장 문제 해결 - 설정 스크립트 실행)
[deployment-guide-redeploy-after-sysprep]:vm-extension-for-sap-standard.md#2cd61f22-187d-42ed-bb8c-def0c983d756 (SAP용 Azure 확장 문제 해결 - sysprep 후 재배포)
[deployment-guide-fix-internet-connection]:vm-extension-for-sap-standard.md#e92bc57d-80d9-4a2b-a2f4-16713a22ad89 (SAP용 Azure 확장 문제 해결 - 인터넷 연결 수정)



## <a name="prerequisites"></a>사전 요구 사항

> [!NOTE]
> 일반 지원 문: SAP용 Azure 확장에 대한 지원은 SAP 지원 채널을 통해 제공됩니다.
> SAP 솔루션용 Azure VM 확장에 대한 지원이 필요한 경우 SAP 지원을 통해 지원 사례를 여세요.
  
> [!NOTE]
> SAP용 Azure 확장의 표준 버전과 새 버전 간에 전환하기 전에 VM 확장을 제거해야 합니다.

> [!NOTE]
> 두 가지 버전의 VM 확장이 있습니다. 이 문서에서는 SAP용 Azure VM 확장의 **표준** 버전을 다룹니다. 새 버전을 설치하는 방법에 대한 지침은 [SAP 솔루션용 Azure VM 확장의 새 버전을 참조하세요.][new-extension]


### <a name="deploy-azure-powershell-cmdlets"></a><a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Azure PowerShell cmdlet 배포

[Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps) 문서에 설명된 단계를 따릅니다.

일반적으로 매월 업데이트되는 PowerShell cmdlet에 대한 업데이트를 자주 확인하십시오. [이](/powershell/azure/install-az-ps#update-the-azure-powershell-module) 문서에 설명된 단계를 따릅니다. SAP Note [1928533] 또는 SAP Note [2015553]에 달리 명시되지 않은 한 Azure PowerShell cmdlet의 최신 버전을 사용하는 것이 좋습니다.

컴퓨터에 설치된 Azure PowerShell cmdlet의 버전을 확인하려면 다음 PowerShell 명령을 실행합니다.

```powershell
(Get-Module Az.Compute).Version
```

### <a name="deploy-azure-cli"></a><a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Azure CLI 배포

[Azure CLI 설치](/cli/azure/install-azure-cli) 문서에 설명된 단계를 따릅니다.

일반적으로 매월 업데이트 되는 Azure CLI에 대한 업데이트를 자주 확인하십시오.

컴퓨터에 설치된 Azure CLI의 버전을 확인하려면 다음 명령을 실행합니다.

```console
az --version
```


## <a name="configure-the-azure-vm-extension-for-sap-solutions-with-powershell"></a><a name="cb095b34-51c9-41f3-aeed-30a16072a1dc"></a>PowerShell을 통해 SAP 솔루션용 Azure VM 확장 구성 
 
다음은 PowerShell을 사용하여 SAP용 Azure 확장을 설치하는 방법입니다.

1. 최신 버전의 Azure PowerShell cmdlet을 설치했는지 확인합니다. 자세한 내용은 [Azure PowerShell cmdlet 배포를 참조하세요.][deployment-guide-4.1]
1. 다음 PowerShell cmdlet을 실행합니다. 사용 가능한 환경 목록을 보려면 cmdlet `Get-AzEnvironment`를 실행합니다. 전역 Azure를 사용하려는 경우 환경은 **AzureCloud** 입니다. Azure 중국 21Vianet의 경우 **AzureChinaCloud** 를 선택합니다. 
   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   
   Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name> 
   ``` 
계정 데이터를 입력하면 스크립트가 필수 확장을 배포하고 필요한 기능을 사용하도록 설정합니다. 이 작업은 몇 분 정도 걸릴 수 있습니다.
`Set-AzVMAEMExtension`에 대한 자세한 내용은 [Set-AzVMAEMExtension][msdn-set-Azvmaemextension]을 참조하세요.

![SAP 관련 Azure cmdlet Set-AzVMAEMExtension의 성공적인 실행][deployment-guide-figure-900]

`Set-AzVMAEMExtension`구성은 SAP용 호스트 데이터 수집을 구성하는 모든 단계를 수행합니다.

스크립트 출력에는 다음 정보가 포함됩니다.

* OS 디스크 및 모든 추가 데이터 디스크에 대한 확인이 구성됩니다.
* 다음 두 개의 메시지는 특정 Storage 계정에 대한 Storage 메트릭의 구성을 확인합니다.
* 출력 한 줄은 SAP용 VM 확장 구성의 실제 업데이트 상태를 나타냅니다.
* 출력의 또 다른 줄은 구성이 배포되거나 업데이트되었음을 확인해 줍니다.
* 출력의 마지막 줄은 정보 제공용이며, SAP용 VM 확장 구성 테스트 옵션을 보여 줍니다.
* SAP용 Azure VM 확장 구성의 모든 단계가 성공적으로 실행되었는지, Azure 인프라에서 필요한 데이터를 제공하는지 확인하려면 [준비 검사에][readiness-check]설명된 대로 SAP용 Azure 확장에 대한 준비 검사를 진행합니다.
* Azure Diagnostics가 관련 데이터를 수집하도록 15-30분 동안 기다립니다.

## <a name="configure-the-azure-vm-extension-for-sap-solutions-with-azure-cli"></a><a name="c691e304-3524-4bfd-8612-992d5715a689"></a>Azure CLI SAP 솔루션용 Azure VM 확장 구성
 
Azure CLI 사용하여 SAP용 Azure VM 확장을 설치하려면 다음을 수행합니다.

1. 최신 버전의 Azure CLI 설치했는지 확인합니다. 자세한 내용은 [Azure CLI 배포를 참조하세요.][deploy-cli] 

1. Azure 계정으로 로그인합니다.
   ```azurecli
   az login
   ```

1. Azure CLI AEM 확장을 설치합니다. 버전 0.2.2 이상을 사용해야 합니다.
   ```azurecli
   az extension add --name aem
   ```

1. 확장 사용: 
   ```azurecli
   az vm aem set -g <resource-group-name> -n <vm name> 
   ```

1. Azure Linux VM에서 SAP용 Azure 확장이 활성화되어 있는지 확인합니다. /var/lib/AzureEnhancedMonitor/PerfCounters 파일이 있는지 확인합니다. 파일이 존재하면 명령 프롬프트에서 이 명령을 실행하여 SAP용 Azure 확장이 수집한 정보를 표시합니다.

   ```console
   cat /var/lib/AzureEnhancedMonitor/PerfCounters
   ```

   출력은 다음과 같이 표시됩니다.

   ```output
   ...
   2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
   2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
   ...
   ```

## <a name="update-the-configuration-of-azure-extension-for-sap"></a>SAP용 Azure 확장 구성 업데이트

다음 시나리오에서 SAP용 Azure 확장의 구성을 업데이트합니다.
* Microsoft/SAP 공동 팀은 VM 확장 기능을 추가했으며 더 많거나 적은 카운터를 요청합니다.
* Microsoft는 데이터를 제공하는 기본 Azure 인프라 신규 버전을 도입했으며, SAP용 Azure 확장은 이러한 변화에 적응할 것입니다.
* Azure VM에서 데이터 디스크를 추가로 탑재하거나 제거합니다. 이 시나리오에서 스토리지 관련 데이터의 컬렉션을 업데이트합니다. 엔드포인트를 추가 또는 삭제하거나 VM에 IP 주소를 할당하여 구성을 변경해도 확장 구성에 영향을 주지 않습니다.
* Azure VM의 크기를 변경합니다(예: 크기 A5에서 다른 VM 크기로).
* Azure VM에 새 네트워크 인터페이스를 추가합니다.

설정을 업데이트하려면 Azure CLI 사용하여 SAP 솔루션용 Azure VM 확장 구성 또는 PowerShell을 사용하여 [SAP 솔루션용 Azure VM 확장][configure-linux] 구성의 단계에 따라 [SAP용 Azure 확장의 구성을 업데이트합니다.][configure-windows]

## <a name="checks-and-troubleshooting"></a><a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>검사 및 문제 해결

Azure VM을 배포하고 SAP용 Azure 확장 관련을 설정한 후, Azure 확장의 모든 구성 요소가 예상한 대로 작동하는지 확인합니다.

준비 검사에 설명된 대로 SAP용 Azure 확장에 대한 [준비 검사를 실행합니다.][readiness-check] 모든 준비 검사 결과가 긍정적이고 모든 관련 성능 카운터가 정상으로 나타나면, SAP용 Azure 확장이 성공적으로 설정된 것입니다. [SAP 리소스][deployment-guide-2.2]의 SAP Notes의 설명대로 SAP 호스트 에이전트 설치를 진행할 수 있습니다. 준비 검사에서 카운터가 누락된 것으로 표시되면 SAP용 Azure 확장 구성에 대한 상태 검사에 설명된 대로 [SAP용 Azure 확장에 대한 상태 검사를][healthcheck]실행합니다. 자세한 문제 해결 옵션은 [Windows 문제 해결][troubleshoot-windows] 또는 [Linux 문제 해결을 참조하세요.][troubleshoot-linux]

### <a name="readiness-check"></a><a name="6b2561a0-b476-4579-847d-6c68117eb26e"></a>준비 상태 검사

이 검사에서는 SAP 애플리케이션 내부에 나타나는 모든 성능 메트릭이 기본 SAP용 Azure 확장에서 제공되는지 확인합니다.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Windows VM에서 준비 검사 실행
 
1. Azure Virtual Machine에 로그인합니다(관리자 계정 사용은 필요하지 않음).
1. 명령 프롬프트 창을 엽니다.
1. 명령 프롬프트에서 SAP용 Azure 확장의 설치 폴더로 디렉터리를 변경하세요. C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;버전>\\drop)로 변경합니다.

   확장에 대한 경로의 *버전* 이 다를 수 있습니다. 설치 폴더에 여러 확장 버전의 폴더가 표시되는 경우 AzureEnhancedMonitoring Windows 서비스의 구성을 확인한 다음, *실행 파일 경로* 로 나타난 폴더로 전환하세요.

   ![SAP용 Azure 확장을 실행하는 서비스의 속성][deployment-guide-figure-1000]

1. 명령 프롬프트에서 매개 변수 없이 **azperflib.exe** 를 실행합니다.

   > [!NOTE]
   > Azperflib.exe는 루프에서 실행되고 60초마다 수집한 카운터를 업데이트합니다. 루프를 종료하려면 명령 프롬프트 창을 닫습니다.
   >
   >

SAP용 Azure 확장이 설치되어 있지 않거나 AzureEnhancedMonitoring 서비스가 실행되고 있지 않으면, 확장이 올바르게 구성되지 않은 것입니다. 확장 문제를 해결 하는 방법에 대 한 자세한 내용은 [Linux의][troubleshoot-linux]Windows 또는 문제 해결 [에 대 한 문제 해결][troubleshoot-windows] 을 참조 하세요.

> [!NOTE]
> Azperflib.exe는 자체 용도로 사용할 수 없는 구성 요소입니다. 이 구성 요소는 VM 관련 Azure 인프라 데이터를 SAP 호스트 에이전트의 VM에 전달합니다.
> 
##### <a name="check-the-output-of-azperflibexe"></a>azperflib.exe의 출력 확인
 
Azperflib.exe 출력은 SAP용 Azure 성능 카운터가 모두 채워진 상태로 표시됩니다. 수집된 카운터 목록의 맨 아래에 있는 요약 및 상태 표시기가 SAP용 Azure 확장의 상태를 보여 줍니다.

![문제가 없음을 나타내는 azperflib.exe를 실행하여 표시된 상태 검사 출력][deployment-guide-figure-1100]
<a name="figure-11"></a>

빈 상태로 보고되는 **카운터 합계** 출력 및 이전 그림에 표시된 **상태 정보** 에 대해 반환되는 결과를 확인합니다.

결과 값을 다음과 같이 해석합니다.

| Azperflib.exe 결과 값 | SAP용 Azure 확장 상태 |
| --- | --- |
| **API 호출 - 사용할 수 없음** | 사용할 수 없는 카운터는 가상 컴퓨터 구성에 적용할 수 없거나 오류입니다. **상태 정보** 를 참조하세요. |
| **카운터 합계 - 비어 있음** |다음 두 Azure Storage 카운터는 비어 있을 수 있습니다. <ul><li>스토리지 읽기 작업 대기 시간 서버 밀리초</li><li>스토리지 읽기 작업 대기 시간 E2E 밀리초</li></ul>그 외의 카운터는 값이 있어야 합니다. |
| **상태 정보** |반환 상태가 **OK** 를 표시하는 경우에만 OK입니다. |
| **진단** |상태 정보에 대한 자세한 정보입니다. |

**상태** 값이 **정상** 이 아닌 경우 [SAP 용 Azure 확장 구성에 대 한 상태 검사][healthcheck]의 지침을 따르세요.
 
##### <a name="run-the-readiness-check-on-a-linux-vm"></a>Linux VM에서 준비 검사 실행

1. SSH를 사용하여 Azure Virtual Machine에 연결합니다.
1. SAP용 Azure 확장의 출력을 확인합니다.

   a.  `more /var/lib/AzureEnhancedMonitor/PerfCounters`을 실행합니다.

   **예상 결과**: 성능 카운터 목록을 반환합니다. 파일은 비어 있으면 안 됩니다.

   b. `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error`을 실행합니다.

   **예상 결과**: 오류가 **없는** 한 줄을 반환합니다(예: **3;config;Error;;0;0;none;0;1456416792;tst-servercs;** ).

   다. `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord`을 실행합니다.

   **예상 결과**: 빈 상태로 반환하거나 존재하지 않습니다.

이전 검사가 성공하지 못한 경우 다음 추가 검사를 실행합니다.

1. waagent가 설치되고 사용하도록 설정되었는지 확인합니다.

   a.  `sudo ls -al /var/lib/waagent/`을 실행합니다.

     **예상 결과**: waagent 디렉터리의 내용을 나열합니다.

   b.  `ps -ax | grep waagent`을 실행합니다.

   **예상 결과**: `python /usr/sbin/waagent -daemon`과 유사한 하나의 항목을 표시합니다.

1. SAP용 Azure 확장이 설치되어 실행되는지 확인합니다.

   a.  `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'`을 실행합니다.

   **예상 결과**: SAP용 Azure 확장 디렉터리의 콘텐츠를 나열합니다.

   b. `ps -ax | grep AzureEnhanced`을 실행합니다.

   **예상 결과**: `python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`과 유사한 하나의 항목을 표시합니다.

1. SAP Note [1031096] 에 설명된 대로 SAP 호스트 에이전트를 설치하고 `saposcol`의 출력을 확인합니다.

   a.  `/usr/sap/hostctrl/exe/saposcol -d`을 실행합니다.

   b.  `dump ccm`을 실행합니다.

   다.  **Virtualization_Configuration\Enhanced Monitoring Access** 메트릭이 **true** 인지 여부를 확인합니다.

SAP NetWeaver ABAP 애플리케이션 서버가 이미 설치된 경우 트랜잭션 ST06을 열고 모니터링이 사용하도록 설정되어 있는지 여부를 확인합니다.

이러한 검사 중 하나라도 실패 하는 경우 확장을 다시 배포 하는 방법에 대 한 자세한 내용은 [Linux 용 문제 해결][troubleshoot-linux] 또는 [Windows에 대 한 문제 해결][troubleshoot-windows]을 참조 하세요.

## <a name="health-checks"></a><a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>상태 확인

[준비 검사][readiness-check]에 설명 된 테스트에 표시 된 대로 일부 인프라 데이터가 올바르게 전달 되지 않으면이 챕터에 설명 된 상태 검사를 실행 하 여 azure 인프라 및 SAP 용 azure 확장이 올바르게 구성 되어 있는지 확인 합니다.

### <a name="health-checks-using-powershell"></a>PowerShell을 사용 하 여 상태 검사

1. [Azure PowerShell cmdlet 배포][deployment-guide-4.1]의 설명대로 Azure PowerShell cmdlet 최신 버전을 설치했는지 확인합니다.
1. 다음 PowerShell cmdlet을 실행합니다. 사용 가능한 환경 목록을 보려면 `Get-AzEnvironment` cmdlet을 실행합니다. 전역 Azure를 사용하려면 **AzureCloud** 환경을 선택합니다. Azure 중국 21Vianet의 경우 **AzureChinaCloud** 를 선택합니다.

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. 선택한 가상 컴퓨터의 구성을 테스트하는 스크립트입니다.

   ![SAP용 Azure 확장의 성공적인 테스트 출력][deployment-guide-figure-1300]

모든 상태 검사 결과가 **OK** 인지 확인합니다. 일부 검사에서 **OK** 가 표시 되지 않는 경우 [Azure CLI를 사용 하 여 sap 솔루션에 대 한 Azure vm 확장 구성][configure-linux] 에서 설명한 대로 업데이트 Cmdlet을 실행 하거나 [POWERSHELL을 사용 하 여 Sap 솔루션에 대 한 azure vm 확장을 구성][configure-windows]합니다. 15 분 동안 기다린 후 [준비 검사][readiness-check] 및이 챕터에 설명 된 검사를 반복 합니다. 검사에서 여전히 일부 또는 모든 카운터에 문제가 있음을 나타내는 경우 [Linux 용 문제 해결][troubleshoot-linux] 또는 [Windows 문제 해결][troubleshoot-windows]을 참조 하세요.

> [!Note]
> 관리되는 표준 Azure 디스크를 사용하는 경우 일부 경고가 발생할 수 있습니다. 테스트에서 "확인"을 반환하는 대신 경고가 표시됩니다. 이 경고는 해당 디스크 형식의 경우 정상적이며 의도된 것입니다. [Linux에 대 한 문제 해결][troubleshoot-linux] 또는 [Windows에 대 한 문제 해결][troubleshoot-windows]도 참조 하세요.
>

### <a name="health-checks-using-azure-cli"></a>Azure CLI를 사용 하 여 상태 검사

Azure CLI를 사용 하 여 SAP 용 Azure VM 확장에 대 한 상태 검사를 실행 하려면 다음을 수행 합니다.
 
1. [Azure CLI 2.0][azure-cli-2]을 설치합니다. 버전 2.19.1 이상을 사용해야 합니다(최신 버전 사용). 
1. Azure 계정으로 로그인합니다.
   ```azurecli
   az login
   ```

1. Azure CLI AEM 확장을 설치합니다. 버전 0.2.2 이상을 사용해야 합니다.
   ```azurecli
   az extension add --name aem
   ```

1. 확장의 설치를 확인 합니다. 
   ```azurecliw
   az vm aem verify -g <resource-group-name> -n <vm name> 
   ```
선택한 가상 컴퓨터의 구성을 테스트하는 스크립트입니다.

모든 상태 검사 결과가 **OK** 인지 확인합니다. 일부 검사에서 **OK** 가 표시 되지 않는 경우 [Azure CLI를 사용 하 여 sap 솔루션에 대 한 Azure vm 확장 구성][configure-linux] 에서 설명한 대로 업데이트 Cmdlet을 실행 하거나 [POWERSHELL을 사용 하 여 Sap 솔루션에 대 한 azure vm 확장을 구성][configure-windows]합니다. 15 분 동안 기다린 후 [준비 검사][readiness-check] 및이 챕터에 설명 된 검사를 반복 합니다. 검사에서 여전히 일부 또는 모든 카운터에 문제가 있음을 나타내는 경우 [Linux 용 문제 해결][troubleshoot-linux] 또는 [Windows 문제 해결][troubleshoot-windows]을 참조 하세요.

## <a name="troubleshooting-for-windows"></a><a name="f566b77e-5427-451b-b4ca-6b93114d51e8"></a>Windows에 대 한 문제 해결
 
### <a name="azure-performance-counters-do-not-show-up-at-all"></a>Azure 성능 카운터가 전혀 표시되지 않습니다.
AzureEnhancedMonitoring Windows 서비스에서 Azure의 성능 메트릭을 수집합니다. 서비스가 올바르게 설치되지 않은 경우 또는 VM에서 실행되지 않는 경우 성능 메트릭을 수집할 수 없습니다.
 
#### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>SAP용 Azure 확장의 설치 디렉터리가 비어 있습니다

##### <a name="issue"></a>문제
설치 디렉터리 C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler \<version> \drop이 비어 있습니다.

##### <a name="solution"></a>해결 방법
확장이 설치되지 않았습니다. (앞에서 설명한) 프록시 문제인지 여부를 결정합니다. 컴퓨터를 다시 시작하거나 `Set-AzVMAEMExtension` 구성 스크립트를 다시 실행해야 할 수 있습니다.

#### <a name="service-for-azure-extension-for-sap-does-not-exist"></a>SAP용 Azure 확장 서비스가 존재하지 않습니다

##### <a name="issue"></a>문제

AzureEnhancedMonitoring Windows 서비스가 존재하지 않습니다.

Azperflib.exe 출력에 오류가 발생합니다.

![SAP용 Azure 확장의 서비스가 실행되지 않음을 나타내는 Azperflib.exe의 실행][deployment-guide-figure-1400]
<a name="figure-14"></a>
 
##### <a name="solution"></a>해결 방법

서비스가 존재하지 않으면 SAP용 Azure 확장이 제대로 설치되지 않은 것입니다. Azure CLI SAP [솔루션용 Azure VM 확장 구성][configure-linux] 또는 [PowerShell을][configure-windows]통해 SAP 솔루션용 Azure VM 확장 구성에 설명된 대로 확장을 다시 배포합니다.

확장을 배포한 후 Azure 성능 카운터가 Azure VM에 제공되는지 다시 확인합니다.

#### <a name="service-for-azure-extension-for-sap-exists-but-fails-to-start"></a>SAP용 Azure 확장 관련 서비스가 존재하나 시작에 실패함
##### <a name="issue"></a>문제
AzureEnhancedMonitoring Windows 서비스가 존재하고 사용하도록 설정되었지만 시작할 수 없습니다. 자세한 내용은 애플리케이션 이벤트 로그를 확인합니다.
##### <a name="solution"></a>해결 방법
구성이 올바르지 않습니다. SAP용 Azure 확장 구성의 설명대로 VM에서 SAP용 Azure 확장을 다시 시작하세요.
 
### <a name="some-azure-performance-counters-are-missing"></a>일부 Azure 성능 카운터가 없습니다.
AzureEnhancedMonitoring Windows 서비스에서 Azure의 성능 메트릭을 수집합니다. 이 서비스는 여러 원본에서 데이터를 가져옵니다. 일부 구성 데이터는 로컬로 수집되고 일부 성능 메트릭은 Azure Diagnostics에서 읽습니다. 스토리지 카운터는 스토리지 구독 수준에 대한 로깅에서 사용됩니다.

SAP Note [1999351]을 사용한 문제 해결로 문제가 해결되지 않으면 `Set-AzVMAEMExtension` 구성 스크립트를 다시 실행합니다. 사용하도록 설정한 후 바로 스토리지 분석 또는 진단 카운터가 생성되지 않을 수 있으므로 1시간 동안 기다려야 할 수 있습니다. 문제가 지속되면 Windows용 BC-OP-NT-AZR 또는 Linux 가상 머신용 BC-OP-LNX-AZR 구성 요소에 대한 SAP 고객 지원 메시지를 엽니다.

## <a name="troubleshooting-for-linux"></a><a name="a4dae567-e7fd-4d4b-8279-510b8e5fae4a"></a>Linux 문제 해결

### <a name="azure-performance-counters-do-not-show-up-at-all"></a>Azure 성능 카운터가 전혀 표시되지 않습니다.
Azure의 성능 메트릭은 데몬에 의해 수집됩니다. 데몬이 실행되지 않는 경우 성능 메트릭은 전혀 수집할 수 없습니다.

#### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>SAP용 Azure 확장의 설치 디렉터리가 비어 있습니다
##### <a name="issue"></a>문제
디렉터리 \\var\\lib\\waagent\\에 SAP용 Azure 확장의 하위 디렉터리가 존재하지 않습니다.
##### <a name="solution"></a>해결 방법
확장이 설치되지 않았습니다. (앞에서 설명한) 프록시 문제인지 여부를 결정합니다. 컴퓨터를 다시 시작하거나 `Set-AzVMAEMExtension` 구성 스크립트를 다시 실행해야 할 수 있습니다.

#### <a name="the-execution-of-set-azvmaemextension-and-test-azvmaemextension-show-warning-messages-stating-that-standard-managed-disks-are-not-supported"></a>Set-AzVMAEMExtension 및 Test-AzVMAEMExtension을 실행하면 표준 Managed Disks를 지원하지 않는다는 경고 메시지가 표시됨
##### <a name="issue"></a>문제
Set-AzVMAEMExtension 또는 Test-AzVMAEMExtension을 실행하는 경우 다음과 같은 메시지가 표시됩니다.

<pre><code>
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
</code></pre>

앞서 설명한 대로 azperfli.exe를 실행하면 비정상 상태를 나타내는 결과를 얻을 수 있습니다.

##### <a name="solution"></a>해결 방법
이 메시지는 표준 Azure Storage 계정에 대한 통계를 확인하기 위해 SAP용 SAP 확장에서 사용하는 API를 표준 Managed Disks가 전달하지 않아 발생합니다. 심각한 문제는 아닙니다. 표준 Disk Storage 계정 관련 데이터 수집을 소개하는 이유는 입력 및 출력 제한이 자주 발생하기 때문입니다. 관리 디스크는 스토리지 계정에서 디스크 수를 제한하여 이러한 제한을 방지합니다. 따라서 해당 유형의 데이터 여부는 중요하지 않습니다.

### <a name="some-azure-performance-counters-are-missing"></a>일부 Azure 성능 카운터가 없습니다.

Azure에서 성능 메트릭은 여러 원본에서 데이터를 가져오는 데몬에 의해 수집됩니다. 일부 구성 데이터는 로컬로 수집되고 일부 성능 메트릭은 Azure Diagnostics에서 읽습니다. 스토리지 카운터는 스토리지 구독의 로그에서 제공됩니다.

알려진 문제의 전체 최신 목록은 SAP용 Azure 확장 관련 문제 해결 정보를 추가로 포함하는 SAP Note [1999351]을 참조하세요.

SAP Note 1999351 사용하여 문제를 해결할 수 없는 경우 [Azure CLI] 사용하여 `Set-AzVMAEMExtension` SAP [솔루션용 Azure VM 확장 구성][configure-linux] 또는 PowerShell을 사용하여 SAP [솔루션용 Azure VM 확장 구성에][configure-windows]설명된 대로 구성 스크립트를 다시 실행합니다. 사용하도록 설정한 후 바로 스토리지 분석 또는 진단 카운터가 생성되지 않을 수 있으므로 1시간 동안 기다려야 할 수 있습니다. 문제가 지속되면 Windows용 BC-OP-NT-AZR 또는 Linux 가상 머신용 BC-OP-LNX-AZR 구성 요소에 대한 SAP 고객 지원 메시지를 엽니다.

## <a name="azure-extension-error-codes"></a>Azure 확장 오류 코드

| 오류 ID | 오류 설명 | 해결 방법 |
|---|---|---|
| <a name="cfg_018"></a>cfg/018 | 앱 구성 없음 | [설치 스크립트 실행][deployment-guide-run-the-script] |
| <a name="cfg_019"></a>cfg/019 | 앱 구성에 배포 ID 없음 | [지원에 문의][deployment-guide-contact-support] |
| <a name="cfg_020"></a>cfg/020 | 앱 구성에 RoleInstanceId 없음 | [지원에 문의][deployment-guide-contact-support] |
| <a name="cfg_022"></a>cfg/022 | 앱 구성에 RoleInstanceId 없음 | [지원에 문의][deployment-guide-contact-support] |
| <a name="cfg_031"></a>cfg/031 | Azure 구성을 읽을 수 없음 | [지원에 문의][deployment-guide-contact-support] |
| <a name="cfg_021"></a>cfg/021 | 앱 구성 파일 없음 | [설치 스크립트 실행][deployment-guide-run-the-script] |
| <a name="cfg_015"></a>cfg/015 | 앱 구성에 VM 크기 없음 | [설치 스크립트 실행][deployment-guide-run-the-script] |
| <a name="cfg_016"></a>cfg/016 | GlobalMemoryStatusEx 카운터가 실패함 | [지원에 문의][deployment-guide-contact-support] |
| <a name="cfg_023"></a>cfg/023 | MaxHwFrequency 카운터가 실패함 | [지원에 문의][deployment-guide-contact-support] |
| <a name="cfg_024"></a>cfg/024 | NIC 카운터가 실패함 | [지원에 문의][deployment-guide-contact-support] |
| <a name="cfg_025"></a>cfg/025 | 디스크 매핑 카운터가 실패함 | [지원에 문의][deployment-guide-contact-support] |
| <a name="cfg_026"></a>cfg/026 | 프로세서 이름 카운터가 실패함 | [지원에 문의][deployment-guide-contact-support] |
| <a name="cfg_027"></a>cfg/027 | 디스크 매핑 카운터가 실패함 | [지원에 문의][deployment-guide-contact-support] |
| <a name="cfg_038"></a>cfg/038 | 확장 구성 파일 config.xml에 메트릭 'Disk type'이 없습니다. 몇 가지 다른 카운터와 함께 'Disk type'이 v2.2.0.68 12/16/2015에 도입되었습니다. 12/16/2015 이전에 확장을 배포한 경우 이전 구성 파일을 사용합니다. Azure 확장 프레임 워크는 확장을 최신 버전으로 자동 업그레이드하지만 config.xml은 변경되지 않은 상태로 유지됩니다. 구성을 업데이트하려면 최신 PowerShell 설치 스크립트를 다운로드하여 실행합니다. | [설치 스크립트 실행][deployment-guide-run-the-script] |
| <a name="cfg_039"></a>cfg/039 | 디스크 캐싱 없음 | [설치 스크립트 실행][deployment-guide-run-the-script] |
| <a name="cfg_036"></a>cfg/036 | 디스크 SLA 처리량 없음 | [설치 스크립트 실행][deployment-guide-run-the-script] |
| <a name="cfg_037"></a>cfg/037 | 디스크 SLA IOPS 없음 | [설치 스크립트 실행][deployment-guide-run-the-script] |
| <a name="cfg_028"></a>cfg/028 | 디스크 매핑 카운터가 실패함 | [지원에 문의][deployment-guide-contact-support] |
| <a name="cfg_029"></a>cfg/029 | 마지막 하드웨어 변경 카운터가 실패함 | [지원에 문의][deployment-guide-contact-support] |
| <a name="cfg_030"></a>cfg/030 | NIC 카운터가 실패함 | [지원에 문의][deployment-guide-contact-support] |
| <a name="cfg_017"></a>cfg/017 | VM의 sysprep로 인해 Windows SID가 변경됨 | [sysprep 후 다시 배포][deployment-guide-redeploy-after-sysprep] |
| <a name="str_007"></a>str/007 | 스토리지 분석에 액세스하지 못함 <br /><br />새로 만든 VM에 스토리지 분석 데이터를 채우는 데 최대 30분까지 소요될 수 있으므로 잠시 후 오류가 사라질 수 있습니다. 오류가 계속 표시되면 설치 스크립트를 다시 실행합니다. | [설치 스크립트 실행][deployment-guide-run-the-script] |
| <a name="str_010"></a>str/010 | 스토리지 분석 카운터 없음 | [설치 스크립트 실행][deployment-guide-run-the-script] |
| <a name="str_009"></a>str/009 | 스토리지 분석이 실패함 | [설치 스크립트 실행][deployment-guide-run-the-script] |
| <a name="wad_004"></a>wad/004 | WAD 구성이 잘못됨 | [설치 스크립트 실행][deployment-guide-run-the-script] |
| <a name="wad_002"></a>wad/002 | 예기치 않은 WAD 형식 | [지원에 문의][deployment-guide-contact-support] |
| <a name="wad_001"></a>wad/001 | WAD 카운터 없음 | [설치 스크립트 실행][deployment-guide-run-the-script] |
| <a name="wad_040"></a>wad/040 | 부실 WAD 카운터 없음 | [지원에 문의][deployment-guide-contact-support] |
| <a name="wad_003"></a>wad/003 | WAD 테이블을 읽을 수 없음 WAD 테이블에 대한 연결이 없습니다. 다음과 같은 여러 원인이 있을 수 있습니다.<br /><br /> 1) 오래된 구성 <br />2) Azure에 대한 네트워크 연결 없음 <br />3) WAD 설치 문제 | [설치 스크립트 실행][deployment-guide-run-the-script]<br />[인터넷 연결 수정][deployment-guide-fix-internet-connection]<br />[지원에 문의][deployment-guide-contact-support] |
| <a name="prf_011"></a>prf/011 | Perfmon NIC 메트릭이 실패함 | [지원에 문의][deployment-guide-contact-support] |
| <a name="prf_012"></a>prf/012 | Perfmon 디스크 메트릭이 실패함 | [지원에 문의][deployment-guide-contact-support] |
| <a name="prf_013"></a>prf/013 | 일부 prefmon 메트릭이 실패함 | [지원에 문의][deployment-guide-contact-support] |
| <a name="prf_014"></a>prf/014 | Perfmon에서 카운터를 만들지 못함 | [지원에 문의][deployment-guide-contact-support] |
| <a name="cfg_035"></a>cfg/035 | 구성된 메트릭 공급자가 없음 | [지원에 문의][deployment-guide-contact-support] |
| <a name="str_006"></a>str/006 | 스토리지 분석 구성이 잘못됨 | [설치 스크립트 실행][deployment-guide-run-the-script] |
| <a name="str_032"></a>str/032 | 스토리지 분석 메트릭이 실패함 | [설치 스크립트 실행][deployment-guide-run-the-script] |
| <a name="cfg_033"></a>cfg/033 | 메트릭 공급자 중 하나가 실패함 | [설치 스크립트 실행][deployment-guide-run-the-script] |
| <a name="str_034"></a>str/034 | 공급자 스레드가 실패함 | [지원에 문의][deployment-guide-contact-support] |

### <a name="detailed-guidelines-on-solutions-provided"></a>제공 된 솔루션에 대 한 자세한 지침

#### <a name="run-the-setup-script"></a><a name="0d2847ad-865d-4a4c-a405-f9b7baaa00c7"></a>설치 스크립트 실행

이 가이드의 [SAP용 Azure 확장 구성][configure-windows] 장에서 설명하는 단계에 따라 확장을 다시 설치합니다. 일부 카운터는 프로비저닝에 최대 30분까지 걸릴 수 있습니다.

오류가 사라지지 않으면 [지원에 문의하세요][deployment-guide-contact-support].

#### <a name="contact-support"></a><a name="3ba34cfc-c9bb-4648-9c3c-88e8b9130ca2"></a>지원에 문의

예기치 않은 오류이거나 알려진 해결 방법이 없습니다. C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\\<version\>\drop(Windows) 또는 /var/log/azure/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux(Linux)에 있는 AzureEnhancedMonitoring_service.log 파일을 수집하고 SAP 지원에 문의하세요.

#### <a name="redeploy-after-sysprep"></a><a name="2cd61f22-187d-42ed-bb8c-def0c983d756"></a>sysprep 후 다시 배포

sysprep을 실행한 일반화된 OS 이미지(SAP 소프트웨어 포함 가능)를 빌드하려는 경우 이 이미지에 SAP용 Azure 확장이 포함되지 않는 것이 좋습니다. 일반화된 OS 이미지의 새 인스턴스가 배포된 후에 SAP용 Azure 확장을 설치해야 합니다.

그러나 sysprep을 실행한 일반화된 OS 이미지에 이미 SAP용 Azure 확장이 포함되어 있는 경우 다음 해결 방법을 적용하여 새로 배포된 VM 인스턴스에서 확장을 재구성할 수 있습니다.

* 새로 배포된 VM 인스턴스에서 다음 폴더의 내용을 삭제합니다.  
  C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler \\ \<version\> \RuntimeSettings C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler \\ \<version\> \runtimesettings

* 이 가이드의 [SAP용 Azure 확장 구성][configure-windows] 장에서 설명하는 단계에 따라 확장을 다시 설치합니다.

#### <a name="fix-internet-connection"></a><a name="e92bc57d-80d9-4a2b-a2f4-16713a22ad89"></a>인터넷 연결 수정

SAP용 Azure 확장을 실행하는 Microsoft Azure 가상 머신에서 인터넷에 액세스할 수 있어야 합니다. Azure VM이 Azure Virtual Network 또는 온-프레미스 도메인에 포함된 경우 관련 프록시 설정이 지정되어 있는지 확인하세요. 이러한 설정은 인터넷에 액세스할 LocalSystem 계정에 대해서도 유효해야 합니다. 이 가이드의 [프록시 구성][configure-proxy] 장을 따르세요.

또한 Azure VM에 대한 고정 IP 주소를 설정해야 하는 경우 Azure VM 내에서 수동으로 설정하지 말고 [Azure PowerShell](../../../virtual-network/ip-services/virtual-networks-static-private-ip-arm-ps.md), [Azure CLI](../../../virtual-network/ip-services/virtual-networks-static-private-ip-arm-cli.md) 및 [Azure Portal](../../../virtual-network/ip-services/virtual-networks-static-private-ip-arm-pportal.md)을 사용하여 설정합니다. 고정 IP는 Azure DHCP 서비스를 통해 전파됩니다.

Azure VM 내에서 고정 IP 주소를 수동으로 설정하는 것은 지원되지 않으며 SAP용 Azure 확장에 문제가 발생할 수 있습니다.


## <a name="next-steps"></a>다음 단계
* [SAP NetWeaver에 대한 Azure Virtual Machines 배포](./deployment-guide.md)
* [SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현](./planning-guide.md)