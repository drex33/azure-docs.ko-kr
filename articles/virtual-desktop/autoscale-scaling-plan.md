---
title: Azure Virtual Desktop 세션 호스트 자동 크기 조정 미리 보기
description: 자동 크기 조정 기능을 사용하여 배포에서 리소스를 할당하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 10/19/2021
ms.author: helohr
manager: femila
ms.custom: references_regions
ms.openlocfilehash: 2636638bcab5577bee2f4c07bae3653cc4151248
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131421699"
---
# <a name="autoscale-preview-for-azure-virtual-desktop-host-pools"></a>Azure Virtual Desktop 호스트 풀에 대한 자동 크기 조정(미리 보기)

> [!IMPORTANT]
> 자동 크기 조정 기능은 현재 미리 보기로 제공됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

자동 크기 조정 기능(미리 보기)을 사용하면 Azure Virtual Desktop 배포의 VM(가상 머신)을 확장 또는 축소하여 배포 비용을 최적화할 수 있습니다. 필요에 따라 다음을 기반으로 크기 조정 계획을 만들 수 있습니다.

- 하루 중 시간
- 특정 요일
- 세션 호스트당 세션 제한

>[!NOTE]
> - Azure Virtual Desktop(클래식)은 자동 크기 조정 기능을 지원하지 않습니다. 
> - Autsoscale은 임시 디스크의 크기 조정을 지원하지 않습니다.

최상의 결과를 위해 Azure Virtual Desktop Azure Resource Manager 템플릿 또는 Microsoft의 타사 도구를 사용하여 배포한 VM에서 자동 크기 조정을 사용하는 것이 좋습니다.

>[!IMPORTANT]
>이 기능의 미리 보기 버전에는 현재 다음과 같은 제한이 있습니다.
>
> - Azure 퍼블릭 클라우드에서만 자동 크기 조정을 사용할 수 있습니다.
> - Azure Portal 자동 크기 조정만 구성할 수 있습니다.
> - 크기 조정 계획은 미국 및 유럽 지역에만 배포할 수 있습니다.

## <a name="requirements"></a>요구 사항

첫 번째 크기 조정 계획을 만들기 전에 다음 지침을 따라야 합니다.

- 현재 풀링된 기존 호스트 풀을 사용하여 자동 크기 조정만 구성할 수 있습니다.
- 자동 크기 조정하는 모든 호스트 풀에는 구성된 MaxSessionLimit 매개 변수가 있어야 합니다. 기본값을 사용하지 마세요. Azure Portal 호스트 풀 설정에서 이 값을 구성하거나 PowerShell에서 [New-AZWvdHostPool](/powershell/module/az.desktopvirtualization/new-azwvdhostpool?view=azps-5.7.0&preserve-view=true) 또는 [Update-AZWvdHostPool](/powershell/module/az.desktopvirtualization/update-azwvdhostpool?view=azps-5.7.0&preserve-view=true) cmdlet을 실행할 수 있습니다.
- VM 컴퓨팅 리소스의 전원을 관리하려면 Azure Virtual Desktop 액세스 권한을 부여해야 합니다.

## <a name="create-a-custom-rbac-role"></a>사용자 지정 RBAC 역할 만들기

크기 조정 계획 만들기를 시작하려면 먼저 구독에서 사용자 지정 RBAC(역할 기반 Access Control) 역할을 만들어야 합니다. 이 역할을 통해 Windows Virtual Desktop에서 구독의 모든 VM을 관리할 수 있습니다. 또한 활성 사용자 세션이 없는 경우 서비스가 호스트 풀과 VM 모두에서 작업을 적용할 수 있습니다.

