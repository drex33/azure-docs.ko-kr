---
title: PowerShell을 사용하여 Log Analytics 만들기 및 구성
description: Azure Monitor의 Log Analytics 작업 영역은 온-프레미스 또는 클라우드 인프라에 있는 서버의 데이터를 저장합니다. Azure 진단에 의해 생성된 경우에 Azure 스토리지에서 머신 데이터를 수집할 수 있습니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/20/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fb4f053c0bab838467ebce742cafdc909d4ce5c9
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130219873"
---
# <a name="create-and-configure-a-log-analytics-workspace-in-azure-monitor-using-powershell"></a>PowerShell을 사용하여 Azure Monitor에서 Log Analytics 작업 영역 만들기 및 구성
이 문서에서는 Azure Monitor에서 Log Analytics 작업 영역을 만들고 구성하는 방법을 보여 줍니다.  


> [!NOTE]
> Log Analytics는 이전에 Operational Insights라고 했기 때문에 cmdlet에서는 Operational Insights라고 합니다.


## <a name="prerequisites"></a>사전 요구 사항
예제는 Az.OperationalInsights 모듈 버전 1.0.0 이상에서 작동합니다.

## <a name="create-workspace"></a>작업 영역 만들기
다음 샘플 스크립트는 데이터 원본 구성 없이 작업 영역을 만듭니다. 

```powershell
$ResourceGroup = "my-resource-group"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique in resource group - Get-Random helps with this for the example code
$Location = "westeurope"

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
```

## <a name="create-workspace-and-configure-data-sources"></a>작업 영역 만들기 및 데이터 원본 구성

다음 샘플 스크립트는 작업 영역을 만들고 여러 데이터 원본을 구성합니다. 데이터 원본은 [Log Analytics 에이전트](../agents/log-analytics-agent.md)를 사용하여 가상 머신을 모니터링하는 경우에만 필요합니다.

스크립트는 다음 작업을 수행합니다.

1. 작업 영역 만들기
2. 사용 가능한 솔루션 나열
3. 작업 영역에 솔루션 추가
4. 저장된 검색 가져오기
5. 저장된 검색 내보내기
6. 컴퓨터 그룹 만들기
7. Windows 에이전트가 설치된 컴퓨터에서 IIS 로그 수집 활성화
8. Linux 컴퓨터에서 논리 디스크 성능 카운터 수집(사용된 Inode 비율, 사용 가능한 MB, 사용된 공간 비율, 초당 디스크 전송, 초당 디스크 읽기, 초당 디스크 쓰기)
9. Linux 컴퓨터에서 syslog 이벤트 수집
10. Windows 컴퓨터에서 애플리케이션 이벤트 로그의 오류 및 경고 이벤트 수집
11. Windows 컴퓨터에서 사용 가능한 메모리(MB) 성능 카운터 수집
12. 사용자 지정 로그 수집

```powershell
$ResourceGroup = "my-resource-group"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique in resource group - Get-Random helps with this for the example code
$Location = "westeurope"

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Perf | where ObjectName == "LogicalDisk" and CounterName == "Current Disk Queue Length" and InstanceName == "C:"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1",
    "description": "Example custom log datasource",
    "inputs": [
        {
            "location": {
            "fileSystemLocations": {
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ],
                "linuxFileTypeLogPaths": [ "/var/logs" ]
                }
            },
        "recordDelimiter": {
            "regexDelimiter": {
                "pattern": "\\n",
                "matchIndex": 0,
                "matchIndexSpecified": true,
                "numberedGroup": null
                }
            }
        }
    ],
    "extractions": [
        {
            "extractionName": "TimeGenerated",
            "extractionType": "DateTime",
            "extractionProperties": {
                "dateTimeExtraction": {
                    "regex": null,
                    "joinStringRegex": null
                    }
                }
            }
        ]
    }
"@

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

# List enabled solutions
(Get-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json

# Create Computer Group based on a query
New-AzOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Enable IIS Log Collection using agent
Enable-AzOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzOperationalInsightsLinuxPerformanceCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernel syslog collection"
Enable-AzOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs

New-AzOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```

> [!NOTE]
> 사용자 지정 로그의 구성을 정의하는 **CustomLogRawJson** 매개 변수의 형식은 복잡할 수 있습니다. [Get-AzOperationalInsightsDataSource](/powershell/module/az.operationalinsights/get-azoperationalinsightsdatasource)를 사용하여 기존 사용자 지정 로그의 구성을 가져옵니다. **Properties** 속성은 **CustomLogRawJson** 매개 변수에 필요한 구성입니다.

