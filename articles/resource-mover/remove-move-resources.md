---
title: Azure Resource Mover의 이동 컬렉션에서 리소스 제거
description: Azure Resource Mover의 이동 컬렉션에서 리소스를 제거하는 방법에 대해 알아봅니다.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 02/22/2020
ms.author: raynew
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a655e8f0a4e4a6d44ce8960b45991cf6e394e2db
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112454321"
---
# <a name="manage-move-collections-and-resource-groups"></a>이동 컬렉션 및 리소스 그룹 관리

이 문서에서는 [Azure Resource Mover](overview.md)에 있는 이동 컬렉션에서 리소스를 제거하거나 이동 컬렉션/리소스 그룹을 제거하는 방법을 설명합니다. 이동 컬렉션은 Azure 지역 간에 Azure 리소스를 이동할 때 사용됩니다.

## <a name="remove-a-resource-portal"></a>리소스(포털) 제거

다음과 같이 Resource Mover 포털에서 이동 컬렉션의 리소스를 제거할 수 있습니다.

1. **전체 지역** 의 컬렉션에서 제거하려는 모든 리소스를 선택하고 **제거** 를 선택합니다. 

    ![제거하기 위해 선택하는 단추](./media/remove-move-resources/portal-select-resources.png)

2. **리소스 제거** 에서 **제거** 를 클릭합니다.

    ![이동 컬렉션에서 리소스를 제거하기 위해 선택하는 단추](./media/remove-move-resources/remove-portal.png)

## <a name="remove-a-move-collectionresource-group-portal"></a>이동 컬렉션/리소스 그룹 제거(포털)

포털에서 이동 컬렉션/리소스 그룹을 제거할 수 있습니다.

1. 위 절차의 지침에 따라 컬렉션에서 리소스를 제거합니다. 리소스 그룹을 제거하는 경우 리소스가 포함되어 있지 않은지 확인합니다.
2. 이동 컬렉션 또는 리소스 그룹을 삭제합니다.  

## <a name="remove-a-resource-powershell"></a>리소스 제거(PowerShell)

PowerShell cmdlet을 사용하면 MoveCollection에서 단일 리소스를 제거하거나 여러 리소스를 제거할 수 있습니다.

### <a name="remove-a-single-resource"></a>단일 리소스 제거

다음과 같이 리소스(이 예제에서는 가상 네트워크 *psdemorm-vnet*)를 제거합니다.

```azurepowershell-interactive
# Remove a resource using the resource ID
Remove-AzResourceMoverMoveResource -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -Name "psdemorm-vnet"
```
**cmdlet 실행 후 출력**

![이동 컬렉션에서 리소스를 제거한 후 텍스트 출력](./media/remove-move-resources/powershell-remove-single-resource.png)

### <a name="remove-multiple-resources"></a>여러 리소스 제거

다음과 같이 여러 리소스를 제거합니다.

1. 종속성의 유효성을 검사합니다.

    ```azurepowershell-interactive
    $resp = Invoke-AzResourceMoverBulkRemove -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemorm-vnet') -ValidateOnly
    ```

    **cmdlet 실행 후 출력**

    ![이동 컬렉션에서 여러 리소스를 제거한 후 출력 텍스트](./media/remove-move-resources/remove-multiple-validate-dependencies.png)

2. 제거해야 하는 종속 리소스를 검색합니다(예제 가상 네트워크 psdemorm-vnet과 함께).

    ```azurepowershell-interactive
    $resp.AdditionalInfo[0].InfoMoveResource
    ```

    **cmdlet 실행 후 출력**

    ![제거해야 하는 종속 리소스를 검색한 후 출력 텍스트](./media/remove-move-resources/remove-multiple-get-dependencies.png)


3. 가상 네트워크와 함께 모든 리소스를 제거합니다.

    
    ```azurepowershell-interactive
    Invoke-AzResourceMoverBulkRemove -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('PSDemoVM','psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg')
    ```

    **cmdlet 실행 후 출력**

    ![이동 컬렉션에서 모든 리소스를 제거한 후 출력 텍스트](./media/remove-move-resources/remove-multiple-all.png)


## <a name="remove-a-collection-powershell"></a>컬렉션 제거(PowerShell)

다음과 같이 구독에서 전체 이동 컬렉션을 제거합니다.

