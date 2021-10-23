---
title: Azure Portal 및 PowerShell을 사용하여 파이프라인 모니터링 및 관리
description: Azure Portal과 Azure PowerShell을 사용하여 사용자가 만든 Azure Data Factory와 파이프라인을 모니터링하고 관리하는 방법에 대해 알아봅니다.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 10/22/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 31c8d885f5139f26cf2fb72ea0ceb0624c8945bf
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130264373"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Azure Portal 및 PowerShell을 사용하여 Azure Data Factory 파이프라인 모니터링 및 관리
> [!div class="op_single_selector"]
> * [Azure 포털/Azure PowerShell 사용](data-factory-monitor-manage-pipelines.md)
> * [모니터링 및 관리 앱 사용](data-factory-monitor-manage-app.md)

> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우 [Data Factory 파이프라인 모니터링 및 관리](../monitor-visually.md)를 참조하세요.

이 문서는 Azure Portal 및 PowerShell을 사용하여 파이프라인을 모니터링하고 관리하고 디버그하는 방법을 설명합니다.

> [!IMPORTANT]
> 모니터링 및 관리 애플리케이션은 데이터 파이프라인 모니터링 및 관리와 문제 해결에 대한 더 나은 지원을 제공합니다. 애플리케이션 사용에 대한 자세한 내용은 [모니터링 및 관리 앱을 사용하여 데이터 팩터리 파이프라인 모니터링 및 관리](data-factory-monitor-manage-app.md)를 참조하세요. 

> [!IMPORTANT]
> Azure Data Factory 버전 1은 이제 새로운 [Azure Monitor 경고 인프라](../../azure-monitor/alerts/alerts-metric.md)를 사용합니다. 이전의 경고 인프라는 사용되지 않습니다. 따라서 버전 1 데이터 팩터리용으로 구성된 기존의 경고는 더 이상 작동하지 않습니다. v1 데이터 팩터리에 대한 기존의 경고는 자동으로 마이그레이션되지 않습니다. 새 경고 인프라에서 이러한 경고를 다시 만들어야 합니다. Azure Portal에 로그인하고 **모니터** 를 선택하여 버전 1 데이터 팩터리의 메트릭(예: 실패한 실행 또는 성공한 실행)에 대한 새 경고를 만듭니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="understand-pipelines-and-activity-states"></a>파이프라인 및 작업 상태 이해
Azure Portal을 사용하여 다음을 수행할 수 있습니다.

* 데이터 팩터리를 다이어그램으로 보기
* 파이프라인에서 활동 보기
* 입력 및 출력 데이터 세트 보기

이 섹션은 데이터 세트 조각이 하나의 상태에서 다른 상태로 전환되는 방식을 설명합니다.   

### <a name="navigate-to-your-data-factory"></a>데이터 팩터리로 이동
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽의 메뉴에서 **데이터 팩터리** 를 클릭합니다. 데이터 팩터리가 보이지 않으면 **더 많은 서비스 >** 를 클릭하고 **인텔리전스 + 분석** 범주 아래에 있는 **데이터 팩터리** 를 클릭합니다.

   :::image type="content" source="./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png" alt-text="모든 > 데이터 팩터리 찾아보기":::
3. **데이터 팩터리** 블레이드에서 관심있는 데이터 팩터리를 선택합니다.

    :::image type="content" source="./media/data-factory-monitor-manage-pipelines/select-data-factory.png" alt-text="데이터 팩터리 선택":::

   데이터 팩터리의 홈 페이지가 표시됩니다.

   :::image type="content" source="./media/data-factory-monitor-manage-pipelines/data-factory-blade.png" alt-text="데이터 팩터리 블레이드":::

#### <a name="diagram-view-of-your-data-factory"></a>데이터 팩터리의 다이어그램 뷰
데이터 팩터리의 **다이어그램** 뷰에는 데이터 팩터리와 그 자산을 모니터링하고 관리하기 위한 단일 돋보기 창이 제공됩니다. 데이터 팩터리의 **다이어그램** 뷰를 보려면 데이터 팩터리 홈 페이지에서 **다이어그램** 을 클릭합니다.

:::image type="content" source="./media/data-factory-monitor-manage-pipelines/diagram-view.png" alt-text="다이어그램 뷰":::

확대, 축소, 크기에 맞게, 100% 확대, 다이어그램 레이아웃 고정, 파이프라인과 데이터 세트 자동 배치가 가능합니다. 또한 데이터 계보 정보도 볼 수 있습니다(즉, 선택한 항목의 업스트림 및 다운스트림 항목 표시).

