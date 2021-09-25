---
title: Azure Pipelines에 Azure DevTest Labs 통합
description: Azure Pipelines 지속적인 통합 및 배달 파이프라인에 Azure DevTest Labs를 통합하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: c226523e9239b096cfd04990833d7c6cb1290400
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128641361"
---
# <a name="integrate-azure-devtest-labs-into-your-azure-pipelines-cicd-pipeline"></a>Azure Pipelines CI/CD 파이프라인에 Azure DevTest Labs 통합

*Azure DevTest Labs Tasks* 확장을 사용하여 Azure Pipelines 지속적인 통합 및 지속적인 배달(CI/CD) 빌드 및 릴리스 파이프라인을 Azure DevTest Labs와 통합할 수 있습니다. 확장은 다음을 포함한 여러 작업을 설치합니다. 

- VM(가상 머신) 만들기
- VM에서 사용자 지정 이미지 만들기
- VM 삭제 

이러한 작업을 통해 특정 테스트 작업에 대한 *골든 이미지* VM를 빠르게 배포하고 테스트가 완료되면 VM을 삭제하는 등의 작업이 쉬워집니다.

이 문서는 Azure DevTest Labs Tasks를 사용하여 VM을 만들어 배포하기, 사용자 지정 이미지 만들기 및 VM 삭제하기 방법을 하나의 릴리스 파이프라인으로 보여줍니다. 일반적으로 고유한 사용자 지정 빌드, 테스트, 배포 파이프라인에서는 해당 작업을 개별적으로 수행합니다.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="prerequisites"></a>필수 구성 요소

