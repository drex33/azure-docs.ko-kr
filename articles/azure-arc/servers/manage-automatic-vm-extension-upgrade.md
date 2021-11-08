---
title: Azure Arc 사용 서버에 대 한 자동 확장 업그레이드 (미리 보기)
description: Azure Arc 사용 서버에 대해 자동 확장 업그레이드 (미리 보기)를 사용 하도록 설정 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 11/06/2021
ms.openlocfilehash: aea00dbe8609d0a8481d9ca742c12e6d0215f189
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132028925"
---
# <a name="automatic-extension-upgrade-preview-for-azure-arc-enabled-servers"></a>Azure Arc 사용 서버에 대 한 자동 확장 업그레이드 (미리 보기)

지원 되는 VM 확장이 설치 된 Azure Arc 사용 서버에 대해 자동 확장 업그레이드 (미리 보기)를 사용할 수 있습니다. 컴퓨터에서 자동 확장 업그레이드 (미리 보기)를 사용 하도록 설정 하면 확장 게시자가 해당 확장에 대 한 새 버전을 릴리스할 때마다 확장이 자동으로 업그레이드 됩니다.

 자동 확장 업그레이드의 기능은 다음과 같습니다.

- 언제 든 지 자동 업그레이드를 옵트인 (opt in) 할 수 있습니다.
- 지원되는 각 확장은 개별적으로 등록되며, 자동으로 업그레이드할 확장을 선택할 수 있습니다.
- 모든 퍼블릭 클라우드 지역에서 지원됩니다.

> [!NOTE]
> 이 릴리스에서는 자동 확장 업그레이드를 구성 하는 데 Azure CLI만 지원 됩니다.

## <a name="how-does-automatic-extension-upgrade-work"></a>자동 확장 업그레이드 작동 방식

확장 업그레이드 프로세스는 Azure Arc 사용 서버에서 지 원하는 기존 Azure VM 확장 버전을 확장 게시자가 게시 했을 때 동일한 확장의 새 버전으로 대체 합니다.

실패한 확장 업데이트는 자동으로 다시 시도됩니다. 재시도는 사용자 개입 없이 며칠마다 자동으로 실행됩니다.

### <a name="availability-first-updates"></a>가용성 우선 업데이트

플랫폼 오케스트레이션 업데이트에 대한 가용성 우선 모델은 Azure의 가용성 구성이 여러 가용성 수준에서 적용되게 합니다.

업데이트를 수행 하는 Arc 사용 서버 그룹의 경우 Azure 플랫폼은 [Automation 확장 업그레이드](../../virtual-machines/automatic-extension-upgrade.md#availability-first-updates)에 설명 된 모델을 따라 업데이트를 오케스트레이션 합니다. 그러나 Arc 사용 서버와 Azure Vm 간에는 몇 가지 주목할 만한 차이점이 있습니다.

**지역 간:**

- 지리적으로 페어링된 지역이 적용 되지 않습니다.

**지역 내에서:**

- 가용성 영역 적용 되지 않습니다.
- 구독에서 Arc 사용 서버에 등록 된 모든 컴퓨터에 대 한 동시 업데이트를 방지 하기 위해 컴퓨터는 최상의 노력을 기준으로 일괄 처리 됩니다.  

## <a name="supported-extensions"></a>지원되는 확장

자동 확장 업그레이드 (미리 보기)는 다음과 같은 확장을 지원 합니다. 이러한 확장은 주기적으로 추가 됩니다.

- Azure Monitor 에이전트-Linux 및 Windows
- Azure Security agent-Linux 및 Windows
- 종속성 에이전트 – Linux 및 Windows
- Key Vault 확장-Linux만
- Log Analytics 에이전트 (OMS 에이전트)-Linux만

## <a name="enabling-automatic-extension-upgrade-preview"></a>자동 확장 업그레이드 사용 (미리 보기)

확장에 대해 자동 확장 업그레이드 (미리 보기)를 사용 하도록 설정 하려면 속성이 `enable-auto-upgrade` 로 설정 되 `true` 고 모든 확장 정의에 개별적으로 추가 되도록 해야 합니다.

,, 및 매개 변수와 함께 [az connectedmachine extension](/cli/azure/connectedmachine/extension) cmdlet을 사용 `--name` `--machine-name` `--enable-auto-upgrade` `--resource-group` 합니다.

```azurecli
az connectedmachine extension update \
    --resource-group resourceGroupName \
    --machine-name machineName \
    --name DependencyAgentLinux \
    --enable-auto-upgrade true
```

Arc 사용 서버에서 모든 확장에 대 한 자동 확장 업그레이드 (미리 보기)의 상태를 확인 하려면 다음 명령을 실행 합니다.

```azurecli
az connectedmachine extension list --resource-group resourceGroupName --machine-name machineName --query "[].{Name:name, AutoUpgrade:properties.enableAutoUpgrade}" --output table
```

## <a name="extension-upgrades-with-multiple-extensions"></a>여러 확장을 이용하는 확장 업그레이드

Arc 사용 서버를 통해 관리 되는 컴퓨터는 자동 확장 업그레이드를 사용 하는 여러 확장을 사용할 수 있습니다. 자동 확장 업그레이드를 사용 하도록 설정 하지 않은 경우에도 동일한 컴퓨터에 다른 확장이 있을 수 있습니다.

컴퓨터에 대해 여러 확장 업그레이드를 사용할 수 있는 경우 업그레이드는 함께 일괄 처리 될 수 있지만 각 확장 업그레이드는 컴퓨터에 개별적으로 적용 됩니다. 한 확장의 실패가 업그레이드 될 다른 확장에 영향을 주지 않습니다. 예를 들어 확장 두 개의 업그레이드가 예약된 후 첫 번째 확장 업그레이드가 실패하더라도, 두 번째 확장은 계속 업그레이드됩니다.

## <a name="disable-automatic-extension-upgrade"></a>자동 확장 업그레이드 사용 안 함

확장에 대 한 자동 확장 업그레이드 (미리 보기)를 사용 하지 않도록 설정 하려면 속성이 `enable-auto-upgrade` 로 설정 되 `false` 고 모든 확장 정의에 개별적으로 추가 되도록 해야 합니다.

### <a name="using-the-azure-cli"></a>Azure CLI 사용

,, 및 매개 변수와 함께 [az connectedmachine extension ](/cli/azure/connectedmachine/extension) cmdlet을 사용 `--name` `--machine-name` `--enable-auto-upgrade` `--resource-group` 합니다.

```azurecli
az connectedmachine extension update \
    --resource-group resourceGroupName \
    --machine-name machineName \
    --name DependencyAgentLinux \
    --enable-auto-upgrade false
```

## <a name="next-steps"></a>다음 단계

- [Azure CLI](manage-vm-extensions-cli.md), [PowerShell](manage-vm-extensions-powershell.md) 또는 [Azure Resource Manager 템플릿](manage-vm-extensions-template.md)을 사용하여 VM 확장을 배포, 관리 및 제거할 수 있습니다.

- 문제 해결 정보는 [VM 확장 문제 해결 안내](troubleshoot-vm-extensions.md)에서 찾을 수 있습니다.
