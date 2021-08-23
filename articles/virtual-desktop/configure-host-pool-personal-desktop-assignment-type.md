---
title: Azure Virtual Desktop 개인 데스크톱 할당 유형 - Azure
description: Azure Virtual Desktop 개인 데스크톱 호스트 풀에 대한 자동 또는 직접 할당을 구성하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 07/09/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: e53bcbca2e40ef9a9587e0b571f2798132c2a829
ms.sourcegitcommit: b044915306a6275c2211f143aa2daf9299d0c574
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113033590"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>개인 데스크톱 호스트 풀 할당 유형 구성

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Azure Virtual Desktop 개체를 통해 Azure Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Azure Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/configure-host-pool-personal-desktop-assignment-type-2019.md)를 참조하세요.

개인 데스크톱 호스트 풀의 할당 유형을 구성하여 필요에 맞게 Azure Virtual Desktop 환경을 조정할 수 있습니다. 이 항목에서는 사용자에 대한 자동 또는 직접 할당을 구성하는 방법을 보여 줍니다.

>[!NOTE]
> 이 문서의 지침은 풀링된 호스트 풀이 아닌 개인 데스크톱 호스트 풀에만 적용됩니다. 풀링된 호스트 풀의 사용자는 특정 세션 호스트에 할당되지 않기 때문입니다.

## <a name="prerequisites"></a>필수 조건

이 문서에서는 Azure Virtual Desktop PowerShell 모듈을 이미 다운로드하여 설치했다고 가정합니다. 그렇지 않은 경우 [PowerShell 모듈 설정](powershell-module.md)의 지침을 따르세요.

## <a name="configure-automatic-assignment"></a>자동 할당 구성

자동 할당은 Azure Virtual Desktop 환경에서 만든 새 개인 데스크톱 호스트 풀의 기본 할당 유형입니다. 사용자를 자동으로 할당하는 데는 특정 세션 호스트가 필요하지 않습니다.

사용자를 자동으로 할당하려면 먼저 사용자가 피드에서 바탕 화면을 볼 수 있도록 개인 데스크톱 호스트 풀에 해당 사용자를 할당합니다. 할당된 사용자가 피드에서 바탕 화면을 시작하면 호스트 풀에 아직 연결되지 않은 경우 사용 가능한 세션 호스트를 클레임하게 됩니다. 그러면 할당 프로세스가 완료됩니다.

사용자를 VM에 자동으로 할당하도록 호스트 풀을 구성하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Automatic
```

개인 데스크톱 호스트 풀에 사용자를 할당하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="configure-direct-assignment"></a>직접 할당 구성

자동 할당과 달리 직접 할당을 사용하는 경우 먼저 개인 데스크톱 호스트 풀과 특정 세션 호스트 모두에 사용자를 할당해야 사용자가 개인 데스크톱에 연결할 수 있습니다. 사용자가 세션 호스트 할당 없이 호스트 풀에만 할당된 경우 리소스에 액세스할 수 없습니다.

사용자를 세션 호스트에 직접 할당해야 하도록 호스트 풀을 구성하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Direct
```

개인 데스크톱 호스트 풀에 사용자를 할당하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

사용자를 특정 세션 호스트에 할당하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
Update-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -AssignedUser <userupn>
```

Azure Portal에서 세션 호스트에 사용자를 직접 할당하려면 다음을 수행합니다.

1. <https://portal.azure.com> 에서 Azure Portal에 로그인합니다.
2. 검색 창에 **Azure Virtual Desktop** 을 입력합니다.
3. **서비스** 에서 **Azure Virtual Desktop** 을 선택합니다.
4. Azure Virtual Desktop 페이지에서 창의 왼쪽에 있는 메뉴로 이동하여 **호스트 풀** 을 선택합니다.
5. 업데이트할 풀의 이름을 선택합니다.
6. 다음으로, 창 왼쪽에 있는 메뉴로 이동하여 **애플리케이션 그룹** 을 선택합니다.
7. 편집할 데스크톱 앱 그룹의 이름을 선택한 다음, 창 왼쪽에 있는 메뉴에서 **할당** 을 선택합니다.
8. **+ 추가** 를 선택한 다음, 이 데스크톱 앱 그룹을 게시할 사용자 또는 사용자 그룹을 선택합니다.
9. 알림 표시줄에서 **VM 할당** 을 선택하여 세션 호스트를 사용자에게 할당합니다.
10. 사용자에게 할당할 세션 호스트를 선택한 다음, **할당** 을 선택합니다.
11. 사용 가능한 사용자 목록에서 세션 호스트를 할당할 사용자를 선택합니다.
12. 완료되면 **선택** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

개인 데스크톱 할당 유형을 구성했으므로 이제 Azure Virtual Desktop 클라이언트에 로그인하여 사용자 세션의 일부로 테스트할 수 있습니다. 다음 두 방법 문서는 선택한 클라이언트를 사용하여 세션에 연결하는 방법을 보여 줍니다.

- [Windows Desktop 클라이언트를 사용하여 연결](./user-documentation/connect-windows-7-10.md)
- [웹 클라이언트를 사용하여 연결](./user-documentation/connect-web.md)
- [Android 클라이언트와 연결](./user-documentation/connect-android.md)
- [iOS 클라이언트와 연결](./user-documentation/connect-ios.md)
- [macOS 클라이언트와 연결](./user-documentation/connect-macos.md)
