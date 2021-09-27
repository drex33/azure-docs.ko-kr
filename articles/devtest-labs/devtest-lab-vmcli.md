---
title: Azure CLI를 사용 하 여 Azure DevTest Labs에서 가상 컴퓨터 만들기 및 관리
description: Azure DevTest Labs를 사용하여 Azure CLI에서 가상 머신을 만들고 관리하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 52d98993de35ccc4b02fb95579719fb68b4017ce
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128599547"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Azure CLI를 사용하여 DevTest Labs에서 가상 머신 만들기 및 관리
이 빠른 시작은 랩에서 개발 컴퓨터를 만들고, 시작하고, 연결하고, 업데이트 및 정리하는 과정을 안내합니다. 

시작하기 전에

* 랩을 만들지 않은 경우 지침은 [여기](devtest-lab-create-lab.md)에서 찾을 수 있습니다.

* [Azure CLI를 설치합니다](/cli/azure/install-azure-cli). 시작하려면 az login을 실행하여 Azure와 연결합니다. 

## <a name="create-and-verify-the-virtual-machine"></a>가상 머신 만들기 및 확인 
DevTest Labs 관련 명령을 실행하기 전에 `az account set` 명령을 사용하여 적절한 Azure 컨텍스트를 설정합니다.

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

가상 머신을 만드는 명령은 `az lab vm create`입니다. 랩, 랩 이름 및 가상 머신 이름에 대한 리소스 그룹이 모두 필요합니다. 나머지 인수는 가상 머신의 유형에 따라 변경됩니다.

다음 명령은 Azure Market Place에서 Windows 기반 이미지를 만듭니다. 이미지의 이름은 Azure Portal 사용하여 가상 머신을 만들 때와 동일합니다. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

다음 명령은 랩에서 사용할 수 있는 사용자 지정 이미지를 기반으로 가상 머신을 만듭니다.

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

**이미지 형식** 인수가 **갤러리에서** **사용자 지정** 으로 변경되었습니다. 이미지 이름은 Azure Portal의 가상 머신을 만들 때 표시되는 이름과 일치합니다.

다음 명령은 ssh 인증을 사용하는 마켓플레이스 이미지에서 VM을 만듭니다.

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

**이미지 형식** 매개 변수를 **수식** 으로 설정하여 수식에 기반하여 가상 머신을 만들 수도 있습니다. 가상 머신에 대한 특정 가상 네트워크를 선택해야 하는 경우 **vnet-name** 및 **서브넷** 매개 변수를 사용합니다. 자세한 내용은 [az lab vm create](/cli/azure/lab/vm#az_lab_vm_create)를 참조하세요.

## <a name="verify-that-the-vm-is-available"></a>VM을 사용할 수 있는지 확인합니다.
`az lab vm show` 명령을 사용하여 VM을 시작하고 연결하기 전에 VM을 사용할 수 있는지 확인합니다. 

```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand 'properties($expand=ComputeVm,NetworkInterface)' --query '{status: computeVm.statuses[0].displayStatus, fqdn: fqdn, ipAddress: networkInterface.publicIpAddress}'
```
```json
{
  "fqdn": "lisalabvm.southcentralus.cloudapp.azure.com",
  "ipAddress": "13.85.228.112",
  "status": "Provisioning succeeded"
}
```

## <a name="start-and-connect-to-the-virtual-machine"></a>가상 머신 시작 및 연결
다음 예제 명령은 VM을 시작합니다.

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

VM: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) 또는 [원격 데스크톱](../virtual-machines/windows/connect-logon.md)에 연결합니다.
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>가상 머신 업데이트
다음 샘플 명령은 VM에 아티팩트 적용.

```azurecli
az lab vm apply-artifacts --lab-name  sampleLabName --name sampleVMName  --resource-group sampleResourceGroup  --artifacts @/artifacts.json
```

```json
[
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-java",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-install-nodejs",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-apt-package",
    "parameters": [
      {
        "name": "packages",
        "value": "abcd"
      },
      {
        "name": "update",
        "value": "true"
      },
      {
        "name": "options",
        "value": ""
      }
    ]
  } 
]
```

### <a name="list-artifacts-available-in-the-lab"></a>랩에서 사용할 수 있는 아티팩트를 나열합니다.

랩의 VM에서 사용할 수 있는 아티팩트 목록을 표시하려면 다음 명령을 실행합니다.

**Cloud Shell - PowerShell**: $ in $expand (예: `$expand): 이전에 backtick (\`) 사용을 확인합니다

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(`$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

**Cloud Shell - Bash**: 명령에서 $ 앞에 슬래시( \\ ) 문자 사용을 확인합니다. 

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(\$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

샘플 출력: 

```json
[
  {
    "artifactId": "/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DevTestLab/labs/<lab name>/artifactSources/public repo/artifacts/windows-7zip",
    "status": "Succeeded"
  }
]
```

## <a name="stop-and-delete-the-virtual-machine"></a>가상 머신 중지 및 삭제    
다음 샘플 명령은 VM을 중지합니다.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

VM을 삭제합니다.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>다음 단계
다음 콘텐츠를 참조하세요: [Azure DevTest Labs용 Azure CLI 설명서](/cli/azure/lab). 
