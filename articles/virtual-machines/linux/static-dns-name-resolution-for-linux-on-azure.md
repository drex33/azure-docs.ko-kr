---
title: Azure CLI에서 VM 이름을 확인하기 위한 내부 DNS 사용
description: 가상 네트워크 인터페이스 카드를 만들고 Azure CLI를 사용하여 Azure에서 VM 이름을 확인하기 위한 내부 DNS를 사용하는 방법
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/16/2017
ms.author: cynthn
ms.openlocfilehash: d2620595a314cef2317ff56224d325438093b5d2
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696127"
---
# <a name="create-virtual-network-interface-cards-and-use-internal-dns-for-vm-name-resolution-on-azure"></a>가상 네트워크 인터페이스 카드 만들기 및 Azure에서 VM 이름 확인을 위해 내부 DNS 사용

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

이 문서에서는 Azure CLI로 가상 네트워크 인터페이스 카드(vNic) 및 DNS 레이블 이름을 사용하여 Linux VM의 정적 내부 DNS 이름을 설정하는 방법을 보여 줍니다. 정적 DNS 이름은 이 문서에서 사용되는 Jenkins 빌드 서버 또는 Git 서버와 같은 영구 인프라 서비스에 사용됩니다.

요구 사항은 다음과 같습니다.

