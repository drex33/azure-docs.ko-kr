---
title: Azure DNS에서 DNS 영역 관리 - PowerShell | Microsoft Docs
description: Azure PowerShell을 사용하여 DNS 영역을 관리할 수 있습니다. 이 문서에서는 Azure DNS에서 DNS 영역을 업데이트, 삭제 및 만드는 방법을 설명합니다.
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2021
ms.author: rohink
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 02c6518a1ccfaa678c42369ae22f67670aaf0566
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108203268"
---
# <a name="how-to-manage-dns-zones-using-powershell"></a>PowerShell을 사용하여 DNS 영역을 관리하는 방법

> [!div class="op_single_selector"]
> * [포털](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure 클래식 CLI](./dns-operations-dnszones-cli.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)

이 문서는 Azure PowerShell을 사용하여 DNS 영역을 관리하는 방법을 보여줍니다. 플랫폼 간 [Azure CLI](dns-operations-dnszones-cli.md) 또는 Azure Portal을 사용하여 DNS 영역을 관리할 수도 있습니다.

이 가이드에서는 특히 공용 DNS 영역을 다룹니다. Azure PowerShell을 사용하여 Azure DNS에서 프라이빗 영역 관리에 대한 자세한 내용은 [Azure PowerShell을 사용하여 Azure DNS Private Zones 시작](private-dns-getstarted-powershell.md)을 참조하세요.

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]

## <a name="create-a-dns-zone"></a>DNS 영역 만들기

DNS 영역은 `New-AzDnsZone` cmdlet을 사용하여 생성됩니다.

다음 예제에서는 *MyDNSResourceGroup* 이라는 리소스 그룹에 *contoso.com* 이라는 DNS 영역을 만듭니다.

```azurepowershell-interactive
New-AzDnsZone -Name contoso.com -ResourceGroupName MyDNSResourceGroup
```

