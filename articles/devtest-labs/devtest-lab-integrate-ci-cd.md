---
title: Azure Pipelines Azure DevTest Labs 통합
description: Azure DevTest Labs CI/CD(연속 통합 및 업데이트) 파이프라인에 Azure Pipelines 통합하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 11/16/2021
ms.openlocfilehash: cd136468aefd68521b911214f9a5bfbc5b99ba56
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132939674"
---
# <a name="integrate-devtest-labs-into-azure-pipelines"></a>DevTest Labs를 Azure Pipelines 통합

Azure DevTest Labs Tasks 확장을 사용하여 Azure DevTest Labs AZURE PIPELINES CI/CD(연속 통합 및 업데이트) 파이프라인에 통합할 수 있습니다. 확장은 다음을 포함하여 Azure Pipelines 여러 작업을 설치합니다.

- VM(가상 머신) 만들기
- VM에서 사용자 지정 이미지 만들기
- VM 삭제

예를 들어 이러한 작업을 통해 골든 *이미지* VM을 신속하게 배포하고 특정 테스트를 실행한 다음 VM을 삭제할 수 있습니다.

이 문서에서는 Azure DevTest Labs Tasks 사용하여 VM을 만들고 배포하고, 사용자 지정 이미지를 만든 다음, 하나의 릴리스 파이프라인에서 모두 VM을 삭제하는 방법을 보여줍니다. 일반적으로 사용자 고유의 빌드, 테스트 및 배포 파이프라인에서 이러한 작업을 개별적으로 수행합니다.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="prerequisites"></a>사전 요구 사항

