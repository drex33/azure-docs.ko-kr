---
title: SAP 솔루션용 Azure VM 확장의 새 버전 | Microsoft Docs
description: SAP용 새 VM 확장을 배포하는 방법을 알아봅니다.
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
ms.openlocfilehash: ce34b313661106e903a92aaf1a3b2f65213a1634
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577272"
---
# <a name="new-version-of-azure-vm-extension-for-sap-solutions"></a>SAP 솔루션용 Azure VM 확장의 새 버전 
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[std-extension]:vm-extension-for-sap-standard.md (SAP 솔루션용 Azure VM 확장의 표준 버전)
[configure-windows]:vm-extension-for-sap-new.md#a876ee7b-43b4-4782-aa5f-73753b6af0ea (PowerShell을 통해 SAP 솔루션에 대한 새 Azure VM 확장 구성)
[troubleshoot-windows]:vm-extension-for-sap-new.md#dee9099b-7b8a-4cdd-86a2-3f6ee964266f (Windows 문제 해결)
[troubleshoot-linux]:vm-extension-for-sap-new.md#02783aa4-5443-43f5-bc11-7af19ebf0c36 (Linux 문제 해결)
[deployment-guide-4.1]:vm-extension-for-sap-new.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Azure PowerShell cmdlet 배포)
[azure-cli-2]:/cli/azure/install-azure-cli
[configure-linux]:vm-extension-for-sap-new.md#fa4428b9-bed6-459a-9dfb-74cc27454481 (Azure CLI 통해 SAP 솔루션용 Azure VM 확장 구성)
[configure-windows]:vm-extension-for-sap-new.md#a876ee7b-43b4-4782-aa5f-73753b6af0ea (PowerShell을 통해 SAP 솔루션용 Azure VM 확장 구성)
[health-check]:vm-extension-for-sap-new.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (상태 검사)
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[readiness-check]:vm-extension-for-sap-new.md#5774c1db-1d3c-4b34-8448-3afd0b0f18ab (준비 검사)

## <a name="prerequisites"></a>필수 조건

> [!NOTE]
> 일반 지원 문: SAP용 Azure 확장에 대한 지원은 SAP 지원 채널을 통해 제공됩니다.
> SAP 솔루션용 Azure VM 확장에 대한 지원이 필요한 경우 SAP 지원을 통해 지원 사례를 여세요.
  
> [!NOTE]
> SAP용 Azure 확장의 표준 버전과 새 버전 간에 전환하기 전에 VM 확장을 제거해야 합니다.

> [!NOTE]
> 두 가지 버전의 VM 확장이 있습니다. 이 문서에서는 SAP용 Azure VM 확장의 **새** 버전을 다룹니다. 표준 버전을 설치하는 방법에 대한 지침은 [SAP 솔루션용 Azure VM 확장의 표준 버전을 참조하세요.][std-extension]

* SAP Host Agent 7.21 PL 47 이상을 사용해야 합니다.
* 확장을 사용하도록 설정된 가상 머신에 management.azure.com 대한 액세스 권한이 있는지 확인합니다.

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
 
## <a name="configure-the-azure-vm-extension-for-sap-solutions-with-powershell"></a><a name="a876ee7b-43b4-4782-aa5f-73753b6af0ea"></a>PowerShell을 통해 SAP 솔루션용 Azure VM 확장 구성
 
새 SAP용 VM 확장은 VM에 할당된 관리 ID를 사용하여 VM의 모니터링 및 구성 데이터에 액세스합니다. PowerShell을 사용하여 새 SAP용 Azure 확장을 설치하려면 먼저 이 ID를 VM에 할당하고 VM에서 사용 중인 모든 리소스(예: 디스크 및 네트워크 인터페이스)에 해당 ID 액세스 권한을 부여해야 합니다.

> [!NOTE]
> 다음 단계에서는 리소스 그룹 또는 개별 리소스(가상 머신, 데이터 디스크 및 네트워크 인터페이스)에 대한 소유자 권한이 필요합니다.

