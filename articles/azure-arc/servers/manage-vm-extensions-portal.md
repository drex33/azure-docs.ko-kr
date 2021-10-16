---
title: Azure Portal에서 VM 확장 사용
description: 이 문서에서는 Azure Portal을 통해 하이브리드 클라우드 환경에서 실행되는 Azure Arc 지원 서버에 가상 머신 확장을 배포하는 방법을 설명합니다.
ms.date: 10/15/2021
ms.topic: conceptual
ms.openlocfilehash: d6ecbbc57237192f68b9949ab960fb36cda64c81
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130069599"
---
# <a name="enable-azure-vm-extensions-from-the-azure-portal"></a>Azure Portal에서 Azure VM 확장을 사용하도록 설정

이 문서에서는 Azure Portal을 통해 Azure Arc 지원 서버에서 지원하는 Azure VM 확장을 Linux 또는 Windows 하이브리드 머신에 배포, 업데이트, 제거하는 방법을 보여 줍니다.

> [!NOTE]
> Key Vault VM 확장은 Azure Portal에서의 배포를 지원하지 않고 Azure CLI, Azure PowerShell 또는 Azure Resource Manager 템플릿을 사용한 배포만 지원합니다.

> [!NOTE]
> Azure Arc 지원 서버는 Azure 가상 머신에 대한 VM 확장의 배포와 관리를 지원하지 않습니다. Azure VM에 관해서는 다음 [VM 확장 개요](../../virtual-machines/extensions/overview.md) 문서를 참조하세요.

## <a name="enable-extensions"></a>확장 사용

Azure Portal를 통해 Azure Arc 사용 서버 관리 컴퓨터에 VM 확장을 적용할 수 있습니다.