사용자 지정 역할을 만들려면 다음 JSON 템플릿을 사용하는 동안 [Azure 사용자 지정 역할의](../role-based-access-control/custom-roles.md) 지침을 따릅니다. 이 템플릿에는 이미 필요한 권한이 포함되어 있습니다. 자세한 지침은 [Azure Portal 사용하여 사용자 지정 역할 할당을](#assign-custom-roles-with-the-azure-portal)참조하세요.
```json
 {
 "properties": {
 "roleName": "Autoscale",
 "description": "Friendly description.",
 "assignableScopes": [
 "/subscriptions/<SubscriptionID>"
 ],
  "permissions": [
   {
   "actions": [
                 "Microsoft.Insights/eventtypes/values/read",
                 "Microsoft.Compute/virtualMachines/deallocate/action",
                 "Microsoft.Compute/virtualMachines/restart/action",
                 "Microsoft.Compute/virtualMachines/powerOff/action",
                 "Microsoft.Compute/virtualMachines/start/action",
                 "Microsoft.Compute/virtualMachines/read",
                 "Microsoft.DesktopVirtualization/hostpools/read",
                 "Microsoft.DesktopVirtualization/hostpools/write",
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/write",
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/delete",
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/read",
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/sendMessage/action",
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/read"
],
  "notActions": [],
  "dataActions": [],
  "notDataActions": []
  }
 ]
}
}
```

## <a name="assign-custom-roles-with-the-azure-portal"></a>Azure Portal 사용하여 사용자 지정 역할 할당

Azure Portal 사용하여 구독에 사용자 지정 역할을 만들고 할당하려면 다음을 수행합니다.

1. Azure Portal을 열고 **구독** 으로 이동합니다.

2. **+** 화면의 왼쪽 위 모서리에서 단추를 선택한 다음, 다음 스크린샷과 같이 드롭다운 메뉴에서 **사용자 지정 역할 추가를** 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![Azure Portal 제어판에서 더하기 기호 단추를 선택할 때 나타나는 드롭다운 메뉴를 보여 주는 스크린샷 사용자 지정 역할 추가 옵션이 선택되고 빨간색 테두리로 강조 표시됩니다.](media/add-custom-role.png)

3. 다음으로, 사용자 지정 역할의 이름을 지정하고 설명을 추가합니다. 역할 이름을 "자동 크기 조정"으로 지정하는 것이 좋습니다.

4. **사용 권한** 탭에서 역할을 할당하는 구독에 다음 권한을 추가합니다.

    ```azcopy
        "Microsoft.Insights/eventtypes/values/read"
                 "Microsoft.Compute/virtualMachines/deallocate/action"
                 "Microsoft.Compute/virtualMachines/restart/action"
                 "Microsoft.Compute/virtualMachines/powerOff/action"
                 "Microsoft.Compute/virtualMachines/start/action"
                 "Microsoft.Compute/virtualMachines/read"
                 "Microsoft.DesktopVirtualization/hostpools/read"
                 "Microsoft.DesktopVirtualization/hostpools/write"
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read"
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/write"
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/delete"
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/read"
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/sendMessage/action"
                 "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/read"
    ```

5. 작업을 마쳤으면 **확인** 을 선택합니다.

그런 다음, 역할을 할당하여 Azure Virtual Desktop에 대한 액세스 권한을 부여해야 합니다.

액세스 권한을 부여할 사용자 지정 역할을 할당하려면 다음을 수행합니다.

1. **액세스 제어(IAM) 탭** 에서 **역할 할당 추가** 를 선택합니다.

2. 방금 만든 역할을 선택하고 다음 화면으로 계속 진행합니다.

3. **+멤버 선택을 선택합니다.** 다음 스크린샷에 표시된 것처럼 검색 표시줄에서 **Windows Virtual Desktop** 을 입력하고 선택합니다. Azure Virtual Desktop(클래식) 배포와 Azure Resource Manager Azure Virtual Desktop 개체가 있는 Azure Virtual Desktop이 있는 경우 이름이 같은 두 개의 앱이 표시됩니다. 둘 다 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![역할 할당 추가 메뉴의 스크린샷. 선택 필드가 빨간색으로 강조 표시되고 사용자가 검색 필드에 "Windows Virtual Desktop"을 입력합니다.](media/search-for-role.png)

4. **검토 + 할당을** 선택하여 할당을 완료합니다.

## <a name="how-creating-a-scaling-plan-works"></a>크기 조정 계획 만들기의 작동 방식

계획을 만들기 전에 다음 사항을 염두에 두어야 합니다.

- 동일한 호스트 풀 유형의 하나 이상의 호스트 풀에 하나의 크기 조정 계획을 할당할 수 있습니다. 크기 조정 계획의 일정은 할당된 모든 호스트 풀에도 적용됩니다.

- 호스트 풀당 하나의 크기 조정 계획만 연결할 수 있습니다. 단일 크기 조정 계획을 여러 호스트 풀에 할당하는 경우 해당 호스트 풀을 다른 크기 조정 계획에 할당할 수 없습니다.

- 크기 조정 계획은 구성된 표준 시간대에서만 작동할 수 있습니다.

- 크기 조정 계획에는 하나 또는 여러 개의 일정이 있을 수 있습니다. 예를 들어 평일과 주말에는 서로 다른 일정이 있습니다.

- 일정을 정의하기 전에 사용 패턴을 이해해야 합니다. 하루 중 다음 시간을 예약해야 합니다.

    - 램프업: 사용량이 시작되는 날의 시작입니다.
    - 사용량이 가장 많은 시간: 사용량이 가장 높은 시간입니다.
    - 램프 다운: 사용량 현황이 꺼져 있는 경우 일반적으로 비용을 절약하기 위해 VM을 종료하는 경우입니다.
    - 사용량이 가장 낮은 시간: 사용량이 가장 낮은 시간입니다. 이 시간 동안 활성화할 수 있는 최대 VM 수를 정의할 수 있습니다.

- 크기 조정 계획은 사용하도록 설정하는 즉시 적용됩니다.

또한 다음 제한 사항을 염두에 두어야 합니다.

- 다른 크기 조정 Microsoft 또는 타사 크기 조정 도구와 함께 자동 크기 조정을 사용하지 마세요. 크기 조정 계획을 적용하는 호스트 풀에 대해 사용하지 않도록 설정해야 합니다.

- 자동 크기 조정은 드레인 모드를 덮어쓰므로 호스트 풀에서 VM을 업데이트할 때 제외 태그를 사용해야 합니다.

- 자동 크기 조정은 호스트 풀 설정에서 기존 부하 분산 알고리즘을 무시하고 대신 일정 구성에 따라 부하 분산을 적용합니다.

## <a name="create-a-scaling-plan"></a>크기 조정 계획 만들기

크기 조정 계획을 만들려면 다음을 수행합니다.

1. [https://portal.azure.com](https://portal.azure.com/)에서 Azure Portal에 로그인합니다.

2. Azure **Virtual Desktop**  >  **크기 조정 계획으로** 이동한 **다음, 만들기를** 선택합니다.

3. 기본 **탭에서** **Project 세부 정보** 아래에서 크기 조정 계획을 할당할 구독의 이름을 선택합니다.

4. 새 리소스 그룹을 만들려면 새 **만들기를** 선택합니다. 기존 리소스 그룹을 사용하려면 드롭다운 메뉴에서 해당 이름을 선택합니다.

5. **이름** 필드에 크기 조정 계획의 이름을 입력합니다.

6. 필요에 따라 사용자에게 표시될 "친숙한" 이름 및 계획에 대한 설명을 추가할 수도 있습니다.

7. **지역의** 경우 크기 조정 계획에 대한 지역을 선택합니다. 개체의 메타데이터는 지역과 연결된 지리에 저장됩니다. 지역에 대한 자세한 내용은 데이터 위치 를 [참조하세요.](data-locations.md)

8. **표준 시간대** 의 경우 계획에 사용할 표준 시간대를 선택합니다.

9. **제외 태그에서** 크기 조정 작업에 포함하지 않으려는 VM에 대한 태그를 입력합니다. 예를 들어 유지 관리를 위해 이 기능을 사용할 수 있습니다. 드레인 모드에서 VM을 설정한 경우 자동 크기 조정이 드레인 모드를 재정의하지 않도록 태그를 사용합니다.

10. 일정 탭으로 가는 **다음** **을** 선택합니다.

## <a name="configure-a-schedule"></a>일정 구성

일정을 사용하면 자동 크기 조정이 하루 동안 램프 업 및 램프 다운 모드를 활성화하는 시기를 정의할 수 있습니다. 일정의 각 단계에서 자동 크기 조정은 세션 호스트에 활성 세션이 없는 경우에만 VM을 해제합니다. 일정을 만들 때 표시되는 기본값은 평일에 제안된 값이지만 필요에 따라 변경할 수 있습니다. 

일정을 만들거나 변경하려면 다음을 수행합니다.

1. **일정** 탭에서 일정 **추가를** 선택합니다.

2. 일정 이름 필드에 일정 **이름을** 입력합니다.

3. 반복 **날짜** 필드에서 일정이 반복되는 요일을 선택합니다.

4. **위로 램프** 탭에서 다음 필드를 입력 합니다.

    - **시작 시간** 의 경우, 최대 업무 시간 동안 vm 준비를 시작 하려면 드롭다운 메뉴에서 시간을 선택 합니다.

    - **부하 분산 알고리즘** 의 경우 **너비 우선 알고리즘** 을 선택 하는 것이 좋습니다. 너비 우선 부하 분산은 사용자를 기존 Vm에 분산 하 여 액세스 시간을 빠르게 유지 합니다.
        
        >[!NOTE]
        >여기에서 선택한 부하 분산 기본 설정은 원래 호스트 풀 설정에 대해 선택한 항목을 재정의 합니다.

    - **최소 백분율의 세션 호스트 vm** 의 경우 진입 및 사용량이 많은 시간 동안 사용 하려는 세션 호스트 리소스의 양을 입력 합니다. 예를 들어 **10%** 를 선택 하 고 호스트 풀에 10 개의 세션 호스트가 있는 경우 자동 크기 조정 기능을 사용 하면 자동으로 실행 되는 시간 동안 사용자 연결에 대해 하나의 세션 호스트를 사용할 수 있습니다.
    
    - **용량 임계값** 에는 발사대 및 피크 단계의 시작을 트리거할 호스트 풀 사용량의 비율을 입력 합니다. 예를 들어 100 세션을 처리할 수 있는 호스트 풀에 대해 **60%** 를 선택 하는 경우 호스트 풀이 60 세션을 초과 하면 자동 크기 조정에서 추가 호스트를 설정 합니다.

5. **사용량이 많은 시간** 탭에서 다음 필드를 입력 합니다.

    - **시작 시간** 에서 하루 중 사용량이 가장 높은 경우 시작 시간을 입력 합니다. 시간이 크기 조정 계획에 대해 지정한 것과 동일한 표준 시간대에 있는지 확인 합니다. 이 시간은 램프 단계에 대 한 종료 시간 이기도 합니다.

    - **부하 분산** 의 경우 너비 우선 또는 깊이 우선 부하 분산 중 하나를 선택할 수 있습니다. 너비 우선 부하 분산은 호스트 풀에서 사용 가능한 모든 세션에 새 사용자 세션을 배포 합니다. 깊이 우선 부하 분산은 세션 제한에 아직 도달 하지 않은 연결 수가 가장 많은 사용 가능한 세션 호스트에 새 세션을 배포 합니다. 부하 분산 유형에 대 한 자세한 내용은 [Azure 가상 데스크톱 부하 분산 방법 구성](configure-host-pool-load-balancing.md)을 참조 하세요.

    >[!NOTE]
    >여기에서 용량 임계값을 변경할 수 없습니다. 대신, **발사대** 에서 입력 한 설정이이 설정으로 전달 됩니다.

    - **램프** 를 사용 하는 경우 비슷한 필드에 값을 **입력 하 여** 시작 합니다. 하지만 이번에는 호스트 풀 사용이 해제 될 때에 해당 합니다. 여기에는 다음 필드가 포함 됩니다.

      - 시작 시간
      - 부하 분산 알고리즘
      - 호스트의 최소 비율 (%)
      - 용량 임계값 (%)
      - 강제로 로그 오프 사용자

    - 마찬가지로, 사용량이 적은 **시간은** **사용량이 많은 시간과** 동일한 방식으로 작동 합니다.

      - 시작 시간으로, 램프 종료 기간의 끝 이기도 합니다.
      - 부하 분산 알고리즘. 각 VM의 세션을 기반으로 세션 호스트의 수를 점차 줄이기 위해 **깊이 우선** 을 선택 하는 것이 좋습니다.
      - 사용량이 많은 시간과 마찬가지로 여기에서 용량 임계값을 구성할 수 없습니다. 대신, **램프** 에 입력 한 값이 전달 됩니다.

## <a name="assign-host-pools"></a>호스트 풀 할당

이제 크기 조정 계획을 설정 했으므로 호스트 풀에 계획을 할당할 시간입니다. 포함 하려는 각 호스트 풀 옆의 확인란을 선택 합니다. 자동 크기 조정을 사용 하지 않으려면 모든 확인란의 선택을 취소 합니다. 나중에 언제 든 지이 설정으로 돌아와서 변경할 수 있습니다.

>[!NOTE]
>호스트 풀에 이미 할당 된 크기 조정 계획을 만들거나 업데이트 하는 경우 해당 변경 내용이 즉시 적용 됩니다.

## <a name="add-tags"></a>태그 추가 

그런 다음 태그를 입력 해야 합니다. 태그는 통합 된 청구에 대 한 리소스를 분류 하는 이름 및 값 쌍입니다. 여러 리소스 및 리소스 그룹에 동일한 태그를 적용할 수 있습니다. 리소스 태그 지정에 대해 자세히 알아보려면 [태그를 사용 하 여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md)을 참조 하세요.

>[!NOTE] 
>태그를 만든 후 다른 탭의 리소스 설정을 변경 하면 태그가 자동으로 업데이트 됩니다.

완료 되 면 **검토 + 만들기** 탭으로 이동 하 여 **만들기** 를 선택 하 여 호스트 풀을 배포 합니다.

## <a name="next-steps"></a>다음 단계

이제 크기 조정 계획을 만들었으므로 다음 몇 가지 작업을 수행할 수 있습니다.

- [새 호스트 풀 및 기존 호스트 풀에 크기 조정 계획 할당](autoscale-new-existing-host-pool.md)
- [크기 조정 계획에 대 한 진단 사용](autoscale-diagnostics.md)
