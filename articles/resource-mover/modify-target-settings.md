---
title: Azure Resource Mover를 사용하여 지역 간에 Azure VM을 이동할 때 대상 설정 수정
description: Azure Resource Mover를 사용하여 지역 간에 Azure VM을 이동할 때 대상 설정을 수정하는 방법에 대해 알아봅니다.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 02/08/2021
ms.author: raynew
ms.openlocfilehash: 7044414d47f685062331bc4aceb1b538d6f9a062
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112461610"
---
# <a name="modify-destination-settings"></a>대상 설정 수정

이 문서에서는 [Azure Resource Mover](overview.md)를 사용하여 Azure 지역 간에 리소스를 이동할 때 대상 설정을 수정하는 방법을 설명합니다.


## <a name="modify-vm-settings"></a>VM 설정 수정

Azure VM 및 관련 리소스를 이동할 때 대상 설정을 수정할 수 있습니다. 다음이 권장됩니다.

- 이동 컬렉션의 유효성을 검사한 후에만 대상 설정을 변경합니다. 단,
    - 원본 리소스를 이동하는 경우 일반적으로 이동 시작 프로세스를 시작할 때까지 이러한 설정을 수정할 수 있습니다.
    - 원본 영역에 기존 리소스를 할당하는 경우 이동 커밋이 완료될 때까지 대상 설정을 수정할 수 있습니다.
- 준비가 완료된 후에는 일부 대상 속성을 편집할 수 없기 때문에 리소스를 준비하기 전에 설정을 수정합니다.

### <a name="settings-you-can-modify"></a>수정할 수 있는 설정

수정할 수 있는 구성 설정은 표에 요약되어 있습니다.

