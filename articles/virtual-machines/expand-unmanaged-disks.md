---
title: Azure에서 관리 되지 않는 디스크 확장
description: 리소스 관리자 배포 모델의 Azure PowerShell를 사용 하 여 가상 컴퓨터에 연결 된 관리 되지 않는 가상 하드 디스크의 크기를 확장 합니다.
author: kirpasingh
manager: roshar
ms.service: virtual-machines
ms.collection: windows
ms.topic: article
ms.date: 11/17/2021
ms.author: kirpas
ms.subservice: disks
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0dbdb19ae72fc3415e31d97ebb417fb2825951b6
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132817888"
---
# <a name="expand-unmanaged-virtual-hard-disks-attached-to-a-virtual-machine"></a>가상 컴퓨터에 연결 된 관리 되지 않는 가상 하드 디스크 확장

이 문서에서는 관리 되지 않는 디스크를 확장 하는 방법을 설명 합니다. 관리 디스크를 확장 하는 방법에 대 한 자세한 내용은 [Windows](windows/expand-os-disk.md) 또는 [Linux](linux/expand-disks.md) 문서를 사용 하세요.

**적용 대상:** : heavy_check_mark: Windows vm: heavy_check_mark: Linux vm: heavy_check_mark: 유연한 확장 집합 

[Azure Marketplace](https://azure.microsoft.com/marketplace/)에서 이미지를 배포 하 여 리소스 그룹에 새 VM (가상 컴퓨터)을 만드는 경우 기본 os (운영 체제) 드라이브는 127 GB입니다 (일부 이미지는 기본적으로 os 디스크 크기가 작음). VM에 데이터 디스크를 추가할 수 있지만(숫자가 선택한 SKU에 따라 다름), 이러한 추록 디스크에는 애플리케이션 및 CPU 집약적 워크로드를 설치하는 것이 좋습니다. 고객은 다음과 같은 특정 시나리오를 특정 지원하기 위해 OS 드라이브를 확장해야 하는 경우가 많습니다.

- OS 드라이브에 구성 요소를 설치하는 레거시 애플리케이션을 지원하기 위해
- OS 드라이브가 대형 온-프레미스로 실제 PC 또는 VM을 마이그레이션하기 위해

> [!IMPORTANT]
> Azure VM의 OS 또는 데이터 디스크 크기를 조정 하려면 VM의 할당을 취소 해야 합니다.
>
> 기존 디스크를 축소하는 것은 지원되지 않으며 이는 잠재적으로 데이터가 손실될 수 있습니다.
> 
> 디스크를 확장 한 후에는 더 큰 디스크를 활용 하기 위해 [Windows](windows/expand-os-disk.md#expand-the-volume-in-the-operating-system) 또는 [Linux](linux/expand-disks.md#expand-a-disk-partition-and-filesystem) 의 OS 내에서 볼륨을 확장 해야 합니다.

## <a name="resize-an-unmanaged-disk-by-using-powershell"></a>PowerShell을 사용하여 비관리 디스크 크기 조정

관리 모드에서 PowerShell ISE 또는 PowerShell 창을 열고 다음 단계를 수행합니다.

- 리소스 관리 모드에서 Microsoft Azure 계정에 로그인하고 구독을 선택합니다.
   
    ```powershell
    Connect-AzAccount
    Select-AzSubscription –SubscriptionName 'my-subscription-name'
    ```

- 리소스 그룹 이름 및 VM 이름을 설정합니다.
   
    ```powershell
    $rgName = 'my-resource-group-name'
    $vmName = 'my-vm-name'
    ```

- VM에 대한 참조를 얻습니다.
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

- VM을 중지한 후 디스크의 크기를 조정합니다.
   
    ```powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

- 관리되지 않는 OS 디스크의 크기를 원하는 값으로 설정하고 VM을 업데이트합니다.
   
    ```powershell
    $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
    Update-AzVM -ResourceGroupName $rgName -VM $vm
    ```
   
    > [!WARNING]
    > 새 크기가 기존 디스크 크기보다 커야 합니다. OS 디스크에 허용되는 최대 크기는 2,048GB입니다. (VHD Blob은 이 크기 이상으로 확장할 수 있지만, OS가 처음 2,048GB의 공간에서만 작동할 수 있습니다.)
    > 
    > 

- 크기를 조정 하려는 데이터 디스크의 크기를 업데이트 합니다. VM에 연결 된 첫 번째 데이터 디스크를 확장 하려면 숫자 인덱스를 사용 하 여 첫 번째 연결 된 데이터 디스크에 대 한 참조를 가져옵니다.

    ```powershell
    $vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
    ```
    
    마찬가지로 디스크의 인덱스 또는 **이름** 속성을 사용 하 여 VM에 연결 된 다른 데이터 디스크를 참조할 수 있습니다.
    
    ```powershell
    ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).DiskSizeGB = 1023
    ```
    

- VM이 업데이트될 때까지 몇 초 정도 걸릴 수 있습니다. 명령 실행이 완료되면 VM을 다시 시작합니다.
   
    ```powershell
    Start-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

## <a name="next-steps"></a>다음 단계

[Azure Portal](windows\attach-managed-disk-portal.md)을 사용하여 디스크를 연결할 수도 있습니다.