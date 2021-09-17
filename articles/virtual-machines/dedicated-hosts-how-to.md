---
title: Azure 전용 호스트 배포
description: Vm 및 확장 집합을 전용 호스트로 배포 합니다.
author: brittanyrowe
ms.author: brittanyrowe
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/01/2021
ms.reviewer: cynthn, zivr
ms.openlocfilehash: c4d895551f852c26d9083c570de29d5cbdedfa53
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123441430"
---
# <a name="deploy-vms-and-scale-sets-to-dedicated-hosts"></a>전용 호스트에 Vm 및 확장 집합 배포

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 균일한 확장 집합

이 문서에서는 Vm (가상 머신) 및 확장 집합 인스턴스를 호스트 하는 Azure [전용 호스트](dedicated-hosts.md) 를 만드는 방법을 안내 합니다.


## <a name="limitations"></a>제한 사항

- 전용 호스트에 사용할 수 있는 크기 및 하드웨어 유형은 지역에 따라 다릅니다. 자세히 알아보려면 호스트 [가격 책정 페이지](https://aka.ms/ADHPricing)를 참조하세요.

## <a name="create-a-host-group"></a>호스트 그룹 만들기

**호스트 그룹** 은 전용 호스트의 모음을 나타내는 리소스입니다. 영역 및 가용성 영역에서 호스트 그룹을 만들고 여기에 호스트를 추가합니다. 고가용성을 계획할 때 추가 옵션을 사용할 수 있습니다. 전용 호스트에서 다음 옵션 중 하나 또는 둘 다를 사용할 수 있습니다.
- 여러 가용성 영역으로 확장. 이 경우에는 사용하려는 각 영역에 호스트 그룹이 있어야 합니다.
- 실제 랙에 매핑된 여러 장애 도메인으로 확장.

두 경우 모두 호스트 그룹의 장애 도메인 수를 제공해야 합니다. 그룹의 장애 도메인을 확장하지 않으려면 장애 도메인 수 1을 사용합니다.

가용성 영역 및 장애 도메인을 모두 사용하도록 결정할 수도 있습니다.

### <a name="portal"></a>[포털](#tab/portal)

이 예에서는 하나의 가용성 영역과 두 개의 장애 도메인을 사용하여 호스트 그룹을 만듭니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다.
1. 왼쪽 위 모서리에서 **리소스 만들기** 를 선택합니다.
1. **호스트 그룹** 을 검색한 다음, 결과에서 **호스트 그룹** 을 선택합니다.
1. **호스트 그룹** 페이지에서 **만들기** 를 선택합니다.
1. 사용하려는 구독을 선택한 다음, **새로 만들기** 를 선택하여 새 리소스 그룹을 만듭니다.
1. **이름** 으로 *myDedicatedHostsRG* 를 입력한 다음, **확인** 을 선택합니다.
1. **호스트 그룹 이름** 에 대해 *myHostGroup* 을 입력합니다.
1. **위치** 에 대해 **미국 동부** 를 선택합니다.
1. **가용성 영역** 에 대해 **1** 을 선택합니다.
1. **장애 도메인 수** 에 대해 **2** 를 선택합니다.
1. **자동 배치** 를 선택하여 VM 및 확장 집합 인스턴스를 이 그룹에서 사용 가능한 호스트에 자동으로 할당합니다.
1. **검토 + 만들기** 를 선택한 다음, 유효성 검사를 기다립니다.
1. **유효성 검사 통과** 메시지가 표시되면 **만들기** 를 선택하여 호스트 그룹을 만듭니다.

호스트 그룹을 만드는 데 몇 분 밖에 걸리지 않습니다.


### <a name="cli"></a>[CLI](#tab/cli)

모든 호스트 SKU를 모든 지역 및 가용성 영역에서 사용할 수 있는 것은 아닙니다. 전용 호스트의 프로 비전을 시작 하기 전에 호스트 가용성 및 모든 제안 제한을 나열할 수 있습니다.

```azurecli-interactive
az vm list-skus -l eastus2  -r hostGroups/hosts  -o table
```

이 예제에서는 [az vm host group create](/cli/azure/vm/host/group#az_vm_host_group_create)를 사용하여 가용성 영역 및 장애 도메인을 모두 사용하는 호스트 그룹을 만듭니다.

```azurecli-interactive
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2
```

VM 및 확장 집합 인스턴스가 하나의 호스트 그룹 내에서 자동으로 호스트에 배치할 수 있게 `--automatic-placement true` 매개 변수를 추가합니다. 자세한 내용은 [수동 및 자동 배치](dedicated-hosts.md#manual-vs-automatic-placement)를 참조하세요.


**다른 예제**

[az vm host group create](/cli/azure/vm/host/group#az_vm_host_group_create)를 사용하여 가용성 영역 1에 호스트 그룹을 만들 수도 있습니다(장애 도메인은 없음).

```azurecli-interactive
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1
```

[az vm host group create](/cli/azure/vm/host/group#az_vm_host_group_create)를 통해 장애 도메인만 사용하며 가용성 영역이 지원되지 않는 지역에서 사용될 호스트 그룹을 만드는 예제는 다음과 같습니다.

```azurecli-interactive
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

이 예제에서는 [AzHostGroup](/powershell/module/az.compute/new-azhostgroup) 를 사용 하 여 2 개의 장애 도메인을 포함 하는 영역 1에 호스트 그룹을 만듭니다.


```azurepowershell-interactive
$rgName = "myDHResourceGroup"
$location = "EastUS"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```


VM 및 확장 집합 인스턴스가 하나의 호스트 그룹 내에서 자동으로 호스트에 배치할 수 있게 `-SupportAutomaticPlacement true` 매개 변수를 추가합니다. 자세한 내용은 [수동 및 자동 배치](dedicated-hosts.md#manual-vs-automatic-placement)를 참조하세요.

---


## <a name="create-a-dedicated-host"></a>전용 호스트 만들기

이제 전용 호스트를 호스트 그룹에 만듭니다. 호스트 이름 외에 호스트의 SKU도 제공해야 합니다. 호스트 SKU는 지원되는 VM 시리즈뿐만 아니라 전용 호스트의 하드웨어 생성도 캡처합니다.

호스트 SKU 및 가격 책정에 대한 자세한 내용은 [Azure Dedicated Host 가격 책정](https://aka.ms/ADHPricing)을 참조하세요.

호스트 그룹에 대 한 장애 도메인 수를 설정 하는 경우 호스트에 대 한 장애 도메인을 지정 해야 합니다.

### <a name="portal"></a>[포털](#tab/portal)

1. 왼쪽 위 모서리에서 **리소스 만들기** 를 선택합니다.
1. **전용 호스트** 를 검색한 다음, 결과에서 **전용 호스트** 를 선택합니다.
1. **전용 호스트** 페이지에서 **만들기** 를 선택합니다.
1. 사용하려는 구독을 선택합니다.
1. **리소스 그룹** 으로 *myDedicatedHostsRG* 를 선택합니다.
1. **인스턴스 세부 정보** 에서 **이름** 에 대해 *myHost* 를 입력하고, 위치에 대해 *미국 동부* 를 선택합니다.
1. **하드웨어 프로필** 에서 **크기 제품군** 에 대해 *표준 Es3 제품군 - 유형 1* 을 선택하고, **호스트 그룹** 에 대해 *myHostGroup* 을 선택한 다음, **장애 도메인** 에 대해 *1* 을 선택합니다. 나머지 필드는 기본값을 그대로 둡니다.
1. 완료되면 **검토 + 만들기** 를 선택하고, 유효성 검사를 기다립니다.
1. **유효성 검사 통과** 메시지가 표시되면 **만들기** 를 선택하여 호스트를 만듭니다.

### <a name="cli"></a>[CLI](#tab/cli)

[az vm host create](/cli/azure/vm/host#az_vm_host_create)를 사용하여 호스트를 만듭니다. 호스트 그룹의 장애 도메인 수를 설정하면 호스트의 장애 도메인을 지정하라는 메시지가 표시됩니다.

```azurecli-interactive
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

이 예제에서는 [AzHost](/powershell/module/az.compute/new-azhost) 를 사용 하 여 호스트를 만들고 장애 도메인을 1로 설정 합니다.


```azurepowershell-interactive
$dHost = New-AzHost `
   -HostGroupName $hostGroup.Name `
   -Location $location -Name myHost `
   -ResourceGroupName $rgName `
   -Sku DSv3-Type1 `
   -AutoReplaceOnFailure 1 `
   -PlatformFaultDomain 1
```

---

## <a name="create-a-vm"></a>VM 만들기

이제 호스트에 VM을 만듭니다.

### <a name="portal"></a>[포털](#tab/portal)

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기** 를 선택합니다.
1. Azure Marketplace 리소스 목록 위의 검색 상자에서 사용할 이미지를 검색하고 선택한 다음, **만들기** 를 선택합니다.
1. **기본** 탭의 **프로젝트 세부 정보** 아래에서 올바른 구독이 선택되었는지 확인한 다음, **리소스 그룹** 으로 *myDedicatedHostsRG* 를 선택합니다.
1. **인스턴스 세부 정보** 아래에서 **가상 머신 이름** 에 *myVM* 을 입력하고 **위치** 에 *미국 동부* 를 선택합니다.
1. **가용성 옵션** 에서 **가용성 영역** 을 선택하고, 드롭다운에서 *1* 을 선택합니다.
1. 크기에 대해 **크기 변경** 을 선택합니다. 사용 가능한 크기 목록에서 **표준 E2s v3** 과 같은 Esv3 시리즈에서 하나를 선택합니다. 사용 가능한 크기를 모두 보려면 필터를 지워야 할 수도 있습니다.
1. 필요에 따라 **기본** 탭의 나머지 필드를 완성합니다.
1. VM에 사용할 호스트를 지정하려면 페이지 위쪽에서 **고급** 탭을 선택하고, **호스트** 섹션에서 **호스트 그룹** 에 대해 *myHostGroup* 을 선택하고, **호스트** 에 대해 *myHost* 를 선택합니다. 그렇지 않으면 VM이 용량이 있는 호스트에 자동으로 배치됩니다. 호스트 그룹 및 호스트를 선택합니다.
    ![호스트 그룹 및 호스트 선택](./media/dedicated-hosts-portal/advanced.png)
1. 나머지 기본값을 그대로 둔 다음, 페이지의 아래에서 **검토 + 만들기** 단추를 선택합니다.
1. 유효성 검사가 통과되었다는 메시지가 표시되면 **만들기** 를 선택합니다.

VM 배포에는 몇 분 정도 걸립니다.


### <a name="cli"></a>[CLI](#tab/cli)

[az vm create](/cli/azure/vm#az_vm_create)를 사용하여 전용 호스트 내에 가상 머신을 만듭니다. 호스트 그룹을 만들 때 가용성 영역을 지정한 경우 가상 머신을 만들 때 동일한 영역을 사용해야 합니다. 이미지 및 호스트 이름과 같은 값을 사용자 고유의 값으로 바꿉니다. Windows VM을 만드는 경우에는를 제거 `--generate-ssh-keys` 하 여 암호를 입력 하 라는 메시지가 표시 됩니다.

```azurecli-interactive
az vm create \
   -n myVM \
   --image myImage \
   --host-group myHostGroup \
   --admin-username azureuser \
   --generate-ssh-keys \
   --size Standard_D4s_v3 \
   -g myDHResourceGroup \
   --zone 1
```

특정 호스트에 VM을 배치하려면 `--host-group`으로 대상 호스트 그룹을 지정하는 대신 `--host`를 사용합니다.

> [!WARNING]
> 리소스가 부족한 호스트에 가상 머신을 만드는 경우 가상 머신이 FAILED 상태로 생성됩니다.


### <a name="powershell"></a>[PowerShell](#tab/powershell)

[New-azvm](/powershell/module/az.compute/new-azvm) 를 사용 하 여 호스트에 새 VM을 만듭니다 .이 예제에서는 호스트 그룹이 영역 1에 있기 때문에 영역 1에서 vm을 만들어야 합니다.


```azurepowershell-interactive
New-AzVM `
   -Credential $cred `
   -ResourceGroupName $rgName `
   -Location $location `
   -Name myVM `
   -HostId $dhost.Id `
   -Image myImage `
   -Zone 1 `
   -Size Standard_D4s_v3
```

> [!WARNING]
> 리소스가 부족한 호스트에 가상 머신을 만드는 경우 가상 머신이 FAILED 상태로 생성됩니다.

---

## <a name="create-a-scale-set"></a>확장 집합 만들기

호스트에 확장 집합을 만들 수도 있습니다.

### <a name="portal"></a>[포털](#tab/portal)

확장 집합을 배포하는 경우 호스트 그룹을 지정합니다.

1. *확장 집합* 을 검색하고 목록에서 **가상 머신 확장 집합** 을 선택합니다.
1. **+ 추가** 를 선택하여 새 확장 집합을 만듭니다.
1. **기본** 탭의 필드를 평소와 같이 완성하지만, 전용 호스트에 대해 선택한 시리즈의 VM 크기(예: **표준 E2s v3**)를 선택해야 합니다.
1. **고급** 탭에서 **분산 알고리즘** 에 대해 **최대 분산** 을 선택합니다.
1. **호스트 그룹** 의 드롭다운에서 호스트 그룹을 선택합니다. 최근에 그룹을 만든 경우 목록에 추가되는 데 1분 정도 걸릴 수 있습니다.


### <a name="cli"></a>[CLI](#tab/cli)

[Az vmss create](/cli/azure/vmss#az_vmss_create)를 사용 하 여 확장 집합을 배포 하는 경우를 사용 하 여 호스트 그룹을 지정 `--host-group` 합니다. 이 예제에서는 최신 Ubuntu LTS 이미지를 배포 합니다. Windows 이미지를 배포 하려면의 값 `--image` 과 제거 `--generate-ssh-keys` 를 대체 하 여 암호를 입력 하 라는 메시지를 표시 합니다.

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --host-group myHostGroup \
  --generate-ssh-keys \
  --size Standard_D4s_v3 \
  -g myDHResourceGroup \
  --zone 1
```

확장 집합을 배포할 호스트를 수동으로 선택하려면 `--host` 및 해당 호스트 이름을 추가합니다.


### <a name="powershell"></a>[PowerShell](#tab/powershell)

[AzVMSS](/powershell/module/az.compute/new-azvmss)를 사용 하 여 호스트에 확장 집합을 배포 합니다. 확장 집합을 배포하는 경우, 호스트 그룹을 지정합니다.

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myDHScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"`
  -HostGroupId $hostGroup.Id
```

확장 집합을 배포할 호스트를 수동으로 선택하려면 `--host` 및 해당 호스트 이름을 추가합니다.

---

## <a name="add-an-existing-vm"></a>기존 VM 추가

전용 호스트에 기존 VM을 추가할 수 있지만 VM이 먼저 Stop\Deallocated에 있어야 합니다. VM을 전용 호스트로 이동하기 전에 VM 구성이 지원되는지 확인합니다.

- VM 크기는 전용 호스트와 동일한 크기의 제품군에 있어야 합니다. 예를 들어 전용 호스트가 DSv3인 경우 VM 크기는 Standard_D4s_v3일 수 있지만 Standard_A4_v2일 수는 없습니다.
- VM은 전용 호스트와 동일한 지역에 있어야 합니다.
- VM은 근접 배치 그룹에 있을 수 없습니다. 전용 호스트로 이동하기 전에 근접 배치 그룹에서 VM을 제거합니다. 자세한 내용은 [근접 배치 그룹에서 VM 이동](./windows/proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group)을 참조하세요.
- VM은 가용성 집합에 있을 수 없습니다.
- VM이 가용성 영역에 있는 경우 호스트 그룹과 동일한 가용성 영역이어야 합니다. VM 및 호스트 그룹에 대한 가용성 영역 설정이 일치해야 합니다.

### <a name="portal"></a>[포털](#tab/portal2)

[포털](https://portal.azure.com)을 사용하여 VM을 전용 호스트로 이동합니다.

1. VM에 대한 페이지를 엽니다.
1. **중지** 를 선택하여 VM을 중지하거나 할당 취소합니다.
1. 왼쪽 메뉴에서 **구성** 을 선택합니다.
1. 드롭다운 메뉴에서 호스트 그룹 및 호스트를 선택합니다.
1. 완료되면 페이지 위쪽에서 **저장** 을 선택합니다.
1. VM이 호스트에 추가되면 왼쪽 메뉴에서 **개요** 를 선택합니다.
1. 페이지 위쪽에서 **시작** 을 선택하여 VM을 다시 시작합니다.


### <a name="powershell"></a>[PowerShell](#tab/powershell2)

변수 값을 사용자의 정보로 바꿉니다.

```azurepowershell-interactive
$vmRGName = "movetohost"
$vmName = "myVMtoHost"
$dhRGName = "myDHResourceGroup"
$dhGroupName = "myHostGroup"
$dhName = "myHost"

$myDH = Get-AzHost `
   -HostGroupName $dhGroupName `
   -ResourceGroupName $dhRGName `
   -Name $dhName

$myVM = Get-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName

$myVM.Host = New-Object Microsoft.Azure.Management.Compute.Models.SubResource

$myVM.Host.Id = "$myDH.Id"

Stop-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName -Force

Update-AzVM `
   -ResourceGroupName $vmRGName `
   -VM $myVM -Debug

Start-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
```

---


## <a name="check-the-status-of-the-host"></a>호스트의 상태 확인

방법에서 아직 사용할 수 있는 용량을 확인 해야 하는 경우 상태를 확인할 수 있습니다.

### <a name="portal"></a>[포털](#tab/portal)

1. 호스트를 검색 하 고 선택 합니다.
1. 호스트의 **개요** 페이지에서 아래로 스크롤하여 호스트에 대해 사용할 수 있는 크기 목록을 확인 합니다. 다음과 유사 하 게 표시 됩니다.

:::image type="content" source="media/dedicated-hosts-portal/host-status.png" alt-text="호스트의 개요 페이지에서 호스트의 사용 가능한 용량을 확인 합니다.":::

### <a name="cli"></a>[CLI](#tab/cli)

[az vm host get-instance-view](/cli/azure/vm/host#az_vm_host_get_instance_view)를 사용하여 호스트 상태와 호스트에 배포할 수 있는 가상 머신 수를 확인할 수 있습니다.

```azurecli-interactive
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```

출력은 다음과 유사합니다.

```json
{
  "autoReplaceOnFailure": true,
  "hostId": "6de80643-0f45-4e94-9a4c-c49d5c777b62",
  "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts/myHost",
  "instanceView": {
    "assetId": "12345678-1234-1234-abcd-abc123456789",
    "availableCapacity": {
      "allocatableVms": [
        {
          "count": 31.0,
          "vmSize": "Standard_D2s_v3"
        },
        {
          "count": 15.0,
          "vmSize": "Standard_D4s_v3"
        },
        {
          "count": 7.0,
          "vmSize": "Standard_D8s_v3"
        },
        {
          "count": 3.0,
          "vmSize": "Standard_D16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-8s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D48s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-16s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-32s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64s_v3"
        }
      ]
    },
    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Provisioning succeeded",
        "level": "Info",
        "message": null,
        "time": "2019-07-24T21:22:40.604754+00:00"
      },
      {
        "code": "HealthState/available",
        "displayStatus": "Host available",
        "level": "Info",
        "message": null,
        "time": null
      }
    ]
  },
  "licenseType": null,
  "location": "eastus2",
  "name": "myHost",
  "platformFaultDomain": 1,
  "provisioningState": "Succeeded",
  "provisioningTime": "2019-07-24T21:22:40.604754+00:00",
  "resourceGroup": "myDHResourceGroup",
  "sku": {
    "capacity": null,
    "name": "DSv3-Type1",
    "tier": null
  },
  "tags": null,
  "type": null,
  "virtualMachines": [
    {
      "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/MYDHRESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/MYVM",
      "resourceGroup": "MYDHRESOURCEGROUP"
    }
  ]
}

```

### <a name="powershell"></a>[PowerShell](#tab/powershell)


매개 변수와 함께 [AzHost](/powershell/module/az.compute/get-azhost) 를 사용 하 여 호스트에 배포할 수 있는 가상 컴퓨터 수와 호스트 상태를 확인할 수 있습니다 `-InstanceView` .

```azurepowershell-interactive
Get-AzHost `
   -ResourceGroupName $rgName `
   -Name myHost `
   -HostGroupName $hostGroup.Name `
   -InstanceView
```

출력은 다음과 유사합니다.

```
ResourceGroupName      : myDHResourceGroup
PlatformFaultDomain    : 1
AutoReplaceOnFailure   : True
HostId                 : 12345678-1234-1234-abcd-abc123456789
ProvisioningTime       : 7/28/2019 5:31:01 PM
ProvisioningState      : Succeeded
InstanceView           :
  AssetId              : abc45678-abcd-1234-abcd-123456789abc
  AvailableCapacity    :
    AllocatableVMs[0]  :
      VmSize           : Standard_D2s_v3
      Count            : 32
    AllocatableVMs[1]  :
      VmSize           : Standard_D4s_v3
      Count            : 16
    AllocatableVMs[2]  :
      VmSize           : Standard_D8s_v3
      Count            : 8
    AllocatableVMs[3]  :
      VmSize           : Standard_D16s_v3
      Count            : 4
    AllocatableVMs[4]  :
      VmSize           : Standard_D32-8s_v3
      Count            : 2
    AllocatableVMs[5]  :
      VmSize           : Standard_D32-16s_v3
      Count            : 2
    AllocatableVMs[6]  :
      VmSize           : Standard_D32s_v3
      Count            : 2
    AllocatableVMs[7]  :
      VmSize           : Standard_D64-16s_v3
      Count            : 1
    AllocatableVMs[8]  :
      VmSize           : Standard_D64-32s_v3
      Count            : 1
    AllocatableVMs[9]  :
      VmSize           : Standard_D64s_v3
      Count            : 1
  Statuses[0]          :
    Code               : ProvisioningState/succeeded
    Level              : Info
    DisplayStatus      : Provisioning succeeded
    Time               : 7/28/2019 5:31:01 PM
  Statuses[1]          :
    Code               : HealthState/available
    Level              : Info
    DisplayStatus      : Host available
Sku                    :
  Name                 : DSv3-Type1
Id                     : /subscriptions/10101010-1010-1010-1010-101010101010/re
sourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts
/myHost
Name                   : myHost
Location               : eastus
Tags                   : {}
```

---

## <a name="deleting-hosts"></a>호스트 삭제 
가상 머신이 배포되지 않은 경우에도 전용 호스트에 대한 요금이 청구됩니다. 비용 절약을 위해 현재 사용하지 않는 호스트를 모두 삭제해야 합니다.

호스트를 사용하는 가상 머신이 더 이상 없는 경우에만 호스트를 삭제할 수 있습니다.

### <a name="portal"></a>[포털](#tab/portal)

1. 호스트를 검색 하 고 선택 합니다.
1. 왼쪽 메뉴에서 **인스턴스** 를 선택 합니다.
1. 각 가상 컴퓨터를 선택 하 고 삭제 합니다.
1. 모든 Vm이 삭제 되 면 호스트에 대 한 **개요** 페이지로 돌아가서 상단 메뉴에서 **삭제** 를 선택 합니다.
1. 호스트를 삭제 한 후 호스트 그룹에 대 한 페이지를 열고 **호스트 그룹 삭제** 를 선택 합니다.

### <a name="cli"></a>[CLI](#tab/cli)

 [az vm delete](/cli/azure/vm#az_vm_delete)를 사용하여 VM을 삭제합니다.

```azurecli-interactive
az vm delete -n myVM -g myDHResourceGroup
```

VM을 삭제한 후 [az vm host delete](/cli/azure/vm/host#az_vm_host_delete)를 사용하여 호스트를 삭제할 수 있습니다.

```azurecli-interactive
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost
```

모든 호스트를 삭제한 후 [az vm host group delete](/cli/azure/vm/host/group#az_vm_host_group_delete)를 사용하여 호스트 그룹을 삭제할 수 있습니다.

```azurecli-interactive
az vm host group delete -g myDHResourceGroup --host-group myHostGroup
```

단일 명령에서 전체 리소스 그룹을 삭제할 수도 있습니다. 그러면 모든 VM, 호스트 및 호스트 그룹을 포함하여 그룹에 생성된 모든 리소스가 삭제됩니다.

```azurecli-interactive
az group delete -n myDHResourceGroup
```


### <a name="powershell"></a>[PowerShell](#tab/powershell)

[Remove-AzVM](/powershell/module/az.compute/remove-azvm)을 사용하여 VM을 삭제합니다.

```azurepowershell-interactive
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

VM을 삭제한 후 [Remove-AzHost](/powershell/module/az.compute/remove-azhost)를 사용하여 호스트를 삭제할 수 있습니다.

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

모든 호스트를 삭제한 후에는 [Remove-AzHostGroup](/powershell/module/az.compute/remove-azhostgroup)을 사용하여 호스트 그룹을 삭제할 수 있습니다.

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)을 사용하여 단일 명령에서 전체 리소스 그룹을 삭제할 수도 있습니다. 그러면 모든 VM, 호스트 및 호스트 그룹을 포함하여 그룹에 생성된 모든 리소스가 삭제됩니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```

---

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [전용 호스트](dedicated-hosts.md) 개요를 참조하세요.

- [Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.compute/vm-dedicated-hosts/README.md)에서 사용할 수 있는 샘플 템플릿이 있습니다. 이 템플릿은 지역의 복원력을 극대화하기 위해 영역과 오류 도메인을 모두 사용합니다.

