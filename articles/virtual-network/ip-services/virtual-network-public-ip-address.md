---
title: Azure 공용 IP 주소 만들기, 변경 또는 삭제
titleSuffix: Azure Virtual Network
description: 공용 IP 주소를 관리합니다. 공용 IP 주소가 어떻게 구성 가능한 설정을 사용하는 리소스인지 알아봅니다.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/20/2021
ms.author: allensu
ms.openlocfilehash: 92f472afdb642633a093ebdf765b0d8ba968bd8a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130228340"
---
# <a name="create-change-or-delete-an-azure-public-ip-address"></a>Azure 공용 IP 주소 만들기, 변경 또는 삭제

공용 IP 주소 및 해당 IP 주소를 생성, 변경 및 삭제하는 방법에 대해 알아봅니다. 공용 IP 주소는 구성 가능한 설정을 사용하는 리소스입니다. 공용 IP 주소를 공용 IP 주소를 지원하는 Azure 리소스에 할당하면 다음을 수행할 수 있습니다.

- Azure Virtual Machines (VM), Azure Application Gateways, Azure Load Balancer, Azure VPN Gateways 등 인터넷에서 리소스로 인바운드 통신.
- 예측 가능한 IP 주소를 사용하는 인터넷으로의 아웃바운드 연결.

[!INCLUDE [ephemeral-ip-note.md](../../../includes/ephemeral-ip-note.md)]

## <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기

포털, PowerShell, CLI 또는 Resource Manager 템플릿을 사용해 공용 IP 주소를 만드는 방법에 대한 지침은 다음 페이지를 참조하세요.

 * [공용 IP 주소 만들기 - 포털](./create-public-ip-portal.md?tabs=option-create-public-ip-standard-zones)
 * [공용 IP 주소 만들기 - PowerShell](./create-public-ip-powershell.md?tabs=option-create-public-ip-standard-zones)
 * [공용 IP 주소 만들기 - Azure CLI](./create-public-ip-cli.md?tabs=option-create-public-ip-standard-zones)
 * [공용 IP 주소 만들기 - 템플릿](./create-public-ip-template.md)

>[!NOTE]
>포털은 두 개의 공용 IP 주소 리소스(IPv4 하나와 IPv6 하나)를 만드는 옵션을 제공하지만 PowerShell과 CLI 명령은 두 IP 버전 중 하나로 된 주소로 하나의 리소스만 만듭니다. IP 버전마다 하나씩 두 개의 공용 IP 주소 리소스를 만들고 싶다면, 공용 IP 주소 리소스의 이름과 IP 버전을 다르게 설정해 가며 같은 명령을 두 번 실행해야 합니다.

