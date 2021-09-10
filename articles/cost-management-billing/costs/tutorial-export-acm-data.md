---
title: 자습서 - Azure Cost Management에서 내보낸 데이터 만들기 및 관리
description: 이 문서에서는 내보낸 Azure Cost Management 데이터를 외부 시스템에서 사용할 수 있도록 만들고 관리하는 방법을 보여줍니다.
author: bandersmsft
ms.author: banders
ms.date: 08/05/2021
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18, devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: f6d640d40b586e435e1484fce8d6560a961ba6ac
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121731945"
---
# <a name="tutorial-create-and-manage-exported-data"></a>자습서: 내보낸 데이터 만들기 및 관리

비용 분석 자습서를 읽으셨다면 Cost Management 데이터를 수동으로 다운로드하는 방법에 익숙하실 것입니다. 그러나 매일, 매주 또는 매월 자동으로 Azure Storage에 Cost Management 데이터를 내보내는 되풀이 작업을 만들 수 있습니다. 내보낸 데이터는 CSV 형식이며 Cost Management에서 수집한 모든 정보를 포함하고 있습니다. Azure Storage로 내보낸 데이터를 외부 시스템에 사용하고 사용자 고유의 사용자 지정 데이터와 결합할 수 있습니다. 그리고 내보낸 데이터를 대시보드나 기타 재무 시스템 같은 외부 시스템에서 사용할 수 있습니다.