다음 예제에서는 두 [Azure Resource Manager 태그](dns-zones-records.md#tags), *project = demo* 및 *env = test* 를 사용하여 DNS 영역을 만드는 방법을 보여 줍니다.

```azurepowershell-interactive
New-AzDnsZone -Name contoso.com -ResourceGroupName MyDNSResourceGroup -Tag @{ project="demo"; env="test" }
```

## <a name="get-a-dns-zone"></a>DNS 영역 가져오기

DNS 영역을 가져오려면 `Get-AzDnsZone` cmdlet을 사용합니다. 이 작업은 Azure DNS의 기존 영역에 해당하는 DNS 영역 개체를 반환합니다. 이 개체는 영역에 대한 데이터(예: 레코드 집합 수)를 포함하지만 레코드 집합 자체는 포함하지 않습니다(`Get-AzDnsRecordSet` 참조).

```azurepowershell-interactive
Get-AzDnsZone -Name contoso.com –ResourceGroupName MyDNSResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-8ec2-f4879750d201
Tags                  : {project, env}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org.,
                        ns4-01.azure-dns.info.}
NumberOfRecordSets    : 2
MaxNumberOfRecordSets : 5000
```

## <a name="list-dns-zones"></a>DNS 영역 나열

`Get-AzDnsZone`에서 영역 이름을 생략하면, 리소스 그룹의 모든 영역을 열거할 수 있습니다. 이 작업은 영역 개체의 배열을 반환합니다.

```azurepowershell-interactive
$zoneList = Get-AzDnsZone -ResourceGroupName MyDNSResourceGroup
$zoneList
```

`Get-AzDnsZone`에서 영역 이름 및 리소스 그룹 이름을 모두 생략하여 Azure 구독의 모든 영역을 열거할 수 있습니다.

```azurepowershell-interactive
$zoneList = Get-AzDnsZone
$zoneList
```

## <a name="update-a-dns-zone"></a>DNS 영역 업데이트

`Set-AzDnsZone`를 사용하여 DNS 영역 리소스를 변경할 수 있습니다. 이 cmdlet은 영역 내의 DNS 레코드 집합을 업데이트하지 않습니다([DNS 레코드를 관리하는 방법](dns-operations-recordsets.md)참조). 영역 리소스 자체의 속성을 업데이트하는 데만 사용됩니다. 이러한 쓰기 가능 영역 속성은 현재 [영역 리소스에 대한 Azure Resource Manager '태그'](dns-zones-records.md#tags)로 제한됩니다.

다음 두 가지 방법 중 하나를 사용하여 DNS 영역을 업데이트합니다.

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>영역 이름 및 리소스 그룹을 사용하여 영역을 지정합니다.

이 접근 방법은 기존 영역 태그를 지정된 값으로 대체합니다.

```azurepowershell-interactive
Set-AzDnsZone -Name contoso.com -ResourceGroupName MyDNSResourceGroup -Tag @{ project="demo"; env="test" }
```

### <a name="specify-the-zone-using-a-zone-object"></a>$zone 개체를 사용하여 영역을 지정합니다.

이 방법은 기존 영역 개체를 검색하고, 태그를 수정한 다음, 변경 내용을 커밋합니다. 이러한 방식으로 기존 태그를 보존할 수 있습니다.

```azurepowershell-interactive
# Get the zone object
$zone = Get-AzDnsZone -Name contoso.com -ResourceGroupName MyDNSResourceGroup

# Remove an existing tag
$zone.Tags.Remove("project")

# Add a new tag
$zone.Tags.Add("status","approved")

# Commit changes
Set-AzDnsZone -Zone $zone
```

$zone 개체와 함께 `Set-AzDnsZone`을 사용하는 경우 동시 변경 내용을 덮어쓰지 않도록 [Etag 검사](dns-zones-records.md#etags)가 사용됩니다. 선택적 `-Overwrite` 스위치를 사용하여 이러한 검사를 무시할 수 있습니다.

## <a name="delete-a-dns-zone"></a>DNS 영역 삭제

`Remove-AzDnsZone` cmdlet을 사용하여 DNS 영역을 삭제할 수 있습니다.

> [!NOTE]
> DNS 영역을 삭제하면 영역 내의 모든 DNS 레코드도 삭제됩니다. 이 작업은 실행 취소할 수 없습니다. DNS 영역을 사용 중인 경우 영역이 삭제되면 영역을 사용하는 서비스가 실패합니다.
>
>실수로 영역이 삭제되는 것을 방지하려면 [DNS 영역 및 레코드를 보호하는 방법](dns-protect-zones-recordsets.md)을 참조하세요.


다음 두 가지 방법 중 하나를 사용하여 DNS 영역을 삭제합니다.

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>영역 이름 및 리소스 그룹 이름을 사용하여 영역을 지정합니다.

```azurepowershell-interactive
Remove-AzDnsZone -Name contoso.com -ResourceGroupName MyDNSResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>$zone 개체를 사용하여 영역을 지정합니다.

`Get-AzDnsZone`에서 반환된 `$zone` 개체를 사용하여 삭제될 영역을 지정할 수 있습니다.

```azurepowershell-interactive
$zone = Get-AzDnsZone -Name contoso.com -ResourceGroupName MyDNSResourceGroup
Remove-AzDnsZone -Zone $zone
```

영역 개체를 매개 변수로 전달하는 대신 파이프할 수도 있습니다.

```azurepowershell-interactive
Get-AzDnsZone -Name contoso.com -ResourceGroupName MyDNSResourceGroup | Remove-AzDnsZone

```

`Set-AzDnsZone`과 마찬가지로, `$zone` 개체를 사용하여 영역을 지정하면 동시 변경 내용을 삭제하지 않도록 Etag 검사가 사용됩니다. 이러한 검사를 무시하려면 `-Overwrite` 스위치를 사용합니다.

## <a name="confirmation-prompts"></a>확인 메시지 표시

`New-AzDnsZone`, `Set-AzDnsZone` 및 `Remove-AzDnsZone` cmdlet은 모두 확인 메시지를 표시하도록 지원합니다.

`$ConfirmPreference` PowerShell 기본 설정 변수 값에 `Medium` 이하의 값이 있는 경우 `New-AzDnsZone` 및 `Set-AzDnsZone`은 확인 메시지를 표시합니다. DNS 영역을 삭제하면 잠재적으로 원치 않는 조건이 발생할 수 있으므로 `Remove-AzDnsZone` cmdlet은 `$ConfirmPreference` PowerShell 변수가 `None` 이외의 값을 갖는 경우 확인 메시지를 표시합니다.

`$ConfirmPreference`의 기본값은 `High`이므로 `Remove-AzDnsZone`는 기본적으로 확인 메시지를 표시합니다.

`-Confirm` 매개 변수를 사용하여 현재 `$ConfirmPreference` 설정을 재정의할 수 있습니다. `-Confirm` 또는 `-Confirm:$True`를 지정하는 경우 cmdlet은 실행하기 전에 확인을 위한 메시지를 표시합니다. `-Confirm:$False`를 지정하는 경우 cmdlet은 확인을 위한 메시지를 표시하지 않습니다.

`-Confirm` 및 `$ConfirmPreference`에 대한 자세한 내용은 [기본 설정 변수 정보](/powershell/module/microsoft.powershell.core/about/about_preference_variables)를 참조하세요.

## <a name="next-steps"></a>다음 단계

DNS 영역에서 [레코드 집합 및 레코드 관리](dns-operations-recordsets.md) 방법을 알아봅니다.
<br>
[Azure DNS에 도메인을 위임](dns-domain-delegation.md)하는 방법을 알아봅니다.
<br>
[Azure DNS PowerShell 참조 설명서](/powershell/module/Az.dns)를 검토합니다.