1. SAP Host Agent 7.21 PL 47 이상을 사용해야 합니다.
1. SAP용 VM 확장의 표준 버전을 제거해야 합니다. 동일한 가상 머신에 SAP용 VM 확장의 두 버전을 모두 설치할 수는 없습니다.
1. 최신 버전의 Azure PowerShell cmdlet(4.3.0 이상)을 설치했는지 확인합니다. 자세한 내용은 [Azure PowerShell cmdlet 배포][deployment-guide-4.1]를 참조하세요.
1. 다음 PowerShell cmdlet을 실행합니다.
    사용 가능한 환경 목록을 보려면 cmdlet `Get-AzEnvironment`를 실행합니다. 전역 Azure를 사용하려는 경우 환경은 **AzureCloud** 입니다. Azure 중국 21Vianet의 경우 **AzureChinaCloud** 를 선택합니다.

    SAP용 VM 확장은 확장이 외부 리소스(예: Azure Resource Manager API)에 연결하는 데 사용해야 하는 프록시 구성을 지원합니다. -ProxyURI 매개 변수를 사용하여 프록시를 설정하세요.

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name> -InstallNewExtension
    ```
 
## <a name="configure-the-azure-vm-extension-for-sap-solutions-with-azure-cli"></a><a name="fa4428b9-bed6-459a-9dfb-74cc27454481"></a>Azure CLI SAP 솔루션용 Azure VM 확장 구성
 
SAP용 새 VM 확장은 VM에 할당된 관리 ID를 사용하여 VM의 모니터링 및 구성 데이터에 액세스합니다.

> [!NOTE]
> 다음 단계를 수행하려면 리소스 그룹 또는 개별 리소스(가상 머신, 데이터 디스크 등)에 대한 소유자 권한이 있어야 합니다.

1. SAP Host Agent 7.21 PL 47 이상을 사용해야 합니다.
1. 현재 버전의 SAP용 VM 확장을 제거해야 합니다. 동일한 VM에 SAP용 VM 확장의 두 버전을 모두 설치할 수는 없습니다. 
1. 최신 버전의 [Azure CLI 2.0(버전][azure-cli-2] 2.19.1 이상)을 설치합니다.
1. Azure 계정으로 로그인합니다.

   ```azurecli
   az login
   ```

1. Azure CLI AEM 확장을 설치합니다. 버전 0.2.2 이상을 사용해야 합니다.
  
   ```azurecli
   az extension add --name aem
   ```
  
1. 새 확장을 사용하도록 설정합니다.
  
   SAP용 VM 확장은 확장이 외부 리소스(예: Azure Resource Manager API)에 연결하는 데 사용해야 하는 프록시 구성을 지원합니다. --proxy-uri 매개 변수를 사용하여 프록시를 설정하세요.

   ```azurecli
   az vm aem set -g <resource-group-name> -n <vm name> --install-new-extension
   ```
 
 
## <a name="readiness-check"></a><a name="5774c1db-1d3c-4b34-8448-3afd0b0f18ab"></a>준비 상태 검사

이 검사에서는 SAP 애플리케이션 내부에 나타나는 모든 성능 메트릭이 기본 SAP용 Azure 확장에서 제공되는지 확인합니다.

### <a name="run-the-readiness-check-on-a-windows-vm"></a>Windows VM에서 준비 검사 실행

1. Azure Virtual Machine에 로그인합니다(관리자 계정 사용은 필요하지 않음).
1. 웹 브라우저를 열고 http://127.0.0.1:11812/azure4sap/metrics 로 이동
1. 브라우저에서 가상 머신의 모니터링 데이터를 포함하는 XML 파일을 표시하거나 다운로드해야 합니다. 그렇지 않은 경우 SAP용 Azure 확장이 설치되어 있는지 확인합니다.
1. XML 파일의 내용을 확인합니다. http://127.0.0.1:11812/azure4sap/metrics 에서 액세스할 수 있는 XML 파일에는 모든 SAP용 Azure 성능 카운터가 채워져 있습니다. SAP용 Azure 확장의 상태에 대한 요약 및 상태 표시기도 포함되어 있습니다.
1. **공급자 상태 설명** 요소의 값을 확인합니다. 값이 **정상이** 아닌 경우 [상태 검사][health-check]챕터의 지침을 따릅니다.
 
### <a name="run-the-readiness-check-on-a-linux-vm"></a>Linux VM에서 준비 검사 실행

1. SSH를 사용하여 Azure Virtual Machine에 연결합니다.
1. 다음 명령의 출력을 확인합니다.
   ```bash
   curl http://127.0.0.1:11812/azure4sap/metrics
   ```
   **예상 결과**: 가상 머신, 해당 디스크 및 네트워크 인터페이스의 모니터링 정보가 포함된 XML 문서를 반환합니다.
   1. SSH를 사용하여 Azure Virtual Machine에 연결합니다.

1. 다음 명령의 출력을 확인합니다.

    ```console
    curl http://127.0.0.1:11812/azure4sap/metrics
    ```
    
   **예상 결과**: 가상 머신, 해당 디스크 및 네트워크 인터페이스의 모니터링 정보가 포함된 XML 문서를 반환합니다.

이전 검사가 성공하지 못한 경우 다음 추가 검사를 실행합니다.

1. waagent가 설치되고 사용하도록 설정되었는지 확인합니다.

   a.  `sudo ls -al /var/lib/waagent/`을 실행합니다.

     **예상 결과**: waagent 디렉터리의 내용을 나열합니다.

   b.  `ps -ax | grep waagent`을 실행합니다.

   **예상 결과**: `python /usr/sbin/waagent -daemon`과 유사한 하나의 항목을 표시합니다.

1. SAP용 Azure 확장이 설치되어 실행되는지 확인합니다.

   a.  `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-*/'`을 실행합니다.

   **예상 결과**: SAP용 Azure 확장 디렉터리의 콘텐츠를 나열합니다.

   b. `ps -ax | grep AzureEnhanced`을 실행합니다.

   **예상 결과**: `/var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-1.0.0.82/AzureEnhancedMonitoring -monitor`과 유사한 하나의 항목을 표시합니다.

