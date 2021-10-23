---
title: Azure 네트워크 보안 그룹 만들기, 변경 또는 삭제
titlesuffix: Azure Virtual Network
description: 보안 규칙에 대한 정보를 찾을 수 있는 위치와 네트워크 보안 그룹을 만들거나 변경하거나 삭제하는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 6dd46099a3f4450dd175d0e61c2a6817de0f76f3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130224826"
---
# <a name="create-change-or-delete-a-network-security-group"></a>네트워크 보안 그룹을 만들기, 변경 또는 삭제

네트워크 보안 그룹의 보안 규칙을 사용하면 가상 네트워크 서브넷 및 네트워크 인터페이스 내외부로 이동할 수 있는 네트워크 트래픽 유형을 필터링할 수 있습니다. 네트워크 보안 그룹에 대한 자세한 내용은 [네트워크 보안 그룹 개요](./network-security-groups-overview.md)를 참조하세요. 다음으로, 네트워크 보안 그룹에 대한 몇 가지 경험을 얻기 위해 [네트워크 트래픽 필터링](tutorial-filter-network-traffic.md) 자습서를 완료합니다.

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

구독이 활성화된 Azure 계정이 없는 경우 계정을 설정합니다. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 이 문서의 나머지 부분을 시작하기 전에 다음 작업 중 하나를 완료합니다.

- **포털 사용자**: Azure 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.

