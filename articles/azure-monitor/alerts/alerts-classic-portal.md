---
title: Azure Monitor를 사용하여 클래식 메트릭 경고 만들기 및 관리
description: Azure Portal, CLI 또는 PowerShell을 사용하여 클래식 메트릭 경고 규칙을 만들고, 보고, 관리하는 방법을 알아봅니다.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/06/2021
ms.openlocfilehash: c5f3bd7d5c8e934977955b2b3f253e131cca7111
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123539194"
---
# <a name="create-view-and-manage-classic-metric-alerts-using-azure-monitor"></a>Azure Monitor를 사용하여 클래식 메트릭 경고 만들기, 보기 및 관리

> [!WARNING]
> 이 문서에서는 기존 클래식 메트릭 경고를 만드는 방법을 설명합니다. 이제 Azure Monitor는 [최신 근 실시간 메트릭 경고 및 새 경고 환경](./alerts-overview.md)을 지원합니다. 클래식 경고는 공용 클라우드 사용자에 게 사용이 [중지](./monitoring-classic-retirement.md) 됩니다. Azure Government 클라우드 및 Azure 중국 21Vianet에 대한 클래식 경고는 **2024년 2월 29일** 에 사용 중지됩니다.
>

Azure Monitor의 클래식 메트릭 경고를 사용하면 메트릭 중 하나가 임계값을 초과할 경우 알림을 받을 수 있습니다. 클래식 메트릭 경고는 무차원 메트릭에 대해서만 경고하는 이전 기능입니다. 클래식 메트릭 경고보다 향상된 기능을 가진, 메트릭 경고라는 최신 기능도 있습니다. [메트릭 경고 개요](./alerts-metric-overview.md)에서 새로운 메트릭 경고 기능에 대해 자세히 알아볼 수 있습니다. 이 문서에서는 Azure Portal, Azure CLI 및 PowerShell을 통해 클래식 메트릭 경고 규칙을 만들고, 보고, 관리하는 방법을 설명합니다.

## <a name="with-azure-portal"></a>Azure Portal 사용

