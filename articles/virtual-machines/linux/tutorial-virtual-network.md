---
title: 자습서 - Linux VM용 Azure 가상 네트워크 만들기 및 관리
description: 이 자습서에서는 Azure CLI를 사용하여 Linux 가상 머신을 위한 Azure 가상 네트워크를 만들고 관리하는 방법을 알아봅니다.
services: virtual-machines
author: cynthn
ms.collection: linux
ms.subservice: networking
ms.service: virtual-machines
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: e259a835046b24d84fad9ec9fdb6416bc1bd6c90
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698949"
---
# <a name="tutorial-create-and-manage-azure-virtual-networks-for-linux-virtual-machines-with-the-azure-cli"></a>자습서: Azure CLI를 사용하여 Linux 가상 머신을 위한 Azure 가상 네트워크 만들기 및 관리

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

Azure 가상 머신은 내부 및 외부 네트워크 통신에서 Azure 네트워킹을 사용합니다. 이 자습서에서는 두 개의 가상 머신을 배포하고 이러한 VM에 Azure 네트워킹을 구성하기 위해 단계별로 안내합니다. 이 자습서의 예제에서는 VM에서 데이터베이스 백 엔드가 있는 웹 애플리케이션을 호스팅한다고 가정하고 있지만 애플리케이션은 이 자습서에서 배포되지 않습니다. 이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 가상 네트워크 및 서브넷 만들기
> * 공용 IP 주소 만들기
> * 프런트 엔드 VM 만들기
> * 네트워크 트래픽 보안
> * 백엔드 VM 만들기

이 자습서에서는 지속적으로 최신 버전으로 업데이트되는 [Azure Cloud Shell](../../cloud-shell/overview.md) 내의 CLI를 사용합니다. Cloud Shell을 열려면 코드 블록 상단에서 **사용해 보세요** 를 선택합니다.

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 자습서에서 Azure CLI 버전 2.0.30 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="vm-networking-overview"></a>VM 네트워킹 개요

Azure 가상 네트워크를 사용하면 네트워크에서 가상 머신, 인터넷 및 다른 Azure 서비스(예: Azure SQL Database) 간에 안전하게 연결할 수 있습니다. 가상 네트워크는 서브넷이라는 논리적 세그먼트로 구분됩니다. 서브넷은 보안 경계로서 네트워크 흐름을 제어하는 데 사용됩니다. VM을 배포할 때는 일반적으로 서브넷에 연결된 가상 네트워크 인터페이스가 포함됩니다.

이 자습서를 완료하면 다음 가상 네트워크 리소스가 만들어집니다.