1. 위 지침에 따라 PowerShell을 사용하여 컬렉션에서 리소스를 제거합니다.
2. 다음과 같이 컬렉션을 제거합니다.

    ```azurepowershell-interactive
    Remove-AzResourceMoverMoveCollection -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"
    ```

    **cmdlet 실행 후 출력**
    
    ![이동 컬렉션을 제거한 후 출력 텍스트](./media/remove-move-resources/remove-collection.png)

## <a name="vm-resource-state-after-removing"></a>제거한 후 VM 리소스 상태

이동 컬렉션에서 VM 리소스를 제거할 때 발생하는 동작은 표에 요약된 대로 리소스 상태에 따라 다릅니다.

###  <a name="remove-vm-state"></a>VM 상태 제거
**리소스 상태** | **VM** | **네트워킹**
--- | --- | --- 
**컬렉션을 이동하기 위해 추가됨** | 이동 컬렉션에서 삭제합니다. | 이동 컬렉션에서 삭제합니다. 
**종속성 해결됨/준비 보류 중** | 이동 컬렉션에서 삭제  | 이동 컬렉션에서 삭제합니다. 
**준비 진행 중**<br/> (또는 기타 상태 진행 중) | 삭제 작업이 오류로 인해 실패합니다.  | 삭제 작업이 오류로 인해 실패합니다.
**준비 실패** | 이동 컬렉션에서 삭제합니다.<br/>복제본 디스크를 포함하여 대상 지역에 생성된 모든 항목을 삭제합니다. <br/><br/> 이동하는 동안 생성된 인프라 리소스는 수동으로 삭제해야 합니다. | 이동 컬렉션에서 삭제합니다.  
**이동 시작 보류 중** | 이동 컬렉션에서 삭제합니다.<br/><br/> VM, 복제본 디스크 등을 포함하여 대상 지역에 생성된 모든 항목을 삭제합니다.  <br/><br/> 이동하는 동안 생성된 인프라 리소스는 수동으로 삭제해야 합니다. | 이동 컬렉션에서 삭제합니다.
**이동 시작 실패** | 이동 컬렉션에서 삭제합니다.<br/><br/> VM, 복제본 디스크 등을 포함하여 대상 지역에 생성된 모든 항목을 삭제합니다.  <br/><br/> 이동하는 동안 생성된 인프라 리소스는 수동으로 삭제해야 합니다. | 이동 컬렉션에서 삭제합니다.
**커밋 보류 중** | 대상 리소스가 먼저 삭제되도록 이동을 무시하는 것이 좋습니다.<br/><br/> 리소스가 **이동 시작 보류 중** 상태로 돌아가고 해당 위치에서 계속할 수 있습니다. | 대상 리소스가 먼저 삭제되도록 이동을 무시하는 것이 좋습니다.<br/><br/> 리소스가 **이동 시작 보류 중** 상태로 돌아가고 해당 위치에서 계속할 수 있습니다. 
**커밋 실패** | 대상 리소스가 먼저 삭제되도록 이동을 무시하는 것이 좋습니다.<br/><br/> 리소스가 **이동 시작 보류 중** 상태로 돌아가고 해당 위치에서 계속할 수 있습니다. | 대상 리소스가 먼저 삭제되도록 이동을 무시하는 것이 좋습니다.<br/><br/> 리소스가 **이동 시작 보류 중** 상태로 돌아가고 해당 위치에서 계속할 수 있습니다.
**취소 완료** | 리소스가 **이동 시작 보류 중** 상태로 돌아갑니다.<br/><br/> VM, 복제본 디스크, 자격 증명 모음 등 대상에서 생성된 모든 항목과 함께 이동 컬렉션에서 삭제됩니다.  <br/><br/> 이동하는 동안 생성된 인프라 리소스는 수동으로 삭제해야 합니다. <br/><br/> 이동하는 동안 생성된 인프라 리소스는 수동으로 삭제해야 합니다. |  리소스가 **이동 시작 보류 중** 상태로 돌아갑니다.<br/><br/> 이동 컬렉션에서 삭제되었습니다.
**무시 실패** | 대상 리소스가 먼저 삭제되도록 이동을 무시하는 것이 좋습니다.<br/><br/> 이후에 리소스가 **이동 시작 보류 중** 상태로 돌아가고 해당 위치에서 계속할 수 있습니다. | 대상 리소스가 먼저 삭제되도록 이동을 무시하는 것이 좋습니다.<br/><br/> 이후에 리소스가 **이동 시작 보류 중** 상태로 돌아가고 해당 위치에서 계속할 수 있습니다.
**원본 삭제 보류 중** | 이동 컬렉션에서 삭제되었습니다.<br/><br/> 대상 지역에 생성된 항목은 삭제되지 않습니다.  | 이동 컬렉션에서 삭제되었습니다.<br/><br/> 대상 지역에 생성된 항목은 삭제되지 않습니다.
**원본 삭제 실패** | 이동 컬렉션에서 삭제되었습니다.<br/><br/> 대상 지역에 생성된 항목은 삭제되지 않습니다. | 이동 컬렉션에서 삭제되었습니다.<br/><br/> 대상 지역에 생성된 항목은 삭제되지 않습니다.
**이동 완료** | 이동 컬렉션에서 삭제되었습니다.<br/><br/> 대상 또는 원본 지역에 생성된 항목은 삭제되지 않습니다. |  이동 컬렉션에서 삭제되었습니다.<br/><br/> 대상 또는 원본 지역에 생성된 항목은 삭제되지 않습니다.

