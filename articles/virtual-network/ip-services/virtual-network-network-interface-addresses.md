---
title: Azure 네트워크 인터페이스용 IP 주소 구성 | Microsoft 문서
description: 네트워크 인터페이스용 개인 및 공용 IP 주소를 추가, 변경 및 제거하는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2020
ms.author: allensu
ms.openlocfilehash: c054d22edfa69d3564af988d18bf40db283ee651
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133434770"
---
# <a name="configure-ip-addresses-for-an-azure-network-interface"></a>Azure 네트워크 인터페이스용 IP 주소 구성

가상 컴퓨터의 NIC (네트워크 인터페이스 컨트롤러)에 개인 및 공용 IP 주소를 할당할 수 있습니다.  가상 머신은 네트워크 인터페이스에 할당된 개인 IP 주소를 통해 Azure Virtual Network 및 연결된 네트워크의 다른 리소스와 통신할 수 있습니다. 또한 개인 IP 주소가 있으면 예측할 수 없는 IP 주소를 사용하는 인터넷으로의 아웃바운드 통신도 가능합니다. 네트워크 인터페이스에 할당 된 [공용 IP 주소](virtual-network-public-ip-address.md) 를 사용 하면 인터넷에서 가상 컴퓨터에 대 한 인바운드 통신을 사용 하도록 설정 하 고 예측 가능한 IP 주소를 사용 하 여 가상 컴퓨터에서 인터넷으로의 아웃 바운드 통신을 사용할 수 있습니다. 자세한 내용은 [Azure에서 아웃바운드 연결 이해](../../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.

네트워크 인터페이스를 만들거나 변경하거나 삭제해야 하는 경우 [네트워크 인터페이스 관리](../../virtual-network/virtual-network-network-interface.md) 문서를 확인하세요. 네트워크 인터페이스를 추가하거나 가상 머신에서 네트워크 인터페이스를 제거해야 하는 경우에는 [네트워크 인터페이스 추가 또는 제거](../../virtual-network/virtual-network-network-interface-vm.md) 문서를 확인하세요.

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

이 문서에서 설명하는 모든 섹션의 단계를 수행하기 전에 다음 작업을 완료해야 합니다.

- 아직 Azure 계정이 없으면 [평가판 계정](https://azure.microsoft.com/free)에 등록합니다.
- 포털을 사용하는 경우 https://portal.azure.com을 열고 Azure 계정으로 로그인합니다.
- 이 문서의 작업을 완료하기 위해 PowerShell 명령을 사용하는 경우 [Azure Cloud Shell](https://shell.azure.com/powershell)에서 명령을 실행하거나 컴퓨터에서 PowerShell을 실행합니다. Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 이 자습서에는 Azure PowerShell 모듈 버전 1.0.0 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.
- 이 문서의 작업을 완료하기 위해 Azure CLI(명령줄 인터페이스)를 사용하는 경우 [Azure Cloud Shell](https://shell.azure.com/bash)에서 명령을 실행하거나 컴퓨터에서 CLI를 실행합니다. 이 자습서에는 Azure CLI 버전 2.0.31 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 또한 Azure CLI를 로컬로 실행하는 경우 `az login`를 실행하여 Azure와 연결해야 합니다.

Azure에 로그인하거나 연결할 때 사용하는 계정이 [네트워크 인터페이스 권한](../../virtual-network/virtual-network-network-interface.md#permissions)에 나열된 적절한 작업이 할당된 [사용자 지정 역할](../../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)이나 [네트워크 기여자](../../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할에 할당되어야 합니다.

## <a name="add-ip-addresses"></a>IP 주소 추가

[Azure 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) 문서에 나열된 제한 내에서 필요한 만큼 많은 [프라이빗](#private) 및 [공용](#public) [IPv4](#ipv4) 주소를 네트워크 인터페이스에 추가할 수 있습니다. 기존 네트워크 인터페이스에 대한(기존 보조 IP 구성이 없는 경우) [보조 IP 구성](#secondary) 하나에 개인 IPv6 주소를 추가할 수 있습니다. 각 네트워크 인터페이스는 IPv6 개인 주소가 최대 하나만 있을 수 있습니다. 필요에 따라 IPv6 네트워크 인터페이스 구성에 공용 IPv6 주소를 추가할 수 있습니다. IPv6 주소 사용에 대한 자세한 내용은 [IPv6](#ipv6)을 참조하세요.

1. Azure Portal 위쪽의 *리소스 검색* 텍스트가 있는 상자에서 *네트워크 인터페이스* 를 입력합니다. 검색 결과에 표시된 **네트워크 인터페이스** 를 선택합니다.
2. 목록에서 IPv4 주소를 추가하려는 네트워크 인터페이스를 선택합니다.
3. **설정** 에서 **IP 구성** 을 선택합니다.
4. **IP 구성** 에서 **+ 추가** 를 선택합니다.
5. 다음을 지정한 다음, **확인** 을 선택합니다.

   |설정|필수 여부|세부 정보|
   |---|---|---|
   |이름|예|네트워크 인터페이스에 대해 고유해야 합니다.|
   |유형|예|기존 네트워크 인터페이스에 IP 구성을 추가할 것이며, 각 NIC에는 [기본](#primary) IP 구성이 있어야 하므로 **보조** 옵션만 선택 가능합니다.|
   |개인 IP 주소 할당 방법|예|[**동적**](#dynamic): Azure는 네트워크 인터페이스가 배포된 서브넷 주소 범위에 대해 사용 가능한 다음 주소를 할당합니다. [**정적**](#static): 네트워크 인터페이스가 배포된 서브넷 주소 범위에 대해 사용되지 않는 주소를 할당합니다.|
   |공용 IP 주소|예|**사용 안 함:** 현재 공용 IP 주소 리소스는 IP 구성과 연결되지 않습니다. **사용:** 기존 IPv4 공용 IP 주소를 선택하거나 새 공용 IP 주소를 만듭니다. 공용 IP 주소를 만드는 방법에 대한 자세한 내용은 [공용 IP 주소](virtual-network-public-ip-address.md#create-a-public-ip-address) 문서를 참조하세요.|
6. [가상 머신 운영 체제에 여러 IP 주소 할당](virtual-network-multiple-ip-addresses-portal.md#os-config) 문서의 지침을 수행하여 가상 머신 운영 체제에 보조 개인 IP 주소를 수동으로 추가합니다. 가상 머신 운영 체제에 IP 주소를 수동으로 추가하기 전에 고려해야 하는 특수한 사항은 [프라이빗](#private) IP 주소를 참조하세요. 가상 머신 운영 체제에는 공용 IP 주소를 추가하지 마세요.

**명령**

|도구|명령|
|---|---|
|CLI|[az network nic ip-config create](/cli/azure/network/nic/ip-config)|
|PowerShell|[Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/add-aznetworkinterfaceipconfig)|

## <a name="change-ip-address-settings"></a>IP 주소 설정 변경

IPv4 주소의 할당 방법을 변경하거나, 고정 IPv4 주소를 변경하거나, 네트워크 인터페이스에 할당된 공용 IP 주소를 변경해야 할 수 있습니다. 가상 머신의 보조 네트워크 인터페이스와 연결된 보조 IP 구성의 프라이빗 IPv4 주소를 변경하는 경우(자세한 정보: [기본 및 보조 네트워크 인터페이스](../../virtual-network/virtual-network-network-interface-vm.md)) 다음 단계를 수행하기 전에 가상 머신을 중지됨(할당 취소됨) 상태로 전환합니다.

1. Azure Portal 위쪽의 *리소스 검색* 텍스트가 있는 상자에서 *네트워크 인터페이스* 를 입력합니다. 검색 결과에 표시된 **네트워크 인터페이스** 를 선택합니다.
2. 목록에서 IP 주소 설정을 보거나 변경하려는 네트워크 인터페이스를 선택합니다.
3. **설정** 에서 **IP 구성** 을 선택합니다.
4. 목록에서 수정하려는 IP 구성을 선택합니다.
5. [IP 구성 추가](#add-ip-addresses)의 5단계에서 설정에 대한 정보를 사용하여 원하는 대로 설정을 변경합니다.
6. **저장** 을 선택합니다.

>[!NOTE]
>기본 네트워크 인터페이스에 여러 IP 구성이 있는 상태에서 기본 IP 구성의 개인 IP 주소를 변경하는 경우에는 Windows 내에서 기본 및 보조 IP 주소를 네트워크 인터페이스에 수동으로 다시 할당해야 합니다(Linux에서는 필요 없음). 운영 체제 내에서 IP 주소를 네트워크 인터페이스에 수동으로 할당하려면 [가상 머신에 여러 IP 주소 할당](virtual-network-multiple-ip-addresses-portal.md#os-config)을 참조하세요. 가상 머신 운영 체제에 IP 주소를 수동으로 추가하기 전에 고려해야 하는 특수한 사항은 [개인](#private) IP 주소를 참조하세요. 가상 머신 운영 체제에는 공용 IP 주소를 추가하지 마세요.

**명령**

|도구|명령|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="remove-ip-addresses"></a>IP 주소 제거

네트워크 인터페이스에서 [프라이빗](#private) 및 [공용](#public) IP 주소를 제거할 수는 있지만, 네트워크 인터페이스에는 항상 프라이빗 IPv4 주소가 하나 이상 할당되어 있어야 합니다.

1. Azure Portal 위쪽의 *리소스 검색* 텍스트가 있는 상자에서 *네트워크 인터페이스* 를 입력합니다. 검색 결과에 표시된 **네트워크 인터페이스** 를 선택합니다.
2. 목록에서 IP 주소를 제거하려는 네트워크 인터페이스를 선택합니다.
3. **설정** 에서 **IP 구성** 을 선택합니다.
4. 삭제하려는 [보조](#secondary) IP 구성([기본](#primary) 구성은 삭제할 수 없음)을 마우스 오른쪽 단추로 선택하고 **삭제** 를 클릭한 다음, **예** 를 선택하여 삭제를 확인합니다. 구성에 연결된 공용 IP 주소 리소스가 있는 경우 해당 리소스는 IP 구성과 분리되지만 삭제되지는 않습니다.

**명령**

|도구|명령|
|---|---|
|CLI|[az network nic ip-config delete](/cli/azure/network/nic/ip-config)|
|PowerShell|[Remove-AzNetworkInterfaceIpConfig](/powershell/module/az.network/remove-aznetworkinterfaceipconfig)|

## <a name="ip-configurations"></a>IP 구성

[프라이빗](#private) 및 [공용](#public)(선택 사항) IP 주소는 네트워크 인터페이스에 할당되어 있는 하나 이상의 IP 구성에 할당됩니다. IP 구성에는 다음과 같은 두 종류가 있습니다.

### <a name="primary"></a>기본

각 네트워크 인터페이스에는 기본 IP 구성 하나가 할당됩니다. 기본 IP 구성의 특성은 다음과 같습니다.

- [프라이빗](#private) [IPv4](#ipv4) 주소가 할당되어 있습니다. 프라이빗 [IPv6](#ipv6) 주소를 기본 IP 구성에 할당할 수는 없습니다.
- [공용](#public) IPv4 주소도 할당되어 있을 수 있습니다. 공용 IPv6 주소를 기본 (IPv4) IP 구성에 할당할 수는 없습니다. 

### <a name="secondary"></a>보조

네트워크 인터페이스에는 기본 IP 구성 외의 보조 IP 구성이 하나 이상 할당되어 있을 수도 있고 그렇지 않을 수도 있습니다. 보조 IP 구성의 특징은 다음과 같습니다.

- 프라이빗 IPv4 또는 IPv6 주소가 할당되어 있어야 합니다. 주소가 IPv6인 경우 네트워크 인터페이스에는 보조 IP 구성이 하나만 포함될 수 있습니다. 주소가 IPv4인 경우 네트워크 인터페이스에는 여러 보조 IP 구성이 할당될 수 있습니다. 네트워크 인터페이스에 할당할 수 있는 개인 및 공용 IPv4 주소의 수에 대해 자세히 알아보려면 [Azure 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) 문서를 참조하세요.
- 공용 IPv4 또는 IPv6 주소도 할당되어 있을 수 있습니다. 네트워크 인터페이스에 여러 IPv4 주소를 할당하면 다음과 같은 시나리오에서 유용합니다.
  - 단일 서버에서 IP 주소와 TLS/SSL 인증서를 사용하여 여러 웹 사이트 또는 서비스를 호스트할 수 있습니다.
  - 가상 머신이 방화벽, 부하 분산 장치 등의 네트워크 가상 어플라이언스로 사용되는 경우
  - Azure Load Balancer 백 엔드 풀에 네트워크 인터페이스용 프라이빗 IPv4 주소를 추가하려는 경우 이전에는 기본 네트워크 인터페이스의 기본 IPv4 주소만 백 엔드 풀에 추가할 수 있었습니다. 여러 IPv4 구성의 부하를 분산하는 방법에 대해 자세히 알아보려면 [여러 IP 구성의 부하 분산](../../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서를 참조하세요. 
  - 네트워크 인터페이스에 할당된 IPv6 주소 하나를 부하 분산하려는 경우. 프라이빗 IPv6 주소를 부하 분산하는 방법에 대해 자세히 알아보려면 [IPv6 주소 부하 분산](../../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서를 참조하세요.

## <a name="address-types"></a>주소 유형

[IP 구성](#ip-configurations)에 할당할 수 있는 IP 주소 유형은 다음과 같습니다.

### <a name="private"></a>프라이빗

개인 [IPv4](#ipv4) 또는 IPv6 주소를 할당하면 가상 머신이 가상 네트워크 또는 기타 연결된 네트워크의 다른 리소스와 통신할 수 있습니다. 

기본적으로 Azure DHCP 서버는 Azure 네트워크 인터페이스의 [기본 IP 구성](#primary)용 프라이빗 IPv4 주소를 가상 머신 운영 체제 내의 네트워크 인터페이스에 할당합니다. 반드시 필요한 경우가 아니면 가상 머신 운영 체제 내에서 네트워크 인터페이스의 IP 주소를 수동으로 설정해서는 안 됩니다.

가상 컴퓨터 운영 체제 내에서 네트워크 인터페이스의 IP 주소를 수동으로 설정해야 하는 시나리오도 있습니다. 예를 들어 Azure 가상 머신에 여러 IP 주소를 추가할 때는 Windows 운영 체제의 기본 및 보조 IP 주소를 수동으로 설정해야 합니다. Linux 가상 머신의 경우 보조 IP 주소만 수동으로 설정해야 합니다. 자세한 내용은 [VM 운영 체제에 IP 주소 추가](virtual-network-multiple-ip-addresses-portal.md#os-config)를 참조하세요. IP 구성에 할당된 주소를 변경해야 하는 경우에는 다음을 수행하는 것이 좋습니다.

1. 가상 머신이 Azure DHCP 서버에서 기본 IP 주소를 수신하는지 확인합니다. Linux VM을 실행하는 경우 운영 체제에서 이 주소를 설정하지 마세요.
2. 변경할 IP 구성을 삭제합니다.
3. 설정하려는 새 주소로 새 IP 구성을 만듭니다.
4. 운영 체제 내의 보조 IP 주소와 Windows 내의 기본 IP 주소를 Azure에서 설정한 주소와 일치하도록 [수동으로 구성](virtual-network-multiple-ip-addresses-portal.md#os-config)합니다. Linux의 OS 네트워크 구성에서 기본 IP 주소를 수동으로 설정하지 마세요. 수동으로 설정하면 구성이 다시 로드될 때 인터넷에 연결하지 못할 수 있습니다.
5. 게스트 운영 체제에서 네트워크 구성을 다시 로드합니다. 이 작업을 수행하려면 시스템을 다시 부팅하거나 NetworkManager를 실행하는 Linux 시스템에서 ‘nmcli con down “System eth0 && nmcli con up “System eth0”’을 실행하면 됩니다.
6. 네트워킹 설정이 원하는 대로 설정되었는지 확인합니다. 시스템의 모든 IP 주소에 대한 연결을 테스트합니다.

위의 단계를 수행하면 가상 머신 운영 체제 내와 Azure 내에서 네트워크 인터페이스에 할당된 개인 IP 주소는 동일하게 유지됩니다. 운영 체제 내에서 IP 주소를 수동으로 설정한 구독 내의 가상 머신을 추적하려는 경우 가상 머신에 Azure [태그](../../azure-resource-manager/management/tag-resources.md)를 추가할 수 있습니다. 예를 들어 "IP 주소 할당: 정적" 등을 사용할 수 있습니다. 이렇게 하면 운영 체제 내에서 IP 주소를 수동으로 설정한 구독 내 가상 머신을 쉽게 찾을 수 있습니다.

또한 개인 IP 주소가 있으면 가상 머신이 같은 가상 네트워크 또는 연결된 가상 네트워크 내의 다른 리소스와 통신할 수 있을 뿐 아니라 인터넷과 아웃바운드로도 통신할 수 있습니다. 아웃바운드 연결은 Azure에서 예측할 수 없는 공용 IP 주소로 변환하는 소스 네트워크 주소입니다. Azure 아웃바운드 인터넷 연결에 대해 자세히 알아보려면 [Azure 아웃바운드 인터넷 연결](../../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서를 참조하세요. 인터넷에서 가상 머신 개인 IP 주소로의 인바운드 통신은 불가능합니다. 아웃 바운드 연결에 예측 가능한 공용 IP 주소가 필요한 경우 네트워크 인터페이스에 공용 IP 주소 리소스를 연결합니다.

### <a name="public"></a>공용

공용 IP 주소 리소스를 통해 할당된 공용 IP 주소를 사용하면 인터넷에서 가상 머신으로의 인바운드 연결을 설정할 수 있습니다. 인터넷으로의 아웃바운드 연결에서는 예측 가능한 IP 주소를 사용합니다. 자세한 내용은 [Azure에서 아웃바운드 연결 이해](../../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요. IP 구성에 공용 IP 주소를 할당할 수 있지만, 반드시 필요한 것은 아닙니다. 공용 IP 주소 리소스를 연결하여 가상 머신에 공용 IP 주소를 할당하지 않더라도 가상 머신은 여전히 인터넷에 아웃바운드로 통신할 수 있습니다. 이 경우 개인 IP 주소는 Azure에서 예측할 수 없는 공용 IP 주소로 변환하는 원본 네트워크 주소입니다. 공용 IP 주소 리소스에 대한 자세한 내용은 [공용 IP 주소 리소스](virtual-network-public-ip-address.md)를 참조하세요.

네트워크 인터페이스에 할당할 수 있는 공용 및 개인 IP 주소의 수에는 제한이 있습니다. 자세한 내용은 [Azure 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) 문서를 참조하세요.

> [!NOTE]
> Azure에서는 가상 머신의 개인 IP 주소를 공용 IP 주소로 변환합니다. 따라서 가상 머신의 운영 체제는 할당된 공용 IP 주소를 인식할 수 없으므로 운영 체제 내에서 공용 IP 주소를 수동으로 할당할 필요가 없습니다.

## <a name="assignment-methods"></a>할당 방법

다음 할당 방법 중 하나를 사용하여 공용 및 개인 IP 주소를 할당합니다.

### <a name="dynamic"></a>동적

동적 프라이빗 IPv4 및 IPv6(선택 사항) 주소는 기본적으로 할당됩니다.

- **공용에만 해당**: Azure는 각 Azure 지역에 고유한 범위에서 주소를 할당합니다. 각 지역에 할당되는 범위에 대한 자세한 내용은 [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 참조하세요. 주소는 가상 머신이 중지(할당 취소) 후 다시 시작될 때 변경할 수 있습니다. 공용 IPv6 주소는 어떤 할당 방법으로도 IP 구성에 할당할 수 없습니다.
- **프라이빗에만 해당**: Azure는 각 서브넷 주소 범위에서 처음 4개 주소를 예약하고 주소를 할당하지 않습니다. Azure는 서브넷 주소 범위에서 리소스에 사용 가능한 다음 주소를 할당합니다. 예를 들어 서브넷의 주소 범위가 10.0.0.0/16이고 주소 10.0.0.4-10.0.0.14가 이미 할당된 경우(.0-.3이 예약됨) Azure는 10.0.0.15를 리소스에 할당합니다. 동적이 기본 할당 방법입니다. 할당되면 네트워크 인터페이스가 삭제되거나 동일한 가상 네트워크 내에서 다른 서브넷에 할당되거나 또는 할당 메서드가 고정으로 변경된 경우 동적 IP 주소만 해제되고 다른 IP 주소가 지정됩니다. 기본적으로 할당 메서드를 동적에서 고정으로 변경하는 경우 Azure는 이전에 동적으로 할당된 주소를 고정 주소로 할당합니다. 

### <a name="static"></a>정적

(필요에 따라) 공용 또는 개인 고정 IPv4 또는 IPv6 주소를 IP 구성에 할당할 수 있습니다. Azure에서 고정 공용 IPv4 주소를 할당하는 방법에 대해 자세히 알아보려면 [공용 IP 주소](virtual-network-public-ip-address.md)를 참조하세요.

- **공용에만 해당**: Azure는 각 Azure 지역에 고유한 범위에서 주소를 할당합니다. Azure [공용](https://www.microsoft.com/download/details.aspx?id=56519), [US 정부](https://www.microsoft.com/download/details.aspx?id=57063), [중국](https://www.microsoft.com/download/details.aspx?id=57062) 및 [독일](https://www.microsoft.com/download/details.aspx?id=57064) 클라우드의 범위(접두사) 목록을 다운로드할 수 있습니다. 할당된 공용 IP 주소 리소스가 삭제되거나 할당 메서드가 동적으로 변경될 때까지 주소는 변경되지 않습니다. 공용 IP 주소 리소스가 IP 구성에 연결된 경우 해당 할당 메서드를 변경하기 전에 IP 구성에서 분리해야 합니다.
- **프라이빗에만 해당**: 서브넷의 주소 범위에서 주소를 선택하고 할당합니다. 할당한 주소는 서브넷의 주소 범위에서 처음 4개 주소 중 하나가 아니고 현재 서브넷의 다른 리소스에 할당되지 않은 서브넷 주소 범위 내의 모든 주소일 수 있습니다. 고정 주소는 네트워크 인터페이스가 삭제되는 경우에만 해제됩니다. 할당 메서드를 고정으로 변경하는 경우, Azure는 주소가 서브넷 주소 범위의 사용 가능한 다음 주소가 아닌 경우에도 이전에 할당한 고정 IP 주소를 동적 주소로 할당합니다. 네트워크 인터페이스가 동일한 가상 네트워크 내의 다른 서브넷에 할당되는 경우에도 주소가 변경됩니다. 하지만 네트워크 인터페이스를 다른 서브넷에 할당하려면 먼저 할당 메서드를 고정에서 동적으로 변경해야 합니다. 네트워크 인터페이스를 다른 서브넷에 할당하면 할당 메서드를 다시 고정으로 변경하고 새로운 서브넷의 주소 범위에서 IP 주소를 할당할 수 있습니다.

## <a name="ip-address-versions"></a>IP 주소 버전

주소를 할당할 때 다음과 같은 버전을 지정할 수 있습니다.

### <a name="ipv4"></a>IPv4

각 네트워크 인터페이스에는 [프라이빗](#private) [IPv4](#ipv4) 주소가 할당된 [기본](#primary) IP 구성 하나가 있어야 합니다. 각각 IPv4 개인 IP 주소와 IPv4 [공용](#public) IP 주소(선택 사항)를 포함하는 [보조](#secondary) IP 구성을 하나 이상 추가할 수 있습니다.

### <a name="ipv6"></a>IPv6

네트워크 인터페이스의 보조 IP 구성 하나에 프라이빗 [IPv6](#ipv6) 주소 하나를 할당할 수도 있고 할당하지 않을 수도 있습니다. 네트워크 인터페이스는 기존 보조 IP 구성을 포함할 수 없습니다. 각 네트워크 인터페이스는 IPv6 개인 주소가 최대 하나만 있을 수 있습니다. 필요에 따라 IPv6 네트워크 인터페이스 구성에 공용 IPv6 주소를 추가할 수 있습니다.

> [!NOTE]
> 포털을 사용하여 IPv6 주소로 네트워크 인터페이스를 만들 수 있더라도 포털을 사용하여 새로운 또는 기존 가상 머신에 기존 네트워크 인터페이스를 추가할 수 없습니다. PowerShell 또는 Azure CLI를 사용하여 프라이빗 IPv6 주소가 포함된 네트워크 인터페이스를 만든 다음, 가상 머신을 만들 때 해당 네트워크 인터페이스를 연결합니다. 프라이빗 IPv6 주소가 할당된 네트워크 인터페이스를 기존 가상 머신에 연결할 수는 없습니다. Portal, CLI, PowerShell 등 어떤 도구로도 가상 머신에 연결된 네트워크 인터페이스의 IP 구성에 프라이빗 IPv6 주소를 추가할 수는 없습니다.

## <a name="skus"></a>SKU

공용 IP 주소는 기본 또는 표준 SKU로 생성됩니다. SKU 차이점에 대한 자세한 내용은 [공용 IP 주소 관리](virtual-network-public-ip-address.md)를 참조하세요.

> [!NOTE]
> 가상 머신의 네트워크 인터페이스에 표준 SKU 공용 IP 주소를 할당할 때 [네트워크 보안 그룹](../../virtual-network/network-security-groups-overview.md#network-security-groups)을 사용하여 원하는 트래픽을 명시적으로 허용해야 합니다. 네트워크 보안 그룹을 만들어 연결하고 원하는 트래픽을 명시적으로 허용해야 리소스와 통신할 수 있습니다.

## <a name="next-steps"></a>다음 단계
다양한 IP 구성으로 가상 머신을 만들려면 다음 문서를 확인하세요.

|Task|도구|
|---|---|
|여러 네트워크 인터페이스를 사용하는 VM 만들기|[CLI](../../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|여러 IPv4 주소가 있는 단일 NIC VM 만들기|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Azure Load Balancer 뒤에 프라이빗 IPv6 주소가 있는 단일 NIC VM 만들기|[CLI](../../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager 템플릿](../../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