![두 서브넷을 사용하는 가상 네트워크](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet* - VM이 다른 VM 및 인터넷과 통신할 때 사용하는 가상 네트워크.
- *myFrontendSubnet* - 프런트 엔드 리소스에서 사용되는 *myVNet* 의 서브넷.
- *myPublicIPAddress* - 인터넷에서 *myFrontendVM* 을 액세스할 때 사용되는 공용 IP 주소.
- *myFrontentNic* - *myFrontendVM* 이 *myBackendVM* 과 통신할 때 사용하는 네트워크 인터페이스.
- *myFrontendVM* - 인터넷과 *myBackendVM* 사이에 통신할 때 사용되는 VM.
- *myBackendNSG* - *myFrontendVM* 과 *myBackendVM* 사이의 통신을 제어하는 네트워크 보안 그룹.
- *myBackendSubnet* - *myBackendNSG* 에 연동되어 백엔드 리소스에 의해 사용되는 서브넷.
- *myBackendNic* - *myBackendVM* 이 *myFrontendVM* 과 통신할 때 사용하는 네트워크 인터페이스.
- *myBackendVM* - 포트 22와 3306을 사용하여 *myFrontendVM* 과 통신하는 VM.

## <a name="create-a-virtual-network-and-subnet"></a>가상 네트워크 및 서브넷 만들기

이 자습서에서는 두 개의 서브넷이 있는 단일 가상 네트워크를 만듭니다. 서브넷 하나는 웹 애플리케이션을 호스트하기 위한 프런트 엔드 서브넷이고 다른 하나는 데이터베이스 서버를 호스트하기 위한 백 엔드 서브넷입니다.

가상 네트워크를 만들려면 먼저 [az group create](/cli/azure/group)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 eastus 위치에 *myRGNetwork* 라는 리소스 그룹을 만듭니다.

```azurecli-interactive 
az group create --name myRGNetwork --location eastus
```

### <a name="create-virtual-network"></a>가상 네트워크 만들기

[az network vnet create](/cli/azure/network/vnet) 명령을 사용하여 가상 네트워크를 만듭니다. 이 예제에서 네트워크의 이름은 *mvVNet* 이며, 주소 접두사는 *10.0.0.0/16* 으로 지정됩니다. *myFrontendSubnet* 이라는 이름과 *10.0.1.0/24* 접두사를 갖는 서브넷도 만들어집니다. 이 자습서의 뒷부분에서 프런트 엔드 VM이 이 서브넷에 연결됩니다. 

```azurecli-interactive 
az network vnet create \
  --resource-group myRGNetwork \
  --name myVNet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myFrontendSubnet \
  --subnet-prefix 10.0.1.0/24
```

### <a name="create-subnet"></a>서브넷 만들기

[az network vnet subnet create](/cli/azure/network/vnet/subnet) 명령을 사용하여 가상 네트워크에 새 서브넷을 추가합니다. 이 예제에서 서브넷의 이름은 *myBackendSubnet* 이며, 주소 접두사는 *10.0.2.0/24* 로 지정됩니다. 이 서브넷은 모든 백 엔드 서비스에서 사용됩니다.

```azurecli-interactive 
az network vnet subnet create \
  --resource-group myRGNetwork \
  --vnet-name myVNet \
  --name myBackendSubnet \
  --address-prefix 10.0.2.0/24
```

이 시점에서 네트워크가 만들어져 하나는 프론트 엔드 서비스를, 다른 하나는 백 엔드 서비스를 위한 두 개의 서브넷으로 분할되었습니다. 다음 섹션에서는 가상 머신을 만들어 이 서브넷에 연결합니다.

## <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기

공용 IP 주소를 사용하면 Azure 리소스를 인터넷에서 액세스할 수 있습니다. 공용 IP 주소의 할당 방법은 동적 또는 정적 할당으로 구성할 수 있습니다. 기본적으로 공용 IP 주소는 동적 할당됩니다. VM 할당을 취소하는 경우 동적 IP 주소도 해제됩니다. 이 동작은 VM 할당 취소를 포함하는 모든 작업 중에 IP 주소가 변경되게 합니다.

할당 방법은 정적으로 설정할 수 있으며, 이렇게 하면 할당 취소된 상태에서도 VM에 할당된 IP 주소는 그대로 유지됩니다. 정적으로 할당된 IP 주소를 사용하는 경우 IP 주소 자체를 지정할 수 없습니다. 대신 사용 가능한 주소 풀에서 할당됩니다.

```azurecli-interactive
az network public-ip create --resource-group myRGNetwork --name myPublicIPAddress
```

[az vm create](/cli/azure/vm) 명령으로 VM을 만들 때 기본적인 공용 IP 주소 할당 방법은 동적입니다. [az vm create](/cli/azure/vm) 명령을 사용하여 가상 머신을 만들 때 `--public-ip-address-allocation static` 인수를 포함하여 정적 공용 IP 주소를 지정합니다. 이 작업은 이 자습서에서 설명하지 않지만, 다음 섹션에서는 동적으로 할당된 IP 주소가 정적으로 할당된 주소로 변경됩니다. 

### <a name="change-allocation-method"></a>할당 방법 변경

IP 주소 할당 방법은 [az network public-ip update](/cli/azure/network/public-ip) 명령을 사용하여 변경할 수 있습니다. 이 예제에서는 프런트 엔드 VM의 IP 주소 할당 방법이 정적으로 변경됩니다.

먼저 VM을 할당 취소합니다.

```azurecli-interactive 
az vm deallocate --resource-group myRGNetwork --name myFrontendVM
```

[az network public-ip update](/cli/azure/network/public-ip) 명령을 사용하여 할당 방법을 업데이트합니다. 이 경우 `--allocation-method`는 *static* 으로 설정됩니다.

```azurecli-interactive 
az network public-ip update --resource-group myRGNetwork --name myPublicIPAddress --allocation-method static
```

VM을 시작합니다.

```azurecli-interactive 
az vm start --resource-group myRGNetwork --name myFrontendVM --no-wait
```

### <a name="no-public-ip-address"></a>공용 IP 주소 없음

종종 VM은 인터넷을 통해 액세스할 필요가 없습니다. 공용 IP 주소 없이 VM을 만들려면 `--public-ip-address ""` 인수에 빈 따옴표 묶음을 사용합니다. 이 구성은 이 자습서의 뒷부분에서 설명합니다.

## <a name="create-a-front-end-vm"></a>프런트 엔드 VM 만들기

[az vm create](/cli/azure/vm) 명령을 사용하여 *myPublicIPAddress* 를 사용하여 *myFrontendVM* 이라는 VM을 만듭니다.

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myFrontendVM \
  --vnet-name myVNet \
  --subnet myFrontendSubnet \
  --nsg myFrontendNSG \
  --public-ip-address myPublicIPAddress \
  --image UbuntuLTS \
  --generate-ssh-keys
```

## <a name="secure-network-traffic"></a>네트워크 트래픽 보안

NSG(네트워크 보안 그룹)에는 VNet(Azure Virtual Network)에 연결된 리소스에 대한 네트워크 트래픽을 허용하거나 거부하는 보안 규칙 목록이 포함되어 있습니다. NSG는 서브넷 또는 개별 네트워크 인터페이스에 연결할 수 있습니다. NSG가 네트워크 인터페이스에 연결되면 연결된 VM만 적용됩니다. NSG를 서브넷에 연결하면 규칙이 서브넷에 연결된 모든 리소스에 적용됩니다. 

### <a name="network-security-group-rules"></a>네트워크 보안 그룹 규칙

NSG 규칙은 트래픽이 허용되거나 거부되는 네트워킹 포트를 정의합니다. 규칙에는 원본 및 대상 IP 주소 범위가 포함될 수 있으므로 특정 시스템이나 서브넷 간의 트래픽이 제어됩니다. NSG 규칙에는 우선 순위(1-4096 사이)도 포함됩니다. 규칙은 우선 순위에 따라 평가됩니다. 우선 순위가 100인 규칙은 우선 순위가 200인 규칙보다 먼저 평가됩니다.

모든 NSG에는 기본 규칙 집합이 포함됩니다. 기본 규칙은 삭제할 수 없지만, 가장 낮은 우선순위가 할당되기 때문에 직접 만든 규칙으로 재정의할 수 있습니다.

NSG의 기본 규칙은 다음과 같습니다.

- **가상 네트워크:** 가상 네트워크에서 시작하고 끝나는 트래픽은 인바운드와 아웃바운드 방향 둘 다에서 허용됩니다.
- **인터넷:** 아웃바운드 트래픽은 허용되지만 인바운드 트래픽은 차단됩니다.
- **부하 분산 장치:** Azure의 부하 분산 장치에서 VM과 역할 인스턴스의 상태를 검색할 수 있도록 허용합니다. 부하 분산된 집합을 사용하지 않는 경우 이 규칙을 재정의할 수 있습니다.

### <a name="create-network-security-groups"></a>네트워크 보안 그룹 만들기

네트워크 보안 그룹은 [az vm create](/cli/azure/vm) 명령을 사용하여 VM과 동시에 만들 수 있습니다. 이렇게 하면 NSG가 VM 네트워크 인터페이스와 연결되고, 모든 원본에서 포트 *22* 의 트래픽을 허용하도록 NSG 규칙이 자동으로 만들어집니다. 이 자습서의 앞부분에서 프런트 엔드 NSG는 프런트 엔드 VM과 함께 자동으로 만들어졌습니다. 22 포트에 대한 NSG 규칙도 자동으로 만들어졌습니다. 

경우에 따라 기본 SSH 규칙을 만들지 않아야 하거나 NSG를 서브넷에 연결해야 하는 경우와 같이 NSG를 미리 만드는 것이 도움이 될 수 있습니다. 

[az network nsg create](/cli/azure/network/nsg) 명령을 사용하여 네트워크 보안 그룹을 만듭니다.

```azurecli-interactive 
az network nsg create --resource-group myRGNetwork --name myBackendNSG
```

NSG는 네트워크 인터페이스 대신 서브넷에 연결됩니다. 이 구성에서 해당 서브넷에 연결되는 모든 VM에서는 NSG 규칙을 상속합니다.

*myBackendSubnet* 이라는 기존 서브넷을 새 NSG로 업데이트합니다.

```azurecli-interactive 
az network vnet subnet update \
  --resource-group myRGNetwork \
  --vnet-name myVNet \
  --name myBackendSubnet \
  --network-security-group myBackendNSG
```

### <a name="secure-incoming-traffic"></a>들어오는 트래픽 보호

프런트 엔드 VM을 만들 때 22 포트에서 들어오는 트래픽을 허용하는 NSG 규칙을 만들었습니다. 이 규칙은 VM에 대한 SSH 연결을 허용합니다. 이 예제에서 트래픽은 *80* 포트에서도 허용되어야 합니다. 이 구성을 사용하면 VM에서 웹 애플리케이션에 액세스할 수 있습니다.

[az network nsg rule create](/cli/azure/network/nsg/rule) 명령을 사용하여 *80* 포트에 대한 규칙을 만듭니다.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myFrontendNSG \
  --name http \
  --access allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 80
```

프런트 엔드 VM은 *22* 포트 및 *80* 포트에서만 액세스할 수 있습니다. 다른 모든 들어오는 트래픽은 네트워크 보안 그룹에서 차단됩니다. NSG 규칙 구성을 시각화하면 도움이 될 수 있습니다. [az network rule list](/cli/azure/network/nsg/rule) 명령을 사용하여 NSG 규칙 구성을 반환합니다. 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myFrontendNSG --output table
```

### <a name="secure-vm-to-vm-traffic"></a>VM 간 트래픽 보호

네트워크 보안 그룹 규칙은 VM 간에도 적용할 수 있습니다. 이 예제에서 프런트 엔드 VM은 *22* 및 *3306* 포트에서 백 엔드 VM과 통신해야 합니다. 이 구성을 사용하면 프런트 엔드 VM에서 SSH 연결을 허용하고, 프런트 엔드 VM의 애플리케이션에서 백 엔드 MySQL 데이터베이스와 통신할 수 있습니다. 프런트 엔드 가상 머신과 백 엔드 가상 머신 간의 다른 모든 트래픽은 차단되어야 합니다.

[az network nsg rule create](/cli/azure/network/nsg/rule) 명령을 사용하여 22 포트에 대한 규칙을 만듭니다. `--source-address-prefix` 인수에서 *10.0.1.0/24* 라는 값을 지정하고 있음에 유의하세요. 이 구성은 프런트 엔드 서브넷에서 NSG를 통해서만 트래픽이 전송되도록 합니다.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name SSH \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "22"
```

이제 3306 포트에서 MySQL 트래픽에 대한 규칙을 추가합니다.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name MySQL \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "3306"
```

마지막으로, NSG에는 동일한 VNet에 있는 VM 간에 모든 트래픽을 허용하는 기본 규칙이 있으므로 백 엔드 NSG에서 모든 트래픽을 차단하는 규칙을 만들 수 있습니다. `--priority`에는 *300* 이라는 값이 지정되며, 이 값은 해당 NSG와 MySQL 규칙 모두에 비해 더 낮습니다. 이 구성은 SSH 및 MySQL 트래픽이 여전히 NSG를 통해 허용되도록 합니다.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name denyAll \
  --access Deny \
  --protocol Tcp \
  --direction Inbound \
  --priority 300 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "*"
```

## <a name="create-back-end-vm"></a>백 엔드 VM 만들기

이제 *myBackendSubnet* 에 연결된 가상 머신을 만듭니다. `--nsg` 인수에는 빈 큰따옴표로 묶은 값이 있어야 합니다. VM과 함께 NSG를 만들 필요가 없습니다. VM은 미리 만든 백 엔드 NSG로 보호되는 백 엔드 서브넷에 연결됩니다. 이 NSG는 VM에 적용됩니다. 또한 `--public-ip-address` 인수에도 빈 큰따옴표로 묶은 값이 있어야 합니다. 이 구성은 공용 IP 주소 없이 VM을 만듭니다. 

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myBackendVM \
  --vnet-name myVNet \
  --subnet myBackendSubnet \
  --public-ip-address "" \
  --nsg "" \
  --image UbuntuLTS \
  --generate-ssh-keys
```

백엔드 VM은 프런트 엔드 서브넷의 *22* 포트 및 *3306* 포트에서만 액세스할 수 있습니다. 다른 모든 들어오는 트래픽은 네트워크 보안 그룹에서 차단됩니다. NSG 규칙 구성을 시각화하면 도움이 될 수 있습니다. [az network rule list](/cli/azure/network/nsg/rule) 명령을 사용하여 NSG 규칙 구성을 반환합니다. 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myBackendNSG --output table
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 가상 머신과 관련된 Azure Networks를 만들고 보호했습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 가상 네트워크 및 서브넷 만들기
> * 공용 IP 주소 만들기
> * 프런트 엔드 VM 만들기
> * 네트워크 트래픽 보안
> * 백 엔드 VM 만들기

VM 디스크를 보호하는 방법에 대한 자세한 내용은 [디스크의 백업 및 재해 복구](../backup-and-disaster-recovery-for-azure-iaas-disks.md)를 참조하세요.