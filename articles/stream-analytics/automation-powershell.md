---
title: PowerShell을 사용 하 여 Azure Stream Analytics 자동 일시 중지
description: 이 문서에서는 PowerShell을 사용 하 여 일정에 따라 Azure Stream Analytics 작업을 자동으로 일시 중지 하는 방법을 설명 합니다.
services: stream-analytics
author: fleid
ms.author: fleide
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/03/2021
ms.openlocfilehash: 2829cd9fe5019f8b628a70bbe0aaf73a6868456d
ms.sourcegitcommit: cae9bf0cad514c974c0c0185e24fd4b4b3132432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133407930"
---
# <a name="auto-pause-a-job-with-powershell-and-azure-functions-or-azure-automation"></a>PowerShell 및 Azure Functions 또는 Azure Automation를 사용 하 여 작업 자동 일시 중지

일부 응용 프로그램의 경우에는 [Azure Stream Analytics](/azure/stream-analytics/stream-analytics-introduction) (global.asa)를 사용 하 여 스트림 처리 방법이 필요 하지만 지속적으로 실행 해야 하는 것은 아닙니다. 다양 한 이유는 다음과 같습니다.

- 일정에 도달 하는 입력 데이터 (시간 맨 위에 ...)
- 들어오는 데이터의 스파스 또는 하위 볼륨 (분당 몇 개의 레코드)
- 시간 이동 기능을 활용 하지만 기본적으로 batch에서 실행 되는 비즈니스 프로세스 (재무 또는 HR ...)
- **낮은 규모의 장기 실행 작업** 을 포함 하는 데모, 프로토타입 또는 테스트