만드는 동안 공용 IP 주소의 특정 특성에 대해 자세히 알아보려면 다음 표를 참조하세요.

   |설정|필수 여부|세부 정보|
   |---|---|---|
   |IP 버전|Yes| IPv4와 IPv6 중 하나를 고르거나 둘 모두를 선택합니다. 둘 모두를 선택하면 IPv4 주소 하나와 IPv6 주소 하나로 이루어진 두 개의 공용 IP 주소가 생성됩니다. [Azure VNET의 IPv6](ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)에 대해 자세한 내용을 알아보세요.|
   |SKU|Yes|SKU 도입 전에 생성된 모든 공용 IP 주소는 **기본** SKU 공용 IP 주소입니다. 공용 IP 주소를 만든 후에는 SKU를 변경할 수 없습니다. 독립 실행형 가상 머신, 가용성 집합 내의 가상 머신 또는 가상 머신 확장 집합은 기본 또는 표준 SKU를 사용할 수 있습니다. 가용성 집합이나 확장 집합 내의 가상 머신 간에 SKU를 혼합해 사용하거나 독립 실행형 VM 내의 SKU를 혼합해 사용할 수 없습니다. **기본** SKU: 가용성 영역을 지원하는 지역에 공용 IP 주소를 만드는 경우 **가용성 영역** 설정이 기본적으로 *없음* 으로 설정됩니다. 기본 공용 IP는 가용성 영역을 지원하지 않습니다. **표준** SKU: 표준 SKU 공용 IP를 가상 머신 또는 부하 분산 장치 프런트 엔드에 연결할 수 있습니다. 가용성 영역을 지원하는 지역에 공용 IP 주소를 만드는 경우 **가용성 영역** 설정이 기본적으로 *영역 중복* 으로 설정됩니다. 가용성 영역에 대한 자세한 내용은 **가용성 영역** 설정을 참조하세요. 주소를 표준 부하 분산 장치에 연결하는 경우에 표준 SKU가 필요합니다. 표준 부하 분산 장치에 대한 자세한 내용은 [Azure Load Balancer 표준 SKU](../../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요. 가상 머신의 네트워크 인터페이스에 표준 SKU 공용 IP 주소를 할당할 때 [네트워크 보안 그룹](../../virtual-network/network-security-groups-overview.md#network-security-groups)을 사용하여 원하는 트래픽을 명시적으로 허용해야 합니다. 네트워크 보안 그룹을 만들어 연결하고 원하는 트래픽을 명시적으로 허용해야 리소스와 통신할 수 있습니다.|
   |계층|Yes|IP 주소가 지역(**지역**)에 연결되어 있는지 여러 지역의 "애니캐스트"(**전체**)인지를 표시합니다. *"전체 계층" IP는 표준 IP의 미리 보기 기능이며 현재는 지역간 부하 분산 장치에서만 사용됩니다*.|
   |이름|예|이름은 선택한 리소스 그룹 내에서 고유해야 합니다.|
   |IP 주소 할당|Yes|**동적:** 동적 주소는 공용 IP 주소가 Azure 리소스에 연결되고 처음으로 시작된 후에만 할당됩니다. 가상 머신과 같은 리소스가 중지(할당 취소)되었다가 다시 시작되면 동적 주소가 변경될 수 있습니다. 가상 머신이 다시 부팅되거나 중지되면 주소가 동일하게 유지됩니다. 공용 IP 주소 리소스가 리소스에서 제거되면 동적 주소가 해제됩니다.<br> **고정:** 공용 IP 주소가 생성되면 고정 주소가 할당됩니다. 고정 주소는 공용 IP 주소 리소스를 삭제할 때까지 해제되지 않습니다. <br>참고: **IP 버전** 으로 *IPv6* 를 선택한 경우, 기본 SKU에 대한 할당 방법은 *동적* 이어야 합니다.  IPv4와 IPv6 모두에게 있어 표준 SKU 주소는 *고정적* 입니다. |
  |라우팅 기본 설정 |Yes| 기본적으로 공용 IP 주소에 대한 라우팅 기본 설정은 사용자에게 Microsoft의 글로벌 광역 네트워크를 통해 트래픽을 전달하는 ‘Microsoft 네트워크’로 설정됩니다.  ‘인터넷’을 선택하면 전송 ISP 네트워크를 사용하여 비용이 최적화된 속도로 트래픽을 전달하는 대신, Microsoft 네트워크에서의 이동이 최소화됩니다.  만든 후에는 공용 IP 주소 라우팅 기본 설정을 변경할 수 없습니다.  라우팅 기본 설정에 대한 자세한 내용은 [라우팅 기본 설정(미리 보기)이란?](routing-preference-overview.md)을 참조하세요.   |
   |유휴 제한 시간(분)|No|연결 유지 메시지를 보내는 데 클라이언트를 사용하지 않고 TCP 또는 HTTP 연결을 유지하는 데 걸리는 시간(분)입니다. **IP 버전** 으로 IPv6을 선택하는 경우 이 값은 4분으로 설정되고 변경될 수 없습니다. |
   |DNS 이름 레이블|No|이름을 만드는 Azure 위치 내에서(모든 구독 및 모든 고객에서) 고유해야 합니다. Azure는 해당 DNS에서 이름과 IP 주소를 자동으로 등록하므로 해당 이름을 사용하는 리소스에 연결할 수 있습니다. Azure에서는 정규화된 DNS 이름을 만들기 위해 제공하는 이름에 *location.cloudapp.azure.com* 과 같은 기본 서브넷을 추가합니다. 두 주소 버전을 모두 만드는 경우 IPv4 및 IPv6 주소 둘 다에 같은 DNS 이름이 할당됩니다. Azure의 기본 DNS는 IPv4 A 및 IPv6 AAAA 이름 레코드를 모두 포함합니다. 기본 DNS는 DNS 조회 중에 두 레코드를 사용하여 응답합니다. 클라이언트는 어떤 주소(IPv4 또는 IPv6)와 통신할지 선택합니다. Azure DNS 서비스를 사용하면 공용 IP 주소로 확인되는 사용자 지정 접미사를 이용해 DNS 이름을 구성할 수 있습니다. 자세한 내용은 [Azure 공용 IP 주소로 Azure DNS 사용](../../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address)을 참조하세요.|
   |이름(IP 버전을 **양쪽 모두** 로 선택한 경우에만 표시됨)|예, IP 버전을 **양쪽 모두** 로 선택한 경우|이름은 이 목록의 첫 번째 **이름** 으로 입력하는 이름과 달라야 합니다. IPv4 주소와 IPv6 주소를 모두 만들도록 선택하면 Portal에서는 각 IP 주소 버전이 할당된 개별 공용 IP 주소 리소스 두 개를 만듭니다.|
   |IP 주소 할당(IP 버전을 **양쪽 모두** 로 선택한 경우에만 표시됨)|예, IP 버전을 **양쪽 모두** 로 선택한 경우| 위의 IP 주소 할당과 동일한 제한 사항 |
   |구독|Yes|공용 IP 주소를 연결할 리소스와 동일한 [구독](../../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)에 있어야 합니다.|
   |Resource group|Yes|공용 IP 주소를 연결할 리소스와 동일하거나 다른 [리소스 그룹](../../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)에 있을 수 있습니다.|
   |위치|예|공용 IP 주소를 연결할 리소스와 동일한 [위치](https://azure.microsoft.com/regions)(지역이라고도 함)에 있어야 합니다.|
   |가용성 영역| No | 이 설정은 지원되는 위치와 IP 주소 형식을 선택하는 경우에만 나타납니다. **기본** SKU 공용 IP와 **글로벌** 계층 공용 IP는 가용성 영역을 지원하지 않습니다. 영역 없음(기본 옵션), 특정 영역 또는 영역 중복을 선택할 수 있습니다. 선택은 특정 도메인 오류 요구 사항에 따라 달라집니다. 지원되는 위치 목록과 가용성 영역에 대한 자세한 내용은 [가용성 영역 개요](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.  

## <a name="view-modify-settings-for-or-delete-a-public-ip-address"></a>공용 IP 주소 설정 보기, 수정하기 또는 공용 IP 주소 삭제하기

   - **보기/목록**: SKU, 주소, 연결 등 공용 IP에 대한 설정을 검토합니다. 연결은 부하 분산 장치 프런트 엔드, 가상 컴퓨터 및 기타 Azure 리소스가 될 수 있습니다.
   - **수정**: [공용 IP 주소 만들기](#create-a-public-ip-address)의 정보를 사용하여 설정을 수정합니다. 유휴 시간 제한, DNS 이름 레이블 또는 할당 방법과 같은 설정입니다. 기본에서 표준으로 공용 IP SKU를 업그레이드하는 전체 프로세스는 [Azure 공용 IP 주소 업그레이드](./public-ip-upgrade-portal.md)를 참조하세요.
   
   >[!WARNING]
   >공용 IP 할당을 고정에서 동적으로 변경하려면 해당하는 IP 구성에서 주소를 제거합니다(**제거** 섹션 참조). 할당 방법을 고정에서 동적으로 변경하면 공용 IP 리소스에 할당된 IP 주소가 손실됩니다. Azure 공용 DNS 서버는 고정 또는 동적 주소와 모든 DNS 이름 레이블(사용자가 정의한 경우) 간의 매핑을 유지하지만, 가상 머신을 중지(할당 취소)된 상태에서 시작할 때 동적 IP 주소가 변경될 수 있습니다. 주소가 변경되지 않도록 하려면 고정 IP 주소를 할당합니다.
   
|작업|Azure Portal|Azure PowerShell|Azure CLI|
|---|---|---|---|
|보기 | 공용 IP의 **개요** 섹션에서 |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress)를 사용하면 공용 IP 주소 개체를 검색하고 그 설정을 확인합니다.| [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show)를 사용하면 설정을 표시합니다.|
|목록 | **공용 IP 주소** 범주에서 |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress)를 사용하면 하나 이상의 공용 IP 주소 개체를 검색하고 그 설정을 확인합니다.|[az network public-ip list](/cli/azure/network/public-ip#az_network_public_ip_list)를 사용하면 공용 IP 주소를 나열합니다.|
|수정 | 연결이 끊긴 IP의 경우 다음에 대한 **구성** 을 선택합니다. </br> 유휴 시간 제한 수정. </br> DNS 이름 레이블. </br> IP 할당을 고정에서 동적으로 변경. </br> 기본 IP를 표준으로 업그레이드 |[Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress)는 설정을 업데이트합니다. |[az network public-ip update](/cli/azure/network/public-ip#az_network_public_ip_update)는 업데이트합니다. |

   - **삭제**: 공용 IP를 삭제하려면 해당 공용 IP 개체가 IP 구성 또는 가상 머신 네트워크 인터페이스에 연결되어 있지 않아야 합니다. 자세한 내용은 다음 표를 참조하세요.

|리소스|Azure portal|Azure PowerShell|Azure CLI|
|---|---|---|---|
|[가상 머신](./remove-public-ip-address-vm.md)|**분리** 를 선택하여 IP 주소를 NIC 구성에서 분리한 다음, **삭제** 를 선택합니다.|[Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress)는 해당 IP 주소를 NIC 구성에서 분리합니다. [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress)는 해당 IP 주소를 삭제합니다.|[az network public-ip update with the "--remove" 매개 변수](/cli/azure/network/public-ip#az_network_public_ip_update)를 사용하여 NIC 구성에서 IP 주소를 제거합니다. [az network public-ip delete](/cli/azure/network/public-ip#az_network_public_ip_delete)를 사용하여 공용 IP를 삭제합니다. |
|부하 분산 장치 프런트 엔드 | 사용하지 않은 공용 IP 주소로 이동하여 **연결** 을 선택합니다. 적절한 프런트 엔드 IP 구성을 사용해 부하 분산 장치를 선택하여 IP를 바꿉니다. 이전 IP는 가상 머신과 동일한 방법을 사용하여 삭제할 수 있습니다.  | [Set-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/set-azloadbalancerfrontendipconfig)를 사용하여 새 프런트 엔드 IP 구성을 공용 부하 분산 장치와 연결합니다. [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress)를 사용하여 공용 IP 주소를 삭제합니다. 또한 [Remove-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/remove-azloadbalancerfrontendipconfig)를 사용하여 프런트 엔드 IP 구성이 두 개 이상인 경우 제거할 수 있습니다. | [az network lb frontend-ip update](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_update)를 사용하여 새 프런트 엔드 IP 구성을 공용 부하 분산 장치와 연결합니다. [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress)를 사용하여 공용 IP를 삭제합니다. 또한 [az network lb frontend-ip delete](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_delete)를 사용하여 프런트 엔드 IP 구성이 두 개 이상인 경우 제거할 수 있습니다. |
|방화벽|해당 없음| [할당 취소](../../firewall/firewall-faq.yml#how-can-i-stop-and-start-azure-firewall)는 방화벽 할당을 취소하고 IP 구성을 모두 제거합니다. | [az network firewall ip-config delete](/cli/azure/network/firewall/ip-config#az_network_firewall_ip_config_delete)를 사용하여 IP를 제거합니다. PowerShell을 사용하여 먼저 할당을 취소합니다. |

## <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

공용 IP를 사용하는 가상 머신 확장 집합을 이용하는 경우, 개별 가상 머신 인스턴스와 연결된 별도의 공용 IP 개체는 없습니다. 그렇지만, 공용 IP 접두사 개체를 [사용해 인스턴스 IP를 생성할 수는 있습니다](https://azure.microsoft.com/resources/templates/vmms-with-public-ip-prefix/).

가상 머신 확장 집합에서 공용 IP를 나열하려면, PowerShell([Get-AzPublicIpAddress -VirtualMachineScaleSetName](/powershell/module/az.network/get-azpublicipaddress))이나 CLI([az virtual machine scale set list-instance-public-ips](/cli/azure/vmss#az_vmss_list_instance_public_ips))를 사용하면 됩니다.

자세한 내용은 [Azure 가상 머신 확장 집합에 대한 네트워킹](../../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine)을 참조하세요.

## <a name="assign-a-public-ip-address"></a>공용 IP 주소 할당

다음 리소스에 공용 IP 주소를 할당하는 방법을 알아봅니다.

- 만드는 중인 [Windows](../../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Linux](../../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 머신. [기존 가상 머신](./virtual-network-network-interface-addresses.md#add-ip-addresses)에 IP를 추가합니다.
- [공용 부하 분산 장치](../../load-balancer/quickstart-load-balancer-standard-public-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Application Gateway](../../application-gateway/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [VPN Gateway를 이용한 사이트 간 연결](../../vpn-gateway/tutorial-site-to-site-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Virtual Machine Scale Set](../../virtual-machine-scale-sets/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [NAT 게이트웨이](../nat-gateway/tutorial-create-nat-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Bastion](../../bastion/quickstart-host-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Firewall](../../firewall/tutorial-firewall-deploy-portal-policy.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [영역 간 부하 분산 장치](../../load-balancer/tutorial-cross-region-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>사용 권한

공용 IP 주소를 관리하려면 계정을 [네트워크 참가자](../../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할에 할당해야 합니다. [사용자 지정](../../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 역할도 지원됩니다. 사용자 지정 역할에는 다음 표에 나열된 적절한 작업을 할당해야 합니다.

| 작업                                                             | Name                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | 공용 IP 주소 읽기                                          |
| Microsoft.Network/publicIPAddresses/write                          | 공용 IP 주소 만들기 또는 업데이트                           |
| Microsoft.Network/publicIPAddresses/delete                         | 공용 IP 주소 삭제                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | 공용 IP 주소를 리소스에 연결                    |

## <a name="next-steps"></a>다음 단계

공용 IP 주소에는 명목 요금이 부과됩니다. 가격을 보려면 [IP 주소 가격](https://azure.microsoft.com/pricing/details/ip-addresses) 페이지를 참조하세요.

- [PowerShell](../../virtual-network/powershell-samples.md) 또는 [Azure CLI](../../virtual-network/cli-samples.md) 샘플 스크립트를 사용하거나 Azure [Resource Manager 템플릿](../../virtual-network/template-samples.md)을 사용하여 공용 IP 주소 만들기
- 공용 IP 주소를 위한 [Azure Policy 정의](../../virtual-network/policy-reference.md) 만들어 할당하기