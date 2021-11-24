---
title: Azure Bastion 구성 설정 정보
description: Azure Bastion에 대해 사용 가능한 구성 설정을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/12/2021
ms.author: cherylmc
ms.custom: ignite-fall-2021
ms.openlocfilehash: c4d141639bf9a377a684161ff47a49d8e5bd0187
ms.sourcegitcommit: 1aeff9f012cfd868104ef0159c5204e402d75696
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133030135"
---
# <a name="about-bastion-configuration-settings"></a>Bastion 구성 설정 정보

이 문서의 섹션에서는 Azure Bastion에 대한 리소스 및 설정을 설명합니다.

## <a name="skus"></a><a name="skus"></a>SKU

SKU는 계층이라고도 합니다. Azure Bastion은 기본 및 표준의 두 가지 SKU 유형을 지원합니다. SKU는 Bastion을 구성할 때 워크플로 중에 Azure Portal에서 구성됩니다. [기본 SKU를 표준 SKU로 업그레이드](#upgradesku)할 수 있습니다.

* **기본 SKU** 는 기본 기능을 제공하여 Azure Bastion이 대상 애플리케이션 VM의 공용 IP 주소를 노출하지 않고 VM(가상 머신)에 대한 RDP/SSH 연결을 관리할 수 있도록 합니다.
* 표준 SKU를 사용하면 Azure Bastion에서 더 큰 규모로 원격 연결을 관리할 수 있는 프리미엄 기능을 사용할 수 있습니다.

다음 표에서는 기능 및 해당 SKU를 보여 줍니다. 

[!INCLUDE [Azure Bastion SKUs](../../includes/bastion-sku.md)]

### <a name="configuration-methods"></a>구성 방법

현재 표준 SKU를 지정 하려면 Azure Portal를 사용 해야 합니다. Azure CLI 또는 Azure PowerShell을 사용하여 Bastion을 구성하는 경우에는 SKU를 지정할 수 없으며 기본 SKU가 기본값입니다.

| 메서드 | 값 | 링크 |
| --- | --- | --- |
| Azure portal | 계층 - 기본 또는 <br>표준 | [빠른 시작 - VM 설정에서 Bastion 구성](quickstart-host-portal.md)<br>[자습서 - Bastion 구성](tutorial-create-host-portal.md) |
| Azure PowerShell | 기본 전용 - 설정 없음 |[Bastion 구성 - PowerShell](bastion-create-host-powershell.md) |
| Azure CLI |  기본 전용 - 설정 없음 | [Bastion 구성 - CLI](create-host-cli.md) |

### <a name="upgrade-a-sku"></a><a name="upgradesku"></a>SKU 업그레이드

Azure Bastion은 기본 SKU에서 표준 SKU로의 업그레이드를 지원합니다.

> [!NOTE]
> 표준 SKU에서 기본 SKU로의 다운그레이드는 지원되지 않습니다. 다운그레이드하려면 Azure Bastion을 삭제한 후 다시 만들어야 합니다.
>

#### <a name="configuration-methods"></a>구성 방법

다음 방법을 사용하여 이 설정을 구성할 수 있습니다.

| 메서드 | 값 | 링크 |
| --- | --- | --- |
| Azure portal |서비스 계층  | [SKU 업그레이드](upgrade-sku.md)|

## <a name="instances-and-host-scaling"></a><a name="instance"></a>인스턴스 및 호스트 크기 조정

인스턴스는 Azure Bastion을 구성할 때 생성되는 최적화된 Azure VM입니다. Azure에서 완전히 관리되며 Azure Bastion에 필요한 모든 프로세스를 실행합니다. 인스턴스를 배율 단위라고도 합니다. Azure Bastion 인스턴스를 통해 클라이언트 VM에 연결합니다. 기본 SKU를 사용하여 Azure Bastion을 구성하면 2개의 인스턴스가 생성됩니다. 표준 SKU를 사용하는 경우 인스턴스 수를 지정할 수 있습니다. 이를 **호스트 크기 조정** 이라고 합니다. 

각 인스턴스는 10~12개의 동시 RDP/SSH 연결을 지원할 수 있습니다. 인스턴스당 연결 수는 클라이언트 VM에 연결할 때 수행하는 작업에 따라 다릅니다. 예를 들어 데이터 집약적인 작업을 수행하는 경우 인스턴스가 처리할 더 큰 로드가 생성됩니다. 동시 세션을 초과하면 추가 배율 단위(인스턴스)가 필요합니다. 

인스턴스는 AzureBastionSubnet에서 생성됩니다. 호스트 크기 조정을 허용 하려면 AzureBastionSubnet는/26 이상 이어야 합니다. 더 작은 서브넷을 사용하면 생성할 수 있는 인스턴스 수가 제한됩니다. AzureBastionSubnet에 대한 자세한 내용은 이 문서의 [서브넷](#subnet) 섹션을 참조하세요.

### <a name="configuration-methods"></a>구성 방법

다음 방법을 사용하여 이 설정을 구성할 수 있습니다.

| 메서드 | 값 | 링크 |
| --- | --- | --- |
| Azure portal |인스턴트 수  | [호스트 크기 조정 구성](configure-host-scaling.md)|


## <a name="azure-bastion-subnet"></a><a name="subnet"></a>Azure Bastion 서브넷

 >[!IMPORTANT]
 >2021 년 11 월 2 일 이후에 배포 되는 Azure 방호 리소스의 경우 최소 AzureBastionSubnet 크기는/26 이상 (/25,/24 등)입니다. 이 날짜 이전에 크기/27의 서브넷에 배포 된 모든 Azure 방호 리소스는이 변경의 영향을 받지 않으며 계속 작동 하지만, 나중에 [호스트 크기 조정을](./configure-host-scaling.md) 사용 하도록 선택 하는 경우 기존 AzureBastionSubnet의 크기를/26으로 늘릴 것을 권장 합니다.
 >

Azure Bastion에는 전용 서브넷 **AzureBastionSubnet** 이 필요합니다. 이 서브넷은 Azure Bastion이 배포된 동일한 가상 네트워크에서 만들어야 합니다. 서브넷에는 다음 구성이 있어야 합니다.

* 서브넷 이름은 *AzureBastionSubnet* 이어야 합니다.
* 서브넷 크기는/26 이상 (/25,/24 등) 이어야 합니다.
* 호스트 크기 조정을 위해 26 이상의 서브넷이 권장됩니다. 더 작은 서브넷 공간을 사용하면 배율 단위 수가 제한됩니다. 자세한 내용은 이 문서의 [호스트 크기 조정](#instance) 섹션을 참조하세요.
* 서브넷은 베스천 호스트와 동일한 VNet 및 리소스 그룹에 있어야 합니다.
* 서브넷은 추가 리소스를 포함할 수 없습니다.

### <a name="configuration-methods"></a>구성 방법

다음과 같은 방법으로 이 설정을 구성할 수 있습니다.

| 메서드 | 값 | 링크 |
| --- | --- |--- |
| Azure portal | 서브넷  |[빠른 시작 - VM 설정에서 Bastion 구성](quickstart-host-portal.md)<br>[자습서 - Bastion 구성](tutorial-create-host-portal.md)|
| Azure PowerShell | -subnetName|[cmdlet](/powershell/module/az.network/new-azbastion#parameters) |
| Azure CLI |  --subnet-name | [command](/cli/azure/network/vnet#az_network_vnet_create) |

## <a name="public-ip-address"></a><a name="public-ip"></a>공용 IP 주소

Azure Bastion에는 공용 IP 주소가 필요합니다. 공용 IP에는 다음 구성이 있어야 합니다.

* 공용 IP 주소 SKU는 **표준** 이어야 합니다.
* 공용 IP 주소 할당 방법은 **고정** 이어야 합니다.
* 공용 IP 주소 이름은 이 공용 IP 주소를 참조하는 리소스 이름입니다.
* Azure Bastion에서 요구하는 기준을 충족하고 아직 사용하지 않는 한, 이미 만든 공용 IP 주소를 사용하도록 선택할 수 있습니다.

### <a name="configuration-methods"></a>구성 방법

다음과 같은 방법으로 이 설정을 구성할 수 있습니다.

| 메서드 | 값 | 링크 |
| --- | --- |--- |
| Azure portal | 공용 IP 주소 |[Azure Portal](https://portal.azure.com)|
| Azure PowerShell | -PublicIpAddress| [cmdlet](/powershell/module/az.network/new-azbastion#parameters)  |
| Azure CLI | --public-ip create |[command](/cli/azure/network/public-ip)
|

## <a name="next-steps"></a>다음 단계

자주 묻는 질문은 [Azure Bastion FAQ](bastion-faq.md)를 참조하세요.
