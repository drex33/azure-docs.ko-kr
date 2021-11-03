---
title: Az.Search 모듈을 사용하는 PowerShell 스크립트
titleSuffix: Azure Cognitive Search
description: PowerShell을 사용하여 Azure Cognitive Search 서비스를 만들고 구성합니다. 서비스를 확대하거나 축소하고, 관리자 및 쿼리 API 키를 관리하며, 시스템 정보를 쿼리할 수 있습니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 08/03/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b0dea6bebd20ca5071a4c1395d5835f31c80e90c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131048105"
---
# <a name="manage-your-azure-cognitive-search-service-with-powershell"></a>PowerShell 스크립트를 사용하여 Azure Cognitive Search 서비스 관리
> [!div class="op_single_selector"]
> * [포털](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [Azure CLI](search-manage-azure-cli.md)
> * [REST API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)

Windows, Linux 또는 [Azure Cloud Shell](../cloud-shell/overview.md)에서 PowerShell cmdlet 및 스크립트를 실행하여 Azure Cognitive Search를 만들고 구성할 수 있습니다. **Az.Search** 모듈은 [Search Management REST APIs](/rest/api/searchmanagement)와 완전한 패리티를 이루고 다음과 같은 작업을 수행할 수 있도록 [Azure PowerShell](/powershell/)를 확장합니다.

> [!div class="checklist"]
> * [구독에서 검색 서비스 나열](#list-search-services)
> * [서비스 정보 반환](#get-search-service-information)
> * [서비스 만들기 또는 삭제](#create-or-delete-a-service)
> * [프라이빗 엔드포인트를 사용하여 서비스 만들기](#create-a-service-with-a-private-endpoint)
> * [관리자 API 키 다시 생성](#regenerate-admin-keys)
> * [쿼리 API 키 만들기 또는 삭제](#create-or-delete-query-keys)
> * [복제본 및 파티션을 사용하여 확장 또는 축소](#scale-replicas-and-partitions)
> * [공유 프라이빗 링크 리소스 만들기](#create-a-shared-private-link-resource)

경우에 따라 위의 목록에 *없는* 작업에 대한 질문을 받게 됩니다. 현재는 **Az.Search** 모듈 또는 Management REST API를 사용하여 서버 이름, 지역 또는 계층을 변경할 수 없습니다. 서비스를 만들 때 전용 리소스가 할당됩니다. 따라서 기본 하드웨어(위치 또는 노드 형식)를 변경하려면 새 서비스가 필요합니다. 한 서비스에서 다른 서비스로 인덱스와 같은 콘텐츠를 전송하기 위한 도구나 API도 마찬가지로 없습니다.

서비스 내에서 콘텐츠 만들기 및 관리는 [Search Service REST API](/rest/api/searchservice/) 또는 [.NET SDK](/dotnet/api/overview/azure/search.documents-readme)를 통해 진행됩니다. 콘텐츠에 대한 전용 PowerShell 명령은 없지만 REST 또는 .NET API를 호출하여 인덱스를 만들고 로드하는 PowerShell 스크립트를 작성할 수 있습니다.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>버전 확인 및 모듈 로드

이 문서의 예제는 대화형이며 관리자 권한이 필요합니다. Azure PowerShell ( **Az** 모듈)이 설치되어 있어야 합니다. 자세한 내용은 [Azure PowerShell 설치](/powershell/azure/)를 참조하세요.

### <a name="powershell-version-check-51-or-later"></a>PowerShell 버전 확인(5.1 이상)

지원되는 모든 운영 체제에서 로컬 PowerShell은 5.1 이상이어야 합니다.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Azure Powershell 로드

**Az** 가 설치되어 있는지 확실하지 않은 경우, 확인 단계로 다음 명령을 실행합니다. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

일부 시스템은 모듈을 자동으로 로드하지 않습니다. 이전 명령에서 오류가 발생하는 경우 모듈 로드를 시도하고, 이것이 실패하면 설치 지침으로 돌아가서 누락된 단계가 있는지 확인합니다.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>브라우저 로그인 토큰을 사용하여 Azure에 연결

포털 로그인 자격 증명을 사용하여 PowerShell에서 구독에 연결할 수 있습니다. 또는 [비대화형으로 서비스 주체를 사용하여 인증](../active-directory/develop/howto-authenticate-service-principal-powershell.md)할 수 있습니다.

```azurepowershell-interactive
Connect-AzAccount
```

여러 Azure 구독이 있는 경우, Azure 구독을 설정합니다. 현재 구독 목록을 보려면 다음 명령을 실행합니다.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

구독을 지정하려면 다음 명령을 실행합니다. 다음 예제에서 구독 이름은 `ContosoSubscription`입니다.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>구독에서 서비스 나열

다음 명령은 [**Az.Resources**](/powershell/module/az.resources)로부터 구독에 이미 프로비저닝된 기존 리소스 및 서비스에 대한 정보를 반환합니다. 이미 생성된 검색 서비스 수를 모르는 경우 이러한 명령은 해당 정보를 반환해 주므로 포털로 이동해 확인할 필요가 없습니다.

첫 번째 명령은 모든 검색 서비스를 반환합니다.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

서비스 목록에서 특정 리소스에 대한 정보를 반환합니다.

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

결과는 다음 출력과 비슷합니다.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>Az.Search 가져오기

[**Az.Search**](/powershell/module/az.search)의 명령은 모듈을 로드할 때까지 사용할 수 없습니다.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>모든 Az.Search 명령 나열

확인 단계로 모듈에서 제공하는 명령 목록을 반환합니다.

```azurepowershell-interactive
Get-Command -Module Az.Search
```

결과는 다음 출력과 비슷합니다.

```
CommandType     Name                                               Version    Source                                                                
-----------     ----                                               -------    ------                                                                
Cmdlet          Get-AzSearchAdminKeyPair                           0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchPrivateEndpointConnection              0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchPrivateLinkResource                    0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchQueryKey                               0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchService                                0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchSharedPrivateLinkResource              0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchAdminKey                               0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchQueryKey                               0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchService                                0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchSharedPrivateLinkResource              0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchPrivateEndpointConnection           0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchQueryKey                            0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchService                             0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchSharedPrivateLinkResource           0.8.0      Az.Search                                                             
Cmdlet          Set-AzSearchPrivateEndpointConnection              0.8.0      Az.Search                                                             
Cmdlet          Set-AzSearchService                                0.8.0      Az.Search                                                             
Cmdlet          Set-AzSearchSharedPrivateLinkResource              0.8.0      Az.Search   
```

이전 버전의 패키지가 있는 경우, 모듈을 업데이트하여 최신 기능을 가져옵니다.

```azurepowershell-interactive
Update-Module -Name Az.Search
```

## <a name="get-search-service-information"></a>검색 서비스 정보 가져오기

**Az.Search** 를 가져오고 검색 서비스를 포함하는 리소스 그룹을 알게 된 이후, [Get-AzSearchService](/powershell/module/az.search/get-azsearchservice)를 실행하여 이름, 지역, 계층, 복제본 및 파티션 개수를 포함한 서비스 정의를 반환할 수 있습니다. 이 명령의 경우 검색 서비스를 포함하는 리소스 그룹을 제공합니다.

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

결과는 다음 출력과 비슷합니다.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : West US
Sku               : Standard
ReplicaCount      : 1
PartitionCount    : 1
HostingMode       : Default
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-or-delete-a-service"></a>서비스 만들기 또는 삭제

[**New-AzSearchService**](/powershell/module/az.search/new-azsearchservice)는 [새 검색 서비스를 만드는](search-create-service-portal.md) 데 사용됩니다.

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3 -HostingMode Default
``` 
결과는 다음 출력과 비슷합니다.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Id                : /subscriptions/<alphanumeric-subscription-ID>/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 3
PartitionCount    : 3
HostingMode       : Default
Tags
```     

### <a name="create-a-service-with-ip-rules"></a>IP 규칙을 사용하여 서비스 만들기

보안 요구 사항에 따라 [구성된 IP 방화벽](service-configure-firewall.md)을 사용하여 검색 서비스를 만드는 것이 좋을 수도 있습니다. 이렇게 하려면 먼저 IP 규칙을 정의한 이를 아래와 같이 `IPRuleList` 매개 변수에 전달합니다.

```azurepowershell-interactive
$ipRules = @([pscustomobject]@{Value="55.5.63.73"},
        [pscustomobject]@{Value="52.228.215.197"},
        [pscustomobject]@{Value="101.37.221.205"})

 New-AzSearchService -ResourceGroupName <resource-group-name> `
                      -Name <search-service-name> `
                      -Sku Standard `
                      -Location "West US" `
                      -PartitionCount 3 -ReplicaCount 3 `
                      -HostingMode Default `
                      -IPRuleList $ipRules
```

### <a name="create-a-service-with-a-system-assigned-managed-identity"></a>시스템 할당 관리 ID를 사용하여 서비스 만들기

[관리 ID를 사용하여 데이터 원본에 연결](search-howto-managed-identities-storage.md)하는 경우와 같은 일부의 경우 [시스템 할당 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 설정해야 합니다. 명령에 `-IdentityType SystemAssigned`를 추가하면 이를 수행할 수 있습니다.

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName <resource-group-name> `
                      -Name <search-service-name> `
                      -Sku Standard `
                      -Location "West US" `
                      -PartitionCount 3 -ReplicaCount 3 `
                      -HostingMode Default `
                      -IdentityType SystemAssigned
```

## <a name="create-a-service-with-a-private-endpoint"></a>프라이빗 엔드포인트를 사용하여 서비스 만들기

Azure Cognitive Search를 위한 [프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)는 가상 네트워크의 클라이언트가 [프라이빗 링크](../private-link/private-link-overview.md)를 통해 검색 인덱스의 데이터에 안전하게 액세스할 수 있도록 합니다. 프라이빗 엔드포인트는 검색 서비스에 [가상 네트워크 주소 공간](../virtual-network/ip-services/private-ip-addresses.md)의 IP 주소를 사용합니다. 클라이언트와 검색 서비스 간의 네트워크 트래픽은 가상 네트워크와 Microsoft 백본 네트워크의 프라이빗 링크를 통과하여 퍼블릭 인터넷에서 공개되지 않도록 합니다. 자세한 내용은 [Azure Cognitive Search용 프라이빗 엔드포인트 만들기](service-create-private-endpoint.md)를 참조하세요.

다음 예제에서는 프라이빗 엔드포인트를 사용하여 검색 서비스를 만드는 방법을 보여 줍니다. 

먼저 `PublicNetworkAccess`가 `Disabled`로 설정된 검색 서비스를 배포합니다.

```azurepowershell-interactive
$searchService = New-AzSearchService `
    -ResourceGroupName <search-service-resource-group-name> `
    -Name <search-service-name> `
    -Sku Standard `
    -Location "West US" `
    -PartitionCount 1 -ReplicaCount 1 `
    -HostingMode Default `
    -PublicNetworkAccess Disabled
```

그런 다음 가상 네트워크, 프라이빗 네트워크 연결 및 프라이빗 엔드포인트를 만듭니다.

```azurepowershell-interactive
# Create the subnet
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
    -Name <subnet-name> `
    -AddressPrefix 10.1.0.0/24 `
    -PrivateEndpointNetworkPolicies Disabled 

# Create the virtual network
$virtualNetwork = New-AzVirtualNetwork `
    -ResourceGroupName <vm-resource-group-name> `
    -Location "West US" `
    -Name <virtual-network-name> `
    -AddressPrefix 10.1.0.0/16 `
    -Subnet $subnetConfig

# Create the private network connection
$privateLinkConnection = New-AzPrivateLinkServiceConnection `
    -Name <private-link-name> `
    -PrivateLinkServiceId $searchService.Id `
    -GroupId searchService

# Create the private endpoint
$privateEndpoint = New-AzPrivateEndpoint `
    -Name <private-endpoint-name> `
    -ResourceGroupName <private-endpoint-resource-group-name> `
    -Location "West US" `
    -Subnet $virtualNetwork.subnets[0] `
    -PrivateLinkServiceConnection $privateLinkConnection
```

마지막으로, 프라이빗 DNS 영역을 만듭니다. 

```azurepowershell-interactive
## Create private dns zone
$zone = New-AzPrivateDnsZone `
    -ResourceGroupName <private-dns-resource-group-name> `
    -Name "privatelink.search.windows.net"

## Create dns network link
$link = New-AzPrivateDnsVirtualNetworkLink `
    -ResourceGroupName <private-dns-link-resource-group-name> `
    -ZoneName "privatelink.search.windows.net" `
    -Name "myLink" `
    -VirtualNetworkId $virtualNetwork.Id

## Create DNS configuration 
$config = New-AzPrivateDnsZoneConfig `
    -Name "privatelink.search.windows.net" `
    -PrivateDnsZoneId $zone.ResourceId

## Create DNS zone group
New-AzPrivateDnsZoneGroup `
    -ResourceGroupName <private-dns-zone-resource-group-name> `
    -PrivateEndpointName <private-endpoint-name> `
    -Name 'myZoneGroup' `
    -PrivateDnsZoneConfig $config
```

PowerShell에서 프라이빗 엔드포인트를 만드는 방법에 대한 자세한 내용은 이 [프라이빗 링크 빠른 시작](../private-link/create-private-endpoint-powershell.md)을 참조하세요.

### <a name="manage-private-endpoint-connections"></a>프라이빗 엔드포인트 연결 관리

프라이빗 엔드포인트 연결을 만드는 것 외에도 연결에 대해 `Get`, `Set`, `Remove`를 사용할 수 있습니다.

[Get-AzSearchPrivateEndpointConnection](/powershell/module/az.search/Get-AzSearchPrivateEndpointConnection)은 프라이빗 엔드포인트 연결을 검색하고 해당 상태를 확인하는 데 사용됩니다.

```azurepowershell-interactive
Get-AzSearchPrivateEndpointConnection -ResourceGroupName <search-service-resource-group-name> -ServiceName <search-service-name>
```

[Set-AzSearchPrivateEndpointConnection](/powershell/module/az.search/Set-AzSearchPrivateEndpointConnection)은 연결을 업데이트하는 데 사용됩니다. 다음 예제에서는 프라이빗 엔드포인트 연결을 거부됨으로 설정합니다.

```azurepowershell-interactive
Set-AzSearchPrivateEndpointConnection -ResourceGroupName <search-service-resource-group-name> -ServiceName <search-service-name> -Name <pe-connection-name> -Status Rejected  -Description "Rejected"
```

[Remove-AzSearchPrivateEndpointConnection](/powershell/module/az.search/Remove-AzSearchPrivateEndpointConnection)은 프라이빗 엔드포인트 연결을 삭제하는 데 사용됩니다.

```azurepowershell-interactive
 Remove-AzSearchPrivateEndpointConnection -ResourceGroupName <search-service-resource-group-name> -ServiceName <search-service-name> -Name <pe-connection-name>
```

## <a name="regenerate-admin-keys"></a>관리자 키 다시 생성

[**New-AzSearchAdminKey**](/powershell/module/az.search/new-azsearchadminkey)는 관리자 [API 키](search-security-api-keys.md)를 롤오버하는 데 사용됩니다. 각 서비스에서 인증된 액세스에 대해 두 개의 관리자 키가 생성됩니다. 모든 요청에 키가 필요합니다. 두 개의 관리자 키는 기능적으로 동일하며, 정보를 검색하거나 개체를 만들고 삭제할 수 있도록 검색 서비스에 완전한 쓰기 권한을 부여합니다. 다른 키를 대체할 때 사용할 수 있도록 두 개의 키가 있습니다. 

`primary` 또는 `secondary`로 지정된 키를 한 번에 하나씩만 다시 생성할 수 있습니다. 중단 없는 서비스의 경우 기본 키를 교체하는 동안 보조 키를 사용하도록 모든 클라이언트 코드를 업데이트해야 합니다. 작업이 진행되는 동안에는 키를 변경하지 마세요.

짐작할 수 있듯이 클라이언트 코드를 업데이트하지 않고 키를 다시 생성하면 이전 키를 사용하는 요청이 실패합니다. 새 키를 모두 다시 생성하더라도 서비스에 영구적으로 액세스하지 못하는 것은 아니며, 포털을 통해 서비스에 계속 액세스할 수 있습니다. 기본 및 보조 키를 다시 생성한 후 새 키를 사용하도록 클라이언트 코드를 업데이트할 수 있으며 그에 따라 작업이 다시 시작됩니다.

API 키 값은 서비스에 의해 생성됩니다. 사용할 Azure Cognitive Search에 대한 사용자 지정 키를 제공할 수 없습니다. 이와 마찬가지로, 관리자 API 키에 대한 사용자 정의 이름이 없습니다. 키에 대한 참조는 `primary` 또는 `secondary`의 고정 문자열입니다. 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <search-service-resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

결과는 다음 출력과 비슷합니다. 한 번에 하나씩만 변경하더라도 두 키가 모두 반환됩니다.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>쿼리 키 만들기 또는 삭제

클라이언트 앱에서 Azure Cognitive Search 인덱스에 읽기 전용으로 액세스하기 위한 쿼리 [API 키](search-security-api-keys.md)를 만들려면 [**New-AzSearchQueryKey**](/powershell/module/az.search/new-azsearchquerykey)를 사용합니다. 쿼리 키는 검색 결과를 검색하기 위해 특정 인덱스에 인증하는 경우에 사용됩니다. 쿼리 키는 인덱스, 데이터 원본 또는 인덱서와 같은 다른 서비스 항목에 대한 읽기 전용 액세스 권한을 부여하지 않습니다.

사용할 Azure Cognitive Search에 대한 키를 제공할 수 없습니다. API 키는 서비스에 의해 생성됩니다.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <search-service-resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>복제본 및 파티션 스케일링

[**Set-AzSearchService**](/powershell/module/az.search/set-azsearchservice)는 서비스 내에서 청구 가능한 리소스를 다시 조정하기 위해 [복제본과 파티션을 늘리거나 줄이는](search-capacity-planning.md) 데 사용됩니다. 복제본 또는 파티션을 늘리면 청구 요금이 증가하며, 이 요금에는 고정 요금과 변동 요금이 둘 다 포함되어 있습니다. 추가 처리 성능이 일시적으로 필요한 경우에는 복제본과 파티션을 늘려 워크로드를 처리할 수 있습니다. 개요 포털 페이지의 모니터링 영역에는 쿼리 대기 시간, 초당 쿼리 수 및 제한(현재 용량이 충분한지 여부를 나타냄)에 대한 타일이 있습니다.

리소스를 추가하거나 제거하는 데 시간이 걸릴 수 있습니다. 용량에 대한 조정 작업은 백그라운드에서 발생하므로 기존 작업을 계속할 수 있습니다. 추가 구성할 필요 없이, 추가 용량이 준비되는 즉시 들어오는 요청에 사용됩니다. 

용량 제거로 인해 장애를 초래할 수 있습니다. 요청 실패를 방지하기 위해 용량을 줄이기 전, 모든 인덱싱 및 인덱서 작업을 중지하는 것이 좋습니다. 그렇게 할 수 없는 경우에는 새 대상 수준에 도달할 때까지 한 번에 하나의 복제본과 파티션을 증분 방식으로 용량을 줄이는 것이 좋습니다.

명령을 제출한 후에는 도중에 종료할 수 있는 방법이 없습니다. 개수를 수정하기 전에 명령이 완료될 때까지 기다려야 합니다.

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <search-service-resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

결과는 다음 출력과 비슷합니다.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 6
PartitionCount    : 6
HostingMode       : Default
Id                : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-a-shared-private-link-resource"></a>공유 프라이빗 링크 리소스 만들기

Azure Cognitive Search API를 통해 생성된 보안 리소스의 프라이빗 엔드포인트를 *공유 프라이빗 링크 리소스* 라고 합니다. 이는 [Azure Private Link 서비스](https://azure.microsoft.com/services/private-link/)와 통합된 스토리지 계정과 같이, 리소스에 대한 액세스를 “공유”하는 것이기 때문입니다.

인덱서를 사용하여 Azure Cognitive Search에서 데이터를 인덱싱하는 경우 데이터 원본이 프라이빗 네트워크에 있으면 아웃바운드 [프라이빗 엔드포인트 연결](../private-link/private-endpoint-overview.md)을 만들어 데이터에 연결할 수 있습니다.

Azure Cognitive Search에서 아웃바운드 프라이빗 엔드포인트를 만들 수 있는 Azure 리소스의 전체 목록은 [여기](search-indexer-howto-access-private.md#shared-private-link-resources-management-apis)에서 관련된 **그룹 ID** 값과 함께 찾을 수 있습니다.

[New-AzSearchSharedPrivateLinkResource](/powershell/module/az.search/New-AzSearchSharedPrivateLinkResource)는 공유 프라이빗 링크 리소스를 만드는 데 사용됩니다. 이 명령을 실행하기 전에 일부 구성이 데이터 원본에 필요할 수 있습니다.

```azurepowershell-interactive
New-AzSearchSharedPrivateLinkResource -ResourceGroupName <search-serviceresource-group-name> -ServiceName <search-service-name> -Name <spl-name> -PrivateLinkResourceId /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/<storage-resource-group-name>/providers/Microsoft.Storage/storageAccounts/myBlobStorage -GroupId <group-id> -RequestMessage "Please approve" 
```

[Get-AzSearchSharedPrivateLinkResource](/powershell/module/az.search/Get-AzSearchSharedPrivateLinkResource)를 사용하면 공유 프라이빗 링크 리소스를 검색하고 해당 상태를 볼 수 있습니다.

```azurepowershell-interactive
Get-AzSearchSharedPrivateLinkResource -ResourceGroupName <search-service-resource-group-name> -ServiceName <search-service-name> -Name <spl-name>
```

다음 명령을 사용하여 연결을 승인해야 이를 사용할 수 있습니다.

```azurepowershell-interactive
Approve-AzPrivateEndpointConnection `
    -Name <spl-name> `
    -ServiceName <search-service-name> `
    -ResourceGroupName <search-service-resource-group-name> `
    -Description = "Approved"
```

[Remove-AzSearchSharedPrivateLinkResource](/powershell/module/az.search/Remove-AzSearchSharedPrivateLinkResource)는 공유 프라이빗 링크 리소스를 삭제하는 데 사용됩니다.

```azurepowershell-interactive
$job = Remove-AzSearchSharedPrivateLinkResource -ResourceGroupName <search-service-resource-group-name> -ServiceName <search-service-name> -Name <spl-name> -Force -AsJob

$job | Get-Job
```

공유 프라이빗 링크 리소스를 설정하는 방법에 대한 자세한 내용은 [프라이빗 엔드포인트를 통한 인덱서 연결 만들기](search-indexer-howto-access-private.md)에 대한 설명서를 참조하세요.

## <a name="next-steps"></a>다음 단계

[인덱스](search-what-is-an-index.md)를 작성하고 포털, REST API 또는 .NET SDK를 사용하여 [인덱스를 쿼리](search-query-overview.md)합니다.

* [Azure Portal에서 Azure Cognitive Search 인덱스 만들기](search-get-started-portal.md)
* [기타 서비스에서 데이터를 로드하기 위한 인덱서 설정](search-indexer-overview.md)
* [Azure Portal에서 검색 탐색기를 사용하여 Azure Cognitive Search 인덱스 쿼리](search-explorer.md)
* [.NET에서 Azure Cognitive Search를 사용하는 방법](search-howto-dotnet-sdk.md)