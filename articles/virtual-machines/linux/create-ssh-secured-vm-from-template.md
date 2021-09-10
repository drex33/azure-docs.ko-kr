---
title: 템플릿에서 Azure에 Linux VM 만들기
description: Azure CLI를 사용하여 Resource Manager 템플릿에서 Linux VM을 만드는 방법
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 03/22/2019
ms.author: cynthn
ms.openlocfilehash: ae954f1a461f2a5fd98b0f0374f6583fed34ce05
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689883"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용하여 Linux 가상 머신을 만드는 방법

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

Azure Cloud shell의 Azure Resource Manager 템플릿과 Azure CLI를 사용하여 Linux VM(가상 머신)을 만드는 방법에 대해 알아봅니다. Windows 가상 머신을 만들려면, [Resource Manager 템플릿에서 windows 가상 머신 만들기](../windows/ps-template.md)를 참조하세요.

하나의 대안은 Azure Portal에서 템플릿을 배포하는 것입니다. 포털에서 템플릿을 열려면, **Azure에 배포** 단추를 선택합니다.

[![Azure에 배포](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-sshkey%2Fazuredeploy.json)

## <a name="templates-overview"></a>템플릿 개요

Azure Resource Manager 템플릿은 Azure 솔루션의 인프라와 구성을 정의하는 JSON 파일입니다. 템플릿을 사용하여 수명 주기 내내 솔루션을 반복적으로 배포하고 안심하고 일관된 상태로 리소스를 배포할 수 있습니다. 템플릿의 형식 및 템플릿을 생성하는 방법에 대해 자세히 알아보려면, [빠른 시작: Azure Portal을 사용하여 Azure Resource Manager 템플릿 만들기 및 배포](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)를 참조하세요. 리소스 유형의 JSON 구문을 보려면 [Azure Resource Manager 템플릿에서 리소스 정의](/azure/templates/microsoft.compute/allversions)를 참조하세요.

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

Azure 가상 컴퓨터를 만드는 데는 일반적으로 다음 두 단계가 포함됩니다.

1. 리소스 그룹을 생성합니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 가상 머신보다 먼저 리소스 그룹을 만들어야 합니다.
1. 가상 머신을 만듭니다.

다음 예제에서는 [Azure 빠른 시작 템플릿](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.compute/vm-sshkey/azuredeploy.json)에서 VM을 만드는 것을 보여 줍니다. 이 배포에는 SSH 인증만 허용됩니다. 메시지가 표시되면 *~/.ssh/id_rsa.pub* 의 내용과 같은 고유한 SSH 공용 키의 값을 제공합니다. SSH 키 쌍을 만들어야 하는 경우 [Azure에서 Linux VM용 SSH 키 쌍을 만들고 사용하는 방법](mac-create-ssh-keys.md)을 참조하세요. 다음은 템플릿의 복사본입니다.

[!code-json[create-linux-vm](~/quickstart-templates/quickstarts/microsoft.compute/vm-sshkey/azuredeploy.json)]

CLI 스크립트를 실행하려면, **사용해보기** 를 선택하여 Azure Cloud Shell을 엽니다. 스크립트를 붙여넣으려면 셸을 마우스 오른쪽 단추로 클릭하고 **붙여넣기** 를 선택합니다.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the project name (used for generating resource names):" &&
read projectName &&
echo "Enter the administrator username:" &&
read username &&
echo "Enter the SSH public key:" &&
read key &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/quickstarts/microsoft.compute/vm-sshkey/azuredeploy.json --parameters projectName=$projectName adminUsername=$username adminPublicKey="$key" &&
az vm show --resource-group $resourceGroupName --name "$projectName-vm" --show-details --query publicIps --output tsv
```

마지막 Azure CLI 명령은 새로 만든 VM의 공용 IP 주소를 표시합니다. 가상 머신에 연결하려면 공용 IP 주소가 필요합니다. 이 문서의 다음 섹션을 참조하세요.

이전 예제에서는 GitHub에 저장된 템플릿을 지정했습니다. 또한 템플릿을 다운로드하거나 만들고 `--template-file` 매개 변수로 로컬 경로를 지정할 수도 있습니다.

다음은 몇 가지 추가 리소스입니다.

- Resource Manager 템플릿을 개발하는 방법을 알아보려면 [Azure Resource Manager 설명서](../../azure-resource-manager/index.yml)를 참조하세요.
- Azure 가상 컴퓨터 스키마를 보려면 [Azure 템플릿 참조](/azure/templates/microsoft.compute/allversions)를 참조하세요.
- 더 많은 가상 머신 템플릿 샘플을 보려면, [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular)을 참조하세요.

## <a name="connect-to-virtual-machine"></a>가상 머신에 연결

그런 다음 정상적으로 VM에 SSH할 수 있습니다. 이전 명령에서 사용자 고유의 공용 IP 주소를 제공합니다.

```bash
ssh <adminUsername>@<ipAddress>
```

## <a name="next-steps"></a>다음 단계

이 예제에서는 기본 Linux VM을 만들었습니다. 애플리케이션 프레임워크를 포함하거나 더 복잡한 환경을 만드는 더 많은 Resource Manager 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular)를 찾아보세요.

템플릿 만들기에 대해 자세히 알아보려면 배포한 리소스 종류의 JSON 구문 및 속성을 확인하세요.

- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
