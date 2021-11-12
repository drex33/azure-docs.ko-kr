---
title: Azure 가상 데스크톱 세션 호스트 자동 크기 조정 미리 보기
description: 자동 크기 조정 기능을 사용 하 여 배포에서 리소스를 할당 하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 10/19/2021
ms.author: helohr
manager: femila
ms.custom: references_regions
ms.openlocfilehash: a88c9ecef36786f67c930a22ecdd67d5890da92f
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399070"
---
# <a name="autoscale-preview-for-azure-virtual-desktop-host-pools"></a>Azure 가상 데스크톱 호스트 풀의 자동 크기 조정 (미리 보기)

> [!IMPORTANT]
> 자동 크기 조정 기능은 현재 미리 보기로 제공됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

자동 크기 조정 기능 (미리 보기)을 사용 하면 Azure 가상 데스크톱 배포의 Vm (가상 머신)을 확장 하거나 축소 하 여 배포 비용을 최적화할 수 있습니다. 사용자의 요구에 따라 다음을 기준으로 크기 조정 계획을 만들 수 있습니다.

- 하루 중 시간
- 특정 요일
- 세션 호스트 당 세션 제한

>[!NOTE]
> - Azure 가상 데스크톱 (클래식)은 자동 크기 조정 기능을 지원 하지 않습니다. 
> - 자동 크기 조정은 Azure 가상 데스크톱 Azure Stack HCI을 지원 하지 않습니다. 
> - Autsoscale는 임시 디스크의 크기 조정을 지원 하지 않습니다.


최상의 결과를 위해 Azure 가상 데스크톱 Azure Resource Manager 템플릿 또는 Microsoft의 자사 도구를 사용 하 여 배포한 Vm에 자동 크기 조정을 사용 하는 것이 좋습니다.

>[!IMPORTANT]
>이 기능의 미리 보기 버전에는 현재 다음과 같은 제한 사항이 있습니다.
>
> - Azure 공용 클라우드에서 자동 크기 조정을 사용할 수 있습니다.
> - 자동 크기 조정은 Azure Portal만 구성할 수 있습니다.
> - 크기 조정 요금제는 미국 및 유럽 지역에만 배포할 수 있습니다.

## <a name="requirements"></a>요구 사항

첫 번째 크기 조정 계획을 만들기 전에 다음 지침을 따라야 합니다.

- 현재 풀링된 기존 호스트 풀에만 자동 크기 조정을 구성할 수 있습니다.
- 자동 크기 조정 하는 모든 호스트 풀에는 구성 된 MaxSessionLimit 매개 변수가 있어야 합니다. 기본값은 사용 하지 마세요. Azure Portal의 호스트 풀 설정에서이 값을 구성 하거나 PowerShell에서 [AZWvdHostPool](/powershell/module/az.desktopvirtualization/new-azwvdhostpool?view=azps-5.7.0&preserve-view=true) 또는 [AZWvdHostPool](/powershell/module/az.desktopvirtualization/update-azwvdhostpool?view=azps-5.7.0&preserve-view=true) cmdlet을 실행할 수 있습니다.
- VM 계산 리소스의 전원을 관리 하려면 Azure 가상 데스크톱 액세스 권한을 부여 해야 합니다.

## <a name="create-a-custom-rbac-role"></a>사용자 지정 RBAC 역할 만들기

크기 조정 계획을 만들기 시작 하려면 먼저 구독에서 사용자 지정 RBAC (역할 기반 Access Control) 역할을 만들어야 합니다. 이 역할을 통해 가상 데스크톱 Windows 구독의 모든 vm을 전원 관리할 수 있습니다. 또한 활성 사용자 세션이 없을 때 서비스에서 호스트 풀과 Vm 모두에 대 한 작업을 적용할 수 있습니다.

