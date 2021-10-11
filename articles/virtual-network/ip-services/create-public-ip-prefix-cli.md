---
title: '빠른 시작: 공용 IP 주소 접두사 만들기 - Azure CLI'
titlesuffix: Azure Virtual Network
description: Azure CLI를 사용하여 공용 IP 주소 접두사를 만드는 방법을 알아봅니다.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: quickstart
ms.date: 10/01/2021
ms.author: allensu
ms.openlocfilehash: 0b72eb4e70fb80602b352c32d6ffbde1cf4336da
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369184"
---
# <a name="quickstart-create-a-public-ip-address-prefix-using-the-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 공용 IP 주소 접두사 만들기

공용 IP 주소 접두사 및 해당 IP 주소를 생성, 변경 및 삭제하는 방법에 대해 알아봅니다. 공용 IP 주소 접두사는 표준 SKU 공용 IP 주소의 연속 범위입니다. 

공용 IP 주소 리소스를 만들 때 접두사에서 고정 공용 IP 주소를 할당하고 주소를 가상 머신, 부하 분산 장치 또는 기타 리소스에 연결할 수 있습니다. 자세한 내용은 [공용 IP 주소 접두사 개요](public-ip-address-prefix.md)를 참조하세요.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 이 자습서에는 Azure CLI 버전 2.0.28 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

