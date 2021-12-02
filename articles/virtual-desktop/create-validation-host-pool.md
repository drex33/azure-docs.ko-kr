---
title: Azure Virtual Desktop 호스트 풀 서비스 업데이트 - Azure
description: 프로덕션에 업데이트를 출시하기 전에 서비스 업데이트를 모니터링하는 유효성 검사 호스트 풀을 만드는 방법입니다.
author: Heidilohr
ms.topic: tutorial
ms.date: 12/01/2021
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: ba0c2989912623c1e2ff7a91030d6dc00590395c
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133440242"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>자습서: 서비스 업데이트의 유효성을 검사하기 위한 호스트 풀 만들기

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Azure Virtual Desktop 개체를 통해 Azure Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Azure Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/create-validation-host-pool-2019.md)를 참조하세요.

호스트 풀은 Azure Virtual Desktop 환경 내에서 하나 이상의 동일한 가상 머신 컬렉션입니다. 서비스 업데이트가 먼저 적용되는 유효성 검사 호스트 풀을 만드는 것이 가장 좋습니다. 유효성 검사 호스트 풀을 사용하면 서비스에서 표준 또는 비유효성 검사 환경에 서비스 업데이트를 적용하기 전에 서비스 업데이트를 모니터링할 수 있습니다. 유효성 검사 호스트 풀이 없으면 오류를 유발하는 변경 내용을 감지하지 못하여 표준 환경의 사용자에게 가동 중지 시간이 발생할 수 있습니다.

앱에서 최신 업데이트가 문제 없이 작동되려면 유효성 검사 호스트 풀이 비유효성 검사 환경의 호스트 풀과 최대한 유사해야 합니다. 사용자는 표준 호스트 풀에 연결하는 것만큼 유효성 검사 호스트 풀에도 자주 연결해야 합니다. 호스트 풀에 대한 테스트를 자동화한 경우 유효성 검사 호스트 풀에 자동화된 테스트를 포함해야 합니다.

[진단 기능](./troubleshoot-set-up-overview.md) 또는 [Azure Virtual Desktop 문제 해결 문서](troubleshoot-set-up-overview.md) 중 하나를 사용하여 유효성 검사 호스트 풀의 문제를 디버그할 수 있습니다.

>[!NOTE]
> 모든 후속 업데이트를 테스트하기 위해 유효성 검사 호스트 풀을 그대로 두는 것이 좋습니다.

## <a name="create-your-host-pool"></a>호스트 풀 만들기

기존 풀링된 호스트 풀 또는 개인 호스트 풀을 유효성 검사 호스트 풀로 구성할 수 있습니다. 다음 문서의 지침에 따라 유효성 검사에 사용할 새 호스트 풀을 만들 수도 있습니다.
- [자습서: Azure Marketplace 또는 Azure CLI를 사용하여 호스트 풀 만들기](create-host-pools-azure-marketplace.md)
- [PowerShell 또는 Azure CLI를 사용하여 호스트 풀 만들기](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>호스트 풀을 유효성 검사 호스트 풀로 정의

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal을 사용하여 유효성 검사 호스트 풀을 구성하려면 다음을 수행합니다.

1. [https://portal.azure.com](<https://portal.azure.com> ) 에서 Azure Portal에 로그인합니다.
2. **Azure Virtual Desktop** 을 검색하여 선택합니다.
3. Azure Virtual Desktop 페이지에서 **호스트 풀** 을 선택합니다.
4. 편집하려는 풀의 이름을 선택합니다.
5. **속성** 을 선택합니다.
6. 유효성 검사 환경 필드에서 **예** 를 선택하여 유효성 검사 환경을 사용하도록 설정합니다.
7. **저장** 을 선택하여 새 설정을 적용합니다.

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

아직 수행하지 않은 경우 [Azure Virtual Desktop PowerShell 모듈 설정](powershell-module.md)의 지침에 따라 PowerShell 모듈을 설정하고 Azure에 로그인합니다.

다음 PowerShell cmdlet을 실행하여 새 호스트 풀을 유효성 검사 호스트 풀로 정의합니다. 괄호 안의 값을 세션에 관련된 값으로 바꿉니다.

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -ValidationEnvironment:$true
```

다음 PowerShell cmdlet을 실행하여 유효성 검사 속성이 설정되어 있는지 확인합니다. 괄호 안의 값을 세션에 관련된 값으로 바꿉니다.

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | Format-List
```

cmdlet의 결과는 다음 출력과 비슷해야 합니다.

```powershell
    HostPoolName        : hostpoolname
    FriendlyName        :
    Description         :
    Persistent          : False
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnvironment : True
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

아직 수행하지 않은 경우 Azure CLI에 대한 환경을 준비하고 로그인합니다.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

새 호스트 풀을 유효성 검사 호스트 풀로 정의하려면 [az desktopvirtualization hostpool update](/cli/azure/desktopvirtualization#az_desktopvirtualization_hostpool_update) 명령을 사용합니다.

```azurecli
az desktopvirtualization hostpool update --name "MyHostPool" \
    --resource-group "MyResourceGroup" \
    --validation-environment true
```

다음 명령을 사용하여 유효성 검사 속성이 설정되어 있는지 확인합니다.

```azurecli
az desktopvirtualization hostpool show --name "MyHostPool" \
    --resource-group "MyResourceGroup" 
```
---

## <a name="update-schedule"></a>업데이트 일정

매월 서비스 업데이트가 수행됩니다. 주요 이슈가 있는 경우 중요 업데이트가 좀 더 자주 제공됩니다.

서비스 업데이트가 있는 경우 환경의 유효성을 검사하려면 매일 로그인하는 몇 명의 사용자가 있어야 합니다. [TechCommunity 사이트](https://techcommunity.microsoft.com/t5/forums/searchpage/tab/message?filter=location&q=wvdupdate&location=forum-board:WindowsVirtualDesktop&sort_by=-topicPostDate&collapse_discussion=true)를 정기적으로 방문하여 서비스 업데이트에 대한 최신 정보를 제공하는 WVDUPdate에 대한 게시물을 따르는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

이제 유효성 검사 호스트 풀을 만들었으므로 Azure Service Health를 사용하여 Azure Virtual Desktop 배포를 모니터링하는 방법을 배울 수 있습니다.

> [!div class="nextstepaction"]
> [서비스 경고 설정](./set-up-service-alerts.md)