Azure Storage로의 Azure 비용 데이터 예약된 내보내기를 만드는 방법에 대해서는 [Azure Cost Management를 사용하여 스토리지로의 내보내기를 예약하는 방법](https://www.youtube.com/watch?v=rWa_xI1aRzo) 비디오를 시청하세요. 다른 비디오를 시청하려면 [Cost Management YouTube 채널](https://www.youtube.com/c/AzureCostManagement)을 방문하세요.

>[!VIDEO https://www.youtube.com/embed/rWa_xI1aRzo]

이 자습서의 예제는 비용 관리 데이터를 내보낸 후 데이터 내보내기가 성공했는지 확인하는 단계를 안내합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 일일 내보내기 만들기
> * 데이터가 수집되는지 확인

## <a name="prerequisites"></a>필수 구성 요소

데이터 내보내기는 [EA(기업계약)](https://azure.microsoft.com/pricing/enterprise-agreement/) 및 [Microsoft 고객 계약](get-started-partners.md) 고객을 비롯한 다양한 종류의 Azure 계정에 사용할 수 있습니다. 지원되는 계정 유형의 전체 목록을 보려면 [Cost Management 데이터 이해](understand-cost-mgt-data.md)를 참조하세요. 다음 Azure 사용 권한 또는 범위는 사용자 및 그룹별 데이터 내보내기에 대해 구독별로 지원됩니다. 범위에 대한 자세한 내용은 [범위 이해 및 작업](understand-work-scopes.md)을 참조하세요.

- 소유자 - 구독에 대한 예약된 내보내기를 생성, 수정 또는 삭제할 수 있습니다.
- 기여자 - 자신의 예약된 내보내기를 생성, 수정 또는 삭제할 수 있습니다. 다른 사용자가 만든 예약된 내보내기의 이름을 수정할 수 있습니다.
- 읽기 권한자 - 권한이 있는 내보내기를 예약할 수 있습니다.

**기업계약 및 Microsoft 고객 계약 범위에 대한 내보내기를 구성하는 데 필요한 액세스 등 범위에 대한 자세한 내용은 [범위 이해 및 작업](understand-work-scopes.md)을 참조하세요**.

Azure Storage 계정의 경우:
- 내보내기에 대한 사용 권한과 독립적으로 구성된 스토리지 계정을 변경하려면 쓰기 사용 권한이 필요합니다.
- BLOB 또는 파일 스토리지에 대해 Azure 스토리지 계정이 구성되어 있어야 합니다.

새 구독이 있는 경우 Cost Management 기능을 즉시 사용할 수 없습니다. 모든 Cost Management 기능을 사용하려면 최대 48시간이 걸릴 수 있습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인
[https://portal.azure.com](https://portal.azure.com/)에서 Azure Portal에 로그인합니다.

## <a name="create-a-daily-export"></a>일일 내보내기 만들기

### <a name="portal"></a>[포털](#tab/azure-portal)

데이터 내보내기를 만들거나 보려면 또는 내보내기를 예약하려면 Azure Portal에서 범위를 선택하고 메뉴에서 **비용 분석** 을 선택합니다. 예를 들어 **구독** 으로 이동하여 목록에서 구독을 선택한 다음, 메뉴에서 **비용 분석** 을 선택합니다. 비용 분석 페이지의 맨 위에서 **설정** 을 선택한 다음, **내보내기** 를 선택합니다.

> [!NOTE]
> - 구독 외에도 리소스 그룹, 관리 그룹, 부서 및 등록에서 내보내기를 만들 수 있습니다. 범위에 대한 자세한 내용은 [범위 이해 및 작업](understand-work-scopes.md)을 참조하세요.
> - 청구 계정 범위 또는 고객 테넌트에서 파트너로 로그인한 경우 파트너 스토리지 계정에 연결된 Azure Storage 계정으로 데이터를 내보낼 수 있습니다. 그러나 CSP 테넌트에 활성 구독이 있어야 합니다.

1. **추가** 를 선택하고 내보내기의 이름을 입력합니다.
1. **메트릭** 에서 선택합니다.
    - **실제 비용(사용량 및 구매)** - 표준 사용량 및 구매를 내보내려면 선택합니다.
    - **분할상환 비용(사용량 및 구매)** - Azure 예약과 같은 구매 분할상환 비용을 내보내려면 선택합니다.
1. **내보내기 유형** 에서 선택합니다.
    - **월간 누계 비용 매일 내보내기** - 월간 누계 비용에 대한 새로운 내보내기 파일을 매일 제공합니다. 최신 데이터는 이전의 매일 내보내기에서 집계됩니다.
    - **지난 7일 동안의 주간 비용 내보내기** – 선택한 내보내기 시작일로부터 지난 7일 동안 발생한 비용의 주간 내보내기를 만듭니다.
    - **지난달 비용의 월간 내보내기** – 내보내기를 만든 현재 달에 비해 지난 달의 비용에 대한 내보내기를 제공합니다. 앞으로, 일정은 매월 5일에 이전 달의 비용으로 내보내기를 실행합니다.
    - **일회성 내보내기** – Azure Blob Storage로 내보낼 기록 데이터의 날짜 범위를 선택할 수 있습니다. 선택한 날부터 최대 90일의 기록 비용을 내보낼 수 있습니다. 이 내보내기는 즉시 실행되며 2시간 이내에 스토리지 계정에서 사용할 수 있습니다.
        내보내기 유형에 따라 시작 날짜를 선택하거나 **From** 및 **To** 날짜를 선택합니다.
1. Azure 스토리지 계정에 대한 구독을 지정한 다음, 리소스 그룹을 선택하거나 새로 만듭니다.
1. 스토리지 계정 이름을 선택하거나 새로 만듭니다.
1. 위치(Azure 지역)를 선택합니다.
1. 내보내기 파일을 보낼 스토리지 컨테이너 및 디렉터리 경로를 지정합니다.
    :::image type="content" source="./media/tutorial-export-acm-data/basics_exports.png" alt-text="새 내보내기 예제" lightbox="./media/tutorial-export-acm-data/basics_exports.png":::
1. 내보내기 세부 정보를 검토하고 **만들기** 를 선택합니다.

새 내보내기는 내보내기 목록에 표시됩니다. 기본적으로 새 내보내기가 사용되도록 설정됩니다. 예정된 내보내기를 해제 또는 삭제하려면 목록에서 항목을 선택한 다음, **사용 안 함** 또는 **삭제** 를 선택합니다.

처음에는 내보내기가 실행될 때까지 12~24시간이 걸릴 수 있습니다. 그러나 내보낸 파일에 데이터가 표시될 때까지는 더 오래 걸릴 수 있습니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

프로그래밍 방식으로 내보내기를 만든 경우 스토리지 계정이 있는 구독과 함께 `Microsoft.CostManagementExports` 리소스 공급자를 수동으로 등록해야 합니다. Azure Portal을 사용하여 내보내기를 만들면 등록이 자동으로 수행됩니다. 리소스 공급자를 등록하는 방법에 대한 자세한 내용은 [리소스 공급자 등록](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)을 참조하세요.

Azure CLI에 대한 환경 준비하는 것으로 시작합니다.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

1. 로그인한 후 현재 내보내기를 보려면 [az costmanagement export list](/cli/azure/costmanagement/export#az_costmanagement_export_list) 명령을 사용합니다.

   ```azurecli
   az costmanagement export list --scope "subscriptions/00000000-0000-0000-0000-000000000000"
   ```

   >[!NOTE]
   >
   >* 구독 외에도 리소스 그룹 및 관리 그룹에 대한 내보내기를 만들 수 있습니다. 범위에 대한 자세한 내용은 [범위 이해 및 작업](understand-work-scopes.md)을 참조하세요.
   >* 청구 계정 범위 또는 고객 테넌트에서 파트너로 로그인한 경우 파트너 스토리지 계정에 연결된 Azure Storage 계정으로 데이터를 내보낼 수 있습니다. 그러나 CSP 테넌트에 활성 구독이 있어야 합니다.

1. 리소스 그룹을 만들거나 기존 리소스 그룹을 사용합니다. 리소스 그룹을 만들려면 [az group create](/cli/azure/group#az_group_create) 명령을 사용합니다.

   ```azurecli
   az group create --name TreyNetwork --location "East US"
   ```

1. 내보내기를 수신하거나 기존 스토리지 계정을 사용할 스토리지 계정을 만듭니다. 스토리지 계정을 만들려면 [az storage account create](/cli/azure/storage/account#az_storage_account_create) 명령을 사용합니다.

   ```azurecli
   az storage account create --resource-group TreyNetwork --name cmdemo
   ```

1. [az costmanagement export create](/cli/azure/costmanagement/export#az_costmanagement_export_create) 명령을 실행하여 내보내기를 만듭니다.

   ```azurecli
   az costmanagement export create --name DemoExport --type ActualCost \
   --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-account-id cmdemo \
   --storage-container democontainer --timeframe MonthToDate --recurrence Daily \
   --recurrence-period from="2020-06-01T00:00:00Z" to="2020-10-31T00:00:00Z" \
   --schedule-status Active --storage-directory demodirectory
   ```

   **--type** 매개 변수의 경우 `ActualCost`, `AmortizedCost` 또는 `Usage`를 선택할 수 있습니다.

   이 예에서는 `MonthToDate`를 사용합니다. 내보내기는 월간 누계 비용에 대해 매일 내보내기 파일을 만듭니다. 최신 데이터는 이번 달 이전 매일 내보내기에서 집계됩니다.

1. 내보내기 작업에 대한 세부 정보를 보려면 [az costmanagement export show](/cli/azure/costmanagement/export#az_costmanagement_export_show) 명령을 사용합니다.

   ```azurecli
   az costmanagement export show --name DemoExport \
      --scope "subscriptions/00000000-0000-0000-0000-000000000000"
   ```

1. [az costmanagement export update](/cli/azure/costmanagement/export#az_costmanagement_export_update) 명령을 사용하여 내보내기를 업데이트합니다.

   ```azurecli
   az costmanagement export update --name DemoExport
      --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-directory demodirectory02
   ```

   이 예제에서는 출력 디렉터리를 변경합니다.

>[!NOTE]
>처음에는 내보내기가 실행될 때까지 12~24시간이 걸릴 수 있습니다. 그러나 내보낸 파일에 데이터가 표시되기까지는 더 오래 걸릴 수 있습니다.

[az costmanagement export delete](/cli/azure/costmanagement/export#az_costmanagement_export_delete) 명령을 사용하여 내보내기를 삭제할 수 있습니다.

```azurecli
az costmanagement export delete --name DemoExport --scope "subscriptions/00000000-0000-0000-0000-000000000000"
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

프로그래밍 방식으로 내보내기를 만든 경우 스토리지 계정이 있는 구독과 함께 `Microsoft.CostManagementExports` 리소스 공급자를 수동으로 등록해야 합니다. Azure Portal을 사용하여 내보내기를 만들면 등록이 자동으로 수행됩니다. 리소스 공급자를 등록하는 방법에 대한 자세한 내용은 [리소스 공급자 등록](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)을 참조하세요.

Azure PowerShell에 대한 환경을 준비하는 것으로 시작합니다.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > **Az.CostManagement** PowerShell 모듈이 미리 보기로 제공되는 동안에는 `Install-Module` cmdlet을 사용하여 별도로 설치해야 합니다. 이 PowerShell 모듈이 일반 공급되면 이후 Az PowerShell 모듈 릴리스의 일부가 되며 기본적으로 Azure Cloud Shell 내에서 사용할 수 있습니다.

  ```azurepowershell-interactive
  Install-Module -Name Az.CostManagement
  ```

1. 로그인한 후 현재 내보내기를 보려면 [Get-AzCostManagementExport](/powershell/module/Az.CostManagement/get-azcostmanagementexport) cmdlet을 사용합니다.

   ```azurepowershell-interactive
   Get-AzCostManagementExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
   ```

   >[!NOTE]
   >
   >* 구독 외에도 리소스 그룹 및 관리 그룹에 대한 내보내기를 만들 수 있습니다. 범위에 대한 자세한 내용은 [범위 이해 및 작업](understand-work-scopes.md)을 참조하세요.
   >* 청구 계정 범위 또는 고객 테넌트에서 파트너로 로그인한 경우 파트너 스토리지 계정에 연결된 Azure Storage 계정으로 데이터를 내보낼 수 있습니다. 그러나 CSP 테넌트에 활성 구독이 있어야 합니다.

1. 리소스 그룹을 만들거나 기존 리소스 그룹을 사용합니다. 리소스 그룹을 만들려면 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet을 사용합니다.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name TreyNetwork -Location eastus
   ```

1. 내보내기를 수신하거나 기존 스토리지 계정을 사용할 스토리지 계정을 만듭니다. 스토리지 계정을 만들려면 [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) cmdlet을 사용합니다.

   ```azurepowershell-interactive
   New-AzStorageAccount -ResourceGroupName TreyNetwork -AccountName cmdemo -SkuName Standard_RAGRS -Location eastus
   ```

1. [New-AzCostManagementExport](/powershell/module/Az.CostManagement/new-azcostmanagementexport) cmdlet을 실행하여 내보내기를 만듭니다.

   ```azurepowershell-interactive
   $Params = @{
     Name = 'DemoExport'
     DefinitionType = 'ActualCost'
     Scope = 'subscriptions/00000000-0000-0000-0000-000000000000'
     DestinationResourceId = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/treynetwork/providers/Microsoft.Storage/storageAccounts/cmdemo'
     DestinationContainer = 'democontainer'
     DefinitionTimeframe = 'MonthToDate'
     ScheduleRecurrence = 'Daily'
     RecurrencePeriodFrom = '2020-06-01T00:00:00Z'
     RecurrencePeriodTo = '2020-10-31T00:00:00Z'
     ScheduleStatus = 'Active'
     DestinationRootFolderPath = 'demodirectory'
     Format = 'Csv'
   }
   New-AzCostManagementExport @Params
   ```

   **DefinitionType** 매개 변수의 경우 `ActualCost`, `AmortizedCost` 또는 `Usage`를 선택할 수 있습니다.

   이 예에서는 `MonthToDate`를 사용합니다. 내보내기는 월간 누계 비용에 대해 매일 내보내기 파일을 만듭니다. 최신 데이터는 이번 달 이전 매일 내보내기에서 집계됩니다.

1. 내보내기 작업에 대한 세부 정보를 보려면 `Get-AzCostManagementExport` cmdlet을 사용합니다.

   ```azurepowershell-interactive
   Get-AzCostManagementExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
   ```

1. [Update-AzCostManagementExport](/powershell/module/Az.CostManagement/update-azcostmanagementexport) cmdlet을 사용하여 내보내기를 업데이트합니다.

   ```azurepowershell-interactive
   Update-AzCostManagementExport -Name DemoExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000' -DestinationRootFolderPath demodirectory02
   ```

   이 예제에서는 출력 디렉터리를 변경합니다.

>[!NOTE]
>처음에는 내보내기가 실행될 때까지 12~24시간이 걸릴 수 있습니다. 그러나 내보낸 파일에 데이터가 표시되기까지는 더 오래 걸릴 수 있습니다.

[Remove-AzCostManagementExport](/powershell/module/Az.CostManagement/remove-azcostmanagementexport) cmdlet을 사용하여 내보내기를 삭제할 수 있습니다.

```azurepowershell-interactive
Remove-AzCostManagementExport -Name DemoExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
```

---

### <a name="export-schedule"></a>내보내기 일정

예약된 내보내기는 처음에 내보내기를 만든 요일과 시간의 영향을 받습니다. 예약된 내보내기를 만들면 나중에 실행되는 각 내보내기에 대해 동일한 빈도로 내보내기가 실행됩니다. 예를 들어 일별 빈도로 월간 누계 비용 매일 내보내기를 설정하는 경우 내보내기가 매일 실행됩니다. 마찬가지로 매주 내보내기의 경우에는 예약된 것과 동일한 요일에 내보내기가 매주 실행됩니다. 내보내기의 정확한 배달 시간은 보장되지 않으며, 내보낸 데이터는 런타임의 4시간 이내에 사용할 수 있습니다.

각 내보내기는 새 파일을 만들므로 이전의 내보내기를 덮어쓰지 않습니다.

#### <a name="create-an-export-for-multiple-subscriptions"></a>여러 구독에 대한 내보내기 만들기

기업계약을 보유한 경우 관리 그룹을 사용하여 단일 컨테이너의 구독 비용 정보를 집계할 수 있습니다. 그런 다음, 관리 그룹에 대한 비용 관리 데이터를 내보낼 수 있습니다. 관리 그룹에 대한 내보내기는 실제 비용만 지원합니다.

다른 구독 유형의 관리 그룹에 대한 내보내기는 지원되지 않습니다.

1. 관리 그룹을 아직 만들지 않은 경우 그룹을 하나 만들고 구독을 할당합니다.
1. 비용 분석에서 범위를 관리 그룹으로 설정하고 **이 관리 그룹 선택** 을 선택합니다.
    :::image type="content" source="./media/tutorial-export-acm-data/management-group-scope.png" alt-text="이 관리 그룹 선택 옵션을 보여 주는 예제" lightbox="./media/tutorial-export-acm-data/management-group-scope.png":::
1. 범위에서 내보내기를 만들어 관리 그룹의 구독에 대한 비용 관리 데이터를 가져옵니다.
    :::image type="content" source="./media/tutorial-export-acm-data/new-export-management-group-scope.png" alt-text="관리 그룹 범위를 사용하여 새 내보내기 옵션을 보여 주는 예제":::

### <a name="file-partitioning-for-large-datasets"></a>대량 데이터 세트에 대한 파일 분할

Microsoft 고객 계약, Microsoft 파트너 계약 또는 기업계약이 있는 경우 내보내기를 사용하도록 설정하여 데이터 수집에 도움이 되는 여러 개의 작은 파일 파티션으로 파일을 청크 분할할 수 있습니다. 내보내기를 처음 구성할 때 **File Partitioning**(파일 분할) 설정을 **켜기** 로 설정합니다. 설정은 기본적으로 **끄기** 입니다.

:::image type="content" source="./media/tutorial-export-acm-data/file-partition.png" alt-text="파일 분할 옵션을 보여 주는 스크린샷" lightbox="./media/tutorial-export-acm-data/file-partition.png" :::

Microsoft 고객 계약, Microsoft 파트너 계약 또는 기업계약이 없는 경우 **파일 분할** 옵션이 표시되지 않습니다.

#### <a name="update-existing-exports-to-use-file-partitioning"></a>파일 분할을 사용하도록 기존 내보내기 업데이트

기존 내보내기가 있고 파일 분할을 설정하려면 새 내보내기를 만듭니다. 파일 분할은 최신 내보내기 버전에서만 사용할 수 있습니다. 생성되는 사용량 파일의 일부 필드가 약간 변경될 수 있습니다.

기존 내보내기에서 파일 분할을 사용하도록 설정하면 파일 출력에서 필드가 약간 변경될 수 있습니다. 처음 설정한 후에 내보내기에 대한 업데이트 때문에 변경 내용이 생성됩니다.

#### <a name="partitioning-output"></a>분할 출력

파일 분할을 사용하도록 설정하면 내보내기의 각 데이터 파티션에 대한 파일을 _manifest.json 파일과 함께 가져옵니다. 매니페스트에는 전체 데이터 세트에 대한 요약과 각 파일 파티션에 대한 정보가 포함되어 있습니다. 각 파일 파티션에는 헤더가 있으며 전체 데이터 세트의 하위 집합만 포함되어 있습니다. 전체 데이터 세트를 처리하려면 내보내기의 각 파티션을 수집해야 합니다.

다음은 _manifest.json 예제 매니페스트 파일입니다.

```json
{
  "manifestVersion": "2021-01-01",
  "dataFormat": "csv",
  "blobCount": 1,
  "byteCount": 160769,
  "dataRowCount": 136,
  "blobs": [
    {
      "blobName": "blobName.csv",
      "byteCount": 160769,
      "dataRowCount": 136,
      "headerRowCount": 1,
      "contentMD5": "md5Hash"
    }
  ]
}
```
## <a name="verify-that-data-is-collected"></a>데이터가 수집되는지 확인

Azure Storage Explorer를 사용하여 간단하게 Cost Management 데이터가 수집되는지 확인하고 내보낸 CSV 파일을 볼 수 있습니다.

내보내기 목록에서 스토리지 계정 이름을 선택합니다. 스토리지 계정 페이지의 탐색기에서 열기를 선택합니다. 확인 상자가 나타나면 **예** 를 선택하여 Azure Storage Explorer에서 파일을 엽니다.

![예제 정보 및 탐색기에서 열기 링크를 표시하는 스토리지 계정 페이지](./media/tutorial-export-acm-data/storage-account-page.png)

Storage Explorer에서 열고 싶은 컨테이너로 이동한 후 현재 월에 해당하는 폴더를 선택합니다. CSV 파일 목록이 표시됩니다. 파일을 하나 선택하고 **열기** 를 선택합니다.

![Storage Explorer에 표시되는 예제 정보](./media/tutorial-export-acm-data/storage-explorer.png)

CSV 파일 확장명을 열도록 설정된 프로그램 또는 애플리케이션에서 파일이 열립니다. 다음은 Excel 예제입니다.

![Excel에 표시되는 내보낸 CSV 데이터 예제](./media/tutorial-export-acm-data/example-export-data.png)

### <a name="download-an-exported-csv-data-file"></a>내보낸 CSV 데이터 파일 다운로드

내보낸 CSV 파일은 Azure Portal에서 다운로드할 수도 있습니다. 다음 단계에서는 비용 분석에서 이를 찾는 방법을 설명합니다.

1. 비용 분석에서 **설정** 을 선택하고 **내보내기** 를 선택합니다.
1. 내보내기 목록에서 내보내기에 대한 스토리지 계정을 선택합니다.
1. 스토리지 계정에서 **컨테이너** 를 선택합니다.
1. 컨테이너 목록에서 컨테이너를 선택합니다.
1. 디렉터리 및 스토리지 BLOB를 통해 원하는 날짜로 이동합니다.
1. CSV 파일을 선택한 다음, **다운로드** 를 선택합니다.

[![데이터 내보내기 예제](./media/tutorial-export-acm-data/download-export.png)](./media/tutorial-export-acm-data/download-export.png#lightbox)

## <a name="view-export-run-history"></a>내보내기 실행 기록 보기

내보내기 목록 페이지에서 개별 내보내기를 선택하여 예약된 내보내기의 실행 기록을 볼 수 있습니다. 또한 내보내기 목록 페이지에서는 이전 내보내기의 실행 시간을 볼 수 있는 빠른 액세스와 다음 번 및 내보내기가 실행될 때 사용할 수 있습니다. 실행 기록을 보여 주는 예제는 다음과 같습니다.

:::image type="content" source="./media/tutorial-export-acm-data/run-history.png" alt-text="스크린샷은 내보내기 창을 보여줍니다.":::

실행 기록을 보려면 내보내기를 선택합니다.

:::image type="content" source="./media/tutorial-export-acm-data/single-export-run-history.png" alt-text="스크린샷은 내보내기의 실행 기록을 보여줍니다.":::

## <a name="access-exported-data-from-other-systems"></a>다른 시스템에서 내보낸 데이터에 액세스

Cost Management 데이터를 내보내는 목적 중 하나는 외부 시스템에서 데이터에 액세스하는 것입니다. 대시보드 시스템 또는 기타 재무 시스템을 사용할 수 있습니다. 이러한 시스템의 종류는 매우 다양하기 때문에 예제를 보여드리는 것이 별 의미가 없습니다.  하지만 [Azure Storage 소개](../../storage/common/storage-introduction.md)를 통해 애플리케이션에서 데이터에 액세스하는 방법을 알아볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 일일 내보내기 만들기
> * 데이터가 수집되는지 확인

그 다음 자습서로 넘어가서 유휴 리소스 및 미달 사용 리소스를 식별하여 최적화하고 효율성을 개선하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [최적화 권장 사항 검토 및 필요한 조치 수행](tutorial-acm-opt-recommendations.md)
