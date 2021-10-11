---
title: '자습서: VM에 대한 라우팅 기본 설정 구성 - Azure CLI'
description: 이 자습서에서는 Azure CLI를 통해 라우팅 기본 설정을 사용하여 공용 IP 주소가 있는 VM을 만드는 방법에 대해 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: tutorial
ms.date: 10/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: 9428d28eabde40dec7ed2805217fb655d8b0f693
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369411"
---
# <a name="tutorial-configure-routing-preference-for-a-vm-using-the-azure-cli"></a>자습서: Azure CLI를 사용하여 VM에 대한 라우팅 기본 설정 구성
이 자습서에서는 가상 머신의 라우팅 기본 설정을 구성하는 방법을 보여줍니다. 라우팅 기본 설정 옵션으로 **인터넷** 을 선택하면 VM의 인터넷 바인딩 트래픽이 ISP 네트워크를 통해 라우팅됩니다. 기본 라우팅은 Microsoft 글로벌 네트워크를 통해 전달됩니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * **인터넷** 라우팅 기본 설정에 대해 구성된 공용 IP 주소를 만듭니다.
> * 가상 머신을 만듭니다.
> * 공용 IP 주소가 **인터넷** 라우팅 기본 설정으로 설정되어 있는지 확인합니다.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 이 자습서에는 Azure CLI 버전 2.0.28 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

**westus2** 위치에 **TutorVMRoutePref-rg** 라는 [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다.

```azurecli-interactive
  az group create \
    --name TutorVMRoutePref-rg \
    --location westus2
```

## <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기

[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)를 사용하여 **TutorVMRoutePref-rg** 에 **myPublicIP** 라는 표준 영역 중복 공용 IPv4 주소를 만듭니다. **인터넷** 의 **태그** 는 **인터넷** 라우팅 기본 설정을 활성화하는 CLI 명령의 매개 변수로 공용 IP 주소에 적용됩니다.

```azurecli-interactive
az network public-ip create \
    --resource-group TutorVMRoutePref-rg \
    --name myPublicIP \
    --version IPv4 \
    --ip-tags 'RoutingPreference=Internet' \
    --sku Standard \
    --zone 1 2 3
```

## <a name="create-virtual-machine"></a>가상 머신 만들기

[az vm create](/cli/azure/vm#az_vm_create)를 사용하여 가상 머신을 만듭니다. 이전 섹션에서 만든 공용 IP 주소는 CLI 명령의 일부로 추가되고 생성 중에 VM에 연결됩니다.

```azurecli-interactive
az vm create \
--name myVM \
--resource-group TutorVMRoutePref-rg \
--public-ip-address myPublicIP \
--size Standard_A2 \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
--admin-username azureuser
```

## <a name="verify-internet-routing-preference"></a>인터넷 라우팅 기본 설정 확인

[az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show)를 사용하여 공용 IP 주소에 대해 **인터넷** 라우팅 기본 설정이 구성되어 있는지 확인합니다.

```azurecli-interactive
az network public-ip show \
    --resource-group TutorVMRoutePref-rg \
    --name myPublicIP \
    --query ipTags \
    --output tsv
```

## <a name="clean-up-resources"></a>리소스 정리

가상 머신과 공용 IP 주소 작업을 마쳤으면 [az group delete](/cli/azure/group#az_group_delete)를 사용하여 리소스 그룹과 리소스 그룹에 포함된 모든 리소스를 삭제합니다.

```azurecli-interactive
  az group delete \
    --name TutorVMRoutePref-rg
```

## <a name="next-steps"></a>다음 단계

다음 문서로 이동하여 라우팅 기본 설정이 혼합된 가상 머신을 만드는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [가상 머신의 라우팅 기본 설정 옵션 두 가지를 모두 구성하기](routing-preference-mixed-network-adapter-portal.md)

