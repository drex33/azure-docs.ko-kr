---
title: 유지 관리 구성과 연결된 리소스를 다른 지역으로 이동
description: VM 유지 관리 구성과 연결된 리소스를 다른 Azure 지역으로 이동하는 방법 알아보기
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 59707cfb6e54329017e36914bccf9b565ec9643c
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690255"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>유지 관리 제어 구성의 리소스를 다른 지역으로 이동

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일 확장 집합

이 문서에 따라 유지 관리 제어 구성과 연결된 리소스를 다른 Azure 지역으로 이동합니다. 여러 가지 이유로 구성을 이동하는 것이 좋습니다. 예를 들어 새 지역을 활용하거나, 특정 지역에서 사용할 수 있는 기능이나 서비스를 배포하거나, 내부 정책 및 거버넌스 요구 사항을 충족하거나, 용량 계획에 대응하기 위해 리소스를 이동할 수 있습니다.

사용자 지정된 유지 관리 구성을 가진 [유지 관리 제어](maintenance-control.md)를 사용하면 플랫폼 업데이트가 VM 및 Azure Dedicated Host에 적용되는 방법을 제어할 수 있습니다. 지역 간에 유지 관리 제어를 이동하는 두 가지 시나리오가 있습니다.

- 구성 자체가 아닌 유지 관리 구성과 연결된 리소스를 이동하려면 이 문서를 따르세요.
- 유지 관리 제어 구성을 이동하되 구성과 연결된 리소스는 이동하지 않으려면 [이 안내](move-region-maintenance-configuration.md)를 따르세요.
- 유지 관리 구성과 이와 연결된 리소스를 모두 이동하려면 먼저 [이 안내](move-region-maintenance-configuration.md)를 따르세요. 그런 다음이 문서의 지침에 따르세요.

## <a name="prerequisites"></a>필수 조건

유지 관리 제어 구성과 연결된 리소스를 이동하기 전에 다음을 수행합니다.

- 시작하기 전에 이동 중인 리소스가 새 지역에 있는지 확인합니다.
- 이동하려는 Azure VM 및 Azure Dedicated Host와 연결된 유지 관리 제어 구성을 확인합니다. 각 리소스를 개별적으로 확인합니다. 현재 여러 리소스에 대한 구성을 검색할 수 있는 방법은 없습니다.
- 리소스에 대한 구성을 검색하는 경우:
    - Azure Dedicated Host ID가 아닌 계정에 대한 구독 ID를 사용해야 합니다.
    - CLI: --output table 매개 변수는 가독성을 위해서만 사용되며 삭제하거나 변경할 수 있습니다.
    - PowerShell: Format-Table Name 매개 변수는 가독성을 위해서만 사용되며 삭제하거나 변경할 수 있습니다.
    - PowerShell을 사용하는 경우 연결된 구성이 없는 리소스에 대한 구성을 나열하려고 하면 오류가 발생합니다. 오류는 다음과 유사합니다. "작업 실패 상태: '찾을 수 없음'. 세부 정보: 404 클라이언트 오류: URL에 대해 찾을 수 없음".

    
## <a name="prepare-to-move"></a>이동 준비

1. 시작하기 전에 이러한 변수를 정의합니다. 각각에 대한 예를 제공했습니다.

    **변수** | **세부 정보** | **예제**
    --- | ---
    $subId | 유지 관리 구성을 포함하는 구독의 ID입니다. | "our-subscription-ID"
    $rsrcGroupName | 리소스 그룹 이름(Azure VM) | "VMResourceGroup"
    $vmName | VM 리소스 이름 |  "myVM"
    $adhRsrcGroupName |  리소스 그룹(전용 호스트) | "HostResourceGroup"
    $adh | Dedicated Host 이름 | "myHost"
    $adhParentName | 상위 리소스 이름 | "HostGroup"
    
2. PowerShell [Get-AZConfigurationAssignment](/powershell/module/az.maintenance/get-azconfigurationassignment) 명령을 사용하여 유지 관리 구성을 검색하려면:

    - Azure Dedicated Host의 경우 다음을 실행합니다.
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - Azure VM의 경우 다음을 실행합니다.

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. CLI [az maintenance assignment](/cli/azure/maintenance/assignment) 명령을 사용하여 유지 관리 구성을 검색하려면 다음 안내를 따르세요.

    - Azure Dedicated Host의 경우:

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - Azure VM의 경우:

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>이동 

1. [이 지침에 따라](../site-recovery/azure-to-azure-tutorial-migrate.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) Azure VM을 새 지역으로 이동합니다.
2. 리소스를 이동한 후 유지 관리 구성을 이동했는지 여부에 따라 유지 관리 구성을 새 지역의 리소스에 적절하게 다시 적용합니다. [PowerShell](../virtual-machines/maintenance-control-powershell.md) 또는 [CLI](../virtual-machines/maintenance-control-cli.md)를 사용하여 리소스에 유지 관리 구성을 적용할 수 있습니다.


## <a name="verify-the-move"></a>이동 확인

새 지역의 리소스를 확인하고 새 지역의 리소스에 대한 관련 구성을 확인합니다. 

## <a name="clean-up-source-resources"></a>원본 리소스 정리

이동 후 원본 영역에서 이동한 리소스를 삭제하는 것이 좋습니다.


## <a name="next-steps"></a>다음 단계

유지 관리 구성을 이동해야 하는 경우 [이 지침](move-region-maintenance-configuration.md)을 따르세요. 
