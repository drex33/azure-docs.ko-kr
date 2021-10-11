---
title: '빠른 시작: 공용 IP 만들기 - Azure CLI'
titleSuffix: Azure Virtual Network
description: Azure CLI를 사용하여 공용 IP를 만드는 방법 알아보기
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: quickstart
ms.date: 10/01/2021
ms.author: allensu
ms.openlocfilehash: 8d8b1525c910592cb9cc35ae1e08da7e9e24e30b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369401"
---
# <a name="quickstart-create-a-public-ip-address-using-the-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 공용 IP 주소 만들기

이 빠른 시작에서는 Azure 공용 IP 주소를 만드는 방법을 알아봅니다. Azure의 공용 IP 주소는 Azure 리소스에 대한 공용 연결에 사용됩니다. 공용 IP 주소는 기본 및 표준의 두 가지 SKU로 제공됩니다. 공용 IP 주소의 두 계층(지역 및 전역)을 사용할 수 있습니다. 공용 IP 주소의 라우팅 기본 설정은 생성 시 설정됩니다. 인터넷 라우팅 및 Microsoft 네트워크 라우팅은 사용 가능한 선택 항목입니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 이 자습서에는 Azure CLI 버전 2.0.28 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

**eastus2** 위치에 **QuickStartCreateIP-rg** 라는 [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다.

```azurecli-interactive
  az group create \
    --name QuickStartCreateIP-rg \
    --location eastus2
```

# <a name="standard-sku"></a>[**표준 SKU**](#tab/create-public-ip-standard)

>[!NOTE]
>표준 SKU 공용 IP는 프로덕션 워크로드용으로 권장됩니다.  SKU에 대한 자세한 내용은 **[공용 IP 주소](public-ip-addresses.md)** 를 참조하세요.
>
>다음 명령은 API 버전 **2020-08-01** 또는 **이상** 에서 작동합니다.  현재 사용 중인 API 버전에 대한 자세한 내용은 [리소스 공급자 및 형식](../../azure-resource-manager/management/resource-providers-and-types.md)을 참조하세요.

[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) 명령을 사용하여 **QuickStartCreateIP-rg** 에서 **myStandardPublicIP** 라는 표준 영역 중복 공용 IPv4 주소를 만듭니다.  

IPv6 주소를 만들려면 **`--version`** 매개 변수를 **IPv6** 로 수정합니다.

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rg \
    --name myStandardPublicIP \
    --version IPv4 \
    --sku Standard \
    --zone 1 2 3
```
> [!IMPORTANT]
> 2020-08-01 이전 API 버전의 경우 **`--zone`** 매개 변수를 지정하지 않고 명령을 실행하여 영역 중복 IP 주소를 만듭니다. 
>

# <a name="basic-sku"></a>[**기본 SKU**](#tab/create-public-ip-basic)

이 섹션에서는 기본 IP를 만듭니다. 기본 공용 IP는 가용성 영역을 지원하지 않습니다.

[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)를 사용하여 **QuickStartCreateIP-rg** 에서 **myBasicPublicIP** 라는 기본 고정 공용 IPv4 주소를 만듭니다.

IPv6 주소를 만들려면 **`--version`** 매개 변수를 **IPv6** 로 수정합니다. 

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rg \
    --name myBasicPublicIP \
    --version IPv4 \
    --sku Basic \
    --allocation-method Static
```
시간에 따라 IP 주소를 변경할 수 있는 경우 **`--allocation-method`** 를 **동적** 으로 변경하여 **동적** IP 할당을 선택할 수 있습니다. 

>[!NOTE]
> 기본 IPv6 주소는 항상 '동적'이어야 합니다.

---

## <a name="create-a-zonal-or-no-zone-ip-address"></a>영역 또는 비영역 IP 주소 만들기

이 섹션에서는 영역 또는 비영역 공용 IP 주소를 만드는 방법을 알아봅니다.

# <a name="zonal"></a>[**영역**](#tab/create-public-ip-zonal)

**QuickStartCreateIP-rg** 에서 **myStandardPublicIP** 라는 영역 2에 표준 영역 공용 IPv4 주소를 만들려면 다음 명령을 사용합니다.

IPv6 주소를 만들려면 **`--version`** 매개 변수를 **IPv6** 로 수정합니다.

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rgLB \
    --name myStandardPublicIP-zonal \
    --version IPv4 \
    --sku Standard \
    --zone 2
```

>[!NOTE]
>위의 영역 옵션은 [가용성 영역](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)이 있는 지역에서만 유효한 옵션입니다.

# <a name="non-zonal"></a>[**비영역**](#tab/create-public-ip-non-zonal)

이 섹션에서는 비영역 IP 주소를 만듭니다.  

>[!NOTE]
>다음 명령은 API 버전 2020-08-01이상에 대해 작동합니다.  현재 사용 중인 API 버전에 대한 자세한 내용은 [리소스 공급자 및 형식](../../azure-resource-manager/management/resource-providers-and-types.md)을 참조하세요.

[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)를 사용하여 **QuickStartCreateIP-rg** 에서 **myStandardPublicIP-nozone** 이라는 비영역 리소스로 표준 공용 IPv4 주소를 만듭니다. 

IPv6 주소를 만들려면 **`--version`** 매개 변수를 **IPv6** 로 수정합니다.

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rg \
    --name myStandardPublicIP-nozone \
    --version IPv4 \
    --sku Standard
```
명령에서 **`--zone`** 매개 변수를 제거하는 것은 모든 지역에서 유효합니다.  

**`--zone`** 매개 변수를 제거하는 것은 [가용성 영역](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)이 없는 지역에서 표준 공용 IP 주소에 대한 기본 선택 항목입니다.

---

## <a name="routing-preference-and-tier"></a>라우팅 기본 설정 및 계층

표준 SKU 정적 공용 IPv4 주소는 라우팅 기본 설정 또는 글로벌 계층 기능을 지원합니다.

# <a name="routing-preference"></a>[**라우팅 기본 설정**](#tab/routing-preference)

기본적으로 공용 IP 주소에 대한 라우팅 기본 설정은 사용자에게 Microsoft의 글로벌 광역 네트워크를 통해 트래픽을 전달하는 "Microsoft 네트워크"로 설정됩니다.  

**인터넷** 을 선택하면 전송 ISP 네트워크를 사용하여 비용 최적화 속도로 트래픽을 전달하는 대신 Microsoft 네트워크에서의 이동이 최소화됩니다.  

라우팅 기본 설정에 대한 자세한 내용은 [라우팅 기본 설정(미리 보기)이란?](routing-preference-overview.md)을 참조하세요.

이 명령은 **인터넷** 형식의 라우팅 기본 설정을 통해 새 표준 영역 중복 공용 IPv4 주소를 만듭니다.

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rg \
    --name myStandardPublicIP-RP \
    --version IPv4 \
    --ip-tags 'RoutingPreference=Internet' \
    --sku Standard \
    --zone 1 2 3
```

# <a name="tier"></a>[**서비스 계층**](#tab/tier)

공용 IP 주소는 단일 지역과 연결됩니다. **글로벌** 계층은 여러 지역에 걸쳐 IP 주소를 확장합니다. 지역 간 부하 분산 장치의 프런트 엔드에는 **글로벌** 계층이 필요합니다.  

자세한 내용은 [지역 간 부하 분산 장치](../../load-balancer/cross-region-overview.md)를 참조하세요.

다음 명령은 글로벌 IPv4 주소를 만듭니다. 이 주소는 지역 간 부하 분산 장치의 프런트 엔드와 연결될 수 있습니다.

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rg \
    --name myStandardPublicIP-Global \
    --version IPv4 \
    --tier global \
    --sku Standard \
```
>[!NOTE]
>글로벌 계층 주소는 가용성 영역을 지원하지 않습니다.

---

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않으려면 다음 단계에 따라 공용 IP 주소를 삭제합니다.

1. 포털 맨 위에 있는 검색 상자에 **리소스 그룹** 을 입력합니다.

2. 검색 결과에서 **리소스 그룹** 을 선택합니다.

3. **QuickStartCreateIP-rg** 를 선택합니다.

4. **리소스 그룹 삭제** 를 선택합니다.

5. **리소스 그룹 이름 입력** 에 대해 **QuickStartCreateIP-rg** 를 입력하고 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

공용 IP 접두사를 만드는 방법을 알아보려면 다음 문서로 계속 진행하세요.
> [!div class="nextstepaction"]
> [Azure CLI를 사용하여 공용 IP 접두사 만들기](create-public-ip-prefix-cli.md)