### <a name="activities-inside-a-pipeline"></a>파이프라인 내부의 활동
1. 파이프라인을 마우스 오른쪽 단추로 클릭하고 **파이프라인 열기** 를 클릭하면 파이프라인 내부의 모든 활동과 활동에 대한 입력 및 출력 데이터 세트가 표시됩니다. 이 기능은 파이프라인에 둘 이상의 작업이 포함된 경우 단일 파이프라인의 운영 계보를 이해하고자 할 때 유용합니다.

    :::image type="content" source="./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png" alt-text="파이프라인 열기 메뉴":::     
2. 다음 예제에서는 입력 및 출력이 있는 파이프라인에서 복사 작업을 볼 수 있습니다. 

    :::image type="content" source="./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png" alt-text="파이프라인 내부의 활동":::
3. 왼쪽 위 모서리의 이동 경로 탐색에서 **Data factory** 링크를 클릭하면 Data factory 홈 페이지로 되돌아갈 수 있습니다.

    :::image type="content" source="./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png" alt-text="데이터 팩터리로 돌아가기":::

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>파이프라인 내부의 각 작업 상태 보기
작업에 의해 생성되는 데이터 세트의 상태를 보면 작업의 현재 상태를 볼 수 있습니다.

**다이어그램** 에서 **OutputBlobTable** 을 두 번 클릭하면 파이프라인 내부에서 실행되는 다양한 작업에 의해 생성되는 모든 조각이 표시됩니다. 복사 작업이 지난 8시간 동안 성공적으로 실행되었고 **준비** 상태의 조각을 생성했다는 것을 확인할 수 있습니다.  

:::image type="content" source="./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png" alt-text="파이프라인 상태":::

데이터 팩터리의 데이터 세트 조각의 상태는 다음 중 하나입니다.

<table>
<tr>
    <th align="left">시스템 상태</th><th align="left">하위 상태</th><th align="left">Description</th>
</tr>
<tr>
    <td rowspan="8">대기 중</td><td>ScheduleTime</td><td>아직 조각이 실행할 시간이 되지 않습니다.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>업스트림 종속성이 준비되지 않습니다.</td>
</tr>
<tr>
<td>ComputeResources</td><td>컴퓨팅 리소스를 사용할 수 없습니다.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>모든 활동 인스턴스는 다른 조각을 실행하고 있습니다.</td>
</tr>
<tr>
<td>ActivityResume</td><td>활동이 일시 중지되어 재개될 때까지 조각을 실행할 수 없습니다.</td>
</tr>
<tr>
<td>재시도</td><td>작업 실행을 다시 시도하는 중입니다.</td>
</tr>
<tr>
<td>유효성 검사</td><td>유효성 검사가 아직 시작되지 않았습니다.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>유효성 감사 다시 시도를 대기 중입니다.</td>
</tr>
<tr>
<tr>
<td rowspan="2">InProgress</td><td>유효성 검사 중</td><td>유효성 검사가 진행 중입니다.</td>
</tr>
<td>-</td>
<td>조각이 처리되고 있습니다.</td>
</tr>
<tr>
<td rowspan="4">실패</td><td>TimedOut</td><td>활동 실행이 활동에서 허용하는 것보다 오래 걸렸습니다.</td>
</tr>
<tr>
<td>취소됨</td><td>이 조각은 사용자 동작으로 취소되었습니다.</td>
</tr>
<tr>
<td>유효성 검사</td><td>유효성 검사가 실패했습니다.</td>
</tr>
<tr>
<td>-</td><td>조각 생성 및 유효성 검사가 실패했습니다.</td>
</tr>
<td>준비</td><td>-</td><td>조각을 사용할 준비가 되었습니다.</td>
</tr>
<tr>
<td>건너뜀</td><td>없음</td><td>조각이 처리되고 있지 않습니다.</td>
</tr>
<tr>
<td>없음</td><td>-</td><td>다른 상태와 함께 존재하기 위해 사용되는 조각이지만, 재설정되었습니다.</td>
</tr>
</table>



**최근에 업데이트된 조각** 블레이드에서 조각 항목을 클릭하면 조각에 대한 세부 사항을 볼 수 있습니다.

:::image type="content" source="./media/data-factory-monitor-manage-pipelines/slice-details.png" alt-text="조각 세부 정보":::