사용자 지정 역할을 만들려면 다음 JSON 템플릿을 사용 하는 동안 [Azure 사용자 지정 역할](../role-based-access-control/custom-roles.md) 의 지침을 따릅니다. 이 템플릿에는 이미 필요한 권한이 포함 되어 있습니다. 자세한 내용은 [Azure Portal를 사용 하 여 사용자 지정 역할 할당](#assign-custom-roles-with-the-azure-portal)을 참조 하세요.
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

## <a name="assign-custom-roles-with-the-azure-portal"></a>Azure Portal를 사용 하 여 사용자 지정 역할 할당

Azure Portal를 사용 하 여 구독에 사용자 지정 역할을 만들고 할당 하려면 다음을 수행 합니다.

1. Azure Portal을 열고 **구독** 으로 이동합니다.

2. **+** 다음 스크린샷에 표시 된 것 처럼 화면의 왼쪽 위 모서리에 있는 단추를 선택 하 고 드롭다운 메뉴에서 **사용자 지정 역할 추가** 를 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![Azure Portal 제어판에서 더하기 기호 단추를 선택할 때 표시 되는 드롭다운 메뉴를 보여 주는 스크린샷 사용자 지정 역할 추가 옵션이 선택 되 고 빨간색 테두리로 강조 표시 됩니다.](media/add-custom-role.png)

3. 다음으로, 사용자 지정 역할의 이름을 지정하고 설명을 추가합니다. "자동 크기 조정" 역할의 이름을로 하는 것이 좋습니다.

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

액세스 권한을 부여 하기 위해 사용자 지정 역할을 할당 하려면:

1. **액세스 제어(IAM) 탭** 에서 **역할 할당 추가** 를 선택합니다.

2. 방금 만든 역할을 선택 하 고 다음 화면으로 진행 합니다.

3. **+ 멤버 선택** 을 선택 합니다. 다음 스크린샷에 표시 된 것 처럼 검색 창에서 **가상 데스크톱 Windows** 를 입력 하 고 선택 합니다. Azure 가상 데스크톱 (클래식) 배포와 azure 가상 데스크톱 개체 Azure Resource Manager 있는 azure 가상 데스크톱이 있으면 동일한 이름의 두 앱이 표시 됩니다. 둘 다 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![역할 할당 추가 메뉴의 스크린샷 사용자가 검색 필드에 "Windows 가상 데스크톱"을 입력 하면 선택 필드가 빨간색으로 강조 표시 됩니다.](media/search-for-role.png)

4. **검토 + 할당** 을 선택 하 여 할당을 완료 합니다.

## <a name="how-creating-a-scaling-plan-works"></a>크기 조정 계획을 만드는 방법

계획을 만들기 전에 다음 사항을 염두에 두어야 합니다.

- 하나의 크기 조정 계획을 동일한 호스트 풀 유형의 호스트 풀 하나 이상에 할당할 수 있습니다. 또한 크기 조정 계획의 일정은 할당 된 모든 호스트 풀에서 적용 됩니다.

- 호스트 풀 당 하나의 크기 조정 계획을 연결할 수 있습니다. 단일 크기 조정 계획을 여러 호스트 풀에 할당 하는 경우 해당 호스트 풀을 다른 크기 조정 계획에 할당할 수 없습니다.

- 크기 조정 계획은 구성 된 표준 시간대 에서만 작동할 수 있습니다.

- 크기 조정 계획에는 하나 또는 여러 개의 일정이 있을 수 있습니다. 예를 들어 평일 및 주말의 일정은 서로 다릅니다.

- 일정을 정의 하기 전에 사용 패턴을 이해 하 고 있는지 확인 합니다. 다음 시간에 예약 해야 합니다.

    - 램프-사용을 선택 하는 경우 일의 시작입니다.
    - 최대 사용 시간: 사용량이 가장 높은 시간입니다.
    - 램프: usage tapers off입니다. 이는 일반적으로 Vm을 종료 하 여 비용을 절감할 때 발생 합니다.
    - 사용량 감소 시: 가능한 가장 낮은 사용량의 시간입니다. 이 시간 동안 활성 상태일 수 있는 최대 Vm 수를 정의할 수 있습니다.

- 크기 조정 계획은 사용 하도록 설정 하는 즉시 적용 됩니다.

또한 다음과 같은 제한 사항을 염두에 두어야 합니다.

- 크기 자동 조정을 다른 Microsoft 또는 타사 크기 조정 도구와 함께 사용 하지 마세요. 크기 조정 계획을 적용 하는 호스트 풀에 대해 해당 설정을 사용 하지 않도록 설정 해야 합니다.

- 자동 크기 조정은 드레이닝 모드를 덮어쓰므로 호스트 풀에서 Vm을 업데이트할 때 제외 태그를 사용 해야 합니다.

- 자동 크기 조정은 호스트 풀 설정에서 기존 부하 분산 알고리즘을 무시 하 고 대신 일정 구성에 따라 부하 분산을 적용 합니다.

## <a name="create-a-scaling-plan"></a>크기 조정 계획 만들기

크기 조정 계획을 만들려면 다음을 수행 합니다.

1. [https://portal.azure.com](https://portal.azure.com/)에서 Azure Portal에 로그인합니다.

2. **Azure 가상 데스크톱**  >  **확장 계획** 으로 이동한 다음, **만들기** 를 선택 합니다.

3. **기본 사항** 탭에서 **세부 정보 Project** 아래를 확인 하 고, 크기 조정 계획을 할당할 구독의 이름을 선택 합니다.

4. 새 리소스 그룹을 만들려면 **새로 만들기** 를 선택 합니다. 기존 리소스 그룹을 사용 하려면 드롭다운 메뉴에서 이름을 선택 합니다.

5. **이름** 필드에 확장 계획의 이름을 입력 합니다.

6. 필요에 따라 사용자에 게 표시 되는 "친숙 한" 이름과 계획에 대 한 설명을 추가할 수도 있습니다.

7. **지역** 에서 크기 조정 계획에 대 한 지역을 선택 합니다. 개체에 대 한 메타 데이터는 지역과 연결 된 지리에 저장 됩니다. 영역에 대 한 자세한 내용은 [데이터 위치](data-locations.md)를 참조 하세요.

8. **표준 시간대** 에 대해 계획에 사용할 표준 시간대를 선택 합니다.

9. **제외 태그** 에서 크기 조정 작업에 포함 하지 않으려는 vm에 대 한 태그를 입력 합니다. 예를 들어 유지 관리를 위해이 기능을 사용할 수 있습니다. 드레이닝 모드에서 Vm을 설정한 경우에는 자동 크기 조정이 드레이닝 모드를 재정의 하지 않도록 태그를 사용 합니다.

10. **다음** 을 선택 하면 **일정** 탭으로 이동 합니다.

## <a name="configure-a-schedule"></a>일정 구성

일정을 사용 하면 자동 크기 조정에서 하루 종일 램프 모드를 활성화 하는 시점을 정의할 수 있습니다. 일정의 각 단계에서 자동 크기 조정은 세션 호스트에 활성 세션이 없는 경우에만 Vm을 끕니다. 일정을 만들 때 표시 되는 기본값은 평일에 제안 되는 값 이지만 필요에 따라 변경할 수 있습니다. 

일정을 만들거나 변경 하려면 다음을 수행 합니다.

1. **일정 탭에서** **일정 추가** 를 선택 합니다.

2. 일정 **이름** 필드에 일정의 이름을 입력 합니다.

3. 반복 **날짜** 필드에서 일정이 반복되는 요일을 선택합니다.

4. **램프업** 탭에서 다음 필드를 입력합니다.

    - **시작 시간의** 경우 드롭다운 메뉴에서 시간을 선택하여 최대 업무 시간에 대한 VM 준비를 시작합니다.

    - **부하 분산 알고리즘** 의 경우 너비 우선 알고리즘 **을** 선택하는 것이 좋습니다. 너비 우선 부하 분산은 액세스 시간을 빠르게 유지하기 위해 기존 VM에 사용자를 분산합니다.
        
        >[!NOTE]
        >여기서 선택하는 부하 분산 기본 설정은 원래 호스트 풀 설정에 대해 선택한 기본 설정을 재정의합니다.

    - **호스트의 최소 백분율** 에 이 단계에서 항상 유지하려는 세션 호스트의 백분율을 입력합니다. 입력한 백분율이 정수가 아닌 경우 가장 가까운 정수로 반올림됩니다. 예를 들어 7개의 세션 호스트가 있는 호스트 풀에서 최소 호스트 비율이 진입 시간 동안 **10%인** 경우 한 VM은 진입 시간 동안 항상 켜지고 자동 크기 조정 기능은 이 VM을 해제하지 않습니다. 
    
    - **용량 임계값** 에 대해 수행할 크기 조정 작업을 트리거할 사용 가능한 호스트 풀 용량의 비율을 입력합니다. 예를 들어 최대 세션 제한이 20인 호스트 풀의 2개 세션 호스트가 켜져 있는 경우 사용 가능한 호스트 풀 용량은 40입니다. 용량 임계값을 **75%로** 설정하고 세션 호스트에 30개 이상의 사용자 세션이 있는 경우 자동 크기 조정 기능은 세 번째 세션 호스트를 켭니다. 그러면 사용 가능한 호스트 풀 용량이 40에서 60으로 변경됩니다.

5. 최대 **시간** 탭에서 다음 필드를 입력합니다.

    - **시작 시간의** 경우 하루 중 사용률이 가장 높은 시작 시간을 입력합니다. 시간이 크기 조정 계획에 대해 지정한 동일한 표준 시간대에 있는지 확인합니다. 이 시간은 진입 단계에 대한 종료 시간이기도합니다.

    - **부하 분산의** 경우 너비 우선 부하 분산 또는 깊이 우선 부하 분산을 선택할 수 있습니다. 너비 우선 부하 분산은 호스트 풀에서 사용 가능한 모든 세션에 새 사용자 세션을 분산합니다. 깊이 우선 부하 분산은 아직 세션 제한에 도달하지 않은 연결 수가 가장 많은 사용 가능한 세션 호스트에 새 세션을 분산합니다. 부하 분산 유형에 대한 자세한 내용은 [Azure Virtual Desktop 부하 분산 방법 구성을 참조하세요.](configure-host-pool-load-balancing.md)

    >[!NOTE]
    >여기서는 용량 임계값을 변경할 수 없습니다. 대신 **램프 업에** 입력한 설정이 이 설정으로 전달됩니다.

    - **램프 다운의** 경우 **램프업과** 비슷한 필드에 값을 입력하지만, 이번에는 호스트 풀 사용량이 감소하는 경우에 해당합니다. 여기에는 다음 필드가 포함됩니다.

      - 시작 시간
      - 부하 분산 알고리즘
      - 호스트의 최소 백분율(%)
      - 용량 임계값(%)
      - 강제 로그오프 사용자

    >[!IMPORTANT]
    >자동 크기 조정 기능을 사용하도록 설정하여 사용자가 램프 다운 중에 로그아웃하도록 한 경우 이 기능은 종료할 사용자 세션 수가 가장 적은 세션 호스트를 선택합니다. 자동 크기 조정 기능은 세션 호스트를 드레인 모드로 만들고, 모든 활성 사용자 세션에 로그아웃될 것임을 알리는 알림을 보낸 다음, 지정된 대기 시간이 끝난 후 모든 사용자를 로그아웃합니다. 자동 크기 조정 기능은 모든 사용자 세션을 로그인한 후 VM의 할당을 해지합니다. 램프 다운 중에 강제 로그아웃을 사용하도록 설정하지 않은 경우 활성 또는 연결이 끊긴 세션이 없는 세션 호스트의 할당이 해제됩니다.

    - 마찬가지로, **익스피트 시간(끄기** 시간)은 **최대 시간()와** 동일한 방식으로 작동합니다.

      - 시작 시간- 진입 중단 기간의 끝이기도 한 시간입니다.
      - 부하 분산 알고리즘. 깊이 **우선을** 선택하여 각 VM의 세션에 따라 세션 호스트 수를 점진적으로 줄이는 것이 좋습니다.
      - 최대 시간과 마찬가지로 여기에서 용량 임계값을 구성할 수 없습니다. 대신 **램프 다운에** 입력한 값이 이월됩니다.

## <a name="assign-host-pools"></a>호스트 풀 할당

크기 조정 계획을 설정했으므로 이제 호스트 풀에 계획을 할당해야 합니다. 포함하려는 각 호스트 풀 옆에 있는 확인란을 선택합니다. 자동 크기 조정을 사용하도록 설정하지 않으려면 모든 확인란을 선택 취소합니다. 나중에 언제든지 이 설정으로 돌아가서 변경할 수 있습니다.

>[!NOTE]
>호스트 풀에 이미 할당된 크기 조정 계획을 만들거나 업데이트하면 변경 내용이 즉시 적용됩니다.

## <a name="add-tags"></a>태그 추가 

그런 다음 태그를 입력해야 합니다. 태그는 통합 청구에 대한 리소스를 분류하는 이름 및 값 쌍입니다. 여러 리소스 및 리소스 그룹에 동일한 태그를 적용할 수 있습니다. 리소스 태그 지정에 대한 자세한 내용은 [태그를 사용하여 Azure 리소스 구성을 참조하세요.](../azure-resource-manager/management/tag-resources.md)

>[!NOTE] 
>태그를 만든 후 다른 탭에서 리소스 설정을 변경하면 태그가 자동으로 업데이트됩니다.

완료되면 **검토 + 만들기** 탭으로 이동하고 **만들기를** 선택하여 호스트 풀을 배포합니다.

## <a name="next-steps"></a>다음 단계

크기 조정 계획을 만들었으므로 다음과 같은 몇 가지 작업을 수행할 수 있습니다.

- [새 호스트 풀과 기존 호스트 풀에 크기 조정 계획 할당](autoscale-new-existing-host-pool.md)
- [크기 조정 계획에 대한 진단 사용](autoscale-diagnostics.md)
