---
title: Av1 시리즈 사용 중지
description: Av1 시리즈 VM 크기에 대한 사용 중지 정보입니다.
author: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 07/26/2021
ms.author: mimckitt
ms.openlocfilehash: cfc30046d33ad9e6f91b866dc5653fbbcbdbd995
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128577735"
---
# <a name="av1-series-retirement"></a>Av1 시리즈 사용 중지

2024년 8월 31일에 기본/표준 A 시리즈 VM을 사용 중지합니다. 이 날짜가 되기 전에 vCPU별로 더 많은 메모리를 제공하고 SSD(반도체 드라이브)에서 더 빠른 스토리지를 제공하는 Av2 시리즈 VM으로 워크로드를 마이그레이션해야 합니다.

> [!NOTE]
> 경우에 따라 크기를 조정하기 전에 사용자가 VM의 할당을 취소해야 합니다. 이는 현재 VM을 호스트하는 하드웨어 클러스터에서 새 크기를 사용할 수 없는 경우에 발생할 수 있습니다.


## <a name="migrate-workloads-from-basic-and-standard-a-series-vms-to-av2-series-vms"></a>기본/표준 A 시리즈 VM의 워크로드를 Av2 시리즈 VM으로 마이그레이션 

[Azure Portal, PowerShell 또는 CLI를](resize-vm.md)사용하여 가상 머신의 크기를 Av2 시리즈로 조정합니다. 다음은 Azure Portal과 PowerShell을 사용하여 VM의 크기를 조정하는 방법에 대한 예입니다. 

> [!IMPORTANT]
> 가상 머신의 크기를 조정하면 다시 시작됩니다. 사용량이 적은 업무 시간에 다시 시작하는 작업을 수행하는 것이 좋습니다. 

### <a name="azure-portal"></a>Azure portal 
1. [Azure Portal](https://portal.azure.com)을 엽니다.
1. 검색에서 **가상 머신** 을 입력합니다.
1. **서비스** 에서 **가상 머신** 을 선택합니다.
1. **가상 머신** 페이지에서 크기를 조정하려는 가상 머신을 선택합니다.
1. 왼쪽 메뉴에서 **크기** 를 선택합니다.
1. 사용 가능한 크기 목록에서 새 Av2 크기를 선택하고 **크기 조정** 을 선택합니다.

### <a name="azure-powershell"></a>Azure PowerShell
1. 리소스 그룹 이름 및 VM 이름 변수를 설정합니다. 크기를 조정하려는 VM의 정보로 값을 바꿉니다. 

    ```powershell
    $resourceGroup = "myResourceGroup"
    $vmName = "myVM"
    ```
2. VM이 호스트되는 하드웨어 클러스터에서 사용할 수 있는 VM 크기를 나열합니다.

    ```powershell
    Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName
    ```

3. 새 크기로 VM의 크기를 조정합니다.

    ```powershell
    $vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
    $vm.HardwareProfile.VmSize = "<newAv2VMsize>"
    Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
    ```

## <a name="help-and-support"></a>도움말 및 지원

질문이 있는 경우 [Microsoft Q&A](/answers/topics/azure-virtual-machines.html)에서 커뮤니티 전문가의 답변을 받으세요. 지원 계획이 있고 기술 지원이 필요한 경우 [지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 만드세요.

1. 문제 유형에서 기술을 선택합니다.
1. 구독의 경우 사용자의 구독을 선택합니다.
1. 서비스에서 내 서비스를 클릭합니다.
1. 서비스 유형에서 Windows/Linux를 실행하는 가상 머신을 선택합니다.
1. 요약에서 요청에 대한 요약 사항을 입력합니다.
1. 문제 유형에서 내 VM 크기 조정 지원을 선택합니다.
1. 문제 하위 유형에서 적용되는 옵션을 선택합니다.

## <a name="next-steps"></a>다음 단계
[Av2 시리즈 VM](av2-series.md)에 대해 알아보기