- **PowerShell 사용자**: [Azure Cloud Shell](https://shell.azure.com/powershell)에서 명령을 실행하거나 사용자의 컴퓨터에서 PowerShell을 실행합니다. Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. Azure Cloud Shell 브라우저 탭에서 **환경 선택** 드롭다운 목록을 찾은 다음, **PowerShell** 을 선택합니다(아직 선택하지 않은 경우).

    PowerShell을 로컬로 실행하는 경우 Azure PowerShell 모듈 버전 1.0.0 이상을 사용합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az.Network`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. `Connect-AzAccount`를 실행하여 Azure와 연결합니다.

- **Azure CLI(명령줄 인터페이스) 사용자**: [Azure Cloud Shell](https://shell.azure.com/bash)에서 명령을 실행하거나 사용자 컴퓨터에서 CLI를 실행합니다. Azure CLI를 로컬로 실행하는 경우 Azure CLI 버전 2.0.28 이상을 사용합니다. 설치되어 있는 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. `az login`를 실행하여 Azure와 연결합니다.

Azure에 로그인하거나 연결할 때 사용하는 계정이 [권한](#permissions)에 나열된 적절한 작업이 할당된 [사용자 지정 역할](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)이나 [네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할에 할당되어야 합니다.

## <a name="work-with-network-security-groups"></a>네트워크 보안 그룹으로 작업

네트워크 보안 그룹의 만들기, [모두 보기](#view-all-network-security-groups), [세부 정보 보기](#view-details-of-a-network-security-group), [변경](#change-a-network-security-group) 및 [삭제](#delete-a-network-security-group)를 수행할 수 있습니다. 네트워크 인터페이스 또는 서브넷에서 네트워크 보안 그룹을 [연결 또는 분리](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface)할 수 있습니다.

### <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

Azure 위치와 구독별로 만들 수 있는 네트워크 보안 그룹 수에 제한이 있습니다. 자세한 정보는 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조하세요.

1. [Azure Portal](https://portal.azure.com) 메뉴 또는 **홈** 페이지에서 **리소스 만들기** 를 선택합니다.

2. **네트워킹** 을 선택한 다음, **네트워크 보안 그룹** 을 선택합니다.

3. **네트워크 보안 그룹 만들기** 페이지의 **기본 사항** 탭에서 다음 설정에 대한 값을 설정합니다.

    | 설정 | 작업 |
    | --- | --- |
    | **구독** | 구독을 선택합니다. |
    | **리소스 그룹** | 기존 리소스 그룹을 선택하거나 **새로 만들기** 를 선택하여 새 리소스 그룹을 만듭니다. |
    | **이름** | 리소스 그룹 내에 고유한 텍스트 문자열을 입력합니다. |
    | **지역** | 원하는 위치를 선택합니다. |

4. **검토 + 만들기** 를 선택합니다.

5. **유효성 검사 통과** 메시지가 표시되면 **만들기** 를 선택합니다.

#### <a name="commands"></a>명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) |
| PowerShell | [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |

### <a name="view-all-network-security-groups"></a>모든 네트워크 보안 그룹 보기

[Azure Portal](https://portal.azure.com)로 이동하여 네트워크 보안 그룹을 확인합니다. **네트워크 보안 그룹** 을 검색하고 선택합니다. 구독에 대한 네트워크 보안 그룹 목록이 표시됩니다.

#### <a name="commands"></a>명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network nsg list](/cli/azure/network/nsg#az_network_nsg_list) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="view-details-of-a-network-security-group"></a>네트워크 보안 그룹 세부 정보 보기

1. [Azure Portal](https://portal.azure.com)로 이동하여 네트워크 보안 그룹을 확인합니다. **네트워크 보안 그룹** 을 검색하고 선택합니다.

2. 네트워크 보안 그룹의 이름을 선택합니다.

**설정** 에서 네트워크 보안 그룹의 메뉴 모음에서 **인바운드 보안 규칙**, **아웃바운드 보안 규칙**, **네트워크 인터페이스** 및 네트워크 보안 그룹이 연결된 **서브넷** 을 볼 수 있습니다.

**모니터링** 아래에서 **진단 설정** 을 사용하거나 사용하지 않도록 설정할 수 있습니다. **지원 + 문제 해결** 에서 **유효한 보안 규칙** 을 볼 수 있습니다. 자세히 알아보려면 [네트워크 보안 그룹에 대한 진단 로깅](virtual-network-nsg-manage-log.md) 및 [VM 네트워크 트래픽 필터 문제 진단](diagnose-network-traffic-filter-problem.md)을 참조하세요.

나열된 일반적인 Azure 설정에 대한 자세한 내용은 다음 문서를 참조하세요.

- [활동 로그](../azure-monitor/essentials/platform-logs-overview.md)
- [액세스 제어(IAM)](../role-based-access-control/overview.md)
- [태그](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [잠금](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Automation 스크립트](../azure-resource-manager/templates/export-template-portal.md)

#### <a name="commands"></a>명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network nsg show](/cli/azure/network/nsg#az_network_nsg_show) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="change-a-network-security-group"></a>네트워크 보안 그룹 변경

1. [Azure Portal](https://portal.azure.com)로 이동하여 네트워크 보안 그룹을 확인합니다. **네트워크 보안 그룹** 을 검색하고 선택합니다.

2. 변경하려는 네트워크 보안 그룹의 이름을 선택합니다.

가장 일반적인 변경 사항은 [보안 규칙을 추가](#create-a-security-rule), [규칙을 제거](#delete-a-security-rule) 및 [서브넷 또는 네트워크 인터페이스에서 네트워크 보안 그룹 연결 또는 분리](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface)입니다.

#### <a name="commands"></a>명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network nsg update](/cli/azure/network/nsg#az_network_nsg_update) |
| PowerShell | [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) |

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>네트워크 인터페이스 또는 서브넷에서 네트워크 보안 그룹 연결 또는 분리

네트워크 보안 그룹을 네트워크 인터페이스에 연결하거나 분리하려면 [네트워크 인터페이스에 네트워크 보안 그룹 연결 또는 분리](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group)를 참조합니다. 네트워크 보안 그룹을 서브넷에 연결하거나 분리하려면 [서브넷 설정 변경](virtual-network-manage-subnet.md#change-subnet-settings)을 참조합니다.

### <a name="delete-a-network-security-group"></a>네트워크 보안 그룹 삭제

서브넷 또는 네트워크 인터페이스에 네트워크 보안 그룹이 연결되어 있는 경우 삭제할 수 없습니다. 네트워크 보안 그룹을 삭제하기 전에 모든 서브넷 및 네트워크 인터페이스에서 분리합니다.

1. [Azure Portal](https://portal.azure.com)로 이동하여 네트워크 보안 그룹을 확인합니다. **네트워크 보안 그룹** 을 검색하고 선택합니다.

2. 삭제하려는 네트워크 보안 그룹의 이름을 선택합니다.

3. 네트워크 보안 그룹의 도구 모음에서 **삭제** 를 선택합니다. 그런 다음, 확인 대화 상자에서 **예** 를 선택합니다.

#### <a name="commands"></a>명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network nsg delete](/cli/azure/network/nsg#az_network_nsg_delete) |
| PowerShell | [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup) |

## <a name="work-with-security-rules"></a>보안 규칙으로 작업

네트워크 보안 그룹은 0개 이상의 보안 규칙을 포함합니다. 보안 규칙의 만들기, [모두 보기](#view-all-security-rules), [세부 정보 보기](#view-details-of-a-security-rule), [변경](#change-a-security-rule) 및 [삭제](#delete-a-security-rule)를 수행할 수 있습니다.

### <a name="create-a-security-rule"></a>보안 규칙 만들기

Azure 위치와 구독별로 만들 수 있는 네트워크 보안 그룹당 규칙 수에 제한이 있습니다. 자세한 정보는 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조하세요.

1. [Azure Portal](https://portal.azure.com)로 이동하여 네트워크 보안 그룹을 확인합니다. **네트워크 보안 그룹** 을 검색하고 선택합니다.

2. 보안 규칙을 추가하려는 네트워크 보안 그룹의 이름을 선택합니다.

3. 네트워크 보안 그룹의 메뉴 모음에서 **인바운드 보안 규칙** 또는 **아웃바운드 보안 규칙** 을 선택합니다.

    추가하지 않았을 수도 있는 규칙을 포함하여 몇 가지 기존 규칙이 나열됩니다. 네트워크 보안 그룹을 만들 때 해당 보안 그룹 안에 몇 가지 기본 보안 규칙이 만들어집니다. 자세한 내용은 [기본 보안 규칙](./network-security-groups-overview.md#default-security-rules)을 참조하세요.  기본 보안 규칙을 삭제할 수 없으나 더 높은 우선 순위의 규칙으로 재정의할 수 있습니다.

4. <a name="security-rule-settings"></a>**추가** 를 선택합니다. 다음 설정에 대한 값을 추가하거나 선택한 다음, **확인** 을 선택합니다.

    | 설정 | 값 | 세부 정보 |
    | ------- | ----- | ------- |
    | **원본** | 다음 중 하나:<ul><li>**임의**</li><li>**IP 주소**</li><li>**서비스 태그**(인바운드 보안 규칙) 또는 **VirtualNetwork**(아웃바운드 보안 규칙)</li><li>**애플리케이션&nbsp;보안&nbsp;그룹**</li></ul> | <p>**IP 주소** 를 선택하는 경우 **원본 IP 주소/CIDR 범위** 를 지정해야 합니다.</p><p>**서비스 태그** 를 선택하는 경우 **원본 서비스 태그** 를 선택할 수도 있습니다.</p><p>**애플리케이션 보안 그룹** 을 선택하는 경우 기존 애플리케이션 보안 그룹도 선택해야 합니다. **원본** 및 **대상** 둘 다에 대해 **애플리케이션 보안 그룹** 을 선택하는 경우 두 애플리케이션 보안 그룹 내 네트워크 인터페이스가 동일한 가상 네트워크에 있어야 합니다.</p> |
    | **원본 IP 주소/CIDR 범위** | 쉼표로 구분된 IP 주소 목록 및 CIDR(Classless Interdomain Routing) 범위 | <p>이 설정은 **원본** 을 **IP 주소** 로 변경하는 경우 나타납니다. 단일 값 또는 쉼표로 구분된 다중 값 목록을 지정해야 합니다. 다중 값의 예는 `10.0.0.0/16, 192.188.1.1`입니다. 지정할 수 있는 값의 수에는 제한이 있습니다. 자세한 내용은 [Azure 제한](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조하세요.</p><p>지정하는 IP 주소를 Azure VM에 할당하는 경우 공용 IP 주소가 아닌 해당 개인 IP 주소를 지정합니다. Azure는 공용 IP 주소를 인바운드 보안 규칙의 개인 IP 주소로 변환한 후, 개인 IP 주소를 아웃바운드 규칙의 공용 IP 주소로 변환하기 전에 보안 규칙을 처리합니다. Azure에서 공용 및 개인 IP 주소에 대한 자세히 알려면 [IP 주소 형식](./ip-services/public-ip-addresses.md)을 참조합니다.</p> |
    | **원본 서비스 태그** | 드롭다운 목록의 서비스 태그 | 인바운드 보안 규칙에 대한 **원본** 을 **서비스 태그** 로 설정하는 경우 이 선택적 설정이 표시됩니다. 서비스 태그는 IP 주소 범주에 대한 사전 정의된 식별자입니다. 사용할 수 있는 서비스 태그 및 각 태그의 의미에 대해 자세히 알려면 [서비스 태그](./network-security-groups-overview.md#service-tags)를 참조합니다. |
    | **원본 애플리케이션 보안 그룹** | 기존 애플리케이션 보안 그룹 | 이 설정은 **원본** 을 **애플리케이션 보안 그룹** 으로 설정하는 경우 표시됩니다. 네트워크 인터페이스와 동일한 지역에 있는 애플리케이션 보안 그룹을 선택합니다. [애플리케이션 보안 그룹을 만드는](#create-an-application-security-group) 방법을 알아봅니다. |
    | **원본 포트 범위** | 다음 중 하나:<ul><li>단일 포트(예: `80`)</li><li>포트의 범위(예: `1024-65535`)</li><li>단일 포트 및/또는 포트 범위의 쉼표로 구분된 목록(예: `80, 1024-65535`)</li><li>모든 포트에 트래픽을 허용하려면 별표(`*`)</li></ul> | 이 설정은 규칙에서 트래픽을 허용하거나 거부하는 포트를 지정합니다. 지정할 수 있는 포트의 수에는 제한이 있습니다. 자세한 내용은 [Azure 제한](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조하세요. |
    | **대상** | 다음 중 하나:<ul><li>**임의**</li><li>**IP 주소**</li><li>**서비스 태그**(아웃바운드 보안 규칙) 또는 **VirtualNetwork**(인바운드 보안 규칙)</li><li>**애플리케이션&nbsp;보안&nbsp;그룹**</li></ul> | <p>**IP 주소** 를 선택하는 경우, **대상 IP 주소/CIDR 범위** 를 지정합니다.</p><p>**VirtualNetwork** 를 선택하는 경우 가상 네트워크의 주소 공간 내 모든 IP 주소에 대한 트래픽이 허용됩니다. **VirtualNetwork** 는 서비스 태그입니다.</p><p>**애플리케이션 보안 그룹** 을 선택하는 경우 기존 애플리케이션 보안 그룹도 선택해야 합니다. [애플리케이션 보안 그룹을 만드는](#create-an-application-security-group) 방법을 알아봅니다.</p> |
    | **대상 IP 주소/CIDR 범위** | IP 주소 및 CIDR 범위의 쉼표로 구분된 목록 | <p>이 설정은 **원본** 을 **IP 주소** 로 변경하는 경우 나타납니다. **원본** 및 **원본 IP 주소/CIDR 범위** 와 유사하게 단일 또는 여러 주소나 범위를 지정할 수 있습니다. 지정할 수 있는 수에는 제한이 있습니다. 자세한 내용은 [Azure 제한](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조하세요.</p><p>지정하는 IP 주소가 Azure VM에 할당된 경우 공용 IP 주소가 아닌 해당 개인 IP 주소를 지정합니다. Azure는 공용 IP 주소를 인바운드 보안 규칙의 개인 IP 주소로 변환한 후, 개인 IP 주소를 아웃바운드 규칙의 공용 IP 주소로 변환하기 전에 보안 규칙을 처리합니다. Azure에서 공용 및 개인 IP 주소에 대한 자세히 알려면 [IP 주소 형식](./ip-services/public-ip-addresses.md)을 참조합니다.</p> |
    | **대상 서비스 태그** | 드롭다운 목록의 서비스 태그 | 이 선택적 설정은 아웃바운드 보안 규칙에 대한 **대상** 을 **서비스 태그** 로 변경하는 경우에 나타납니다. 서비스 태그는 IP 주소 범주에 대한 사전 정의된 식별자입니다. 사용할 수 있는 서비스 태그 및 각 태그의 의미에 대해 자세히 알려면 [서비스 태그](./network-security-groups-overview.md#service-tags)를 참조합니다. |
    | **대상 애플리케이션 보안 그룹** | 기존 애플리케이션 보안 그룹 | 이 설정은 **대상** 을 **애플리케이션 보안 그룹** 으로 설정하는 경우 표시됩니다. 네트워크 인터페이스와 동일한 지역에 있는 애플리케이션 보안 그룹을 선택합니다. [애플리케이션 보안 그룹을 만드는](#create-an-application-security-group) 방법을 알아봅니다. |
    | **대상 포트 범위** | 다음 중 하나:<ul><li>단일 포트(예: `80`)</li><li>포트의 범위(예: `1024-65535`)</li><li>단일 포트 및/또는 포트 범위의 쉼표로 구분된 목록(예: `80, 1024-65535`)</li><li>모든 포트에 트래픽을 허용하려면 별표(`*`)</li></ul> | **원본 포트 범위** 와 마찬가지로 단일 또는 여러 포트와 범위를 지정할 수 있습니다. 지정할 수 있는 수에는 제한이 있습니다. 자세한 내용은 [Azure 제한](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조하세요. |
    | **프로토콜** | **모든**, **TCP**, **UDP** 또는 **ICMP** | 규칙을 TCP(Transmission Control Protocol), UDP(User Datagram Protocol) 또는 ICMP(Internet Control Message Protocol)로 제한할 수 있습니다. 기본값은 규칙이 모든 프로토콜에 적용되는 것입니다. |
    | **동작** | **허용** 또는 **거부** | 이 설정은 이 규칙이 제공된 원본 및 대상 구성에 대한 액세스를 허용하거나 거부하는지 여부를 지정합니다. |
    | **우선 순위** | 네트워크 보안 그룹 내의 모든 보안 규칙에 대해 100-4096 사이의 고유한 값 | Azure는 보안 규칙을 우선 순위에 따라 처리합니다. 번호가 낮을수록 우선 순위가 높습니다. 규칙을 만들 때 100, 200 및 300 같이 우선 순위 번호 사이의 간격을 두는 것이 좋습니다. 간격을 두면 향후 기존 규칙보다 우선 순위가 더 높거나 더 낮게 만들 필요가 있는 규칙을 추가하기가 더 쉬워집니다. |
    | **이름** | 네트워크 보안 그룹 내에서 규칙에 대한 고유한 이름 | 이름은 최대 80자까지 가능합니다. 문자나 숫자로 시작하고 문자, 숫자 또는 밑줄로 끝나야 합니다. 이름은 문자, 숫자, 밑줄, 마침표 또는 하이픈만 포함할 수 있습니다. |
    | **설명** | 텍스트 설명 | 보안 규칙에 대한 텍스트 설명을 선택적으로 지정할 수 있습니다. 설명은 140자를 초과할 수 없습니다. |

#### <a name="commands"></a>명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) |
| PowerShell | [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |

### <a name="view-all-security-rules"></a>모든 보안 규칙 보기

네트워크 보안 그룹은 0개 이상의 규칙을 포함합니다. 경로를 볼 때 나열되는 정보에 대한 자세한 내용은 [네트워크 보안 그룹 개요](./network-security-groups-overview.md)를 참조합니다.

1. 네트워크 보안 그룹의 규칙을 보려면 [Azure Portal](https://portal.azure.com)로 이동합니다. **네트워크 보안 그룹** 을 검색하고 선택합니다.

2. 규칙을 보려는 네트워크 보안 그룹의 이름을 선택합니다.

3. 네트워크 보안 그룹의 메뉴 모음에서 **인바운드 보안 규칙** 또는 **아웃바운드 보안 규칙** 을 선택합니다.

목록에는 만든 모든 규칙 및 네트워크 보안 그룹의 [기본 보안 규칙](./network-security-groups-overview.md#default-security-rules)이 포함됩니다.

#### <a name="commands"></a>명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network nsg rule list](/cli/azure/network/nsg/rule#az_network_nsg_rule_list) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="view-details-of-a-security-rule"></a>보안 규칙의 세부 정보 보기

1. 네트워크 보안 그룹의 규칙을 보려면 [Azure Portal](https://portal.azure.com)로 이동합니다. **네트워크 보안 그룹** 을 검색하고 선택합니다.

2. 규칙의 세부 사항을 보려는 네트워크 보안 그룹의 이름을 선택합니다.

3. 네트워크 보안 그룹의 메뉴 모음에서 **인바운드 보안 규칙** 또는 **아웃바운드 보안 규칙** 을 선택합니다.

4. 세부 정보를 보려는 규칙을 선택합니다. 모든 설정에 대한 자세한 내용은 [보안 규칙 설정](#security-rule-settings)을 참조하세요.

    > [!NOTE]
    > 이 절차는 사용자 지정 보안 규칙에만 적용됩니다. 기본 보안 규칙을 선택하는 경우에는 작동하지 않습니다.

#### <a name="commands"></a>명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network nsg rule show](/cli/azure/network/nsg/rule#az_network_nsg_rule_show) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="change-a-security-rule"></a>보안 규칙 변경

1. [보안 규칙 세부 정보 보기](#view-details-of-a-security-rule)에서 단계를 완료합니다.

2. 필요에 따라 설정을 변경한 다음, **저장** 을 선택합니다. 모든 설정에 대한 자세한 내용은 [보안 규칙 설정](#security-rule-settings)을 참조하세요.

    > [!NOTE]
    > 이 절차는 사용자 지정 보안 규칙에만 적용됩니다. 기본 보안 규칙은 변경할 수 없습니다.

#### <a name="commands"></a>명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network nsg rule update](/cli/azure/network/nsg/rule#az_network_nsg_rule_update) |
| PowerShell | [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig) |

### <a name="delete-a-security-rule"></a>보안 규칙 삭제

1. [보안 규칙 세부 정보 보기](#view-details-of-a-security-rule)에서 단계를 완료합니다.

2. **삭제** 를 선택한 후 **예** 를 선택합니다.

    > [!NOTE]
    > 이 절차는 사용자 지정 보안 규칙에만 적용됩니다. 기본 보안 규칙을 삭제할 수 없습니다.

#### <a name="commands"></a>명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network nsg rule delete](/cli/azure/network/nsg/rule#az_network_nsg_rule_delete) |
| PowerShell | [Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig) |

## <a name="work-with-application-security-groups"></a>애플리케이션 보안 그룹으로 작업

애플리케이션 보안 그룹에는 0개 이상의 네트워크 인터페이스가 포함되어 있습니다. 자세한 내용은 [애플리케이션 보안 그룹](./network-security-groups-overview.md#application-security-groups)을 참조하세요. 애플리케이션 보안 그룹의 모든 네트워크 인터페이스는 동일한 가상 네트워크에 있어야 합니다. 애플리케이션 보안 그룹에 네트워크 인터페이스를 추가하는 방법을 알아보려면 [애플리케이션 보안 그룹에 네트워크 인터페이스 추가](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups)를 참조합니다.

### <a name="create-an-application-security-group"></a>애플리케이션 보안 그룹 만들기

1. [Azure Portal](https://portal.azure.com) 메뉴 또는 **홈** 페이지에서 **리소스 만들기** 를 선택합니다.

2. 검색 상자에서 *애플리케이션 보안 그룹* 을 입력합니다.

3. **애플리케이션 보안 그룹** 페이지에서 **만들기** 를 선택합니다.

4. **애플리케이션 보안 그룹 만들기** 페이지의 **기본 사항** 탭에서 다음 설정에 대한 값을 설정합니다.

    | 설정 | 작업 |
    | --- | --- |
    | **구독** | 구독을 선택합니다. |
    | **리소스 그룹** | 기존 리소스 그룹을 선택하거나 **새로 만들기** 를 선택하여 새 리소스 그룹을 만듭니다. |
    | **이름** | 리소스 그룹 내에 고유한 텍스트 문자열을 입력합니다. |
    | **지역** | 원하는 위치를 선택합니다. |

5. **검토 + 만들기** 를 선택합니다.

6. **검토 + 만들기** 탭에서 **유효성 검사 통과** 메시지가 표시되면 **만들기** 를 선택합니다.

#### <a name="commands"></a>명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network asg create](/cli/azure/network/asg#az_network_asg_create) |
| PowerShell | [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup) |

### <a name="view-all-application-security-groups"></a>모든 애플리케이션 보안 그룹 보기

[Azure Portal](https://portal.azure.com)로 이동하여 애플리케이션 보안 그룹을 봅니다. **애플리케이션 보안 그룹** 을 검색하여 선택합니다. Azure Portal에 애플리케이션 보안 그룹의 목록이 표시됩니다.

#### <a name="commands"></a>명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network asg list](/cli/azure/network/asg#az_network_asg_list) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="view-details-of-a-specific-application-security-group"></a>특정 애플리케이션 보안 그룹의 세부 정보 보기

1. [Azure Portal](https://portal.azure.com)로 이동하여 애플리케이션 보안 그룹을 봅니다. **애플리케이션 보안 그룹** 을 검색하여 선택합니다.

2. 세부 정보를 보려는 애플리케이션 보안 그룹의 이름을 선택합니다.

#### <a name="commands"></a>명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network asg show](/cli/azure/network/asg#az_network_asg_show) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="change-an-application-security-group"></a>애플리케이션 보안 그룹 변경

1. [Azure Portal](https://portal.azure.com)로 이동하여 애플리케이션 보안 그룹을 봅니다. **애플리케이션 보안 그룹** 을 검색하여 선택합니다.

2. 변경하려는 애플리케이션 보안 그룹의 이름을 선택합니다.

3. 수정하려는 설정 옆의 **변경** 을 선택합니다. 예를 들어 **태그** 를 추가 또는 제거하거나 **리소스 그룹** 또는 **구독** 을 변경할 수 있습니다.

    > [!NOTE]
    > 위치는 변경할 수 없습니다.

    메뉴 모음에서 **IAM(액세스 제어)** 를 선택할 수도 있습니다. **IAM(액세스 제어)** 페이지에서 애플리케이션 보안 그룹에 대한 사용 권한을 할당하거나 제거할 수 있습니다.

#### <a name="commands"></a>명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network asg update](/cli/azure/network/asg#az_network_asg_update) |
| PowerShell | PowerShell cmdlet 없음 |

### <a name="delete-an-application-security-group"></a>애플리케이션 보안 그룹 삭제

애플리케이션 보안 그룹에 네트워크 인터페이스가 있는 경우에는 삭제할 수 없습니다. 애플리케이션 보안 그룹에서 네트워크 인터페이스를 모두 삭제하려면 네트워크 인터페이스 설정을 변경하거나 네트워크 인터페이스를 삭제합니다. 자세히 알아보려면 [애플리케이션 보안 그룹에서 추가 또는 삭제](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) 또는 [네트워크 인터페이스 삭제](virtual-network-network-interface.md#delete-a-network-interface)를 참조하세요.

1. [Azure Portal](https://portal.azure.com)로 이동하여 애플리케이션 보안 그룹을 관리합니다. **애플리케이션 보안 그룹** 을 검색하여 선택합니다.

2. 삭제하려는 애플리케이션 보안 그룹의 이름을 선택합니다.

3. **삭제** 를 선택하고 **예** 를 선택하여 애플리케이션 보안 그룹을 삭제합니다.

#### <a name="commands"></a>명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network asg delete](/cli/azure/network/asg#az_network_asg_delete) |
| PowerShell | [Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup) |

## <a name="permissions"></a>사용 권한

네트워크 보안 그룹, 보안 규칙 및 애플리케이션 보안 그룹에서 작업을 수행하려면 다음 표에 나열된 적절한 사용 권한이 할당된 [네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할 또는 [사용자 지정 역할](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)에 계정을 할당해야 합니다.

### <a name="network-security-group"></a>네트워크 보안 그룹

| 작업                                                        |   이름                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   네트워크 보안 그룹 가져오기                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   네트워크 보안 그룹 만들기 또는 업데이트                             |
| Microsoft.Network/networkSecurityGroups/delete                |   네트워크 보안 그룹 삭제                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   네트워크 보안 그룹을 서브넷 또는 네트워크 인터페이스에 연결 


>[!NOTE]
> 네트워크 보안 그룹에 대해 `write` 작업을 수행하려면 구독 계정에 `Microsoft.Network/networkSecurityGroups/write` 권한과 함께 리소스 그룹에 대한 최소 `read` 권한이 있어야 합니다.


### <a name="network-security-group-rule"></a>네트워크 보안 그룹 규칙

| 작업                                                        |   이름                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/securityRules/read            |   규칙 가져오기                                                            |
| Microsoft.Network/networkSecurityGroups/securityRules/write           |   규칙 만들기 또는 업데이트                                               |
| Microsoft.Network/networkSecurityGroups/securityRules/delete          |   규칙 삭제                                                         |

### <a name="application-security-group"></a>애플리케이션 보안 그룹

| 작업                                                                     | 이름                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | IP 구성을 애플리케이션 보안 그룹에 조인|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | 보안 규칙을 애플리케이션 보안 그룹에 조인    |
| Microsoft.Network/applicationSecurityGroups/read                           | 애플리케이션 보안 그룹 가져오기                        |
| Microsoft.Network/applicationSecurityGroups/write                          | 애플리케이션 보안 그룹 만들기 또는 업데이트           |
| Microsoft.Network/applicationSecurityGroups/delete                         | 애플리케이션 보안 그룹 삭제                     |

## <a name="next-steps"></a>다음 단계

- [PowerShell](powershell-samples.md) 또는 [Azure CLI](cli-samples.md) 샘플 스크립트를 사용하거나 Azure [Resource Manager 템플릿](template-samples.md)을 사용하여 네트워크 또는 애플리케이션 보안 그룹 만들기
- 가상 네트워크에 대한 [Azure Policy 정의](./policy-reference.md) 만들기 및 할당