1. SAP Note [1031096] 에 설명된 대로 SAP 호스트 에이전트를 설치하고 `saposcol`의 출력을 확인합니다.

   a.  `/usr/sap/hostctrl/exe/saposcol -d`을 실행합니다.

   b.  `dump ccm`을 실행합니다.

   다.  **Virtualization_Configuration\Enhanced Monitoring Access** 메트릭이 **true** 인지 여부를 확인합니다.

SAP NetWeaver ABAP 애플리케이션 서버가 이미 설치된 경우 트랜잭션 ST06을 열고 모니터링이 사용하도록 설정되어 있는지 여부를 확인합니다.

이러한 검사 중 어느 것이라도 실패하고 확장을 다시 배포하는 방법에 대한 자세한 내용은 [Windows 문제 해결][troubleshoot-windows] 또는 [Linux 문제 해결을][troubleshoot-linux] 참조하세요.
 
## <a name="health-checks"></a><a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>상태 확인

[준비 검사에][readiness-check]설명된 테스트에 표시된 대로 일부 인프라 데이터가 올바르게 전달되지 않는 경우 이 챕터에 설명된 상태 검사를 실행하여 Azure 인프라 및 SAP용 Azure 확장이 올바르게 구성되었는지 확인합니다.

### <a name="health-checks-using-powershell"></a>PowerShell을 사용하여 상태 검사

