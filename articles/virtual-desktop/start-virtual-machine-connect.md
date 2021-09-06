---
title: 가상 머신 연결 시작 - Azure
description: 연결 시 가상 머신 시작 기능을 구성하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 08/06/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 301a2b0626b6dd40f90a8b693e3284c12d948fa1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528367"
---
# <a name="start-virtual-machine-on-connect"></a>연결 시 Virtual Machine 시작

연결 시 VM(가상 머신) 시작 기능을 사용하면 최종 사용자가 필요할 때만 VM을 켤 수 있으므로 비용을 절감할 수 있습니다. 그런 다음 필요하지 않을 때 VM을 끌 수 있습니다.

>[!NOTE]
>Azure Virtual Desktop(클래식)은 이 기능을 지원하지 않습니다.

## <a name="requirements-and-limitations"></a>요구 사항 및 제한 사항:

PowerShell 및 Azure Portal을 사용하여 개인 또는 풀링된 호스트 풀에 대해 연결 시 VM 시작 기능을 사용하도록 설정할 수 있습니다.

다음 원격 데스크톱 클라이언트는 연결 시 VM 시작 기능을 지원합니다.

- [웹 클라이언트](./user-documentation/connect-web.md?toc=/azure/virtual-desktop/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [Windows 클라이언트(버전 1.2.2061 이상)](./user-documentation/connect-windows-7-10.md?toc=/azure/virtual-desktop/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [Android 클라이언트(버전 10.0.10 이상)](./user-documentation/connect-android.md?toc=/azure/virtual-desktop/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [macOS 클라이언트(버전 10.6.4 이상)](./user-documentation/connect-macos.md?toc=/azure/virtual-desktop/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [iOS 클라이언트(버전 10.2.5 이상)](./user-documentation/connect-ios.md?toc=/azure/virtual-desktop/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [Microsoft Store 클라이언트(버전 10.2.2005.0 이상)](./user-documentation/connect-microsoft-store.md?toc=/azure/virtual-desktop/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [씬 클라이언트 지원](./user-documentation/linux-overview.md?toc=/azure/virtual-desktop/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)에 나열된 씬 클라이언트

## <a name="create-a-custom-role-for-start-vm-on-connect"></a>연결 시 VM 시작에 대한 사용자 지정 역할 만들기

연결 시 VM 시작 기능을 구성하려면 먼저 VM에 사용자 지정 RBAC(역할 기반 액세스 제어) 역할을 할당해야 합니다. 이 역할을 통해 Azure Virtual Desktop에서 구독의 VM을 관리할 수 있습니다. 또한 이 역할을 사용하여 VM을 켜고, 상태를 확인하고, 진단 정보를 보고할 수 있습니다. 각 역할의 기능에 대해 자세히 알아보려면 [Azure 사용자 지정 역할](../role-based-access-control/custom-roles.md)을 참조하세요.

### <a name="use-the-azure-portal"></a>Azure Portal 사용

Azure Portal을 사용하여 연결 시 VM 시작에 대한 사용자 지정 역할을 할당하려면 다음을 수행합니다.

1. Azure Portal을 열고 **구독** 으로 이동합니다.

2. **액세스 제어(IAM)** 로 이동하고 **사용자 지정 역할 추가** 를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![액세스 제어(IAM)에 있는 추가 단추의 드롭다운 메뉴 스크린샷. “사용자 지정 역할 추가”가 빨간색으로 강조 표시되어 있음](media/add-custom-role.png)

3. 다음으로, 사용자 지정 역할의 이름을 지정하고 설명을 추가합니다. 이름은 “start VM on connect”로 지정하는 것이 좋습니다.

4. **사용 권한** 탭에서 역할을 할당하는 구독에 다음 권한을 추가합니다. 
 
   - Microsoft.Compute/virtualMachines/start/action
   - Microsoft.Compute/virtualMachines/read

5. 작업을 마쳤으면 **확인** 을 선택합니다.

그런 다음, 역할을 할당하여 Azure Virtual Desktop에 대한 액세스 권한을 부여해야 합니다.

사용자 지정 역할을 할당하려면 다음을 수행합니다.

1. **액세스 제어(IAM) 탭** 에서 **역할 할당 추가** 를 선택합니다.

2. 방금 만든 역할을 선택합니다.

3. 검색 창에 **Windows Virtual Desktop** 을 입력하고 선택합니다(곧 "Azure Virtual Desktop"으로 업데이트됨).

      >[!NOTE]
      >Azure Virtual Desktop(클래식)을 배포한 경우 두 개의 앱이 표시될 수 있습니다. 표시되는 두 앱에 역할을 할당합니다.
      >
      > [!div class="mx-imgBorder"]
      > ![액세스 제어(IAM) 탭의 스크린샷. 검색 창에서 Azure Virtual Desktop과 Azure Virtual Desktop(클래식)이 둘 다 빨간색으로 강조 표시되어 있음](media/add-role-assignment.png)

### <a name="create-a-custom-role-with-a-json-file-template"></a>JSON 파일 템플릿을 사용하여 사용자 지정 역할 만들기

JSON 파일을 사용하여 사용자 지정 역할을 만드는 경우 다음 예제에서는 사용할 수 있는 기본 템플릿을 보여 줍니다. *AssignableScopes* 의 구독 ID 값을 역할을 할당할 구독 ID 값을 바꿔야 합니다.

```json
{
  "Name": "Start VM on connect (Custom)",
  "IsCustom": true,
  "Description": "Start VM on connect with AVD (Custom)",
  "Actions": [
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/read"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

JSON 템플릿을 사용하려면 JSON 파일을 저장하고 관련 구독 정보를 *할당 가능한 범위* 에 추가한 다음, PowerShell에서 다음 cmdlet을 실행합니다.

```powershell
New-AzRoleDefinition -InputFile "C:\temp\filename"
```

사용자 지정 역할을 만드는 방법에 대한 자세한 내용은 [Azure PowerShell을 사용하여 Azure 사용자 지정 역할 만들기 또는 업데이트](../role-based-access-control/custom-roles-powershell.md#create-a-custom-role-with-json-template)를 참조하세요.

## <a name="configure-the-start-vm-on-connect-feature"></a>연결 시 VM 시작 기능 구성

구독에 역할을 할당했으므로 이제 연결 시 VM 시작 기능을 구성할 수 있습니다.

### <a name="deployment-considerations"></a>배포 고려 사항 

연결 시 VM 시작은 호스트 풀 설정입니다. 이 기능을 사용할 사용자 그룹을 선택하려는 경우 추가하려는 사용자에게 필요한 역할만 할당해야 합니다.

개인 데스크톱의 경우 이 기능은 서비스가 이미 할당했거나 사용자에게 할당할 기존 VM만 켭니다. 풀링된 호스트 풀 시나리오에서 서비스는 VM이 켜져 있지 않을 때만 VM을 켭니다. 이 기능은 첫 번째 VM이 세션 제한에 도달할 때만 추가 VM을 켭니다.

>[!IMPORTANT]
> 이 기능은 기존 호스트 풀에서만 구성할 수 있습니다. 새 호스트 풀을 만들 때는 이 기능을 사용할 수 없습니다.

### <a name="use-the-azure-portal"></a>Azure Portal 사용

Azure Portal을 사용하여 연결 시 VM 시작을 구성하려면 다음을 수행합니다.

1. 브라우저를 열고 [Azure Portal](https://portal.azure.com)로 이동합니다.

2. Azure Portal에서 **Azure Virtual Desktop** 으로 이동합니다.

3. **호스트 풀** 을 선택한 다음 설정을 사용하도록 설정할 호스트 풀로 이동합니다.

4. 호스트 풀에서 **속성** 을 선택합니다. **연결 시 VM 시작** 에서 **예** 를 선택한 다음, **저장** 을 선택하여 설정을 즉시 적용합니다.

    > [!div class="mx-imgBorder"]
    > ![속성 창의 스크린샷. 연결 시 VM 시작 옵션이 빨간색으로 강조 표시되어 있음](media/properties-start-vm-on-connect.png)

### <a name="use-powershell"></a>PowerShell 사용

PowerShell을 사용하여 이 설정을 구성하려면 구성하려는 리소스 그룹 및 호스트 풀의 이름이 있어야 합니다. 또한 [Azure PowerShell 모듈(버전 2.1.0 이상)](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.1.0)을 설치해야 합니다.

PowerShell을 사용하여 연결 시 VM 시작을 구성하려면 다음을 수행합니다.

1. PowerShell 명령 창을 엽니다.

2. 다음 cmdlet을 실행하여 연결 시 VM 시작을 사용하도록 설정합니다.

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$true
    ```

3. 다음 cmdlet을 실행하여 연결 시 VM 시작을 사용하지 않도록 설정합니다.

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$false
    ```

## <a name="user-experience"></a>사용자 환경

일반적인 세션에서는, 물리적 컴퓨터를 켜는 것과 매우 유사하게 VM을 다시 켜려면 시간이 필요하기 때문에 사용자가 할당 취소된 VM에 연결하는 데 걸리는 시간이 늘어납니다. 원격 데스크톱 클라이언트에는 표시기가 있으며, 이를 통해 사용자는 연결하는 동안 PC의 전원이 켜져 있음을 알 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

기능에서 문제가 발생하면 Azure Virtual Desktop [진단 기능](diagnostics-log-analytics.md)을 사용하여 문제를 확인하는 것이 좋습니다. 오류 메시지가 표시되면 메시지 내용을 자세히 확인하고 오류 이름을 참조용으로 다른 위치에 복사해 두어야 합니다.

[Azure Monitor for Azure Virtual Desktop](azure-monitor.md)을 사용하여 문제 해결 방법에 대한 제안을 받을 수도 있습니다.

VM이 켜지지 않으면 다른 작업을 수행하기 전에 켜려고 했던 VM의 상태를 확인해야 합니다.

## <a name="next-steps"></a>다음 단계

문제 해결 설명서 또는 진단 기능에서 해결할 수 없는 문제가 발생하는 경우 [연결 시 VM 시작 FAQ](start-virtual-machine-connect-faq.md)를 확인하세요.
