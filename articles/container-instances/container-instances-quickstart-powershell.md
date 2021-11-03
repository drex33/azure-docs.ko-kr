---
title: 빠른 시작 - 컨테이너 인스턴스에 Docker 컨테이너 배포 - PowerShell
description: 이 빠른 시작에서는 Azure PowerShell을 사용하여, 격리된 Azure 컨테이너 인스턴스에서 실행하는 컨테이너화된 웹앱을 신속하게 배포합니다.
services: container-instances
manager: gwallace
ms.date: 03/21/2019
ms.topic: quickstart
ms.service: container-instances
ms.custom: devx-track-azurepowershell, mvc, mode-api
ms.openlocfilehash: c882944691818bf15a5d15b325d39d6f2b4ed5d3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131045314"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 Azure에서 컨테이너 인스턴스 배포

Azure Container Instances를 사용하여 Azure에서 서버리스 Docker 컨테이너를 간단하고 빠르게 실행합니다. Azure Kubernetes Service와 같은 풀 컨테이너 오케스트레이션 플랫폼이 필요하지 않을 경우 애플리케이션을 요청 시 컨테이너 인스턴스에 배포합니다.

이 빠른 시작에서는 Azure PowerShell을 사용하여 격리된 Windows 컨테이너를 배포하고 해당 애플리케이션을 정규화된 도메인 이름(FQDN)으로 사용 가능하게 합니다. 단일 배포 명령을 실행한 후 몇 초 내에 컨테이너에서 실행 중인 애플리케이션을 찾아볼 수 있습니다.

![Azure Container Instances에 배포되어 브라우저에 표시된 응용 프로그램][qs-powershell-01]

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택한 경우 이 자습서에서는 Azure PowerShell 모듈을 실행해야 합니다. `Get-Module -ListAvailable Az`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

모든 Azure 리소스와 마찬가지로 Azure 컨테이너 인스턴스는 리소스 그룹에 배포해야 합니다. 리소스 그룹을 사용하면 관련 Azure 리소스를 구성하고 관리할 수 있습니다.

먼저, 다음 [New-AzResourceGroup][New-AzResourceGroup] 명령을 사용하여 *eastus* 위치에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>컨테이너 만들기

이제 리소스 그룹이 있으므로 Azure에서 컨테이너를 실행할 수 있습니다. Azure PowerShell을 사용하여 컨테이너 인스턴스를 만들려면 [New-AzContainerGroup][New-AzContainerGroup] cmdlet에 리소스 그룹 이름, 컨테이너 인스턴스 이름 및 Docker 컨테이너 이미지를 제공합니다. 이 빠른 시작에서는 공용 `mcr.microsoft.com/windows/servercore/iis:nanoserver` 이미지를 사용합니다. 이 이미지는 Nano Server에서 실행하도록 Microsoft IIS(인터넷 정보 서비스)를 패키지합니다.

열려는 하나 이상의 포트, DNS 이름 레이블 또는 둘 다를 지정하여 컨테이너를 인터넷에 공개할 수 있습니다. 이 빠른 시작에서는 DNS 이름 레이블이 있는 컨테이너를 배포하여 IIS를 공개적으로 연결할 수 있도록 합니다.

컨테이너 인스턴스를 시작하려면 다음과 유사한 명령을 실행합니다. 인스턴스를 만드는 Azure 지역 내에서 고유한 `-DnsNameLabel` 값을 설정합니다. "DNS 이름 레이블을 사용할 수 없습니다"라는 오류 메시지가 표시되면 다른 DNS 이름 레이블을 사용해 보세요.

```azurepowershell-interactive
New-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image mcr.microsoft.com/windows/servercore/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

몇 초 내에 Azure로부터 응답을 받습니다. 컨테이너의 `ProvisioningState`는 처음에는 **Creating**(만드는 중)이지만 1-2분 내에 **Succeeded**(성공)으로 전환됩니다. [Get-AzContainerGroup][Get-AzContainerGroup] cmdlet을 사용하여 배포 상태를 확인합니다.

```azurepowershell-interactive
Get-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

컨테이너의 프로비전 상태, FQDN(정규화된 도메인 이름) 및 IP 주소가 cmdlet의 출력에 표시됩니다.

```console
PS Azure:\> Get-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer


ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            : Always
IpAddress                : 52.226.19.87
DnsNameLabel             : aci-demo-win
Fqdn                     : aci-demo-win.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Windows
Volumes                  :
State                    : Pending
Events                   : {}
```

컨테이너의 `ProvisioningState`가 **Succeeded** 이면 브라우저에서 `Fqdn`으로 이동합니다. 다음과 비슷한 웹 페이지가 표시됩니다. Docker 컨테이너에서 실행되는 애플리케이션이 Azure에 성공적으로 배포되었습니다.

![Azure Container Instances를 사용하여 배포된 IIS가 브라우저에 표시됨][qs-powershell-01]

## <a name="clean-up-resources"></a>리소스 정리

컨테이너 작업을 완료했으면 [Remove-AzContainerGroup][Remove-AzContainerGroup] cmdlet을 사용하여 컨테이너를 제거합니다.

```azurepowershell-interactive
Remove-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 공용 Docker Hub 리포지토리의 이미지로 Azure 컨테이너 인스턴스를 만들었습니다. 컨테이너 이미지를 빌드하고 프라이빗 Azure 컨테이너 레지스트리에서 배포하려면 Azure Container Instances 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Container Instances 자습서](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzResourceGroup]: /powershell/module/az.resources/new-Azresourcegroup
[New-AzContainerGroup]: /powershell/module/az.containerinstance/new-Azcontainergroup
[Get-AzContainerGroup]: /powershell/module/az.containerinstance/get-Azcontainergroup
[Remove-AzContainerGroup]: /powershell/module/az.containerinstance/remove-Azcontainergroup
