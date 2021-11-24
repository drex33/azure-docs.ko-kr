---
title: ARM 템플릿을 사용하여 Azure AMD 기반 기밀 VM 만들기(미리 보기)
description: ARM 템플릿을 사용하여 AMD 기반 기밀 VM(기밀 가상 머신)을 빠르게 만드는 방법을 알아봅니다. Azure Portal 또는 Azure CLI에서 기밀 VM을 배포합니다.
author: RunCai
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 11/15/2021
ms.author: RunCai
ms.openlocfilehash: 81508017a445bb636561f799263b1bbc1631e23c
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132942647"
---
# <a name="quickstart-deploy-confidential-vm-with-arm-template-preview"></a>빠른 시작: ARM 템플릿을 사용하여 기밀 VM 배포(미리 보기)

> [!IMPORTANT]
> Azure 기밀 컴퓨팅의 기밀 VM(기밀 가상 머신)은 현재 미리 보기로 제공됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 [기밀 VM](confidential-vm-overview.md)을 빠르게 만들 수 있습니다. 사용자가 만든 기밀 VM은 VM 메모리 암호화 및 격리를 수행하기 위해 AMD SEV-SNP가 지원하는 AMD 프로세서에서 실행됩니다. 자세한 내용은 [기밀 VM 개요](confidential-vm-overview.md)를 참조하세요.

이 자습서에서는 사용자 지정 구성을 사용하여 기밀 VM을 배포하는 방법을 설명합니다. 

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 평가판 계정은 이 자습서에 사용된 VM에 액세스할 수 없습니다. 한 가지 옵션은 [종량제 구독](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)을 사용하는 것입니다. 
- Azure CLI(Azure 명령줄 인터페이스)에서 배포하려는 경우 [PowerShell을 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-6.6.0)하고 [Azure CLI를 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)합니다.

## <a name="deploy-confidential-vm-template-from-azure-portal"></a>Azure Portal에서 기밀 VM 템플릿 배포