**eastus2** 위치에 **QuickStartCreateIPPrefix-rg** 라는 [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다.

```azurecli-interactive
  az group create \
    --name QuickStartCreateIPPrefix-rg \
    --location eastus2
```

## <a name="create-a-public-ip-address-prefix"></a>공용 IP 주소 접두사 만들기

이 섹션에서는 Azure PowerShell을 사용하여 영역 중복, 영역 및 비 영역 공용 IP 접두사를 만듭니다. 

예제의 접두사는 다음과 같습니다.

* **IPv4** - /28(16개 주소)

* **IPv6** - /124(16개 주소)

사용 가능한 접두사 크기에 대한 자세한 내용은 [접두사 크기](public-ip-address-prefix.md#prefix-sizes)를 참조하세요.

**myPublicIpPrefix** 라는 [az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_create)를 사용하여 공용 IP 접두사를 **eastus2** 위치에 만듭니다.

## <a name="ipv4"></a>IPv4

# <a name="zone-redundant-ipv4-prefix"></a>[**영역 중복 IPv4 접두사**](#tab/ipv4-zone-redundant)

IPv4 공용 IP 접두사를 만들려면 **`--version`** 매개 변수에 **IPv4** 를 입력합니다. 영역 중복 IPv4 접두사를 만들려면 **`--zone`** 매개 변수에 **1,2,3** 을 입력합니다.

```azurecli-interactive
  az network public-ip prefix create \
    --length 28 \
    --name myPublicIpPrefix \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv4 \
    --zone 1 2 3
```

# <a name="zonal-ipv4-prefix"></a>[**영역 IPv4 접두사**](#tab/ipv4-zonal)

IPv4 공용 IP 접두사를 만들려면 **`--version`** 매개 변수에 **IPv4** 를 입력합니다. **`--zone`** 매개 변수에 **2** 를 입력하여 zone 2에 영역 IP 접두사를 만듭니다.

```azurecli-interactive
  az network public-ip prefix create \
    --length 28 \
    --name myPublicIpPrefix-zonal \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv4 \
    --zone 2
```

>[!NOTE]
>위의 영역 옵션은 [가용성 영역](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)이 있는 지역에서만 유효한 옵션입니다.

# <a name="non-zonal-ipv4-prefix"></a>[**비 영역 IPv4 접두사**](#tab/ipv4-non-zonal)

IPv4 공용 IP 접두사를 만들려면 **`--version`** 매개 변수에 **IPv4** 를 입력합니다. **`--zone`** 매개 변수를 제거하여 비영역 IP 접두사를 만듭니다.

```azurecli-interactive
  az network public-ip prefix create \
    --length 28 \
    --name myPublicIpPrefix-nozone \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv4
```

명령에서 **`--zone`** 매개 변수를 제거하는 것은 모든 지역에서 유효합니다.  

**`--zone`** 매개 변수를 제거하는 것은 [가용성 영역](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)이 없는 지역에서 표준 공용 IP 주소에 대한 기본 선택 항목입니다.

---



## <a name="ipv6"></a>IPv6

# <a name="zone-redundant-ipv6-prefix"></a>[**영역 중복 IPv6 접두사**](#tab/ipv6-zone-redundant)

IPv4 공용 IP 접두사를 만들려면 **`--version`** 매개 변수에 **IPv6** 를 입력합니다. 영역 중복 IPv6 접두사를 만들려면 **`--zone`** 매개 변수에 **1,2,3** 을 입력합니다.

```azurecli-interactive
  az network public-ip prefix create \
    --length 124 \
    --name myPublicIpPrefix \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv6 \
    --zone 1 2 3
```

# <a name="zonal-ipv6-prefix"></a>[**영역 IPv6 접두사**](#tab/ipv6-zonal)

IPv6 공용 IP 접두사를 만들려면 **`--version`** 매개 변수에 **IPv6** 를 입력합니다. **`--zone`** 매개 변수에 **2** 를 입력하여 zone 2에 영역 IP 접두사를 만듭니다.

```azurecli-interactive
  az network public-ip prefix create \
    --length 124 \
    --name myPublicIpPrefix-zonal \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv6 \
    --zone 2
```

>[!NOTE]
>위의 영역 옵션은 [가용성 영역](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)이 있는 지역에서만 유효한 옵션입니다.

# <a name="non-zonal-ipv6-prefix"></a>[**비 영역 IPv6 접두사**](#tab/ipv6-non-zonal)

IPv6 공용 IP 접두사를 만들려면 **`--version`** 매개 변수에 **IPv6** 를 입력합니다. **`--zone`** 매개 변수를 제거하여 비영역 IP 접두사를 만듭니다.

```azurecli-interactive
  az network public-ip prefix create \
    --length 124 \
    --name myPublicIpPrefix-nozone \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv6
```

명령에서 **`--zone`** 매개 변수를 제거하는 것은 모든 지역에서 유효합니다.  

**`--zone`** 매개 변수를 제거하는 것은 [가용성 영역](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)이 없는 지역에서 표준 공용 IP 주소에 대한 기본 선택 항목입니다.

---

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>접두사로 고정 공용 IP 주소 만들기

접두사를 만들었으면 접두사로 고정 IP 주소를 만들어야 합니다. 이 섹션에서는 앞서 만든 접두사에서 고정 IP 주소를 만듭니다.

**myPublicIpPrefix** 접두사에서 [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)를 사용하여 공용 IP 주소를 만듭니다.

# <a name="ipv4-address"></a>[**IPv4 주소**](#tab/ipv4-address)

IPv4 공용 IP 주소를 만들려면 **`--version`** 매개 변수에 **IPv4** 를 입력합니다.

```azurecli-interactive
  az network public-ip create \
    --name myPublicIP \
    --resource-group QuickStartCreateIPPrefix-rg \
    --allocation-method Static \
    --public-ip-prefix myPublicIpPrefix \
    --sku Standard \
    --version IPv4
```

# <a name="ipv6-address"></a>[**IPv6 주소**](#tab/ipv6-address)

IPv6 공용 IP 접두사를 만들려면 **`--version`** 매개 변수에 **IPv6** 를 입력합니다.

```azurecli-interactive
  az network public-ip create \
    --name myPublicIP \
    --resource-group QuickStartCreateIPPrefix-rg \
    --allocation-method Static \
    --public-ip-prefix myPublicIpPrefix \
    --sku Standard \
    --version IPv6
```

---

>[!NOTE]
>표준 SKU로 만든 고정 공용 IP 주소만 접두사의 범위에서 할당할 수 있습니다. 공용 IP 주소 SKU에 대해 자세히 알아보려면 [공용 IP 주소](public-ip-addresses.md#public-ip-addresses)를 참조하세요.

## <a name="delete-a-prefix"></a>접두사 삭제

이 섹션에서는 접두사를 삭제하는 방법을 알아봅니다.

공용 IP 접두사를 삭제하려면 [az network public-ip prefix delete](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_delete)를 사용합니다.

```azurecli-interactive
  az network public-ip prefix delete \
    --name myPublicIpPrefix \
    --resource-group QuickStartCreateIPPrefix-rg
```

>[!NOTE]
>접두사 내 주소가 공용 IP 주소 리소스에 연결되어 있으면 공용 IP 주소 리소스를 먼저 삭제해야 합니다. [공용 IP 주소 삭제](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address)를 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서는 공용 IP 접두사와 해당 접두사에서 공용 IP를 만들었습니다. 


공용 IP 접두사 작업을 마쳤으면 [az group delete](/cli/azure/group#az_group_delete)를 사용하여 리소스 그룹과 리소스 그룹에 포함된 모든 리소스를 삭제합니다.

```azurecli-interactive
  az group delete \
    --name QuickStartCreateIPPrefix-rg
```

## <a name="next-steps"></a>다음 단계

Azure PowerShell을 사용하여 공용 IP 접두사를 만드는 방법을 알아보려면 다음 문서로 계속 진행하세요.
> [!div class="nextstepaction"]
> [Azure CLI를 사용하여 공용 IP 만들기](create-public-ip-cli.md)