- Azure Portal [DevTest Labs 랩을 만들거나](devtest-lab-create-lab.md)기존 랩을 사용합니다.
- [Azure DevOps Services](https://dev.azure.com) 조직에 등록하거나 로그인하고 프로젝트를 [만들거나](/vsts/organizations/projects/create-project)기존 프로젝트를 사용합니다.
- Visual Studio Marketplace에서 Azure DevTest Labs Tasks 확장을 설치합니다.
  
  1. [Azure DevTest Labs 작업](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)으로 이동합니다.
  1. **무료로 가져오기** 를 선택합니다.
  1. 드롭다운 목록에서 Azure DevOps Services 조직을 선택한 다음, **설치를** 선택합니다. 

## <a name="create-a-template-to-build-a-lab-vm"></a>랩 VM을 빌드하는 템플릿 만들기 

먼저 주문형 랩 VM을 만드는 ARM(Azure Resource Manager) 템플릿을 생성합니다.

1. Azure Portal 랩의 위쪽 메뉴 모음에서 **추가를** 선택합니다.
1. 기본 선택 화면에서 VM에 대한 Windows 기본 이미지를 **선택합니다.**
1. 랩 **리소스 만들기** 화면의 **Artifacts** 아래에서 **Artifacts 추가 또는 제거를** 선택합니다.
1. **아티팩트 추가** 화면에서 *winrm* 을 검색한 다음 **WinRM 구성** 옆에 있는 화살표를 선택합니다.
1. **아티팩트 추가** 창에서 VM의 FQDN(정규화된 도메인 이름)을 입력합니다(예: `contosolab00000000000000.westus3.cloudapp.azure.com` ). **확인** 을 선택한 후 **만들기** 를 선택합니다.
1. 고급 **설정** 탭을 선택하고 IP **주소** 에 대해 **공용** 을 선택합니다.
   > [!NOTE]
   > WinRM 아티팩트에서 공유 IP 주소를 사용하는 경우 외부 포트를 WinRM 포트에 매핑하는 NAT(네트워크 주소 변환) 규칙을 추가해야 합니다. 공용 IP 주소를 사용하여 VM을 만드는 경우에는 NAT 규칙이 필요하지 않습니다. 이 연습에서는 공용 IP 주소를 사용하여 VM을 만듭니다.
1. **ARM 템플릿 보기를** 선택합니다.
1. 템플릿 코드를 복사하고 로컬 소스 제어 분기에 *CreateVMTemplate.json이라는* 파일로 저장합니다.
1. 프로젝트의 소스 제어 시스템에 대한 템플릿을 체크 인합니다.

자세한 내용 및 세부 정보는 [Resource Manager 템플릿 사용을 참조하세요.](devtest-lab-use-resource-manager-template.md)

## <a name="create-a-script-to-get-vm-properties"></a>VM 속성을 가져오는 스크립트 만들기

다음으로, **Azure 파일 복사** 및 대상 **컴퓨터의 PowerShell과** 같은 작업 단계에서 VM에 앱을 배포하는 데 사용하는 값을 수집하는 스크립트를 만듭니다. 일반적으로 이러한 작업을 사용하여 Azure VM에 고유한 앱을 배포합니다. VM 리소스 그룹 이름, IP 주소 및 FQDN(정규화된 도메인 이름)과 같은 값을 요구하는 작업입니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

GetLabVMParams.ps1 *같은* 이름으로 다음 스크립트를 저장하고 프로젝트의 소스 제어 시스템에 체크 인합니다.

```powershell
Param( [string] $labVmId)

$labVmComputeId = (Get-AzResource -Id $labVmId).Properties.ComputeId

# Get lab VM resource group name
$labVmRgName = (Get-AzResource -Id $labVmComputeId).ResourceGroupName

# Get the lab VM Name
$labVmName = (Get-AzResource -Id $labVmId).Name

# Get lab VM public IP address
$labVMIpAddress = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
                -Name $labVmName).IpAddress

# Get lab VM FQDN
$labVMFqdn = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
           -Name $labVmName).DnsSettings.Fqdn

# Set a variable labVmRgName to store the lab VM resource group name
Write-Host "##vso[task.setvariable variable=labVmRgName;]$labVmRgName"

# Set a variable labVMIpAddress to store the lab VM Ip address
Write-Host "##vso[task.setvariable variable=labVMIpAddress;]$labVMIpAddress"

# Set a variable labVMFqdn to store the lab VM FQDN name
Write-Host "##vso[task.setvariable variable=labVMFqdn;]$labVMFqdn"
```

## <a name="create-a-release-pipeline-in-azure-pipelines"></a>Azure Pipelines에서 릴리스 파이프라인 만들기

다음으로, Azure Pipelines 릴리스 파이프라인을 만듭니다. 파이프라인 작업은 ARM 템플릿을 만들 때 VM에 할당한 값을 사용합니다.

1. Azure DevOps Services 프로젝트 페이지의 왼쪽 탐색 창에서 **Pipelines**  >  **릴리스를** 선택합니다.
1. **릴리스 만들기를** 선택합니다.
1. 새 **릴리스 파이프라인** 페이지에서 **변수 탭을** 선택합니다.
1. **추가를** 선택하고 다음 **이름** 및 **값** 쌍을 입력하고 각 쌍을 추가한 후 **추가를** 선택합니다.
   - *vmName:* ARM 템플릿에서 할당한 VM 이름입니다.
   - *userName:* VM에 액세스하기 위한 사용자 이름입니다.
   - *password*: 사용자 이름에 대한 암호입니다. 암호를 숨기고 보호하려면 자물쇠 아이콘을 선택합니다.

### <a name="create-a-devtest-labs-vm"></a>DevTest Labs VM 만들기

다음 단계에서는 향후 배포에 사용할 골든 이미지 VM을 만듭니다. 이 단계에서는 **Azure DevTest Labs VM 만들기** 작업을 사용합니다.

1. 새 릴리스 파이프라인 페이지의 **파이프라인** 탭에서 **1단계의** 하이퍼링크 텍스트를 선택합니다.
1. 왼쪽 창에서 에이전트 작업 옆에 있는 **+** 더하기 기호를 선택합니다.
1. 오른쪽 창의 **작업 추가에서** **VM 만들기 Azure DevTest Labs** 검색하여 선택하고 **추가를** 선택합니다. 
1. 왼쪽 창에서 **Azure DevTest Labs VM 만들기** 작업을 선택합니다.
1. 오른쪽 창에서 다음과 같이 양식을 작성합니다.

   - **Azure RM 구독:** 드롭다운 목록에서 서비스 연결 또는 구독을 선택하고 필요한 경우 **권한 부여를** 선택합니다.
     > [!NOTE]
     > Azure 구독에 대한 더 제한된 권한 연결을 만드는 자세한 내용은 [Azure Resource Manager 서비스 엔드포인트를](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)참조하세요.
   - **랩:** DevTest Labs 랩 이름을 선택합니다.
   - **템플릿:** 프로젝트 리포지토리에 체크 인한 템플릿 파일을 찾아 선택합니다.
   - **매개 변수 파일:** 리포지토리에 체크 인한 매개 변수 파일을 찾아 선택합니다.
   - **매개 변수 재정의:** 를 `-newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)` 입력합니다.
   - 출력 **변수 를** 드롭다운하고 **참조 이름** 아래에 만든 랩 VM ID에 대한 변수를 입력합니다. 기본 *labVmId* 를 사용하는 경우 후속 작업에서 변수를 **$(labVmId)** 로 참조할 수 있습니다.

     기본값 이외의 이름을 만들 수 있지만 후속 작업에서 올바른 이름을 사용해야 합니다. 랩 VM ID는 형식으로 작성할 수 `/subscriptions/{subscription Id}/resourceGroups/{resource group Name}/providers/Microsoft.DevTestLab/labs/{lab name}/virtualMachines/{vmName}` 있습니다.

### <a name="collect-the-details-of-the-devtest-labs-vm"></a>DevTest Labs VM의 세부 정보 수집

다음으로, 파이프라인은 만든 스크립트를 실행하여 DevTest Labs VM의 세부 정보를 수집합니다.

1. 릴리스 파이프라인 **파이프라인** 탭의 **1단계에서** 하이퍼링크 텍스트를 선택한 다음 **+** 에이전트 **작업** 옆에 있는 더하기 기호를 선택합니다.
1. 오른쪽 창의 **작업 추가에서** **Azure PowerShell** 검색하여 선택하고 **추가를** 선택합니다.
1. 왼쪽 창에서 **Azure PowerShell 스크립트 FilePath** 작업을 선택합니다.
1. 오른쪽 창에서 다음과 같이 양식을 작성합니다.
   - **Azure 구독:** 서비스 연결 또는 구독을 선택합니다.
   - **스크립트 유형:** **스크립트 파일 경로** 를 선택합니다.
   - **스크립트 경로:** 소스 코드 리포지토리에 체크 인한 PowerShell 스크립트를 찾아 선택합니다. 기본 제공 속성을 사용하여 경로를 단순화할 수 있습니다(예: `$(System.DefaultWorkingDirectory/Scripts/GetLabVMParams.ps1` ).
   - **스크립트 인수:** 이전 작업에서 채우던 **labVmId** 변수의 이름(예: *-labVmId '$(labVmId)')을* 입력합니다.

스크립트는 필요한 값을 수집하고 릴리스 파이프라인 내의 환경 변수에 저장하므로 이후 단계에서 참조할 수 있습니다.

### <a name="create-a-vm-image-from-the-devtest-labs-vm"></a>DevTest Labs VM에서 VM 이미지 만들기

다음 작업은 랩에 새로 배포된 VM의 이미지를 만듭니다. 이미지를 사용하여 요청 시 VM의 복사본을 만들어 개발자 작업을 수행하거나 테스트를 실행할 수 있습니다.

1. 릴리스 파이프라인 **파이프라인** 탭의 **1단계에서** 하이퍼링크 텍스트를 선택한 다음 **+** 에이전트 **작업** 옆에 있는 더하기 기호를 선택합니다.
1. **작업 추가** 에서 **Azure DevTest Labs VM로 사용자 지정 이미지 만들기** 를 선택하고 **추가** 를 선택합니다.
1. 왼쪽 창에서 **Azure DevTest Labs 사용자 지정 이미지 만들기** 작업을 선택합니다.
1. 오른쪽 창에서 다음과 같이 양식을 작성합니다.
   - **Azure RM 구독:** 서비스 연결 또는 구독을 선택합니다.
   - **랩:** 랩을 선택합니다.
   - **사용자 지정 이미지 이름:** 사용자 지정 이미지의 이름을 입력합니다.
   - **설명:** 올바른 이미지를 쉽게 선택할 수 있도록 선택적 설명을 입력합니다.
   - **원본 랩 VM:** 원본 **labVmId 입니다.** **labVmId** 변수의 기본 이름을 변경한 경우 여기에 입력합니다. 기본값은 **$(labVmId)** 입니다.
   - **출력 변수:** 필요한 경우 기본 사용자 지정 이미지 ID 변수의 이름을 편집할 수 있습니다.
   
### <a name="deploy-your-app-to-the-devtest-labs-vm-optional"></a>DevTest Labs VM에 앱 배포(선택 사항)

새 DevTest Labs VM에 앱을 배포하는 작업을 추가할 수 있습니다. 앱을 배포하지 않고 DevTest Labs VM 및 사용자 지정 이미지를 만드는 실험만 하려는 경우 이 단계를 건너뛸 수 있습니다.

일반적으로 앱을 배포하는 데 사용하는 작업은 대상 머신의 **Azure 파일 복사** 및 **PowerShell입니다.** 릴리스 파이프라인 내에서 **labVmRgName**, labVMIpAddress 및 **labVMFqdn이라는** 세 가지 구성 변수에서 작업 매개 변수에 필요한 VM 정보를 찾을 수 있습니다.  

### <a name="delete-the-vm"></a>VM 삭제

최종 작업은 랩에서 배포한 VM을 삭제 하는 것입니다. 일반적으로 개발자 작업을 수행 하거나 배포 된 VM에서 필요한 테스트를 실행 한 후 VM을 삭제 합니다.

1. 릴리스 파이프라인 **파이프라인** 탭에서 **1 단계** 에서 하이퍼링크 텍스트를 선택 하 고 **+** **에이전트 작업** 옆에 있는 더하기 기호를 선택 합니다.
1. **작업 추가** 에서 **Azure DevTest Labs로 VM 삭제** 를 선택하고 **추가** 를 선택합니다. 
1. 아래와 같이 작업을 구성합니다.
   - **AZURE RM 구독**: 서비스 연결 또는 구독을 선택 합니다.
   - **랩**: 랩을 선택 합니다.
   - **가상 컴퓨터**: 삭제 하려는 VM을 선택 합니다.
   - **출력 변수**: **참조 이름** 아래에서 **랩 vmid** 변수의 기본 이름을 변경한 경우 여기에 입력 합니다. 기본값은 **$(labVmId)** 입니다.
   
### <a name="save-the-release-pipeline"></a>릴리스 파이프라인 저장

새 릴리스 파이프라인을 저장하려면 다음을 수행합니다.

1. 릴리스 파이프라인 페이지의 맨 위에서 **새 릴리스 파이프라인** 을 선택 하 고 파이프라인에 대 한 새 이름을 입력 합니다.
1. 오른쪽 위에서 **저장** 을 선택 합니다.

## <a name="create-and-run-a-release"></a>릴리스 만들기 및 실행

새 파이프라인을 사용하여 릴리스를 만들고 실행하려면 다음을 수행합니다.

1. 릴리스 파이프라인 페이지의 오른쪽 위에서 **릴리스 만들기** 를 선택 합니다. 
1. **아티팩트** 에서 최신 빌드를 선택하고 **만들기** 를 선택합니다.
   
각 릴리스 단계에서 Azure Portal의 랩 보기를 새로 고쳐 VM 만들기, 이미지 만들기 및 VM 삭제를 확인할 수 있습니다.

사용자 지정 이미지를 사용하여 필요할 때마다 VM을 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계
- [ARM 템플릿을 사용 하 여 다중 VM 환경을 만드는](devtest-lab-create-environment-from-arm.md)방법에 대해 알아봅니다.
- [공용 DevTest labs GitHub](https://github.com/Azure/azure-quickstart-templates)리포지토리에서 DevTest labs 자동화를 위한 더 빠른 시작 ARM 템플릿을 탐색 합니다.
- 필요한 경우 [Azure Pipelines 문제 해결](/azure/devops/pipelines/troubleshooting)을 참조 하세요.
 