1. [포털](https://portal.azure.com/)에서 모니터링할 리소스를 찾아서 선택합니다.

2. **모니터링** 섹션에서 **경고(클래식)** 를 선택합니다. 텍스트와 아이콘은 리소스마다 약간씩 다를 수 있습니다. 여기서 **경고(클래식)** 를 찾을 수 없는 경우 **경고** 또는 **경고 규칙** 에서 찾을 수 있습니다.

    ![모니터링](media/alerts-classic-portal/AlertRulesButton.png)

3. **Add metric alert(클래식)** 명령을 선택하고 필드를 채웁니다.

    ![Add alert](media/alerts-classic-portal/AddAlertOnlyParamsPage.png)

4. 경고 규칙의 **이름을 지정** 합니다. 그리고 **설명** 을 선택합니다. 설명도 알림 이메일에 표시됩니다.

5. 모니터링할 **메트릭** 을 선택합니다. 메트릭의 **조건** 및 **임계값** 을 선택합니다. 경고를 트리거하기 전에 메트릭 규칙을 만족해야 하는 **기간** 도 선택합니다. 예를 들어 "지난 5분" 기간을 사용하고 경고가 80% 이상인 CPU를 찾는다면 이 경고는 CPU가 5분 동안 계속 80%를 넘으면 트리거됩니다. 첫 번째 트리거가 발생한 후 CPU가 5분 동안 80% 미만을 유지하면 다시 트리거됩니다. CPU 메트릭 측정은 1분마다 발생합니다.

6. 경고가 발생하면 관리자 및 공동 관리자가 이메일 알림을 수신하게 하려면 **소유자에게 이메일 보내기...** 를 선택합니다.

7. 경고가 발생하면 추가 이메일 주소에 알림을 보내려면 **추가 관리자 이메일** 필드에 해당 이메일 주소를 추가합니다. 메일 주소가 여러 개인 경우 *email\@contoso.com;email2\@contoso.com* 처럼 세미콜론을 사용하여 구분할 수 있습니다.

8. 경고가 발생할 때 URI를 호출하려면 **Webhook** 필드에 유효한 URI를 입력합니다.

9. Azure Automation을 사용하는 경우 경고가 발생할 때 실행할 Runbook을 선택할 수 있습니다.

10. **확인** 을 선택하여 경고를 만듭니다.

앞서 설명한 대로 몇 분 안에 경고가 활성화 및 트리거됩니다.

경고를 만든 후에는 해당 경고를 선택하고 다음 작업 중 하나를 수행할 수 있습니다.

* 전날의 메트릭 임계값 및 실제 값을 표시하는 그래프 보기
* 편집 또는 삭제
* 해당 경고에 대한 알림 수신을 일시 중지 또는 재개하려면 **사용 중지** 또는 **사용** 하도록 설정합니다.

## <a name="with-azure-cli"></a>Azure CLI 사용

이전 섹션에서는 Azure Portal을 사용하여 메트릭 경고 규칙을 만들고, 보고, 관리하는 방법을 설명했습니다. 이번 섹션에서는 플랫폼 간 [Azure CLI](/cli/azure/get-started-with-azure-cli)를 사용하여 동일한 작업을 수행하는 방법을 설명합니다. Azure CLI를 가장 빠르게 사용하는 방법은 [Azure Cloud Shell](../../cloud-shell/overview.md)입니다.

### <a name="get-all-classic-metric-alert-rules-in-a-resource-group"></a>리소스 그룹의 모든 클래식 메트릭 경고 규칙 가져오기

```azurecli
az monitor alert list --resource-group <group name>
```

### <a name="see-details-of-a-particular-classic-metric-alert-rule"></a>특정 클래식 메트릭 경고 규칙의 세부 정보 참조

```azurecli
az monitor alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-classic-metric-alert-rule"></a>클래식 메트릭 경고 규칙 만들기

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-a-classic-metric-alert-rule"></a>클래식 메트릭 경고 규칙 삭제

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="with-powershell"></a>PowerShell 사용

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

이 섹션에서는 PowerShell 명령을 사용하여 클래식 메트릭 경고를 만들고, 보고, 관리하는 방법을 보여 줍니다. 문서의 예제에서는 클래식 메트릭 경고에 대해 Azure Monitor cmdlet을 사용하는 방법을 보여 줍니다.

1. 아직 PowerShell이 컴퓨터에서 실행되도록 설정하지 않았으면 지금 설정합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/)을 참조하세요. [Azure Monitor(Insights) Cmdlet](/powershell/module/az.applicationinsights)에서 Azure Monitor PowerShell cmdlet의 전체 목록을 살펴볼 수도 있습니다.

2. 먼저 Azure 구독에 로그인합니다.

    ```powershell
    Connect-AzAccount
    ```

3. 로그인 화면이 표시됩니다. 로그인하면 계정, 테넌트 ID 및 기본 구독 ID가 표시됩니다. 모든 Azure cmdlet은 기본 구독의 컨텍스트에서 작동합니다. 액세스할 수 있는 구독 목록을 보려면 다음 명령을 사용합니다.

    ```powershell
    Get-AzSubscription
    ```

4. 작업 컨텍스트를 다른 구독으로 변경하려면 다음 명령을 사용합니다.

    ```powershell
    Set-AzContext -SubscriptionId <subscriptionid>
    ```

5. 리소스 그룹에 대한 모든 클래식 메트릭 경고 규칙을 검색할 수 있습니다.

    ```powershell
    Get-AzAlertRule -ResourceGroup montest
    ```

6. 클래식 메트릭 경고 규칙의 세부 정보를 볼 수 있습니다.

    ```powershell
    Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
    ```

7. 대상 리소스에 대해 설정된 모든 경고 규칙을 검색할 수 있습니다. 예를 들어 VM에 설정된 모든 경고 규칙을 검색할 수 있습니다.

    ```powershell
    Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
    ```

8. 클래식 경고 규칙은 PowerShell을 통해 더 이상 만들 수 없습니다. 경고 규칙을 만들려면 새 [‘Add-AzMetricAlertRule’](/powershell/module/az.monitor/add-azmetricalertrule) 명령을 사용해야 합니다.

## <a name="next-steps"></a>다음 단계

- [Resource Manager 템플릿을 사용하여 클래식 메트릭 경고 만들기](./alerts-enable-template.md)
- [웹후크를 사용하여 비 Azure 시스템에 알리도록 클래식 메트릭 경고 설정](./alerts-webhooks.md)