조각이 여러 번 실행된 경우 **활동 실행** 목록에 여러 행이 표시됩니다. **활동 실행** 목록에서 실행 항목을 클릭하면 활동 실행에 대한 세부 사항을 볼 수 있습니다. 목록에 모든 로그 파일과 함께 오류 메시지가 있다면 표시됩니다. 이 기능은 데이터 팩터리를 벗어나지 않고 로그를 보면서 디버그하기에 매우 유용합니다.

:::image type="content" source="./media/data-factory-monitor-manage-pipelines/activity-run-details.png" alt-text="작업 실행 세부 정보":::

조각이 **준비** 상태가 아닌 경우 **준비되지 않은 업스트림** 목록에서 [준비] 상태가 아니고 현재 조각의 실행을 차단하는 업스트림 조각을 확인할 수 있습니다. 이 기능은 조각이 **대기 중** 상태일 때 조각이 기다리고 있는 업스트림 종속성을 이해하려는 경우에 유용합니다.

:::image type="content" source="./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png" alt-text="준비되지 않은 업스트림 조각":::

### <a name="dataset-state-diagram"></a>데이터 세트 상태 다이어그램
데이터 팩터리를 배포하고 파이프라인의 활성 기간이 유효한 경우 데이터 세트 조각은 하나의 상태에서 다른 상태로 전환됩니다. 현재 조각 상태는 다음과 같은 상태 다이어그램을 따릅니다.

:::image type="content" source="./media/data-factory-monitor-manage-pipelines/state-diagram.png" alt-text="상태 다이어그램":::

데이터 팩터리의 데이터 세트 상태 전환 흐름에는 Waiting(대기)-&gt; In-Progress/In-Progress (Validating)(진행 중/진행 중(확인 중)) -&gt; Ready/Failed(준비/실패)가 포함됩니다.

조각은 **Waiting**(대기) 상태로 시작하여 실행 전에 사전 조건이 충족되기를 기다립니다. 그런 다음 작업은 실행을 시작하고 조각은 **In-Progress**(진행 중) 상태가 됩니다. 작업 실행이 성공 또는 실패할 수 있습니다. 조각은 실행 결과에 따라 **Ready**(준비) 또는 **Failed**(실패)로 표시됩니다.

조각은 **Ready**(준비) 또는 **Failed**(실패) 상태에서 **Waiting**(대기 중) 상태로 다시 설정할 수 있습니다. 조각 상태를 **Skip**(건너뛰기)로 표시할 수도 있으며, 이렇게 하면 작업이 실행되지 않고 조각이 처리되지 않습니다.

## <a name="pause-and-resume-pipelines"></a>파이프라인 일시 중지 및 다시 시작
Azure PowerShell을 사용하여 파이프라인을 관리할 수 있습니다. 예를 들어 Azure PowerShell cmdlet을 실행하여 파이프라인을 일시 중지하거나 다시 시작할 수 있습니다. 

> [!NOTE] 
> 다이어그램 보기는 파이프라인 일시 중지 및 다시 시작을 지원하지 않습니다. 사용자 인터페이스를 사용하려는 경우 모니터링 및 관리 애플리케이션을 사용합니다. 애플리케이션 사용에 대한 자세한 내용은 [모니터링 및 관리 앱을 사용하여 데이터 팩터리 파이프라인 모니터링 및 관리](data-factory-monitor-manage-app.md) 문서를 참조하세요. 

**Suspend-AzDataFactoryPipeline** Powershell cmdlet을 사용하여 파이프라인을 일시 중지/일시 중단할 수 있습니다. 이 cmdlet은 문제가 해결될 때까지 파이프라인을 실행하지 않으려는 경우 유용합니다. 

```powershell
Suspend-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
예를 들면 다음과 같습니다.

```powershell
Suspend-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

파이프라인 문제가 해결되고 나면 다음 PowerShell 명령을 실행하여 일시 중단된 파이프라인을 다시 시작할 수 있습니다.

```powershell
Resume-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
예를 들면 다음과 같습니다.

```powershell
Resume-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

## <a name="debug-pipelines"></a>파이프라인 디버깅
Azure Data Factory는 Azure Portal 및 Azure PowerShell을 사용하여 파이프라인 디버그와 문제 해결을 위한 다양한 기능을 제공합니다.