* [Azure 계정](https://azure.microsoft.com/pricing/free-trial/)
* [SSH 공용 및 프라이빗 키 파일](mac-create-ssh-keys.md)

## <a name="quick-commands"></a>빠른 명령
태스크를 빠르게 완료해야 하는 경우 다음 섹션에서 필요한 명령에 대해 자세히 알아보세요. 각 단계에 대한 보다 자세한 내용 및 상황 설명은 [여기서부터](#detailed-walkthrough) 문서 끝까지 참조하세요. 이러한 단계를 수행하려면 최신 [Azure CLI](/cli/azure/install-az-cli2)를 설치하고 [az login](/cli/azure/reference-index)을 사용하여 Azure 계정에 로그인해야 합니다.

사전 요구 사항: 리소스 그룹, 가상 네트워크 및 서브넷, SSH 인바운드가 있는 네트워크 보안 그룹.

### <a name="create-a-virtual-network-interface-card-with-a-static-internal-dns-name"></a>정적 내부 DNS 이름을 가진 가상 네트워크 인터페이스 카드 만들기
[az network nic create](/cli/azure/network/nic)를 사용하여 vNic를 만듭니다. `--internal-dns-name` CLI 플래그는 가상 네트워크 인터페이스 카드(vNic)에 대한 정적 DNS 이름을 제공하는 DNS 레이블을 설정하기 위한 것입니다. 다음 예제에서는 `myNic`라는 vNic를 만들고 `myVnet` 가상 네트워크에 연결하며 `jenkins`이라는 내부 DNS 이름 레코드를 만듭니다.

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

### <a name="deploy-a-vm-and-connect-the-vnic"></a>VM 배포 및 vNic 연결
[az vm create](/cli/azure/vm)로 VM을 만듭니다. `--nics` 플래그는 Azure에 배포하는 동안 VM에 vNic를 연결합니다. 다음 예제에서는 Azure Managed Disks를 사용하여 가상 컴퓨터 `myVM`을 만들고 이전 단계에서 vNic `myNic`를 연결합니다.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="detailed-walkthrough"></a>자세한 연습

Azure에서 전체 CiCd(지속적인 통합 및 지속적인 배포) 인프라는 특정 서버가 정적이거나 서버의 수명이 길어야 합니다. 가상 네트워크 및 네트워크 보안 그룹과 같은 Azure 자산은 정적이고 거의 배포되지 않은 수명이 긴 리소스인 것이 좋습니다. 가상 네트워크를 배포하면 인프라에 어떤 부정적인 영향을 주지 않고 새 배포에서 다시 사용할 수 있습니다. 나중에 개발 또는 테스트 환경을 위해 이 가상 네트워크에 Git 리포지토리 서버 및 Jenkins 자동화 서버 제공 CiCd를 추가할 수 있습니다.  

내부 DNS 이름은 Azure Virtual Network 내에서만 확인할 수 있습니다. DNS 이름은 내부용이므로 외부 인터넷에서 확인할 수 없으며 인프라에 대한 추가 보안을 제공합니다.

다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에 `myResourceGroup`, `myNic` 및 `myVM`이 포함됩니다.

## <a name="create-the-resource-group"></a>리소스 그룹 만들기
먼저 [az group create](/cli/azure/group)을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 `westus` 위치에 `myResourceGroup`이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-virtual-network"></a>가상 네트워크 만들기

다음 단계는 VM을 시작할 가상 네트워크를 빌드하는 것입니다. 가상 네트워크는 이 연습을 위한 서브넷을 포함합니다. Azure Virtual Network에 대한 자세한 내용은 [가상 네트워크 만들기](../../virtual-network/manage-virtual-network.md#create-a-virtual-network)를 참조하세요. 

[az network vnet create](/cli/azure/network/vnet)를 사용하여 가상 네트워크를 만듭니다. 다음 예제에서는 `myVnet`이라는 가상 네트워크와 `mySubnet`이라는 서브넷을 만듭니다.

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-the-network-security-group"></a>네트워크 보안 그룹 만들기
Azure 네트워크 보안 그룹은 네트워크 계층에서 방화벽과 동일합니다. 네트워크 보안 그룹에 대한 자세한 내용은 [Azure CLI에서 NSG를 만드는 방법](../../virtual-network/tutorial-filter-network-traffic-cli.md)을 참조하세요. 

[az network nsg create](/cli/azure/network/nsg)를 사용하여 네트워크 보안 그룹을 만듭니다. 다음 예제에서는 `myNetworkSecurityGroup`이라는 네트워크 보안 그룹을 만듭니다.

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-rule-to-allow-ssh"></a>SSH를 허용하는 인바운드 규칙 추가
[az network nsg rule create](/cli/azure/network/nsg/rule)를 사용하여 네트워크 보안 그룹에 대해 인바운드 규칙을 추가합니다. 다음 예제는 `myRuleAllowSSH`이라는 규칙을 만듭니다.

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myRuleAllowSSH \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow
```

## <a name="associate-the-subnet-with-the-network-security-group"></a>네트워크 보안 그룹과 서브넷 연결
서브넷을 네트워크 보안 그룹과 연결하려면 [az network vnet subnet update](/cli/azure/network/vnet/subnet)를 사용합니다. 다음 예제에서는 `mySubnet`이라는 서브넷을 네트워크 보안 그룹 `myNetworkSecurityGroup`에 연결합니다.

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```


## <a name="create-the-virtual-network-interface-card-and-static-dns-names"></a>가상 네트워크 인터페이스 카드 및 정적 DNS 이름 만들기
Azure는 매우 유연하지만 VM 이름 확인을 위해 DNS 이름을 사용하려면 DNS 레이블을 포함하는 가상 네트워크 인터페이스 카드(vNic)를 만들어야 합니다. VNic는 인프라 수명 주기를 넘어 다른 VM에 연결하여 다시 사용할 수 있기 때문에 중요합니다. 이 방법을 통해 VM이 임시 리소스가 되는 동안 vNic를 정적 리소스로 유지할 수 있습니다. vNic에 DNS 레이블 지정을 사용하여 VNet의 다른 VM에서 간단한 이름 확인을 사용할 수 있습니다. 확인 가능한 이름을 사용하면 다른 VM에서 DNS 이름 `Jenkins`로 자동화 서버에 액세스하거나 `gitrepo`로 Git 서버에 액세스할 수 있습니다.  

[az network nic create](/cli/azure/network/nic)를 사용하여 vNic를 만듭니다. 다음 예제에서는 `myNic`라는 vNic를 만들고 `myVnet` 가상 네트워크 `myVnet`에 연결하며 `jenkins`이라는 내부 DNS 이름 레코드를 만듭니다.

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>가상 네트워크 인프라에 VM 배포
이제 가상 네트워크, 서브넷, 네트워크 보안 그룹이 SSH에 대한 포트 22 및 vNic를 제외한 모든 인바운드 트래픽을 차단하여 서브넷을 보호하는 방화벽의 역할을 하게 됩니다. 이제 이 기존 네트워크 인프라 내에 VM을 배포할 수 있습니다.

[az vm create](/cli/azure/vm)로 VM을 만듭니다. 다음 예제에서는 Azure Managed Disks를 사용하여 가상 컴퓨터 `myVM`을 만들고 이전 단계에서 vNic `myNic`를 연결합니다.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

기존 리소스를 호출하기 위해 CLI 플래그를 사용하여 Azure에서 기존 네트워크 내에 VM을 배포하도록 지시합니다. 다시 말해, VNet 및 서브넷이 배포되면 Azure 지역 내에서 정적 또는 영구적으로 리소스로 유지할 수 있습니다.  

## <a name="next-steps"></a>다음 단계
* [Azure CLI 명령을 직접 사용하여 Linux VM에 대한 고유한 사용자 지정 환경 만들기](create-cli-complete.md)
* [템플릿을 사용하여 Azure에서 Linux VM 만들기](create-ssh-secured-vm-from-template.md)
