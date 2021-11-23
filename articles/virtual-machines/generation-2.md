---
title: 2세대 VM에 대한 Azure 지원
description: 2세대 VM에 대한 Azure 지원 개요
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/26/2021
ms.author: jushiman
ms.openlocfilehash: 8f216abdd6a171e0640752f32740df0ed31a4be7
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132936266"
---
# <a name="support-for-generation-2-vms-on-azure"></a>Azure의 2세대 VM 지원

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일 확장 집합

이제 Azure에서 2세대용 가상 머신(VM)에 대한 지원이 제공됩니다. 가상 머신을 만든 후에는 가상 머신의 세대를 변경할 수 없으므로 세대를 선택하기 전에 이 페이지의 고려 사항을 검토하세요.

2세대 VM은 1세대 VM에서 지원되지 않는 주요 기능들을 지원합니다. 이러한 기능에는 메모리 증가, Intel SGX(Software Guard Extensions) 및 vPMEM(가상화된 영구 메모리)이 포함됩니다. 온-프레미스에서 실행되는 2세대 VM에는 아직 Azure에서 지원되지 않는 몇 가지 기능이 있습니다. 자세한 내용은 [특징과 기능](#features-and-capabilities) 섹션을 참조하세요.

2세대 VM은 1세대 VM에서 사용되는 BIOS 기반 아키텍처 대신 새 UEFI 기반 부팅 아키텍처를 사용합니다. 1세대 VM과 비교할 때 2세대 VM은 부팅 및 설치 시간을 향상시킬 수 있습니다. 2세대 VM에 대한 개요와 1세대와 2세대 간의 일부 차이점에 대한 설명은 [Hyper-V에 1세대 또는 2세대 가상 머신을 만들어야 하나요?](/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)를 참조하세요.

## <a name="generation-2-vm-sizes"></a>2세대 VM 크기

이제 Azure는 다음과 같이 선택된 VM 시리즈에 대한 2세대 지원을 제공합니다.


* [B 시리즈](sizes-b-series-burstable.md)
* [DCsv2 시리즈](dcv2-series.md)
* [DSv2 시리즈](dv2-dsv2-series.md)
* [Dsv3 시리즈](dv3-dsv3-series.md)
* [Dsv4 시리즈](dv4-dsv4-series.md)
* [Dasv4 시리즈](dav4-dasv4-series.md)
* [Ddsv4 시리즈](ddv4-ddsv4-series.md)
* [Dasv5 시리즈](dasv5-dadsv5-series.md)
* [Dadsv5 시리즈](dasv5-dadsv5-series.md)
* [DCasv5 시리즈](dcasv5-dcadsv5-series.md)
* [DCadsv5 시리즈](dcasv5-dcadsv5-series.md)
* [Dv5 시리즈](dv5-dsv5-series.md)
* [Dsv5 시리즈](dv5-dsv5-series.md)
* [Esv3 시리즈](ev3-esv3-series.md)
* [Esv4 시리즈](ev4-esv4-series.md)
* [Easv4 시리즈](eav4-easv4-series.md)
* [Edsv4 시리즈](edv4-edsv4-series.md)
* [Easv5 시리즈](easv5-eadsv5-series.md)
* [Eadsv5 시리즈](easv5-eadsv5-series.md)
* [ECasv5 시리즈](ecasv5-ecadsv5-series.md)
* [ECadsv5 시리즈](ecasv5-ecadsv5-series.md)
* [Edv5 시리즈](edv5-edsv5-series.md)
* [Edsv5 시리즈](edv5-edsv5-series.md)
* [Ev5 시리즈](ev5-esv5-series.md)
* [Esv5 시리즈](ev5-esv5-series.md)
* [Fsv2 시리즈](fsv2-series.md)
* [GS 시리즈](sizes-previous-gen.md#gs-series)
* [HB 시리즈](hb-series.md)
* [HC 시리즈](hc-series.md)
* [Ls 시리즈](sizes-previous-gen.md#ls-series) 
* [Lsv2 시리즈](lsv2-series.md)
* [M 시리즈](m-series.md)
* [Mv2 시리즈](mv2-series.md)<sup>1</sup>
* [Msv2 및 Mdsv2 Medium Memory 시리즈](msv2-mdsv2-series.md)<sup>1</sup>
* [NCv2 시리즈](ncv2-series.md) 
* [NCv3 시리즈](ncv3-series.md)
* [ND 시리즈](nd-series.md)
* [ND A100 v4 시리즈](nda100-v4-series.md)
* [NDv2 시리즈](ndv2-series.md)
* [NVv3 시리즈](nvv3-series.md)
* [NVv4 시리즈](nvv4-series.md)
* [NCasT4_v3 시리즈](nct4-v3-series.md)
* [NDm A100 v4 시리즈](ndm-a100-v4-series.md)

<sup>1</sup> Mv2 시리즈, DC 시리즈, NDv2 시리즈, Msv2 및 Mdsv2 시리즈 Medium Memory는 1 세대 VM 이미지를 지원 하지 않으며 2 세대 이미지의 하위 집합만 지원 합니다. 자세한 내용은 [Mv2 시리즈 설명서](mv2-series.md), [DSv2 시리즈](dv2-dsv2-series.md), [ND A100 v4 시리즈](nda100-v4-series.md), [NDv2 시리즈](ndv2-series.md)및 [Msv2 및 Mdsv2 Medium Memory 시리즈](msv2-mdsv2-series.md) 를 참조 하세요.


## <a name="generation-2-vm-images-in-azure-marketplace"></a>Azure Marketplace의 2세대 VM 이미지

2세대 VM은 다음 Marketplace 이미지를 지원합니다.

* Windows Server 2019, 2016, 2012 R2, 2012
* Windows 10 Pro, Windows 10 Enterprise
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4
* Ubuntu Server 16.04, 18.04, 19.04, 19.10, 20.04 
* RHEL 8.2, 8.1, 8.0, 7.9, 7.7, 7.6, 7.5, 7.4, 7.0, 8.3
* Cent OS 8.1, 8.0, 7.7, 7.6, 7.5, 7.4, 8.2, 8.3
* Oracle Linux 7.7, 7.7-CI, 7.8

> [!NOTE]
> Mv2 시리즈, DC 시리즈, ND A100 v4 시리즈, NDv2 시리즈, Msv2 및 Mdsv2 시리즈와 같은 특정 가상 머신 크기는 이러한 이미지의 하위 집합만 지원할 수 있습니다. 자세한 내용은 관련 가상 머신 크기 설명서를 참조 하세요.

## <a name="on-premises-vs-azure-generation-2-vms"></a>온-프레미스 대비 Azure 2세대 VM

Azure는 현재 온-프레미스 Hyper-V가 2세대 VM에 대해 지원하는 기능 중 일부를 지원하지 않습니다.

| 2세대 기능                | 온-프레미스 Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| 보안 부팅                         | :heavy_check_mark:  | 신뢰할 수 있는 시작(미리 보기)   |
| 보호된 VM                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | 신뢰할 수 있는 시작(미리 보기)  |
| VBS(가상화 기반 보안) | :heavy_check_mark:  | :heavy_check_mark:   |
| VHDX 형식                         | :heavy_check_mark:  | :x:   |

자세한 내용은 [신뢰할 수 있는 시작(미리 보기)](trusted-launch.md)을 참조하세요.

## <a name="features-and-capabilities"></a>특징과 기능

### <a name="generation-1-vs-generation-2-features"></a>1세대와 2세대 간 특징 비교

| 기능 | 1세대 | 2세대 |
|---------|--------------|--------------|
| 부팅             | PCAT                      | UEFI                               |
| 디스크 컨트롤러 | IDE                       | SCSI                               |
| VM 크기         | 모든 VM 크기 | [사용 가능한 크기 보기](#generation-2-vm-sizes) |

### <a name="generation-1-vs-generation-2-capabilities"></a>1세대와 2세대 간 기능 비교

| 기능 | 1세대 | 2세대 |
|------------|--------------|--------------|
| OS 디스크 > 2TB                    | :x:                | :heavy_check_mark: |
| 사용자 지정 디스크/이미지/OS 교체         | :heavy_check_mark: | :heavy_check_mark: |
| 가상 머신 확장 집합 지원 | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :heavy_check_mark: |
| 백업/복원                    | :heavy_check_mark: | :heavy_check_mark: |
| Azure Compute Gallery             | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md)             | :heavy_check_mark: | :heavy_check_mark:                |
| [서버 쪽 암호화](disk-encryption.md)            | :heavy_check_mark: | :heavy_check_mark: |


## <a name="creating-a-generation-2-vm"></a>2세대 VM 만들기

### <a name="marketplace-image"></a>Marketplace 이미지

Azure Portal 또는 Azure CLI에서는 UEFI 부팅을 지원하는 Marketplace 이미지에서 2세대 VM을 만들 수 있습니다.

#### <a name="azure-portal"></a>Azure portal

Azure Portal에서 2세대(Gen2) VM을 만드는 단계는 아래와 같습니다.

1. [https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.
1. **리소스 만들기** 를 선택합니다.
1. 왼쪽의 Azure Marketplace에서 **모두 보기** 를 클릭합니다.
1. Gen2를 지원하는 이미지를 선택합니다.
1. **만들기** 를 클릭합니다.
1. **고급** 탭의 **VM 생성** 섹션에서 **Gen 2** 옵션을 선택합니다.
1. **기본 사항** 탭의 **인스턴스 세부 정보** 에서 **크기** 로 이동하여 **VM 크기 선택** 블레이드를 엽니다.
1. [지원되는 2세대 VM](#generation-2-vm-sizes)을 선택합니다.
1. 페이지의 나머지 부분을 진행하여 VM 만들기를 완료합니다.

![1세대 또는 2세대 VM 선택](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

또한 PowerShell을 사용하면 1세대 또는 2세대 SKU를 직접 참조하여 VM을 만들 수도 있습니다.

예를 들어, 다음 PowerShell cmdlet을 사용하여 `WindowsServer` 제품의 SKU 목록을 가져올 수 있습니다.

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

Windows Server 2012를 OS로 사용하여 VM을 만드는 경우, 1세대(BIOS) 또는 2세대(UEFI) VM SKU 중 하나를 선택하면 다음과 같이 표시됩니다.

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

지원되는 Marketplace 이미지의 최신 목록은 [특징과 기능](#features-and-capabilities) 섹션을 참조하세요.

#### <a name="azure-cli"></a>Azure CLI

또는 Azure CLI를 사용하여 **게시자** 에 나열된 사용 가능한 2세대 이미지를 볼 수 있습니다.

```azurecli
az vm image list --publisher Canonical --sku gen2 --output table --all
```

### <a name="managed-image-or-managed-disk"></a>관리되는 이미지 또는 관리 디스크

관리되는 이미지 또는 관리 디스크에서 1세대 VM을 만드는 것과 같은 방법으로 2세대 VM을 만들 수 있습니다.

### <a name="virtual-machine-scale-sets"></a>가상 머신 크기 집합

또한 가상 머신 확장 집합을 사용하여 2세대 VM을 만들 수도 있습니다. Azure CLI에서 Azure 확장 집합을 사용하여 2세대 VM을 만듭니다.

## <a name="frequently-asked-questions"></a>질문과 대답

* **모든 Azure 지역에서 2세대 VM을 사용할 수 있나요?**  
    예. 다만 모든 지역에서 모든 [2세대 VM 크기](#generation-2-vm-sizes)를 사용할 수 있는 것은 아닙니다. 2세대 VM의 가용성은 VM 크기의 가용성에 따라 달라집니다.

* **1세대 VM과 2세대 VM 간에 가격 차이가 있나요?**  
   아니요.

* **온-프레미스 2세대 VM의 .vhd 파일이 있습니다. 이 .vhd 파일을 사용하여 Azure에서 2세대 VM을 만들 수 있나요?**
  예. 2세대 .vhd 파일을 Azure로 가져와서 이를 사용해 2세대 VM을 만들 수 있습니다. 이렇게 하려면 다음 절차를 따릅니다.
    1. VM을 만들려는 동일한 지역의 스토리지 계정에 .vhd를 업로드합니다.
    1. .vhd 파일에서 관리 디스크를 만듭니다. Hyper-V 생성 속성을 V2로 설정합니다. 다음 PowerShell 명령은 관리 디스크를 만들 때 Hyper-V 생성 속성을 설정합니다.

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. 디스크를 사용할 수 있게 되면 이 디스크를 연결하여 VM을 만듭니다. 만들어진 VM은 2세대 VM이 됩니다.
    2세대 VM이 만들어질 때 필요에 따라 이 VM의 이미지를 일반화할 수 있습니다. 이미지를 일반화하여 여러 VM을 만드는 데 사용할 수 있습니다.

* **OS 디스크 크기를 늘리는 방법은 무엇인가요?**  

  2TiB보다 큰 OS 디스크는 2세대 VM의 새로운 특징입니다. 기본적으로 OS 디스크의 크기는 2세대 VM에서 2TiB보다 작습니다. 디스크 크기는 최대 권장 용량인 4TiB까지 늘릴 수 있습니다. Azure CLI 또는 Azure Portal을 사용하여 OS 디스크 크기를 늘립니다. 프로그래밍 방식으로 디스크를 확장하는 방법에 대한 자세한 내용은 [Windows](./windows/expand-os-disk.md) 또는 [Linux](./linux/resize-os-disk-gpt-partition.md)용 **디스크 크기 조정** 을 참조하세요.

  Azure Portal에서 OS 디스크 크기를 늘리려면 다음을 수행하세요.

  1. Azure Portal에서 VM 속성 페이지로 이동합니다.
  1. VM을 종료하고 할당을 취소하려면 **중지** 단추를 선택합니다.
  1. **디스크** 섹션에서 크기를 늘릴 OS 디스크를 선택합니다.
  1. **디스크** 섹션에서 **구성** 을 선택하고 **크기** 를 원하는 값으로 업데이트합니다.
  1. VM 속성 페이지로 돌아가서 VM을 **시작** 합니다.
  
  2TiB보다 큰 OS 디스크에 대한 경고가 표시될 수 있습니다. 이 경고는 2세대 VM에는 적용되지 않습니다. 그러나 4TiB보다 큰 OS 디스크는 지원되지 않습니다.

* **2세대 VM은 가속화된 네트워킹을 지원하나요?**  
    예. 자세한 내용은 [가속화된 네트워킹을 사용하여 VM 만들기](../virtual-network/create-vm-accelerated-networking-cli.md)를 참조하세요.

* **2세대 VM은 Azure에서 보안 부팅 또는 vTPM을 지원하나요?**
    VTPM 및 보안 부팅은 모두 2세대 VM에 대한 신뢰할 수 있는 시작(미리 보기) 기능입니다. 자세한 내용은 [신뢰할 수 있는 시작](trusted-launch.md)을 참조하세요.
    
* **VHDX는 2세대에서 지원되나요?**  
    아니요. 2세대 VM은 VHD만 지원합니다.

* **2세대 VM은 Azure Ultra Disk Storage를 지원하나요?**  
    예.

* **1세대에서 2세대로 VM을 마이그레이션할 수 있나요?**  
    아니요. VM을 만든 후에는 VM의 세대를 변경할 수 없습니다. VM 세대를 전환해야 할 경우, 다른 세대의 새 VM을 만드세요.

* **Gen2 VM을 만들려고 할 때 크기 선택기에서 내 VM 크기를 사용할 수 없는 이유는 무엇인가요?**

    이 문제는 다음을 수행하여 해결할 수 있습니다.

    1. **VM 생성** 속성이 **고급** 탭의 **Gen 2** 로 설정되었는지 확인합니다.
    1. [Gen2 VM을 지원하는 VM 크기](#generation-2-vm-sizes)를 검색하고 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

2세대 VM을 사용한 [신뢰할 수 있는 시작(미리 보기)](trusted-launch-portal.md)에 대해 자세히 알아보세요.

[Hyper-V의 2세대 가상 머신](/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)에 대해 알아봅니다.