- [Azure DevOps](https://dev.azure.com) 조직에 등록 또는 로그인하고 해당 조직에 [프로젝트](/vsts/organizations/projects/create-project)를 만듭니다. 
  
- Visual Studio Marketplace에서 Azure DevTest Labs Tasks 확장을 설치합니다.
  
  1. [Azure DevTest Labs 작업](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)으로 이동합니다.
  1. **무료로 가져오기** 를 선택합니다.
  1. 드롭다운에서 Azure DevOps 조직을 선택하고 **설치** 를 선택합니다. 
  
## <a name="create-the-template-to-build-an-azure-vm"></a>Azure VM을 빌드하는 템플릿 만들기 

이 섹션에서는 필요에 따라 Azure VM을 만드는 데 사용하는 Azure Resource Manager 템플릿을 만드는 방법을 설명합니다.

1. 구독에서 Resource Manager 템플릿을 만들려면 [Resource Manager 템플릿 사용](devtest-lab-use-resource-manager-template.md)에 나온 절차를 수행합니다.
   
1. Resource Manager 템플릿을 생성하기 전에, VM 만들기의 일부로 [WinRM 아티팩트](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm)를 추가합니다. *Azure 파일 복사* 및 *대상 머신의 PowerShell* 과 같은 배포 작업에는 WinRM 액세스가 필요합니다. WinRM 아티팩트에는 매개 변수로 호스트 이름이 필요하며, 이는 VM의 FQDN(정규화된 도메인 이름)이어야 합니다. 
   
   > [!NOTE]
   > WinRM을 공유 IP 주소와 함께 사용하는 경우 NAT 규칙을 추가하여 외부 포트를 WinRM 포트에 매핑해야 합니다. 공용 IP 주소를 사용하여 VM을 만드는 경우에는 NAT 규칙이 필요하지 않습니다.
   
   
1. 컴퓨터에 *CreateVMTemplate.js* 라는 파일로 템플릿을 저장합니다.
   
1. 소스 제어 시스템에서 템플릿을 검사합니다.

## <a name="create-a-script-to-get-vm-properties"></a>VM 속성을 가져오는 스크립트 만들기

릴리스 파이프라인에서 *Azure 파일 복사* 또는 *대상 머신의 PowerShell* 등의 작업 단계를 수행하는 경우 다음 스크립트는 VM에 앱을 배포해야 하는 값을 수집합니다. 일반적으로 이러한 작업을 사용하여 Azure VM에 앱을 배포합니다. VM 리소스 그룹 이름, IP 주소 및 FQDN(정규화된 도메인 이름)과 같은 값을 요구하는 작업입니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

스크립트 파일을 만들려면 다음을 수행합니다.

1. 텍스트 편집기를 열고 다음 스크립트를 붙여넣습니다.
   
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

1. *GetLabVMParams.ps1* 와 같은 이름으로 파일을 저장하고 소스 제어 시스템에서 확인합니다. 

## <a name="create-a-release-pipeline-in-azure-pipelines"></a>Azure Pipelines에서 릴리스 파이프라인 만들기

새 릴리스 파이프라인을 만들려면 다음을 수행합니다.

1. Azure DevOps 프로젝트 페이지의 왼쪽 탐색 영역에서 **파이프라인** > **릴리스** 를 선택합니다.
1. **새 파이프라인** 을 선택합니다.
1. **템플릿 선택** 에서 아래로 스크롤하여 **빈 작업** 을 선택하고 **적용** 을 선택합니다.

### <a name="add-and-set-variables"></a>변수 추가 및 설정

파이프라인 작업은 Azure Portal에서 Resource Manager 템플릿을 만들 때 VM에 할당한 값을 사용합니다. 

값에 대한 변수를 추가하려면 다음을 수행합니다. 

1. 파이프라인 페이지에서 **변수** 탭을 선택합니다.
   
1. 각 변수에 대해 **추가** 를 선택하고 이름 및 값을 입력합니다.
   
   |이름|값|
   |---|---|
   |*vmName*|Resource Manager 템플릿에서 할당한 VM 이름|
   |*userName*|VM에 액세스하기 위한 사용자 이름|
   |*password*|사용자의 암호. 암호를 숨기고 보호하려면 자물쇠 아이콘을 선택합니다.

### <a name="create-a-devtest-labs-vm"></a>DevTest Labs VM 만들기

다음 단계에서는 향후 배포에 사용할 골든 이미지 VM을 만듭니다. *Azure DevTest Labs로 VM 만들기* 작업을 사용하여 Azure DevTest Labs 인스턴스 내에서 VM을 만듭니다.

1. 릴리스 파이프라인 **파이프라인** 탭에서 **1단계** 에서 **단계 작업 보기** 로 하이퍼링크된 텍스트를 선택하고 **에이전트 작업** 옆에 있는 더하기 기호 **+** 를 선택합니다. 
   
1. **작업 추가** 에서 **Azure DevTest Labs로 VM 만들기** 를 선택하고 **추가** 를 선택합니다. 
   
1. 왼쪽 창에서 **Azure DevTest Labs VM 만들기** 를 선택합니다. 

1. 오른쪽 창에서 다음과 같이 양식을 작성합니다.
   
   |필드|값|
   |---|---|
   |**Azure RM 구독**|드롭다운 목록에서 **사용 가능한 Azure 서비스 연결** 또는 **사용 가능한 Azure 구독** 에서 서비스 연결 또는 구독을 선택하고 필요한 경우 **권한 부여** 를 선택합니다.<br /><br />**참고:** Azure 구독에 대해 더욱 제한된 권한 연결을 만드는 방법에 대한 자세한 내용은 [Azure Resource Manager 서비스 엔드포인트](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)를 참조하세요.|
   |**랩 이름**|랩 VM을 만들 기존 랩의 이름을 선택합니다.|
   |**템플릿 이름**|소스 코드 리포지토리에 저장한 템플릿 파일의 전체 경로 및 이름을 입력합니다. 기본 제공 속성을 사용하여 경로를 단순화할 수 있습니다. 예를 들면 다음과 같습니다.<br /><br />`$(System.DefaultWorkingDirectory)/Templates/CreateVMTemplate.json`|
   |**템플릿 매개 변수**|앞에서 정의한 변수에 대한 매개 변수를 입력합니다.<br /><br />`-newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)`|
   |**출력 변수** > **랩 VM ID**|만든 랩 VM ID에 대한 변수를 입력합니다. 기본 **labVMId** 를 사용하는 경우 *$(labVMId)* 로 후속 작업의 변수를 참조할 수 있습니다.<br /><br />기본값 이외의 이름을 만들 수 있지만 후속 작업에서 올바른 이름을 사용해야 합니다. 랩 VM ID는 다음과 같은 형식으로 작성할 수 있습니다.<br /><br />`/subscriptions/{subscription Id}/resourceGroups/{resource group Name}/providers/Microsoft.DevTestLab/labs/{lab name}/virtualMachines/{vmName}`|

### <a name="collect-the-details-of-the-devtest-labs-vm"></a>DevTest Labs VM의 세부 정보 수집

이전에 만든 스크립트를 실행하여 DevTest Labs VM의 세부 정보를 수집합니다. 

1. 릴리스 파이프라인 **파이프라인** 탭에서 **1단계** 에서 **단계 작업 보기** 로 하이퍼링크된 텍스트를 선택하고 **에이전트 작업** 옆에 있는 더하기 기호 **+** 를 선택합니다. 
   
1. **작업 추가** 에서 **Azure PowerShell**, **추가** 를 선택합니다. 
   
1. 왼쪽 창에서 **Azure PowerShell 스크립트: FilePath** 를 선택합니다. 
   
1. 오른쪽 창에서 다음과 같이 양식을 작성합니다.
   
   |필드|값|
   |---|---|
   |**Azure 연결 형식**|**Azure Resource Manager** 를 선택합니다.|
   |**Azure 구독**|서비스 연결 또는 구독을 선택합니다.| 
   |**스크립트 유형**|**스크립트 파일 경로** 를 선택합니다.|
   |**스크립트 경로**|소스 코드 리포지토리에 저장한 PowerShell 스크립트의 전체 경로 및 이름을 입력합니다. 기본 제공 속성을 사용하여 경로를 단순화할 수 있습니다. 예를 들면 다음과 같습니다.<br /><br />`$(System.DefaultWorkingDirectory/Scripts/GetLabVMParams.ps1`|
   |**스크립트 인수**|이전 작업에서 채워진 *labVmId* 변수의 이름을 입력합니다. 예를 들면 다음과 같습니다.<br /><br />`-labVmId '$(labVMId)'`|

스크립트는 필수 값을 수집하여 이후 단계에서 쉽게 참조할 수 있도록 릴리스 파이프라인 내 환경 변수에 이를 저장합니다.

### <a name="create-a-vm-image-from-the-devtest-labs-vm"></a>DevTest Labs VM에서 VM 이미지 만들기

다음 작업은 Azure DevTest Labs 인스턴스에 새로 배포된 VM의 이미지를 만드는 것입니다. 그런 다음, 개발 작업을 실행하거나 일부 테스트를 실행하고자 할 때마다 필요에 따라 이미지를 사용하여 VM의 복사본을 만듭니다. 

1. 릴리스 파이프라인 **파이프라인** 탭에서 **1단계** 에서 **단계 작업 보기** 로 하이퍼링크된 텍스트를 선택하고 **에이전트 작업** 옆에 있는 더하기 기호 **+** 를 선택합니다. 
   
1. **작업 추가** 에서 **Azure DevTest Labs VM로 사용자 지정 이미지 만들기** 를 선택하고 **추가** 를 선택합니다. 
   
1. 아래와 같이 작업을 구성합니다.
   
   |필드|값|
   |---|---|
   |**Azure RM 구독**|서비스 연결 또는 구독을 선택합니다.|
   |**랩 이름**|이미지를 만들 기존 랩의 이름을 선택합니다.|
   |**사용자 지정 이미지 이름**|사용자 지정 이미지의 이름을 입력합니다.|
   |**설명**(선택 사항)|나중에 올바른 이미지를 쉽게 선택할 수 있도록 설명을 입력합니다.|
   |**원본 랩 VM** > **원본 랩 VM ID**|사용자가 LabVMId 변수의 기본 이름을 변경한 경우 여기에 입력합니다. 기본값은 **$(labVMId)** 입니다.|
   |**출력 변수** > **사용자 지정 이미지 ID**|필요한 경우 변수의 기본 이름을 편집할 수 있습니다.|
   
### <a name="deploy-your-app-to-the-devtest-labs-vm-optional"></a>DevTest Labs VM에 앱 배포(선택 사항)

새 DevTest Labs VM에 앱을 배포하는 작업을 추가할 수 있습니다. 일반적으로 앱을 배포하는 데 사용하는 작업은 *Azure 파일 복사* 및 *대상 컴퓨터의 PowerShell* 입니다.

이러한 작업의 매개 변수에 필요한 VM 정보는 릴리스 파이프라인 내 **labVmRgName**, **labVMIpAddress** 및 **labVMFqdn** 이라는 세 가지 구성 변수에 저장됩니다. 앱을 배포하지 않고 DevTest Labs VM 및 사용자 지정 이미지를 만들어 시험하려는 경우 이 단계를 건너뛸 수 있습니다.

### <a name="delete-the-vm"></a>VM 삭제

최종 작업은 Azure DevTest Labs 인스턴스에 배포된 VM을 삭제하는 것입니다. 일반적으로 배포된 VM에서 필요한 개발 작업을 실행하거나 테스트를 실행한 후 VM을 삭제합니다. 

1. 릴리스 파이프라인 **파이프라인** 탭에서 **1단계** 에서 **단계 작업 보기** 로 하이퍼링크된 텍스트를 선택하고 **에이전트 작업** 옆에 있는 더하기 기호 **+** 를 선택합니다. 
   
1. **작업 추가** 에서 **Azure DevTest Labs로 VM 삭제** 를 선택하고 **추가** 를 선택합니다. 
   
1. 아래와 같이 작업을 구성합니다.
   
   - **Azure RM 구독** 에서 서비스 연결 또는 구독을 선택합니다. 
   - **Lab VM ID** 는 사용자가 LabVMId 변수의 기본 이름을 변경한 경우 여기에 입력합니다. 기본값은 **$(labVMId)** 입니다.
   
### <a name="save-the-release-pipeline"></a>릴리스 파이프라인 저장

새 릴리스 파이프라인을 저장하려면 다음을 수행합니다.

1. 릴리스 파이프라인 페이지에서 **새 릴리스 파이프라인** 이라는 이름을 선택하고 파이프라인의 새 이름을 입력합니다. 
   
1. 오른쪽 위에서 **저장** 아이콘을 선택합니다. 

## <a name="create-and-run-a-release"></a>릴리스 만들기 및 실행

새 파이프라인을 사용하여 릴리스를 만들고 실행하려면 다음을 수행합니다.

1. 릴리스 파이프라인 페이지의 오른쪽 위에서 **릴리스 만들기** 를 선택합니다. 
   
1. **아티팩트** 에서 최신 빌드를 선택하고 **만들기** 를 선택합니다.
   
1. 각 릴리스 단계마다 Azure Portal에서 DevTest Labs 인스턴스 보기를 새로 고침하여 VM 만들기, 이미지 만들기 및 VM 삭제를 확인합니다.

사용자 지정 이미지를 사용하여 필요할 때마다 VM을 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계
- [Resource Manager 템플릿으로 다중 VM 환경을 만드는](devtest-lab-create-environment-from-arm.md) 방법을 알아봅니다.
- [공용 DevTest Labs GitHub 리포지토리](https://github.com/Azure/azure-quickstart-templates)에서 DevTest Labs 자동화를 위한 더 많은 빠른 시작 Resource Manager 템플릿을 살펴봅니다.
- 필요한 경우 [Azure DevOps 문제 해결](/azure/devops/pipelines/troubleshooting) 페이지를 참조하세요.
 
