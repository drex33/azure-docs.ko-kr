---
title: Azure Automation 변경 내용 추적 및 인벤토리에서 컴퓨터 제거
description: 이 문서에서는 변경 내용 추적 및 인벤토리에서 Azure 및 비 Azure 컴퓨터를 제거 하는 방법을 설명 합니다.
services: automation
ms.subservice: change-inventory-management
ms.topic: conceptual
ms.date: 10/26/2021
ms.openlocfilehash: 810b2ac388b0f387183f735437ccc2b2a3918cfd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026302"
---
# <a name="remove-machines-from-change-tracking-and-inventory"></a>변경 내용 추적 및 인벤토리에서 컴퓨터 제거

사용자 환경에서 Azure 또는 비 Azure 컴퓨터에 대 한 변경 내용을 추적 하는 작업이 완료 되 면 [변경 내용 추적 및 인벤토리](overview.md) 기능을 사용 하 여 관리를 중단할 수 있습니다. 관리를 중지하려면 자동화 계정에 연결된 Log Analytics 작업 영역에서 저장된 검색 쿼리 `MicrosoftDefaultComputerGroup`을 편집합니다.

## <a name="sign-into-the-azure-portal"></a>Azure Portal에 로그인합니다.

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="to-remove-your-machines"></a>컴퓨터를 제거 하려면

1. Azure Portal의 상단 탐색에서 **Cloud Shell** 을 시작합니다. Azure Cloud Shell에 익숙하지 않은 경우 [Azure Cloud Shell 개요](../../cloud-shell/overview.md)를 참조하세요.

2. 다음 방법을 사용 하 여 관리에서 제거 하려는 Azure 가상 컴퓨터 또는 비 Azure 컴퓨터의 UUID를 식별 합니다.

   # <a name="azure-vm"></a>[Azure VM](#tab/azure-vm)

   ```azurecli
   az vm show -g MyResourceGroup -n MyVm -d
   ```

   # <a name="non-azure-machine"></a>[비 Azure 컴퓨터](#tab/non-azure-machine)

   ```kusto
   Heartbeat
   | where TimeGenerated > ago(30d)
   | where ComputerEnvironment == "Non-Azure"
   | summarize by Computer, VMUUID
   ```

   ---

3. Azure Portal에서 **Log Analytics 작업 영역** 으로 이동합니다. 목록에서 작업 영역을 선택합니다.

4. Log Analytics 작업 영역에서 왼쪽 메뉴의 **컴퓨터 그룹** 을 선택합니다.

5. 오른쪽 창의 **컴퓨터 그룹** 에서 **저장된 그룹** 탭이 기본적으로 표시됩니다.

6. 테이블에서 **레거시 범주** 값이 **ChangeTracking** 인 **MicrosoftDefaultComputerGroup** 항목의 오른쪽에 있는 **쿼리 실행** 아이콘을 클릭합니다.

7. 쿼리 편집기에서 쿼리를 검토하고 VM에 대한 UUID를 찾습니다. VM에 대한 UUID를 제거하고 제거하려는 다른 VM에 대한 단계를 반복합니다.

   > [!NOTE]
   > 보호를 강화하려면 편집하기 전에 쿼리 복사본을 만들어야 합니다. 그런 다음 문제가 발생하면 이를 복원할 수 있습니다.

   원래 쿼리로 시작하고 정리 또는 유지 관리 작업을 지원하는 머신을 다시 추가하려면 다음 쿼리를 복사합니다.

   ```kusto
   Heartbeat
   | where Computer in~ ("") or VMUUID in~ ("")
   | distinct Computer
   ```

8. 위쪽 막대에서 **저장 > 기능으로 저장** 을 선택하여 편집을 마치면 저장된 검색을 저장합니다. 메시지가 표시되면 다음을 지정합니다.

    * **이름**: ChangeTracking__MicrosoftDefaultComputerGroup
    * **컴퓨터 그룹으로 저장** 을 선택합니다.
    * **레거시 범주**: ChangeTracking

>[!NOTE]
>최근 24시간 동안 평가된 모든 머신을 보고하기 때문에 등록 취소된 후에도 머신이 계속 표시됩니다. 머신을 제거한 후에는 더 이상 나열되지 않을 때까지 24시간 동안 대기해야 합니다.

## <a name="next-steps"></a>다음 단계

이 기능을 다시 사용하도록 설정하려면 [Automation 계정에서 변경 내용 추적 및 인벤토리 사용](enable-from-automation-account.md), [Azure Portal을 검색하여 변경 내용 추적 및 인벤토리를 사용하도록 설정](enable-from-portal.md), [Runbook에서 변경 내용 추적 및 인벤토리 사용](enable-from-runbook.md), [Azure VM에서 변경 내용 추적 및 인벤토리 사용](enable-from-vm.md)을 참조하세요.