1. 브라우저에서 [Azure Portal](https://portal.azure.com)로 이동합니다.

2. 포털에서 **서버 - Azure Arc** 를 찾아 목록에서 하이브리드 머신을 선택합니다.

3. **확장** 을 선택한 뒤 **추가** 를 선택합니다. 사용 가능한 확장 목록에서 원하는 확장을 선택하고 마법사의 지시를 따릅니다. 이 예제에서는 Log Analytics VM 확장을 배포합니다.

    ![선택한 머신에 대한 VM 확장 선택](./media/manage-vm-extensions/add-vm-extensions.png)

    다음 예제에서는 Azure Portal에서 Log Analytics VM 확장을 설치하는 방법을 보여 줍니다.

    ![Log Analytics VM 확장 설치](./media/manage-vm-extensions/mma-extension-config.png)

    설치를 완료하려면 작업 영역 ID 및 기본 키를 제공해야 합니다. 관련 정보를 찾는 방법에 익숙하지 않은 경우 [작업 영역 ID 및 키 가져오기](../../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)를 참조하세요.

4. 제공된 필수 정보를 확인한 후 **검토 + 만들기** 를 선택합니다. 배포에 대한 요약이 표시되고 배포 상태를 검토할 수 있습니다.

>[!NOTE]
>여러 확장을 함께 일괄 처리하고 프로세스할 수 있지만, 설치는 순차적으로 진행됩니다. 첫 번째 확장 설치가 완료되면 다음 확장을 설치하려고 시도합니다.

## <a name="list-extensions-installed"></a>설치된 확장 나열

Azure Portal에서 Azure Arc 사용 서버에 대 한 VM 확장 목록을 가져올 수 있습니다. 다음 단계를 수행하여 목록을 확인합니다.

1. 브라우저에서 [Azure Portal](https://portal.azure.com)로 이동합니다.

2. 포털에서 **서버 - Azure Arc** 를 찾아 목록에서 하이브리드 머신을 선택합니다.

3. **확장** 을 선택하면 설치된 확장 목록이 반환됩니다.

    :::image type="content" source="media/manage-vm-extensions/list-vm-extensions.png" alt-text="선택한 머신에 배포된 VM 확장 나열" border="true":::

## <a name="upgrade-extensions"></a>확장 업그레이드

지원 되는 확장의 새 버전이 릴리스되면 확장을 해당 최신 릴리스로 업그레이드할 수 있습니다. Azure arc 사용 서버는 Azure Arc 사용 서버를 탐색할 때 Azure Portal에 배너를 표시 하 여 컴퓨터에 설치 된 하나 이상의 확장에 사용할 수 있는 업그레이드를 알려 줍니다. 선택한 Azure Arc 사용 서버에 대해 설치 된 확장 목록을 보면 **업데이트 사용 가능** 이라는 레이블이 지정 된 열을 확인할 수 있습니다. 확장의 최신 버전이 릴리스되면 해당 확장의 **업데이트 사용 가능** 값에 **예** 값이 표시됩니다.

>[!NOTE]
>현재이 환경에 대 한 Azure Portal에서 word **업데이트** 를 사용 하는 동안 작업의 동작을 정확 하 게 나타내지 않습니다. 확장은 컴퓨터 또는 서버에 현재 설치 된 확장의 최신 버전을 설치 하 여 업그레이드 됩니다.

확장을 최신 버전으로 업그레이드 해도 해당 확장의 구성에는 영향을 주지 않습니다. 업그레이드 하는 모든 확장 프로그램에 대 한 구성 정보를 다시 지정할 필요가 없습니다.

:::image type="content" source="media/manage-vm-extensions-portal/vm-extensions-update-status.png" alt-text="VM 확장 업데이트 상태 나열" border="true":::

다음 단계를 수행 하 여 하나를 업그레이드 하거나 Azure Portal에서 업그레이드에 적합 한 확장을 여러 개 선택할 수 있습니다.

> [!NOTE]
> 현재 Azure Portal 에서만 확장을 업그레이드할 수 있습니다. 지금은 Azure CLI에서이 작업을 수행 하거나 Azure Resource Manager 템플릿을 사용 하 여이 작업을 수행할 수 없습니다.

1. 브라우저에서 [Azure Portal](https://portal.azure.com)로 이동합니다.

2. 포털에서 **서버 - Azure Arc** 를 찾아 목록에서 하이브리드 머신을 선택합니다.

3. **확장** 을 선택한 다음, **업데이트 사용 가능** 열에서 확장 상태를 검토합니다.

다음 세 가지 방법 중 하나로 확장을 업그레이드할 수 있습니다.

* 설치된 확장 목록에서 확장을 선택한 다음, 확장 속성에서 **업데이트** 옵션을 선택합니다.

    :::image type="content" source="media/manage-vm-extensions-portal/vm-extensions-update-from-extension.png" alt-text="선택한 확장에서 확장을 업그레이드 합니다." border="true":::

* 설치된 확장 목록에서 확장을 선택한 다음, 페이지 맨 위에서 **업데이트** 옵션을 선택합니다.

* 설치 된 확장 목록에서 업그레이드할 수 있는 확장을 하나 이상 선택 하 고 **업데이트** 옵션을 선택 합니다.

    :::image type="content" source="media/manage-vm-extensions-portal/vm-extensions-update-selected.png" alt-text="선택한 확장 업데이트" border="true":::

## <a name="uninstall-extensions"></a>확장 제거

Azure Portal에서 Azure Arc 사용 서버에서 하나 이상의 확장을 제거할 수 있습니다. 확장을 제거하려면 다음 단계를 수행합니다.

1. 브라우저에서 [Azure Portal](https://portal.azure.com)로 이동합니다.

2. 포털에서 **서버 - Azure Arc** 를 찾아 목록에서 하이브리드 머신을 선택합니다.

3. **확장** 을 선택한 다음, 설치된 확장 목록에서 확장을 선택합니다.

4. **제거** 를 선택하고 확인 메시지가 표시되면 **예** 를 선택하여 계속합니다.

## <a name="next-steps"></a>다음 단계

- [Azure CLI](manage-vm-extensions-cli.md), [PowerShell](manage-vm-extensions-powershell.md) 또는 [Azure Resource Manager 템플릿](manage-vm-extensions-template.md)을 사용하여 VM 확장을 배포, 관리 및 제거할 수 있습니다.

- 문제 해결 정보는 [VM 확장 문제 해결 안내](troubleshoot-vm-extensions.md)에서 찾을 수 있습니다.
