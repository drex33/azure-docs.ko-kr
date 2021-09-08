---
title: Azure Virtual Desktop 호스트 풀 삭제 - Azure
description: Azure Virtual Desktop에서 호스트 풀을 삭제하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 07/23/2021
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: f0ea0e89ef70983f5c74b37365305cfab3e984bc
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123098508"
---
# <a name="delete-a-host-pool"></a>호스트 풀 삭제

Azure Virtual Desktop에서 만든 모든 호스트 풀은 세션 호스트 및 앱 그룹에 연결됩니다. 호스트 풀을 삭제하려면 연결된 앱 그룹 및 세션 호스트를 삭제해야 합니다. 앱 그룹을 삭제하는 것은 매우 간단하지만 세션 호스트를 삭제하는 것은 더 복잡합니다. 세션 호스트를 삭제하는 경우 활성 사용자 세션이 없는지 확인해야 합니다. 사용자가 데이터를 잃지 않도록 세션 호스트의 모든 사용자 세션을 로그오프해야 합니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 호스트 풀을 삭제하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **Azure Virtual Desktop** 을 검색하여 선택합니다.

3. 페이지 왼쪽에 있는 메뉴에서 **호스트 풀** 을 선택한 다음, 삭제할 호스트 풀의 이름을 선택합니다.

4. 페이지 왼쪽에 있는 메뉴에서 **애플리케이션 그룹** 을 선택합니다.

5. 삭제할 호스트 풀의 모든 애플리케이션 그룹을 선택한 다음, **제거** 를 선택합니다.

6. 앱 그룹을 제거한 후 페이지 왼쪽에 있는 메뉴로 이동하여 **개요** 를 선택합니다.

7. **제거** 를 선택합니다.

8. 삭제할 호스트 풀에 세션 호스트가 있는 경우 계속 진행할 수 있도록 권한을 요청하는 메시지가 표시됩니다. **Yes** 를 선택합니다.

9. 이제 Azure Portal에서 모든 세션 호스트를 제거하고 호스트 풀을 삭제합니다. 세션 호스트와 관련된 VM은 삭제되지 않으며 구독 상태로 유지됩니다.

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 호스트 풀을 삭제하려면 먼저 호스트 풀의 모든 앱 그룹을 삭제해야 합니다. 모든 앱 그룹을 삭제하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
Remove-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
```

그런 다음, 이 cmdlet을 실행하여 호스트 풀을 삭제합니다.

```powershell
Remove-AzWvdHostPool -Name <hostpoolname> -ResourceGroupName <resourcegroupname> -Force:$true
```

이 cmdlet은 호스트 풀의 세션 호스트에서 기존 사용자 세션을 모두 제거합니다. 또한 호스트 풀에서 세션 호스트를 등록 취소합니다. 모든 관련 VM(가상 머신)은 여전히 구독 내에 존재합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 호스트 풀을 삭제하려면 먼저 호스트 풀의 모든 앱 그룹을 삭제해야 합니다. 

모든 앱 그룹을 삭제하려면 [az desktopvirtualization applicationgroup delete](/cli/azure/desktopvirtualization/applicationgroup#az_desktopvirtualization_applicationgroup_delete) 명령을 사용합니다.

```azurecli
az desktopvirtualization applicationgroup delete --name "MyApplicationGroup" --resource-group "MyResourceGroup"
```

그런 다음 [az desktopvirtualization hostpool delete](/cli/azure/desktopvirtualization/hostpool#az_desktopvirtualization_hostpool_delete) 명령을 사용하여 호스트 풀을 삭제합니다.

```azurecli
az desktopvirtualization hostpool delete --force true --name "MyHostPool" --resource-group "MyResourceGroup"
```

이 삭제는 호스트 풀의 세션 호스트에서 기존 사용자 세션을 모두 제거합니다. 또한 호스트 풀에서 세션 호스트를 등록 취소합니다. 모든 관련 VM(가상 머신)은 여전히 구독 내에 존재합니다.

---

## <a name="next-steps"></a>다음 단계

호스트 풀을 만드는 방법을 알아보려면 다음 문서를 확인하세요.

- [Azure Portal로 호스트 풀 만들기](create-host-pools-azure-marketplace.md)
- [PowerShell을 사용한 호스트 풀 만들기](create-host-pools-powershell.md)

호스트 풀 설정을 구성하는 방법을 알아보려면 다음 문서를 확인하세요.

- [호스트 풀의 원격 데스크톱 프로토콜 속성 사용자 지정](customize-rdp-properties.md)
- [Azure Virtual Desktop 부하 분산 방법 구성](configure-host-pool-load-balancing.md)
- [개인 데스크톱 호스트 풀 할당 유형 구성](configure-host-pool-personal-desktop-assignment-type.md)