> [!NOTE] 
> 모니터링 및 관리 앱을 사용하여 오류를 해결하기가 훨씬 쉽습니다. 애플리케이션 사용에 대한 자세한 내용은 [모니터링 및 관리 앱을 사용하여 데이터 팩터리 파이프라인 모니터링 및 관리](data-factory-monitor-manage-app.md) 문서를 참조하세요. 

### <a name="find-errors-in-a-pipeline"></a>파이프라인에서 오류 찾기
파이프라인에서 작업 실행이 실패하면 파이프라인에 의해 생성된 데이터 세트는 실패로 인해 오류 상태가 됩니다. 다음과 같은 방법을 사용하여 Azure Data Factory에서 오류의 문제를 해결하고 디버그할 수 있습니다.

#### <a name="use-the-azure-portal-to-debug-an-error"></a>Azure Portal을 사용한 오류 디버그:
1. **테이블** 블레이드에서 **상태** 가 **실패** 로 설정된 문제 조각을 클릭합니다.

   :::image type="content" source="./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png" alt-text="문제 조각이 있는 테이블 블레이드":::
2. **데이터 조각** 블레이드에서 실패한 작업 실행을 클릭합니다.

   :::image type="content" source="./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png" alt-text="오류가 있는 데이터 조각":::
3. **작업 실행 세부 정보** 블레이드에서 HDInsight 처리와 관련된 파일을 다운로드할 수 있습니다. Status/stderr에 대한 **다운로드** 를 클릭하여 오류에 대한 세부 정보를 포함하는 오류 로그 파일을 다운로드합니다.

   :::image type="content" source="./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png" alt-text="오류가 있는 작업 실행 세부 정보 블레이드":::     

#### <a name="use-powershell-to-debug-an-error"></a>PowerShell을 사용한 오류 디버그
1. **PowerShell** 을 시작합니다.
2. **Get-AzDataFactorySlice** 명령을 실행하여 조각과 해당 상태를 표시합니다. 조각의 상태가 **실패** 로 표시됩니다.        

    ```powershell   
    Get-AzDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   예를 들면 다음과 같습니다.

    ```powershell   
    Get-AzDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   **StartDateTime** 을 파이프라인의 시작 시간으로 바꿉니다. 
3. 이제 **Get-AzDataFactoryRun** cmdlet을 실행하여 조각의 작업 실행에 대한 세부 정보를 가져옵니다.

    ```powershell   
    Get-AzDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    예를 들면 다음과 같습니다.

    ```powershell   
    Get-AzDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    StartDateTime 값은 이전 단계에서 기록한 오류/문제 조각의 시작 시간입니다. 날짜-시간은 큰따옴표로 묶어야 합니다.
4. 출력에 오류에 대한 세부 정보가 다음과 같이 표시됩니다.

    ```   
    Id                      : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
    ResourceGroupName       : ADF
    DataFactoryName         : LogProcessingFactory3
    DatasetName               : EnrichedGameEventsTable
    ProcessingStartTime     : 10/10/2014 3:04:52 AM
    ProcessingEndTime       : 10/10/2014 3:06:49 AM
    PercentComplete         : 0
    DataSliceStart          : 5/5/2014 12:00:00 AM
    DataSliceEnd            : 5/6/2014 12:00:00 AM
    Status                  : FailedExecution
    Timestamp               : 10/10/2014 3:04:52 AM
    RetryAttempt            : 0
    Properties              : {}
    ErrorMessage            : Pig script failed with exit code '5'. See wasb://        adfjobs@spestore.blob.core.windows.net/PigQuery
                                    Jobs/841b77c9-d56c-48d1-99a3-
                8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for
                more details.
    ActivityName            : PigEnrichLogs
    PipelineName            : EnrichGameLogsPipeline
    Type                    :
    ```