Azure Portal에서 ARM 템플릿을 사용하여 기밀 VM을 만들고 배포하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. [기밀 VM ARM 템플릿을 엽니다](https://aka.ms/deploycvmazure). 

    1. **구독** 의 경우 [사전 요구 사항](#prerequisites)을 충족하는 Azure 구독을 선택합니다.
    
    1. **리소스 그룹** 의 경우 드롭다운 메뉴에서 기존 리소스 그룹을 선택합니다. 또는 **새로 만들기** 를 선택하고 고유한 이름을 입력한 다음, **확인** 을 선택합니다.
    
    1. **지역** 에 VM을 배포할 Azure 지역을 선택합니다.
    
    1. **VM 이름** 에 VM의 이름을 입력합니다.
    
    1. **VM 위치** 에 VM의 위치를 선택합니다. 
    
        > [!NOTE]
        > 일부 위치에서는 기밀 VM을 사용할 수 없습니다. 현재 지원되는 위치는 [Azure 지역별 사용 가능한 VM 제품](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)을 참조하세요.
    
    1. **VM 크기** 에 사용할 VM 크기를 선택합니다.
    
    1. **OS 이미지 이름** 에 VM에 사용할 OS 이미지를 선택합니다.
    
    1. **OS 디스크 유형** 에 사용할 OS 디스크 유형을 선택합니다.
    
    1. **관리자 사용자 이름** 에 VM의 관리자 사용자 이름을 입력합니다.
    
    1. **관리자 암호 또는 키** 에 관리자 계정의 암호를 입력합니다. 암호가 [Linux VM](../virtual-machines/linux/faq.yml#what-are-the-password-requirements-when-creating-a-vm-) 또는 [Windows VM](../virtual-machines/windows/faq.yml#what-are-the-password-requirements-when-creating-a-vm-)의 복잡성 요구 사항을 충족하는지 확인합니다.
    
    1. **부팅 진단** 의 경우 VM에 대한 부팅 진단을 사용할지 여부를 선택합니다. 기본 설정은 **false** 입니다.
    
    1. **보안 유형** 의 경우 VM을 배포하기 전에 전체 OS 디스크 암호화를 사용할지 여부를 선택합니다. **VMGuestStateOnly** 옵션은 OS 디스크 암호화를 제공하지 않습니다. **DiskWithVMGuestState** 옵션은 플랫폼 관리형 키를 사용하여 전체 OS 디스크 암호화를 사용하도록 설정합니다.
    
    1. **보안 부팅 사용** 에 대해 **true** 를 선택합니다. 이 설정은 올바르게 서명된 부팅 구성 요소만 로드할 수 있도록 합니다.

1. **검토 + 만들기** 를 선택하여 구성의 유효성을 검사합니다.

1. 유효성 검사가 완료될 때까지 기다립니다. 필요한 경우 유효성 검사 문제를 해결한 다음, **검토 + 만들기** 를 다시 선택합니다.

1. **검토 + 만들기** 창에서 **만들기** 를 선택하여 VM을 배포합니다.

## <a name="deploy-confidential-vm-template-with-azure-cli"></a>Azure CLI를 사용하여 기밀 VM 템플릿 배포

Azure CLI를 통해 ARM 템플릿을 사용하여 기밀 VM을 만들고 배포하려면 다음을 수행합니다.

1. Azure CLI에서 Azure 계정에 로그인합니다.

    ```powershell-interactive
    az login
    ```

1. Azure 구독을 설정합니다. `<subscription-id>`를 사용자의 구독 식별자로 바꿉니다. [사전 요구 사항](#prerequisites)을 충족하는 구독을 사용해야 합니다.

    ```powershell-interactive
    az account set --subscription <subscription-id>
    ```

1. 기밀 VM에 대한 변수를 설정합니다. 배포 이름(`$deployName`), 리소스 그룹(`$resourceGroup`), VM 이름(`$vmName`) 및 Azure 지역(`$region`)을 입력합니다. 샘플 값을 사용자 고유의 정보로 바꿉니다.

    > [!NOTE]
    > 일부 위치에서는 기밀 VM을 사용할 수 없습니다. 현재 지원되는 위치는 [Azure 지역별 사용 가능한 VM 제품](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)을 참조하세요.

    ```powershell-interactive
    $deployName="<deployment-name>"
    $resourceGroup="<resource-group-name>"
    $vmName= "<confidential-vm-name>"
    $region="<region-name>"
    ```

    지정한 리소스 그룹이 없으면 해당 이름으로 리소스 그룹을 만듭니다.
    
    ```powershell-interactive
    az group create -n $resourceGroup -l $region
    ```

1. 사용자 지정 매개 변수 파일이 있는 ARM 템플릿을 사용하여 Azure에 VM 배포

      
    ```powershell-interactive
    az deployment group create `
     -g $resourceGroup `
     -n $deployName `
     -u "https://aka.ms/CVMTemplate" `
     -p "<json-parameter-file-path>" `
     -p vmLocation=$region `
        vmName=$vmName
    ```


### <a name="define-custom-parameter-file"></a>사용자 지정 매개 변수 파일 정의

Azure CLI를 사용하여 기밀 VM을 만들 때 사용자 지정 매개 변수 파일을 정의해야 합니다. 사용자 지정 JSON 매개 변수 파일을 만들려면 다음을 수행합니다.

1. Azure CLI에서 Azure 계정에 로그인합니다.

1. JSON 매개 변수 파일을 만듭니다. 예들 들어 `azuredeploy.parameters.json`입니다.

1. 사용 중인 OS 이미지에 따라 [예제 Windows 매개 변수 파일](#example-windows-parameter-file) 또는 [예제 Linux 매개 변수 파일](#example-linux-parameter-file)에 복사합니다.

1. 필요에 따라 매개 변수 파일에서 JSON 코드를 편집합니다. 예를 들어 OS 이미지 이름(`osImageName`), 관리자 사용자 이름(`adminUsername`) 등을 업데이트할 수 있습니다.

#### <a name="example-windows-parameter-file"></a>예제 Windows 매개 변수 파일

이 예제를 사용하여 Windows 기반 기밀 VM에 대한 사용자 지정 매개 변수 파일을 만듭니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {

    "vmSize": {
      "value": "Standard_DC2as_v5"
    },
    "osImageName": {
      "value": "Windows Server 2022 Gen 2"
    },
    "securityType": {
      "value": "DiskWithVMGuestState"
    },
    "adminUsername": {
      "value": "testuser"
    },
    "adminPasswordOrKey": {
      "value": "Password123@@"
    }
  }
}
```

#### <a name="example-linux-parameter-file"></a>예제 Linux 매개 변수 파일

이 예제를 사용하여 Linux 기반 기밀 VM에 대한 사용자 지정 매개 변수 파일을 만듭니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {

    "vmSize": {
      "value": "Standard_DC2as_v5"
    },
    "osImageName": {
      "value": "Ubuntu 20.04 LTS Gen 2"
    },
    "securityType": {
      "value": "DiskWithVMGuestState"
    },
    "adminUsername": {
      "value": "testuser"
    },
    "authenticationType": {
      "value": "sshPublicKey"
    },
    "adminPasswordOrKey": {
      "value": {your ssh public key}
    }
  }
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [빠른 시작: Azure Portal에서 AMD의 기밀 VM 만들기](quick-create-confidential-vm-portal-amd.md)