위의 예에서 regexDelimiter는 줄 바꿈에 대한 “\\n”으로 정의되었습니다. 로그 구분 기호는 타임스탬프일 수도 있습니다.  지원되는 형식은 다음과 같습니다.

| 형식 | Json RegEx 형식에서는 표준 RegEx에서 모든 `\`에 대해 두 개의 `\\`를 사용하므로 RegEx 앱에서 테스트하는 경우 `\\`를 `\`로 줄입니다. |
| --- | --- |
| `YYYY-MM-DD HH:MM:SS` | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` |
| `M/D/YYYY HH:MM:SS AM/PM` | `(([0-1]\\d)|[0-9])/(([0-3]\\d)|(\\d))/((\\d{2})|(\\d{4}))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]\\s(AM|PM|am|pm)` |
| `dd/MMM/yyyy HH:MM:SS` | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|May|Apr|Jul|Jun|Aug|Oct|Sep|Nov|Dec|jan|feb|mar|may|apr|jul|jun|aug|oct|sep|nov|dec)\\/((19|20)[0-9][0-9]))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9])` |
| `MMM dd yyyy HH:MM:SS` | `(((?:Jan(?:uary)?|Feb(?:ruary)?|Mar(?:ch)?|Apr(?:il)?|May|Jun(?:e)?|Jul(?:y)?|Aug(?:ust)?|Sep(?:tember)?|Sept|Oct(?:ober)?|Nov(?:ember)?|Dec(?:ember)?)).*?((?:(?:[0-2]?\\d{1})|(?:[3][01]{1})))(?![\\d]).*?((?:(?:[1]{1}\\d{1}\\d{1}\\d{1})|(?:[2]{1}\\d{3})))(?![\\d]).*?((?:(?:[0-1][0-9])|(?:[2][0-3])|(?:[0-9])):(?:[0-5][0-9])(?::[0-5][0-9])?(?:\\s?(?:am|AM|pm|PM))?))` |
| `yyMMdd HH:mm:ss` | `([0-9]{2}([0][1-9]|[1][0-2])([0-2][0-9]|[3][0-1])\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` |
| `ddMMyy HH:mm:ss` | `(([0-2][0-9]|[3][0-1])([0][1-9]|[1][0-2])[0-9]{2}\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s?([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0-1]?[0-9]|[2][0-3]):([0-5][0-9]):([0-5][0-9])` |
| `MMM  d HH:mm:ss` <br> MMM 뒤에 두 개의 공백 | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` |
| `dd/MMM/yyyy:HH:mm:ss +zzzz` <br> 여기서 +는 + 또는 - <br> 여기서 zzzz 시간 오프셋 | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\/((19|20)[0-9][0-9]):([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])\\s[\\+|\\-][0-9]{4})` |
| `yyyy-MM-ddTHH:mm:ss` <br> T는 리터럴 문자 T | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))T((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` |

## <a name="troubleshooting"></a>문제 해결
지난 14일 동안 삭제되어 [일시 삭제 상태](../logs/delete-workspace.md#soft-delete-behavior)인 작업 영역을 만들면 작업 영역 구성에 따라 작업의 결과가 달라질 수 있습니다.
1. 삭제된 작업 영역과 작업 영역 이름, 리소스 그룹, 구독 및 지역이 동일한 경우 해당 데이터, 구성 및 연결된 에이전트를 포함한 작업 영역이 복구됩니다.
2. 작업 영역 이름은 리소스 그룹당 고유해야 합니다. 리소스 그룹의 일시 삭제에서도 이미 존재하는 작업공간 이름을 사용하는 경우 *작업공간 이름 'workspace-name'이 고유하지 않습니다* 또는 *충돌* 이라는 오류가 발생합니다. 일시 삭제를 재정의하고 작업 영역을 영구적으로 삭제하고 같은 이름으로 새 작업 영역을 만들려면 다음 단계를 수행하여 작업 영역을 먼저 복구한 후 영구 삭제를 수행합니다.
   * [작업 영역](../logs/delete-workspace.md#recover-workspace)을 복구합니다.
   * 작업 영역을 [영구 삭제](../logs/delete-workspace.md#permanent-workspace-delete)합니다.
   * 동일한 작업 영역 이름을 사용하여 새 작업 영역을 만듭니다.


## <a name="next-steps"></a>다음 단계
* [Log Analytics PowerShell Cmdlet 검토](/powershell/module/az.operationalinsights/) 를 참조하세요.