1. [Azure PowerShell cmdlet 배포][deployment-guide-4.1]의 설명대로 Azure PowerShell cmdlet 최신 버전을 설치했는지 확인합니다.
1. 다음 PowerShell cmdlet을 실행합니다. 사용 가능한 환경 목록을 보려면 `Get-AzEnvironment` cmdlet을 실행합니다. 전역 Azure를 사용하려면 **AzureCloud** 환경을 선택합니다. Azure 중국 21Vianet의 경우 **AzureChinaCloud** 를 선택합니다.

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```
1. 스크립트는 선택한 가상 머신의 구성을 테스트합니다.

모든 상태 검사 결과가 **OK** 인지 확인합니다. 일부 검사에서 **확인을** 표시하지 않는 경우 Azure CLI SAP [솔루션용 Azure VM 확장 구성][configure-linux] 또는 PowerShell을 통해 SAP 솔루션용 [Azure VM 확장 구성에][configure-windows]설명된 대로 업데이트 cmdlet을 실행합니다. 준비 검사 및 이 챕터에 설명된 [검사를][readiness-check] 반복합니다. 검사에서 여전히 일부 또는 모든 카운터에 문제가 있음을 나타내는 경우 [Linux 문제 해결][troubleshoot-linux] 또는 [Windows 문제 해결을][troubleshoot-windows]참조하세요.

### <a name="health-checks-using-azure-cli"></a>Azure CLI 사용하여 상태 검사

Azure CLI 사용하여 SAP용 Azure VM 확장에 대한 상태 검사를 실행하려면 다음을 수행합니다.
 
1. [Azure CLI 2.0][azure-cli-2]을 설치합니다. 버전 2.19.1 이상을 사용해야 합니다(최신 버전 사용). 
1. Azure 계정으로 로그인합니다.
   ```azurecli
   az login
   ```

1. Azure CLI AEM 확장을 설치합니다. 버전 0.2.2 이상을 사용해야 합니다.
   ```azurecli
   az extension add --name aem
   ```

1. 확장 설치를 확인합니다. 
   ```azurecli
   az vm aem verify -g <resource-group-name> -n <vm name> 
   ```
선택한 가상 컴퓨터의 구성을 테스트하는 스크립트입니다.

모든 상태 검사 결과가 **OK** 인지 확인합니다. 일부 검사에서 **확인을** 표시하지 않는 경우 Azure CLI SAP [솔루션용 Azure VM 확장 구성][configure-linux] 또는 PowerShell을 통해 SAP 솔루션용 [Azure VM 확장 구성에][configure-windows]설명된 대로 업데이트 cmdlet을 실행합니다. 준비 검사 및 이 챕터에 설명된 [검사를][readiness-check] 반복합니다. 검사에서 여전히 일부 또는 모든 카운터에 문제가 있음을 나타내는 경우 [Linux 문제 해결][troubleshoot-linux] 또는 [Windows 문제 해결을][troubleshoot-windows]참조하세요.


## <a name="troubleshooting-for-windows"></a><a name="dee9099b-7b8a-4cdd-86a2-3f6ee964266f"></a>Windows 문제 해결
 
### <a name="azure-performance-counters-do-not-show-up-at-all"></a>Azure 성능 카운터가 전혀 표시되지 않습니다.
AzureEnhancedMonitoring 프로세스에서 Azure의 성능 메트릭을 수집합니다. VM에서 프로세스가 실행되지 않는 경우 성능 메트릭을 전혀 수집할 수 없습니다.

#### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>SAP용 Azure 확장의 설치 디렉터리가 비어 있습니다
##### <a name="issue"></a>문제
설치 디렉터리 C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Windows\\&lt;version>이 비어 있습니다.
##### <a name="solution"></a>해결 방법
확장이 설치되지 않았습니다. (앞에서 설명한) 프록시 문제인지 여부를 결정합니다. 컴퓨터를 다시 시작하거나 VM 확장을 다시 설치해야 할 수 있습니다.
 
### <a name="some-azure-performance-counters-are-missing"></a>일부 Azure 성능 카운터가 없습니다.

AzureEnhancedMonitoring Windows 프로세스에서 Azure의 성능 메트릭을 수집합니다. 프로세스가 여러 원본에서 데이터를 가져옵니다. 일부 구성 데이터는 로컬로 수집되고 일부 성능 메트릭은 Azure Monitor에서 읽습니다.

SAP Note [1999351]을 사용하여 문제를 해결해도 문제가 지속되면 Windows용 BC-OP-NT-AZR 또는 Linux 가상 머신용 BC-OP-LNX-AZR 구성 요소에서 SAP 고객 지원 메시지를 엽니다. 로그 파일 C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Windows\\&lt;version>\\logapp.txt를 인시던트에 연결합니다.

## <a name="troubleshooting-for-linux"></a><a name="02783aa4-5443-43f5-bc11-7af19ebf0c36"></a>Linux 문제 해결

### <a name="azure-performance-counters-do-not-show-up-at-all"></a>Azure 성능 카운터가 전혀 표시되지 않습니다.
Azure의 성능 메트릭은 데몬에 의해 수집됩니다. 데몬이 실행되지 않는 경우 성능 메트릭은 전혀 수집할 수 없습니다.

#### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>SAP용 Azure 확장의 설치 디렉터리가 비어 있습니다
##### <a name="issue"></a>문제
/var/lib/waagent/ 디렉터리에는 SAP용 Azure 확장에 대한 하위 디렉터리 가 없습니다.
##### <a name="solution"></a>해결 방법
확장이 설치되지 않았습니다. (앞에서 설명한) 프록시 문제인지 여부를 결정합니다. 컴퓨터를 다시 시작하거나 VM 확장을 다시 설치해야 할 수 있습니다.
 
### <a name="some-azure-performance-counters-are-missing"></a>일부 Azure 성능 카운터가 없습니다.

Azure에서 성능 메트릭은 여러 원본에서 데이터를 가져오는 데몬에 의해 수집됩니다. 일부 구성 데이터는 로컬로 수집되고 일부 성능 메트릭은 Azure Monitor에서 읽습니다.
알려진 문제의 전체 최신 목록은 SAP용 Azure 확장 관련 문제 해결 정보를 추가로 포함하는 SAP Note [1999351]을 참조하세요.
SAP Note [1999351]을 사용하여 문제를 해결해도 문제가 지속되면 [SAP용 Azure 확장 구성][configure-windows]의 설명에 따라 확장을 다시 설치합니다. 문제가 지속되면 Windows용 BC-OP-NT-AZR 또는 Linux 가상 머신용 BC-OP-LNX-AZR 구성 요소에 대한 SAP 고객 지원 메시지를 엽니다. 로그 파일 /var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-&lt;version>/logapp.txt를 인시던트에 연결합니다.


## <a name="azure-extension-error-codes"></a>Azure 확장 오류 코드
 
모든 오류 ID에는 a-#의 형식으로 고유한 태그가 있습니다. 여기서 #은 숫자입니다. 특정 오류 및 가능한 해결 방법은 빠르게 검색할 수 있습니다.
 
| 오류 ID | 오류 설명 | 솔루션 |
|---|---|---|
| `a-0116` | 인증 토큰 없음 | 추가 정보:<br />확장은 Azure Monitor에서 VM 메트릭에 액세스하기 위한 인증 토큰을 가져올 수 없습니다. VM 메트릭을 제공하려면 VM 자체, 모든 디스크 및 VM에 연결된 모든 NIC와 같은 VM 리소스에 액세스해야 합니다.<br />해결 방법:<br />VM 관리 ID를 사용하도록 설정하고 VM 리소스 그룹에 대한 읽기 역할을 부여하세요. 설치 스크립트를 사용하는 경우 스크립트가 이를 수행합니다. 일반적으로 VM 관리 ID를 수동으로 사용하도록 설정하고 할당할 필요가 없습니다. |

## <a name="next-steps"></a>다음 단계
* [SAP NetWeaver에 대한 Azure Virtual Machines 배포](./deployment-guide.md)
* [SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현](./planning-guide.md)