5. 출력에 표시된 ID 값을 사용하여 **Save-AzDataFactoryLog** cmdlet을 실행하고 이 cmdlet에 **-DownloadLogsoption** 옵션을 사용하여 로그 파일을 다운로드할 수 있습니다.

    ```powershell
    Save-AzDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>파이프라인에서 실패한 항목 다시 실행

> [!IMPORTANT]
> 모니터링 및 관리 앱을 사용하여 오류를 해결하고 실패한 조각을 다시 실행하는 것이 더 쉽습니다. 애플리케이션 사용에 대한 자세한 내용은 [모니터링 및 관리 앱을 사용하여 데이터 팩터리 파이프라인 모니터링 및 관리](data-factory-monitor-manage-app.md)를 참조하세요. 

### <a name="use-the-azure-portal"></a>Azure Portal 사용
파이프라인에서 실패에 대한 문제를 해결하고 디버그한 후에는 오류 조각으로 이동하고 명령 모음의 **실행** 단추를 클릭하여 실패한 항목을 다시 실행할 수 있습니다.

:::image type="content" source="./media/data-factory-monitor-manage-pipelines/rerun-slice.png" alt-text="실패한 조각 다시 실행":::

정책 오류(예: 데이터를 사용할 수 없음)로 인해 조각 유효성 검사에 실패한 경우에는 명령 모음의 **유효성 검사** 단추를 클릭하여 오류를 해결하고 유효성 검사를 다시 할 수 있습니다.

:::image type="content" source="./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png" alt-text="오류 수정 및 유효성 검사":::

### <a name="use-azure-powershell"></a>Azure PowerShell 사용
**Set-AzDataFactorySliceStatus** cmdlet을 사용하여 실패를 다시 실행할 수 있습니다. cmdlet에 대한 구문 및 기타 세부 정보는 [Set-AzDataFactorySliceStatus](/powershell/module/az.datafactory/set-azdatafactoryslicestatus) 항목을 참조하세요.

**예:**

다음 예제에서는 Azure Data Factory 'WikiADF'에서 'DAWikiAggregatedData' 테이블의 모든 조각 상태를 'Waiting'(대기 중)으로 설정합니다.

'UpdateType'이 'UpstreamInPipeline'으로 설정되며 이것은 테이블과 모든 종속(업스트림) 테이블에 대한 각 조각의 상태를 'Waiting'(대기 중)으로 설정합니다. 이 매개 변수에 사용할 수 있는 다른 값은 'Individual'(개별)입니다.

```powershell
Set-AzDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```
## <a name="create-alerts-in-the-azure-portal"></a>Azure Portal에서 경고 만들기

1.  Azure Portal에 로그인하고 **모니터 -> 경고** 를 선택하여 경고 페이지를 엽니다.

    :::image type="content" source="media/data-factory-monitor-manage-pipelines/v1alerts-image1.png" alt-text="경고 페이지를 엽니다.":::

2.  **+ 새로운 경고 규칙** 을 선택하여 새 경고를 만듭니다.

    :::image type="content" source="media/data-factory-monitor-manage-pipelines/v1alerts-image2.png" alt-text="새 경고 만들기":::

3.  **경고 조건** 을 정의합니다. (**리소스 종류별 필터** 필드에서 **데이터 팩터리** 를 선택해야 합니다.) **차원** 값을 지정할 수도 있습니다.

    :::image type="content" source="media/data-factory-monitor-manage-pipelines/v1alerts-image3.png" alt-text="경고 조건 정의 - 대상 선택":::

    :::image type="content" source="media/data-factory-monitor-manage-pipelines/v1alerts-image4.png" alt-text="경고 조건 정의 - 경고 기준 추가":::

    :::image type="content" source="media/data-factory-monitor-manage-pipelines/v1alerts-image5.png" alt-text="경고 조건 정의 - 경고 논리 추가":::

4.  **경고 세부 정보** 를 정의합니다.

    :::image type="content" source="media/data-factory-monitor-manage-pipelines/v1alerts-image6.png" alt-text="경고 세부 정보 정의":::

5.  **작업 그룹** 을 정의합니다.

    :::image type="content" source="media/data-factory-monitor-manage-pipelines/v1alerts-image7.png" alt-text="작업 그룹 정의 - 새 작업 그룹 만들기":::

    :::image type="content" source="media/data-factory-monitor-manage-pipelines/v1alerts-image8.png" alt-text="작업 그룹 정의 - 속성 설정":::

    :::image type="content" source="media/data-factory-monitor-manage-pipelines/v1alerts-image9.png" alt-text="작업 그룹 정의 - 새 작업 그룹이 생성됨":::

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>다른 리소스 그룹 또는 구독으로 데이터 팩터리 이동
Data Factory의 홈 페이지에서 **이동** 명령 모음 단추를 사용하여 다른 리소스 그룹이나 다른 구독으로 데이터 팩터리를 이동할 수 있습니다.

:::image type="content" source="./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png" alt-text="데이터 팩터리 이동":::

데이터 팩터리와 함께 관련된 모든 리소스(예: 데이터 팩터리와 관련된 경고)를 이동할 수도 있습니다.

:::image type="content" source="./media/data-factory-monitor-manage-pipelines/MoveResources.png" alt-text="리소스 이동 대화 상자":::