## <a name="sql-resource-state-after-removing"></a>제거한 후 SQL 리소스 상태

이동 컬렉션에서 Azure SQL 리소스를 제거할 때 발생하는 동작은 표에 요약된 대로 리소스 상태에 따라 다릅니다.

**리소스 상태** | **SQL** 
--- | --- 
**컬렉션을 이동하기 위해 추가됨** | 이동 컬렉션에서 삭제합니다. 
**종속성 해결됨/준비 보류 중** | 이동 컬렉션에서 삭제 
**준비 진행 중**<br/> (또는 기타 상태 진행 중)  | 삭제 작업이 오류로 인해 실패합니다. 
**준비 실패** | 이동 컬렉션에서 삭제<br/><br/>대상 지역에 생성된 모든 항목을 삭제합니다. 
**이동 시작 보류 중** |  이동 컬렉션에서 삭제<br/><br/>대상 지역에 생성된 모든 항목을 삭제합니다. SQL 데이터베이스가 이 지점에 있으며 삭제됩니다. 
**이동 시작 실패** | 이동 컬렉션에서 삭제<br/><br/>대상 지역에 생성된 모든 항목을 삭제합니다. SQL 데이터베이스가 이 지점에 있으며 삭제되어야 합니다. 
**커밋 보류 중** | 대상 리소스가 먼저 삭제되도록 이동을 무시하는 것이 좋습니다.<br/><br/> 리소스가 **이동 시작 보류 중** 상태로 돌아가고 해당 위치에서 계속할 수 있습니다.
**커밋 실패** | 대상 리소스가 먼저 삭제되도록 이동을 무시하는 것이 좋습니다.<br/><br/> 리소스가 **이동 시작 보류 중** 상태로 돌아가고 해당 위치에서 계속할 수 있습니다. 
**취소 완료** |  리소스가 **이동 시작 보류 중** 상태로 돌아갑니다.<br/><br/> SQL 데이터베이스를 포함하여 대상에서 생성된 모든 항목과 함께 이동 컬렉션에서 삭제됩니다. 
**무시 실패** | 대상 리소스가 먼저 삭제되도록 이동을 무시하는 것이 좋습니다.<br/><br/> 이후에 리소스가 **이동 시작 보류 중** 상태로 돌아가고 해당 위치에서 계속할 수 있습니다. 
**원본 삭제 보류 중** | 이동 컬렉션에서 삭제되었습니다.<br/><br/> 대상 지역에 생성된 항목은 삭제되지 않습니다. 
**원본 삭제 실패** | 이동 컬렉션에서 삭제되었습니다.<br/><br/> 대상 지역에 생성된 항목은 삭제되지 않습니다. 
**이동 완료** | 이동 컬렉션에서 삭제되었습니다.<br/><br/> 대상 또는 원본 지역에 생성된 항목은 삭제되지 않습니다.

## <a name="next-steps"></a>다음 단계

Resource Mover를 사용하여 다른 지역으로 [VM 이동](tutorial-move-region-virtual-machines.md)을 시도합니다.