이러한 작업을 지속적으로 실행 하지 않을 경우의 이점이 **시간이 지남에 따라** 스트리밍 단위당 Stream Analytics 작업이 [청구](https://azure.microsoft.com/pricing/details/stream-analytics/) 되기 때문에 **비용이 절감** 됩니다.

이 문서에서는 Azure Stream Analytics 작업에 대 한 자동 일시 중지를 설정 하는 방법을 설명 합니다. 여기서는 일정에 따라 작업을 자동으로 일시 중지 하 고 다시 시작 하는 작업을 구성 합니다. **일시 중지** 기간을 사용 하는 경우 청구를 방지 하기 위해 실제 작업 [상태가](/azure/stream-analytics/job-states) **중지** 됩니다.

먼저 전반적인 디자인에 대해 설명한 다음 필요한 구성 요소를 살펴보고, 몇 가지 구현 세부 정보를 살펴보겠습니다.

> [!NOTE]
> 작업을 자동으로 일시 중지 하는 단점이 있습니다. 주는 짧은 대기 시간/실시간 기능의 손실 및 작업이 일시 중지 되는 동안 입력 이벤트 백로그가 자율 증가 하는 것을 허용 하는 잠재적인 위험입니다. 자동 일시 중지는 대규모로 실행 되는 대부분의 프로덕션 시나리오에서 고려 하지 않아야 합니다.

## <a name="design"></a>디자인

이 예에서는 M 분 동안 작업을 일시 중지 하기 전에 N 분 동안 작업을 실행 하려고 합니다. 작업이 일시 중지 되 면 입력 데이터가 사용 되지 않으며 업스트림이 누적 됩니다. 작업이 시작 된 후에는 해당 백로그를 사용 하 여 trickling에서 데이터를 처리 합니다.

![시간이 지남에 따라 자동 일시 중지 된 작업의 동작을 보여 주는 다이어그램](./media/automation/principle.png)

작업을 실행 하는 경우 메트릭이 정상 상태가 될 때까지 작업을 중지 하면 안 됩니다. 관심이 있는 메트릭은 입력 백로그 및 [워터 마크](/azure/stream-analytics/stream-analytics-time-handling#background-time-concepts)입니다. 둘 다 N 분 이상 기준에 있는지 확인 합니다. 이 동작은 다음 두 가지 동작으로 변환 됩니다.

- M 분 후 중지 된 작업이 다시 시작 됨
- 실행 중인 작업은 백로그 및 워터 마크 메트릭이 정상 상태가 되는 즉시 N 분 후에 중지 됩니다.

![작업의 가능한 상태를 보여 주는 다이어그램](./media/automation/States.png)

예를 들어 N = 5 분 및 M = 10 분을 생각해 보겠습니다. 이러한 설정을 사용 하는 경우 15 분 내에 받은 모든 데이터를 처리 하는 데 5 분 이상이 소요 됩니다. 잠재적인 비용 절감은 최대 66%입니다.

작업을 다시 시작 하기 위해 `When Last Stopped` [start 옵션](/azure/stream-analytics/start-job#start-options)을 사용 합니다. 이 옵션을 선택 하면 작업이 중지 된 후에는 업스트림로 백로그 된 모든 이벤트를 처리 합니다. 이 경우 두 가지 주의 사항이 있습니다. 첫째, 작업은 입력 스트림의 보존 기간 보다 오래 중지 된 상태로 유지 되지 않습니다. 하루에 한 번만 작업을 실행 하는 경우 [이벤트 허브 보존 기간이](/azure/event-hubs/event-hubs-faq#what-is-the-maximum-retention-period-for-events-) 1 일 이상 인지 확인 해야 합니다. 두 번째로, 작업을 한 번 이상 시작 해야 해당 모드를 사용할 수 `When Last Stopped` 있습니다 (그렇지 않은 경우에는 그대로 중지 되지 않음). 따라서 작업의 첫 번째 실행은 수동으로 실행 해야 합니다. 그렇지 않으면 해당 사례를 포함 하도록 스크립트를 확장 해야 합니다.

마지막 고려 사항은 이러한 작업을 idempotent 하는 것입니다. 이러한 방식으로 사용 편의성과 복원 력을 모두 사용 하 여 의도 하지 않은 결과 없이 반복할 수 있습니다.

## <a name="components"></a>구성 요소

### <a name="api-calls"></a>API 호출

다음과 같은 **측면** 에서이에 대해 다음과 같은 작업을 수행 해야 합니다.

- **현재 작업 상태 가져오기** (*global.asa 리소스 관리*)
  - 실행 중인 경우
    - **시작 이후 시간 가져오기** (*로그*)
    - **현재 메트릭 값 가져오기** (*메트릭*)
    - 해당 하 **는 경우 작업을 중지 합니다** (*global.asa 리소스 관리*).
  - 중지 된 경우
    - **중지 후 시간 가져오기** (*로그*)
    - 해당 하 **는 경우 작업을 시작 합니다** (*global.asa 리소스 관리*).

이러한 *리소스 관리* 를 위해 [REST API](/rest/api/streamanalytics/), [.net SDK](/dotnet/api/microsoft.azure.management.streamanalytics) 또는 CLI 라이브러리 ([Az CLI](/cli/azure/stream-analytics), [PowerShell](/powershell/module/az.streamanalytics)) 중 하나를 사용할 수 있습니다.

*메트릭* 및 *로그* 의 경우 Azure의 모든 항목은 유사한 API 서피스와 선택 하 여 [Azure Monitor](/azure/azure-monitor/overview)에서 중앙 집중화 됩니다. Api를 쿼리할 때는 로그와 메트릭이 항상 1 ~ 3 분이 됩니다. 따라서 N에서 5를 설정 하는 것은 일반적으로 작업을 실행 하는 데 6 ~ 8 분이 있음을 의미 합니다. 또 다른 고려 사항은 메트릭이 항상 내보내지는 것입니다. 작업이 중지 되 면 API는 빈 레코드를 반환 합니다. 관련 값만 확인 하려면 API 호출의 출력을 정리 해야 합니다.

### <a name="scripting-language"></a>스크립트 언어

이 문서에서는 **PowerShell** 에서 자동 일시 중지를 구현 하기로 결정 했습니다. 첫 번째 이유는 [PowerShell](/powershell/scripting/overview) 이 이제 플랫폼 간입니다. 모든 OS에서 실행할 수 있으므로 배포를 더 쉽게 수행할 수 있습니다. 두 번째 이유는 문자열이 아닌 개체를 사용 하 고 반환 하기 때문입니다. 개체는 자동화 작업을 위한 구문 분석 및 처리를 용이 하 게 합니다.

PowerShell에서 다음과 같이 필요한 모든 항목에 대해 az [. Monitor](/powershell/module/az.monitor/) 및 [Az. Embarks Analytics](/powershell/module/az.streamanalytics/)인 [az PowerShell](/powershell/azure/new-azureps-module-az) 모듈을 사용 합니다.

- 현재 작업 상태의 [AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/get-azstreamanalyticsjob)
- [AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob)  /  [AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob)
- [AzMetric](/powershell/module/az.monitor/get-azmetric) WITH `InputEventsSourcesBacklogged` [(global.asa 메트릭)](/azure/azure-monitor/essentials/metrics-supported#microsoftstreamanalyticsstreamingjobs)
- 로 시작 하는 이벤트 이름에 대 한 [AzActivityLog](/powershell/module/az.monitor/get-azactivitylog)`Stop Job`

### <a name="hosting-service"></a>호스팅 서비스

PowerShell 작업을 호스트 하려면 예약 된 실행을 제공 하는 서비스가 필요 합니다. 여러 가지 옵션이 있지만 서버를 사용 하지 않는 것을 확인 합니다.

- [Azure Functions](/azure/azure-functions/functions-overview)거의 모든 코드 조각을 실행할 수 있는 서버 리스 계산 엔진입니다. 함수는 매 초까지 실행할 수 있는 [타이머 트리거](/azure/azure-functions/functions-bindings-timer?tabs=csharp) 를 제공 합니다.
- [Azure Automation](/azure/automation/overview)운영 클라우드 워크 로드 및 리소스를 위해 빌드된 관리 되는 서비스입니다. 청구서에 적합 하지만, 최소 일정 간격은 1 시간 ( [해결 방법](/azure/automation/shared-resources/schedules#schedule-runbooks-to-run-more-frequently)으로 낮음)입니다.

해결 방법을 고려 하지 않는 경우 Azure Automation 작업을 배포 하는 더 쉬운 방법입니다. 하지만 비교할 수 있도록이 문서에서는 먼저 로컬 스크립트를 작성 합니다. 스크립트를 정상적으로 작동 하는 경우 함수 및 Automation 계정 모두에서 배포 합니다.

### <a name="developer-tools"></a>개발자 도구

[함수](/azure/azure-functions/create-first-function-vs-code-powershell) 및 high에 대해 모두 [vscode](https://code.visualstudio.com/)를 사용 하 여 로컬 [](/azure/stream-analytics/quick-create-visual-studio-code)개발을 권장 합니다. 로컬 IDE를 사용 하면 소스 제어를 사용 하 고 배포를 쉽게 반복할 수 있습니다. 하지만 여기서는 [Azure Portal](https://portal.azure.com)프로세스를 설명 합니다.

## <a name="writing-the-powershell-script-locally"></a>로컬로 PowerShell 스크립트 작성

스크립트를 개발 하는 가장 좋은 방법은 로컬입니다. PowerShell은 플랫폼 간 이므로 모든 OS에서 스크립트를 작성 하 고 테스트할 수 있습니다. Windows [powershell 7](/powershell/scripting/install/installing-powershell-on-windows)및 [Az powershell](/powershell/azure/install-az-ps)과 함께 [Windows 터미널](https://www.microsoft.com/p/windows-terminal/9n0dx20hk701) 를 사용할 수 있습니다.

사용할 마지막 스크립트는 [함수](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/Automation/Auto-pause/run.ps1) (및 [Azure Automation](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/Automation/Auto-pause/runbook.ps1))에 사용할 수 있습니다. 호스팅 환경 (함수 또는 자동화)에 유선으로 연결 된 아래에 설명 된 것과 다릅니다. 이러한 측면은 나중에 설명 하겠습니다. 첫째, **로컬로 실행** 되는 버전을 단계별로 살펴보겠습니다.

이 스크립트는 의도적으로 간단한 형태로 작성 되었으므로 모두 이해할 수 있습니다.

위쪽에서 필수 매개 변수를 설정 하 고 초기 작업 상태를 확인 합니다.

```PowerShell

# Setting variables
$restartThresholdMinute = 10 # This is M
$stopThresholdMinute = 5 # This is N

$maxInputBacklog = 0 # The amount of backlog we tolerate when stopping the job (in event count, 0 is a good starting point)
$maxWatermark = 10 # The amount of watermark we tolerate when stopping the job (in seconds, 10 is a good starting point at low SUs)

$subscriptionId = "<Replace with your Subscription Id - not the name>"
$resourceGroupName = "<Replace with your Resource Group Name>"
$asaJobName = "<Replace with your ASA job name>"

$resourceId = "/subscriptions/$($subscriptionId )/resourceGroups/$($resourceGroupName )/providers/Microsoft.StreamAnalytics/streamingjobs/$($asaJobName)"

# If not already logged, uncomment and run the 2 following commands
# Connect-AzAccount
# Set-AzContext -SubscriptionId $subscriptionId

# Check current ASA job status
$currentJobState = Get-AzStreamAnalyticsJob  -ResourceGroupName $resourceGroupName -Name $asaJobName | Foreach-Object {$_.JobState}
Write-Output "asaRobotPause - Job $($asaJobName) is $($currentJobState)."

```

작업을 실행 하는 경우 작업이 최소 N 분, 해당 백로그 및 워터 마크를 실행 하 고 있는지 확인 합니다.

```PowerShell

# Switch state
if ($currentJobState -eq "Running")
{
    # First we look up the job start time with Get-AzActivityLog
    ## Get-AzActivityLog issues warnings about deprecation coming in future releases, here we ignore them via -WarningAction Ignore
    ## We check in 1000 record of history, to make sure we're not missing what we're looking for. It may need adjustment for a job that has a lot of logging happening.
    ## There is a bug in Get-AzActivityLog that triggers an error when Select-Object First is in the same pipeline (on the same line). We move it down.
    $startTimeStamp = Get-AzActivityLog -ResourceId $resourceId -MaxRecord 1000 -WarningAction Ignore | Where-Object {$_.EventName.Value -like "Start Job*"}
    $startTimeStamp = $startTimeStamp | Select-Object -First 1 | Foreach-Object {$_.EventTimeStamp}

    # Then we gather the current metric values
    ## Get-AzMetric issues warnings about deprecation coming in future releases, here we ignore them via -WarningAction Ignore
    $currentBacklog = Get-AzMetric -ResourceId $resourceId -TimeGrain 00:01:00 -MetricName "InputEventsSourcesBacklogged" -DetailedOutput -WarningAction Ignore
    $currentWatermark = Get-AzMetric -ResourceId $resourceId -TimeGrain 00:01:00 -MetricName "OutputWatermarkDelaySeconds" -DetailedOutput -WarningAction Ignore

    # Metric are always lagging 1-3 minutes behind, so grabbing the last N minutes means checking N+3 actually. This may be overly safe and fined tune down per job.
    $Backlog =  $currentBacklog.Data |
                    Where-Object {$_.Maximum -ge 0} | # We remove the empty records (when the job is stopped or starting)
                    Sort-Object -Property Timestamp -Descending |
                    Where-Object {$_.Timestamp -ge $startTimeStamp} | # We only keep the records of the latest run
                    Select-Object -First $stopThresholdMinute | # We take the last N records
                    Measure-Object -Sum Maximum # We sum over those N records
    $BacklogSum = $Backlog.Sum

    $Watermark = $currentWatermark.Data |
                    Where-Object {$_.Maximum -ge 0} |
                    Sort-Object -Property Timestamp -Descending |
                    Where-Object {$_.Timestamp -ge $startTimeStamp} |
                    Select-Object -First $stopThresholdMinute |
                    Measure-Object -Average Maximum # Here we average
    $WatermarkAvg = [int]$Watermark.Average # Rounding the decimal value casting it to integer

    # Since we called Get-AzMetric with a TimeGrain of a minute, counting the number of records gives us the duration in minutes
    Write-Output "asaRobotPause - Job $($asaJobName) is running since $($startTimeStamp) with a sum of $($BacklogSum) backlogged events, and an average watermark of $($WatermarkAvg) sec, for $($Watermark.Count) minutes."

    # -le for lesser or equal, -ge for greater or equal
    if (
        ($BacklogSum -ge 0) -and ($BacklogSum -le $maxInputBacklog) -and ` # is not null and is under the threshold
        ($WatermarkAvg -ge 0) -and ($WatermarkAvg -le $maxWatermark) -and ` # is not null and is under the threshold
        ($Watermark.Count -ge $stopThresholdMinute) # at least N values
        )
    {
        Write-Output "asaRobotPause - Job $($asaJobName) is stopping..."
        Stop-AzStreamAnalyticsJob -ResourceGroupName $resourceGroupName -Name $asaJobName
    }
    else {
        Write-Output "asaRobotPause - Job $($asaJobName) is not stopping yet, it needs to have less than $($maxInputBacklog) backlogged events and under $($maxWatermark) sec watermark for at least $($stopThresholdMinute) minutes."
    }
}

```

작업이 중지 된 경우 마지막 "작업 중지" 작업 인 경우 로그를 확인 합니다.

```PowerShell

elseif ($currentJobState -eq "Stopped")
{
    # First we look up the job start time with Get-AzActivityLog
    ## Get-AzActivityLog issues warnings about deprecation coming in future releases, here we ignore them via -WarningAction Ignore
    ## We check in 1000 record of history, to make sure we're not missing what we're looking for. It may need adjustment for a job that has a lot of logging happening.
    ## There is a bug in Get-AzActivityLog that triggers an error when Select-Object First is in the same pipeline (on the same line). We move it down.
    $stopTimeStamp = Get-AzActivityLog -ResourceId $resourceId -MaxRecord 1000 -WarningAction Ignore | Where-Object {$_.EventName.Value -like "Stop Job*"}
    $stopTimeStamp = $stopTimeStamp | Select-Object -First 1 | Foreach-Object {$_.EventTimeStamp}

    # Get-Date returns a local time, we project it to the same time zone (universal) as the result of Get-AzActivityLog that we extracted above
    $minutesSinceStopped = ((Get-Date).ToUniversalTime()- $stopTimeStamp).TotalMinutes

    # -ge for greater or equal
    if ($minutesSinceStopped -ge $restartThresholdMinute)
    {
        Write-Output "asaRobotPause - Job $($jobName) was paused $([int]$minutesSinceStopped) minutes ago, set interval is $($restartThresholdMinute), it is now starting..."
        Start-AzStreamAnalyticsJob -ResourceGroupName $resourceGroupName -Name $asaJobName -OutputStartMode LastOutputEventTime
    }
    else{
        Write-Output "asaRobotPause - Job $($jobName) was paused $([int]$minutesSinceStopped) minutes ago, set interval is $($restartThresholdMinute), it will not be restarted yet."
    }
}
else {
    Write-Output "asaRobotPause - Job $($jobName) is not in a state I can manage: $($currentJobState). Let's wait a bit, but consider helping is that doesn't go away!"
}
```

결국 작업 완료를 기록 합니다.

```PowerShell

# Final ASA job status check
$newJobState = Get-AzStreamAnalyticsJob  -ResourceGroupName $resourceGroupName -Name $asaJobName | Foreach-Object {$_.JobState}
Write-Output "asaRobotPause - Job $($asaJobName) was $($currentJobState), is now $($newJobState). Job completed."

```

## <a name="option-1-hosting-the-task-in-azure-functions"></a>옵션 1: Azure Functions에서 작업 호스팅

참조를 위해 Azure Functions 팀은 포괄적인 [PowerShell 개발자 가이드](/azure/azure-functions/functions-reference-powershell?tabs=portal)를 유지 관리 합니다.

먼저 새 **함수 앱** 필요 합니다. 함수 앱은 여러 함수를 호스트할 수 있는 솔루션과 비슷합니다.

전체 절차는 [여기에](/azure/azure-functions/functions-create-function-app-portal#create-a-function-app)있지만, [여기서는 Azure Portal](https://portal.azure.com)이동하여 다음을 사용하여 새 함수 앱을 만듭니다.

- 게시: **코드**
- 런타임: **PowerShell Core**
- 버전: **7 이상**

프로비전되면 전체 구성부터 시작하겠습니다.

### <a name="managed-identity-for-azure-functions"></a>Azure Functions 관리 ID

함수에는 ASA 작업을 시작하고 중지할 수 있는 권한이 필요합니다. 관리 ID 를 통해 이러한 권한을 [할당합니다.](/azure/active-directory/managed-identities-azure-resources/overview)

첫 번째 단계는 해당 [프로시저에](/azure/app-service/overview-managed-identity?toc=%2Fazure%2Fazure-functions%2Ftoc.json&tabs=dotnet#using-the-azure-portal)따라 함수에 **대해 시스템 할당 관리 ID를** 사용하도록 설정하는 것입니다.

이제 자동 일시 중지하려는 ASA 작업에 대해 해당 ID에 대한 올바른 권한을 부여할 수 있습니다. 이를 위해 **ASA** 작업(함수가 아닌)에 대한 포털의 **액세스 제어(IAM)** 에서 위의 함수 이름을 선택하여 *관리 ID* 형식의 멤버에 대한 역할 *기여자에게* **역할 할당을** 추가합니다.

![ASA 작업에 대한 IAM 설정의 스크린샷](./media/automation/function-asa-role.png)

PowerShell 스크립트에서 관리 ID가 제대로 설정되었는지 확인하는 검사를 추가할 수 있습니다(최종 스크립트는 [여기에서](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/Automation/Auto-pause/run.ps1)사용 가능).

```PowerShell

# Check if managed identity has been enabled and granted access to a subscription, resource group, or resource
$AzContext = Get-AzContext -ErrorAction SilentlyContinue
if (-not $AzContext.Subscription.Id)
{
    Throw ("Managed identity is not enabled for this app or it has not been granted access to any Azure resources. Please see /azure/app-service/overview-managed-identity for additional details.")
}

```

또한 함수가 작동하도록 몇 가지 로깅 정보를 추가합니다.

```PowerShell

$currentUTCtime = (Get-Date).ToUniversalTime()

# Write an information log with the current time.
Write-Host "asaRobotPause - PowerShell timer trigger function is starting at time: $currentUTCtime"

```

### <a name="parameters-for-azure-functions"></a>Azure Functions 매개 변수

Functions의 스크립트에 매개 변수를 전달하는 가장 좋은 방법은 함수 앱 애플리케이션 설정을 [환경 변수로](/azure/azure-functions/functions-reference-powershell?tabs=portal#environment-variables)사용하는 것입니다.

이렇게 하려면 첫 번째 단계는 함수 앱 페이지에서 해당 [프로시저](/azure/azure-functions/functions-how-to-use-azure-function-app-settings?tabs=portal#settings)다음에 매개 변수를 **App 설정** 정의하는 것입니다. 다음이 필요합니다.

|속성|값|
|-|-|
|maxInputBacklog|작업을 중지할 때 허용되는 백로그의 양(이벤트 수에서 0은 좋은 시작점)입니다.|
|maxWatermark|작업을 중지할 때 허용되는 워터마크 양(초 단위, 10은 낮은US에서 좋은 시작점)|
|restartThresholdMinute|M: 중지된 작업이 다시 시작될 때까지의 시간(분)|
|stopThresholdMinute|N: 실행 중인 작업이 중지될 때까지의 냉각 시간(분)입니다. 입력 백로그는 해당 시간 동안 0으로 유지되어야 합니다.|
|subscriptionId|자동 일시 중지할 ASA 작업의 SubscriptionId(이름 제외)|
|resourceGroupName|자동 일시 중지할 ASA 작업의 리소스 그룹 이름|
|asaJobName|aut-paused할 ASA 작업의 이름|

나중에 변수를 로드하도록 PowerShell 스크립트를 업데이트해야 합니다.

```PowerShell
$maxInputBacklog = $env:maxInputBacklog
$maxWatermark = $env:maxWatermark

$restartThresholdMinute = $env:restartThresholdMinute
$stopThresholdMinute = $env:stopThresholdMinute

$subscriptionId = $env:subscriptionId
$resourceGroupName = $env:resourceGroupName
$asaJobName = $env:asaJobName

```

### <a name="powershell-module-requirements"></a>PowerShell 모듈 요구 사항

ASA 명령(예: )을 사용하기 위해 Az PowerShell을 로컬로 설치해야 했던 것과 동일한 방식으로 `Start-AzStreamAnalyticsJob` [함수 앱 호스트에 추가해야](/azure/azure-functions/functions-reference-powershell?tabs=portal#dependency-management)합니다.

이렇게 하려면 함수 앱 페이지로 이동하여 `Functions`  >  `App files` 를 선택하고 `requirements.psd1` 줄의 압축을 풀 수 `'Az' = '6.*'` 있습니다. 해당 변경이 적용하려면 전체 앱을 다시 시작해야 합니다.

![함수 앱에 대한 앱 파일 설정의 스크린샷](./media/automation/function-app-files.png)

### <a name="creating-the-function"></a>함수 만들기

모든 구성이 완료되면 함수 앱 내에서 스크립트를 실행하는 특정 함수를 만들 수 있습니다.

타이머에서 트리거되는 함수인 포털에서 개발할 `0 */1 * * * *` *것입니다(1분마다 0초에*"로 [읽는](/azure/azure-functions/functions-bindings-timer?tabs=csharp#ncrontab-expressions) 1분마다").

![함수 앱에서 새 타이머 트리거 함수를 만드는 스크린샷](./media/automation/new-function-timer.png)

필요한 경우 일정을 업데이트하여 에서 타이머 값을 변경할 수 있습니다. `Integration`

![함수의 통합 설정 스크린샷](./media/automation/function-timer.png)

그런 다음, 에서 `Code + Test` 스크립트를 `run.ps1` 복사하고 테스트할 수 있습니다. 전체 스크립트는 [여기에서](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/Automation/Auto-pause/run.ps1)복사할 수 있습니다. 비즈니스 논리는 TRY/CATCH 문으로 이동되어 처리 중에 오류가 발생하는 경우 적절한 오류를 생성합니다.

![함수에 대한 Code+Test의 스크린샷](./media/automation/function-code.png)

창에서 **테스트/실행을** 통해 모든 것이 잘 실행되는지 확인할 수 `Code + Test` 있습니다. 창을 볼 수도 `Monitor` 있지만 항상 몇 번의 실행이 늦습니다.

![성공한 실행의 출력 스크린샷](./media/automation/function-run.png)

### <a name="setting-an-alert-on-the-function-execution"></a>함수 실행에 대한 경고 설정

마지막으로 함수가 성공적으로 실행되지 않으면 경고를 통해 알림을 받습니다. 경고는 약간의 비용이 들지만 비용이 많이 드는 상황을 방지할 수 있습니다.

함수 **앱** 페이지의 에서 `Logs` 지난 5분 동안 성공하지 못한 모든 실행을 반환하는 다음 쿼리를 실행합니다.

```SQL
requests
| where success == false
| where timestamp > ago(5min)
| summarize failedCount=sum(itemCount) by operation_Name
| order by failedCount desc
```

쿼리 편집기에서 를 `New alert rule` 선택합니다. 다음 화면에서 **측정값을** 다음과 같이 정의합니다.

- 측정값: failedCount
- 집계 형식: 총계
- 집계 세분성: 5분

다음으로 **경고 논리를** 다음과 같이 설정합니다.

- 연산자: 초과
- 임계값: 0
- 평가 빈도: 5분

이 위치에서 새 [작업 그룹을](/azure/azure-monitor/alerts/action-groups?WT.mc_id=Portal-Microsoft_Azure_Monitoring)다시 사용하거나 만든 다음 구성을 완료합니다.

경고가 제대로 설정되어 있는지 확인하기 위해 `throw "Testing the alert"` PowerShell 스크립트의 아무 곳에나 추가하고 5분 동안 기다렸다가 이메일을 받을 수 있습니다.

## <a name="option-2-hosting-the-task-in-azure-automation"></a>옵션 2: Azure Automation 작업 호스팅

먼저 새 **Automation 계정** 가 필요합니다. Automation 계정은 여러 Runbook을 호스트할 수 있는 솔루션과 비슷합니다.

절차는 [여기에 있습니다.](/azure/automation/quickstarts/create-account-portal) 여기서는 탭에서 직접 시스템 할당 관리 ID를 사용하도록 선택할 수 `advanced` 있습니다.

참고로 Automation 팀에는 PowerShell Runbook을 시작하는 [데 적합한 자습서가](/azure/automation/learn/powershell-runbook-managed-identity) 있습니다.

### <a name="parameters-for-azure-automation"></a>Azure Automation 매개 변수

Runbook을 사용하면 PowerShell의 클래식 매개 변수 구문을 사용하여 인수를 전달할 수 있습니다.

```PowerShell
Param(
    [string]$subscriptionId,
    [string]$resourceGroupName,
    [string]$asaJobName,

    [int]$restartThresholdMinute,
    [int]$stopThresholdMinute,

    [int]$maxInputBacklog,
    [int]$maxWatermark
)
```

### <a name="managed-identity-for-azure-automation"></a>Azure Automation 관리 ID

Automation 계정은 프로비전 중에 관리 ID를 받아야 합니다. 그러나 필요한 경우 해당 [프로시저를](/azure/automation/enable-managed-identity-for-automation)사용하여 사용하도록 설정할 수 있습니다.

함수와 마찬가지로 자동 일시 중지하려는 ASA 작업에 대한 올바른 권한을 부여해야 합니다.

이를 위해 Automation 페이지가 아닌 **ASA 작업에** 대한 포털의 **액세스 제어(IAM)에서** 위의 Automation 계정 이름을 선택하여 *관리 ID* 유형의 멤버에 대한 역할 *기여자에게* **역할 할당을** 추가합니다.

![ASA 작업에 대한 IAM 설정의 스크린샷](./media/automation/function-asa-role.png)

PowerShell 스크립트에서 관리 ID가 제대로 설정되었는지 확인하는 검사를 추가할 수 있습니다(최종 스크립트는 [여기에서](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/Automation/Auto-pause/runbook.ps1)사용 가능).

```PowerShell
# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process | Out-Null

# Connect using a Managed Service Identity
try {
        $AzureContext = (Connect-AzAccount -Identity).context
    }
catch{
        Write-Output "There is no system-assigned user identity. Aborting.";
        exit
    }
```

### <a name="creating-the-runbook"></a>Runbook 만들기

구성이 완료되면 스크립트를 실행할 특정 Runbook을 Automation 계정 내에 만들 수 있습니다. 여기서는 Az PowerShell을 요구 사항으로 추가할 필요가 없으며 이미 기본 제공되어 있습니다.

포털의 프로세스 자동화에서 `Runbooks` 를 선택한 다음, `Create a runbook` Runbook 유형으로 를 선택하고 `PowerShell` 위의 버전을 `7` 버전으로 선택합니다(현재 `7.1 (preview)` ).

이제 스크립트를 붙여넣고 테스트할 수 있습니다. 전체 스크립트는 [여기에서](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/Automation/Auto-pause/runbook.ps1)복사할 수 있습니다. 비즈니스 논리는 TRY/CATCH 문으로 이동되어 처리 중에 오류가 발생하는 경우 적절한 오류를 생성합니다.

![Azure Automation Runbook 스크립트 편집기의 스크린샷](./media/automation/automation-code.png)

에서 모든 것이 제대로 연결되어 있는지 확인할 수 `Test Pane` 있습니다.

그 후에는 `Publish` Runbook을 일정에 연결할 수 있는 작업이 필요합니다. 일정 만들기 및 연결은 여기서는 설명되지 않는 간단한 프로세스입니다. 이제 1시간 미만의 일정 [간격을](/azure/automation/shared-resources/schedules#schedule-runbooks-to-run-more-frequently) 달성할 수 있는 해결 방법이 있다는 점을 기억해 두는 것이 좋습니다.

마지막으로 경고를 설정할 수 있습니다. 첫 번째 단계는 Automation 계정의 [진단 설정을](/azure/azure-monitor/essentials/diagnostic-settings?tabs=CMD#create-in-azure-portal) 통해 로그를 사용하도록 설정하는 것입니다. 두 번째 단계는 함수에 대해 했던 것처럼 쿼리를 통해 오류를 캡처하는 것입니다.

## <a name="outcome"></a>결과

ASA 작업을 살펴보면 모든 것이 두 곳에서 예상대로 실행되는 것을 볼 수 있습니다.

활동 로그에서 다음을 수행합니다.

![ASA 작업의 로그 스크린샷](./media/automation/asa-logs.png)

그리고 메트릭을 통해 다음을 수행합니다.

![ASA 작업의 메트릭 스크린샷](./media/automation/asa-metrics.png)

스크립트가 이해되면 간단하게 다시 작업하여 범위를 확장할 수 있습니다. 단일 작업 목록이 아닌 작업 목록을 대상으로 지정하도록 쉽게 업데이트할 수 있습니다. 더 큰 범위는 태그, 리소스 그룹 또는 전체 구독을 통해 정의하고 처리할 수 있습니다.

## <a name="get-support"></a>지원 받기

추가 지원이 필요한 경우 [Azure Stream Analytics용 Microsoft Q&A 질문 페이지](/answers/topics/azure-stream-analytics.html)를 참조하세요.

## <a name="next-steps"></a>다음 단계

PowerShell을 사용하여 Azure Stream Analytics 작업 관리를 자동화하는 기본 사항도 배웠습니다. 자세히 알아보려면 다음 아티클을 참조하세요.

- [Azure Stream Analytics 소개](stream-analytics-introduction.md)
- [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
- [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics Management .NET SDK](/previous-versions/azure/dn889315(v=azure.100))
- [Azure  Stream Analytics 쿼리 언어 참조](/stream-analytics-query/stream-analytics-query-language-reference)
- [Azure Stream Analytics 관리 REST API 참조](/rest/api/streamanalytics/)
