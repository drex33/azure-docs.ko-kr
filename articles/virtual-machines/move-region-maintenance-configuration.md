---
title: 유지 관리 구성을 다른 Azure 지역으로 이동
description: VM 유지 관리 구성을 다른 Azure 지역으로 이동하는 방법을 알아봅니다.
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: ad0f4905e5e6f404ef9eb86d42d729e1559bef90
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698041"
---
# <a name="move-a-maintenance-control-configuration-to-another-region"></a>유지 관리 제어 구성을 다른 지역으로 이동

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

이 문서에 따라 유지 관리 제어 구성을 다른 Azure 지역으로 이동합니다. 여러 가지 이유로 구성을 이동하는 것이 좋습니다. 예를 들어 새 지역을 활용하거나, 특정 지역에서 사용할 수 있는 기능이나 서비스를 배포하거나, 내부 정책 및 거버넌스 요구 사항을 충족하거나, 용량 계획에 대응하기 위해 리소스를 이동할 수 있습니다.

사용자 지정된 유지 관리 구성을 가진 [유지 관리 제어](maintenance-control.md)를 사용하면 플랫폼 업데이트가 VM 및 Azure Dedicated Host에 적용되는 방법을 제어할 수 있습니다. 지역 간에 유지 관리 제어를 이동하는 두 가지 시나리오가 있습니다.

- 구성과 연결된 리소스가 아닌, 유지 관리 제어 구성을 이동하려면 이 문서의 지침을 따르세요.
- 구성 자체가 아닌, 유지 관리 구성과 연결된 리소스를 이동하려면 [다음 지침을 따르세요.](move-region-maintenance-configuration-resources.md)
- 유지 관리 구성과 그와 연결된 리소스를 모두 이동하려면, 먼저 이 문서의 지침을 따릅니다. 그리고 나서 [다음 지침](move-region-maintenance-configuration-resources.md)을 따릅니다.

## <a name="prerequisites"></a>필수 조건

유지 관리 제어 구성 이동을 시작하기 전에 다음을 수행합니다.

- 유지 관리 구성은 Azure VM 또는 Azure Dedicated Host와 연결됩니다. 시작하기 전에 새 지역에 VM/호스트 리소스가 있는지 확인합니다.
- Identify: 
    - 기존 유지 관리 제어 구성.
    - 기존 구성이 현재 있는 리소스 그룹입니다. 
    - 새 지역으로 이동한 후 구성을 추가할 리소스 그룹입니다. 
    - 이동하려는 유지 관리 구성과 연결된 리소스입니다.
    - 새 지역의 리소스가 현재 유지 관리 구성과 연결된 리소스와 동일한지 확인합니다. 이 구성은 새 지역에서 이전과 동일한 이름을 가질 수 있지만 필수는 아닙니다.

## <a name="prepare-and-move"></a>준비 및 이동 

1. 각 구독의 모든 유지 관리 구성을 검색합니다. CLI [az maintenance configuration list](/cli/azure/maintenance/configuration#az_maintenance_configuration_list) 명령을 실행하여 이 작업을 수행합니다. $subId는 구독 ID로 대체합니다.

    ```
    az maintenance configuration list --subscription $subId --query "[*].{Name:name, Location:location, ResGroup:resourceGroup}" --output table
    ```
2. 구독 내에서 구성 레코드의 반환된 테이블 목록을 검토합니다. 예를 들면 다음과 같습니다. 목록에는 특정 환경에 대한 값이 포함됩니다.

    **이름** | **위치** | **리소스 그룹**
    --- | --- | ---
    유지 관리 건너뛰기 | eastus2 | configuration-resource-group
    IgniteDemoConfig | eastus2 | configuration-resource-group
    defaultMaintenanceConfiguration-eastus | eastus | test-configuration
    

3. 참조용으로 목록을 저장합니다. 이것은 구성을 이동할 때 모든 것이 이동되었는지 확인하는 데 도움이 됩니다.
4. 참조로 각 구성/리소스 그룹을 새 지역의 새 리소스 그룹에 매핑합니다.
5. [PowerShell](../virtual-machines/maintenance-control-powershell.md#create-a-maintenance-configuration)또는 [CLI](../virtual-machines/maintenance-control-cli.md#create-a-maintenance-configuration)를 사용하여 새 지역에서 새 유지 관리 구성을 만듭니다.
6. [PowerShell](../virtual-machines/maintenance-control-powershell.md#assign-the-configuration)또는 [CLI](../virtual-machines/maintenance-control-cli.md#assign-the-configuration)를 사용하여 구성을 새 지역의 리소스와 연결합니다.


## <a name="verify-the-move"></a>이동 확인

구성을 이동한 후에는 새 지역의 구성과 리소스를 준비한 테이블 목록과 비교합니다.


## <a name="clean-up-source-resources"></a>원본 리소스 정리

이동 후 원본 지역, [PowerShell](../virtual-machines/maintenance-control-powershell.md#remove-a-maintenance-configuration) 또는 [CLI](../virtual-machines/maintenance-control-cli.md#delete-a-maintenance-configuration)에서 이동된 유지 관리 구성을 삭제하는 것이 좋습니다.


## <a name="next-steps"></a>다음 단계

유지 관리 구성과 연결된 리소스를 이동해야 하는 경우 [다음 지침](move-region-maintenance-configuration-resources.md)을 따르세요. 