**리소스** | **옵션** 
--- | --- 
**VM 이름** | 대상 옵션:<br/><br/> - 대상 지역에 동일한 이름을 사용하여 새 VM을 만듭니다.<br/><br/> - 대상 지역에 다른 이름을 사용하여 새 VM을 만듭니다.<br/><br/> - 대상 지역에서 기존 VM을 사용합니다.<br/><br/> 수정하는 설정을 제외하고 새 VM을 만드는 경우 새 대상 VM에 원본과 동일한 설정이 할당됩니다.
**VM 가용성 영역** | 대상 VM이 배치될 가용성 영역입니다. 원본 설정을 변경하지 않으려는 경우 또는 가용성 영역에 VM을 추가하지 않으려는 경우에는 **적용할 수 없음** 을 선택합니다.
**VM SKU** | 대상 VM에 사용되는 [VM 유형](https://azure.microsoft.com/pricing/details/virtual-machines/series/)(대상 지역에서 사용 가능).<br/><br/> 선택한 대상 VM은 원본 VM보다 작으면 안 됩니다.
**VM 가용성 집합** | 대상 VM이 배치되는 가용성 집합입니다. 원본 설정을 변경하지 않으려는 경우 또는 가용성 집합에 VM을 추가하지 않으려는 경우 **적용할 수 없음** 을 선택합니다.
**VM 키 자격 증명 모음** | VM에서 Azure Disk Encryption을 사용하도록 설정하는 경우 연결된 키 자격 증명 모음입니다.
**디스크 암호화 집합** | VM에서 서버 쪽 암호화를 위해 고객 관리형 키를 사용하는 경우의 연결된 디스크 암호화 집합입니다.
**리소스 그룹** | 대상 VM이 배치될 리소스 그룹입니다.
**네트워킹 리소스** | 네트워크 인터페이스, 가상 네트워크(VNets/), 네트워크 보안 그룹/네트워크 인터페이스에 대한 옵션<br/><br/> - 대상 지역에 동일한 이름으로 새 리소스를 만듭니다.<br/><br/> - 대상 지역에 다른 이름으로 새 리소스를 만듭니다.<br/><br/> - 대상 지역에 기존 네트워킹 리소스를 사용합니다.<br/><br/> 수정하는 설정을 제외하고 새 대상 리소스를 만드는 경우 원본 리소스와 동일한 설정이 할당됩니다.
**공용 IP 주소 이름, SKU, 영역** | 표준 공용 IP 주소에 대한 이름, [SKU](../virtual-network/public-ip-addresses.md#sku), [영역](../virtual-network/public-ip-addresses.md#standard)을 지정합니다.<br/><br/> 영역 중복으로 지정하려면 **영역 중복** 으로 입력합니다.
**부하 분산 장치 이름, SKU 및 영역** | 부하 분산 장치의 이름, SKU(기본 또는 표준), 영역을 지정합니다.<br/><br/> 표준 SKU 사용을 권장합니다.<br/><br/> 영역 중복으로 지정하려면 **영역 중복** 으로 지정합니다.
**리소스 종속성** | 각 종속성에 대한 옵션<br/><br/>- 리소스는 대상 지역으로 이동하는 원본 종속 리소스를 사용합니다.<br/><br/> - 리소스는 대상 지역에 있는 서로 다른 종속 리소스를 사용합니다. 이 경우 대상 지역의 비슷한 리소스에서 선택할 수 있습니다.

### <a name="edit-vm-destination-settings"></a>VM 대상 설정 편집

종속 리소스를 원본 지역에서 대상으로 이동하지 않으려면 몇 가지 다른 옵션을 사용할 수 있습니다.

- 대상 지역에 동일한 이름으로 새 리소스를 만듭니다. 다른 설정을 지정하지 않으면 새 리소스의 설정이 원본 리소스와 동일하게 설정됩니다.
- 대상 지역에서 기존 리소스를 사용합니다.

정확한 동작은 리소스 종류에 따라 달라집니다. 대상 설정 수정에 대한 [자세한 정보](modify-target-settings.md)를 알아봅니다.

리소스 이동 컬렉션의 **대상 구성** 항목을 사용하여 리소스에 대한 대상 설정을 수정할 수 있습니다. 

설정을 수정하려면 

1. **지역 간** 페이지에서 > **대상 구성** 열의 리소스 항목에 대한 링크를 클릭합니다.
2. **구성 설정** 에서 대상 지역에 새 VM을 만들 수 있습니다.
3. 새 가용성 영역, 가용성 집합 또는 SKU를 대상 VM에 할당합니다. **가용성 영역** 및 **SKU**

편집 중인 리소스에 대해서만 변경 내용이 적용됩니다. 모든 종속 리소스를 개별적으로 업데이트해야 합니다.


## <a name="modify-sql-settings"></a>SQL 설정 수정

Azure SQL Database 리소스를 이동하는 경우 이동에 대한 대상 설정을 수정할 수 있습니다. 

- SQL 데이터베이스의 경우
    - 이동 준비를 하기 전에 대상 구성 설정을 수정하는 것이 좋습니다.
    - 대상 데이터베이스와 데이터베이스에 대한 영역 중복에 대한 설정을 수정할 수 있습니다.
- 탄력적 풀의 경우
    -  이동을 시작하기 전에 언제든지 대상 구성을 수정할 수 있습니다.
    - 대상인 탄력적 풀 및 풀에 대한 영역 중복을 수정할 수 있습니다. 

### <a name="sql-settings-you-can-modify"></a>수정할 수 있는 SQL 설정

**설정** | **SQL 데이터베이스** | **탄력적 풀**
--- | --- | ---
**이름** | 대상 지역에 동일한 이름을 사용하여 새 데이터베이스를 만듭니다.<br/><br/> 대상 지역에 다른 이름으로 새 데이터베이스를 만듭니다.<br/><br/> 대상 지역에 기존 데이터베이스를 사용합니다. | 대상 지역에 동일한 이름으로 새 탄력적 풀을 만듭니다.<br/><br/> 대상 지역에 다른 이름으로 새 탄력적 풀을 만듭니다.<br/><br/> 대상 지역에 기존 탄력적 풀을 사용합니다.
**영역 중복** | 영역 중복을 지원하는 지역에서 지원하지 않는 지역으로 이동하려면 영역 설정에서 **사용 안 함** 을 입력합니다.<br/><br/> 영역 중복을 지원하지 않는 지역에서 지원하는 지역으로 이동하려면 영역 설정에서 **사용** 을 입력합니다. | 영역 중복을 지원하는 지역에서 지원하지 않는 지역으로 이동하려면 영역 설정에서 **사용 안 함** 을 입력합니다.<br/><br/> 영역 중복을 지원하지 않는 지역에서 지원하는 지역으로 이동하려면 영역 설정에서 **사용** 을 입력합니다.

### <a name="edit-sql-destination-settings"></a>SQL 대상 설정 편집

Azure SQL Database 리소스에 대한 대상 설정은 다음과 같이 수정합니다. 

1. **모든 지역** 에서 수정하려는 리소스에 대해 **대상 구성** 항목을 클릭합니다.
2. **구성 설정** 에서 위의 표에 요약된 대상 설정을 지정합니다.


## <a name="modify-settings-in-powershell"></a>PowerShell에서 설정 수정

PowerShell에서 설정을 수정할 수 있습니다.

1)  속성을 편집하려는 이동 리소스를 검색합니다. 예를 들어 VM 실행을 검색하려면 다음을 수행합니다.

    ```azurepowershell
    $moveResourceObj = Get-AzResourceMoverMoveResource -MoveCollectionName "PS-centralus-westcentralus-demoRMS1" -ResourceGroupName "RG-MoveCollection-demoRMS" -Name "PSDemoVM"
    ```
2)  리소스 설정을 대상 리소스 설정 개체에 복사합니다.

    ```azurepowershell
    $TargetResourceSettingObj = $moveResourceObj.ResourceSetting
    ```

3)  대상 리소스 설정 개체에서 매개 변수를 설정합니다. 예를 들어 대상 VM의 이름을 변경하려면 다음을 수행합니다.

    ```azurepowershell
    $TargetResourceSettingObj.TargetResourceName="PSDemoVM-target"
    ```

4)  리소스 이동 대상 설정을 업데이트합니다. 이 예제에서는 VM의 이름을 *PSDemoVM* 에서 *PSDemoVMTarget* 으로 변경합니다.

    ```azurepowershell
    Update-AzResourceMoverMoveResource -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -SourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/PSDemoRM/providers/Microsoft.Compute/virtualMachines/PSDemoVM" -Name "PSDemoVM" -ResourceSetting $TargetResourceSettingObj
    ```
    **출력**
    ![대상 설정 수정 후 텍스트 출력](./media/modify-target-settings/update-settings.png)


## <a name="next-steps"></a>다음 단계

다른 지역으로 [Azure VM을 이동](tutorial-move-region-virtual-machines.md)합니다.
