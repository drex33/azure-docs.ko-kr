---
title: 빠른 시작 - Azure CLI를 사용하여 Windows VM 만들기
description: 이 빠른 시작에서는 Azure CLI를 사용하여 Windows 가상 머신을 만드는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 08/09/2021
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: ca4466c307a893ac7058581a5e22aec87b9a71a2
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694687"
---
# <a name="quickstart-create-a-windows-virtual-machine-with-the-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Windows 가상 머신 만들기

**적용 대상:** :heavy_check_mark: Windows VM

명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 Azure CLI가 사용됩니다. 이 빠른 시작에서는 Azure CLI를 사용하여 Windows Server 2019을 실행하는 Azure에서 VM(가상 머신)을 배포하는 방법을 보여 줍니다. 작업에서 VM을 보려면 VM에 RDP를 수행하고 IIS 웹 서버를 설치합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요** 를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/bash](https://shell.azure.com/bash)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사** 를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, **Enter** 키를 눌러 실행합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>가상 머신 만들기

[az vm create](/cli/azure/vm)로 VM을 만듭니다. 다음 예제에서는 *myVM* 이라는 VM을 만듭니다. 이 예에서는 관리자 이름으로 *azureuser* 를 사용합니다. 

[Azure VM의 암호 요구 사항](./faq.yml#what-are-the-password-requirements-when-creating-a-vm-
)을 충족하는 암호를 제공해야 합니다. 

아래 예제를 사용하여 명령줄에서 암호를 입력하라는 메시지가 표시됩니다. 암호에 대한 값을 사용하여 `--admin-password` 매개 변수를 추가할 수도 있습니다. 사용자 이름 및 암호는 나중에 VM에 연결할 때 사용됩니다.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Win2019Datacenter \
    --public-ip-sku Standard \
    --admin-username azureuser 
```

VM 및 지원 리소스를 만드는 데 몇 분이 걸립니다. 다음 예제 출력은 VM 만들기 작업이 완료되었음을 보여줍니다.


```output
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

VM의 출력에서 사용자 고유의 `publicIpAddress`를 적어 둡니다. 이 주소는 다음 단계에서 VM에 액세스하는 데 사용됩니다.

## <a name="open-port-80-for-web-traffic"></a>웹 트래픽에 대해 포트 80 열기

기본적으로 Azure에서 Windows VM을 만들 때만 RDP 연결이 열립니다. [az vm open-port](/cli/azure/vm)를 사용하여 IIS 웹 서버와 사용하기 위해 TCP 포트 80을 엽니다.

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>가상 머신에 연결

다음 명령을 사용하여 로컬 컴퓨터에서 원격 데스크톱 세션을 만듭니다. IP 주소를 VM의 공용 IP 주소로 바꿉니다. 메시지가 표시되면 VM을 만들 때 사용되는 자격 증명을 입력합니다.

```powershell
mstsc /v:publicIpAddress
```

## <a name="install-web-server"></a>웹 서버 설치

실제로 작동하는 VM을 보려면 IIS 웹 서버를 설치합니다. VM에서 PowerShell 프롬프트를 열고 다음 명령을 실행합니다.

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

여기까지 마쳤으면 VM에 대한 RDP 연결을 닫습니다.

## <a name="view-the-web-server-in-action"></a>실제로 작동 중인 웹 서버 보기

IIS를 설치하고 현재 포트 80이 인터넷에서 VM에 열려 있으면 사용자가 선택한 웹 브라우저를 사용하여 기본 IIS 시작 페이지를 봅니다. 이전 단계에서 가져온 VM의 공용 IP 주소를 사용합니다. 다음 예제는 기본 IIS 웹 사이트를 보여줍니다.

![IIS 기본 사이트](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group) 명령을 사용하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 간단한 가상 머신을 배포하고, 웹 트래픽에 대한 네트워크 포트를 열고, 기본 웹 서버를 설치했습니다. Azure 가상 머신에 대한 자세한 내용을 알아보려면 Windows VM의 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [Azure Windows 가상 머신 자습서](./tutorial-manage-vm.md)
