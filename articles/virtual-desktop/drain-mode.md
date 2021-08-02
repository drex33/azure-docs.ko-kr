---
title: Azure Virtual Desktop 드레이닝 모드 설정 - Azure
description: Azure Virtual Desktop에서 드레이닝 모드를 구성 및 사용하는 방법을 설명합니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/14/2021
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: 2ff90e2800da857dd5c82cd3b9402fa2454575ac
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111757844"
---
# <a name="set-drain-mode"></a>드레이닝 모드 설정

드레이닝 모드는 사용자 세션을 방해하지 않고 패치를 적용하고 유지 관리를 수행하려는 경우 세션 호스트를 격리합니다. 격리되면 세션 호스트는 새 사용자 세션을 허용하지 않습니다. 모든 새 연결은 사용 가능한 다음 세션 호스트로 리디렉션됩니다. 세션 호스트의 기존 연결은 사용자가 로그아웃하거나 관리자가 세션을 종료할 때까지 계속 작동합니다. 세션 호스트가 드레이닝 모드일 때 관리자는 Azure Virtual Desktop 서비스를 거치지 않고도 서버에 원격으로 연결할 수 있습니다. 이 설정을 풀링된 데스크톱과 개인 데스크톱 모두에 적용할 수 있습니다.

## <a name="set-drain-mode-using-the-azure-portal"></a>Azure Portal을 사용하여 드레이닝 모드 설정

Azure Portal에서 드레이닝 모드를 켜려면:

1. Azure Portal을 열고 격리할 호스트 풀로 이동합니다.

2. 탐색 메뉴에서 **세션 호스트** 를 선택합니다.

3. 그런 다음 드레이닝 모드를 켜려는 호스트를 선택한 다음 **드레이닝 모드 켜기** 를 선택합니다.

4. 드레이닝 모드를 끄려면 드레이닝 모드가 켜져 있는 호스트 풀을 선택한 다음 **드레이닝 모드 끄기** 를 선택합니다.

## <a name="set-drain-mode-using-powershell"></a>PowerShell을 사용하여 드레이닝 모드 설정

[Update-AzWvdSessionhost](/powershell/module/az.desktopvirtualization/update-azwvdsessionhost?view=azps-5.8.0&preserve-view=true) 명령의 일부인 *AllowNewSessions* 매개 변수를 사용하여 PowerShell에서 드레이닝 모드를 설정할 수 있습니다.

드레이닝 모드를 사용하도록 설정하려면 이 cmdlet을 실행합니다.

```powershell
Update-AzWvdSessionHost -ResourceGroupName <resourceGroupName> -HostPoolName <hostpoolname> -Name <hostname> -AllowNewSession:$False
```

드레이닝 모드를 사용하지 않도록 설정하려면 이 cmdlet을 실행합니다.

```powershell
Update-AzWvdSessionHost -ResourceGroupName <resourceGroupName> -HostPoolName <hostpoolname> -Name <hostname> -AllowNewSession:$True
```

>[!IMPORTANT]
>설정을 적용하는 모든 세션 호스트에 대해 이 명령을 실행해야 합니다.

## <a name="next-steps"></a>다음 단계

Azure Virtual Desktop의 Azure Portal에 대해 자세히 알아보려면 [자습서](create-host-pools-azure-marketplace.md)를 확인하세요. 기본 사항에 대해 잘 알고 있는 경우 [MSIX 앱 연결](app-attach-azure-portal.md) 및 [Azure Advisor](azure-advisor.md)와 같이 Azure Portal에서 사용할 수 있는 몇 가지 다른 기능을 확인하세요.

PowerShell 메서드를 사용하는 경우 모듈에서 수행할 수 있는 작업을 확인하려면 [ Virtual Desktop용 PowerShell 모듈 설정](powershell-module.md) 및 [PowerShell 참조](/powershell/module/az.desktopvirtualization/)를 설정합니다.
