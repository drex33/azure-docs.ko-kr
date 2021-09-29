---
title: Azure Purview를 모니터링하는 방법
description: Azure Monitor를 사용하여 Azure Purview 메트릭, 경고 및 진단 설정을 구성하는 방법에 대해 알아봅니다.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: 7028bb42481c2559d7aa9d044bd25771efc41f31
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129213739"
---
# <a name="azure-purview-metrics-in-azure-monitor"></a>Azure Monitor의 Azure Purview 메트릭

이 문서에서는 Azure Monitor를 사용하여 Azure Purview 메트릭, 경고 및 진단 설정을 구성하는 방법을 설명합니다.

## <a name="monitor-azure-purview"></a>Azure Purview 모니터링

Azure Purview 관리자는 Azure Monitor를 사용하여 Purview 계정의 작동 상태를 추적할 수 있습니다. 메트릭은 잠재적 문제를 추적하고, 문제를 해결하고, Purview 계정의 안정성을 향상할 수 있는 데이터 요소를 제공하기 위해 수집됩니다. Azure Purview에서 발생하는 이벤트의 메트릭은 Azure monitor로 전송됩니다.

## <a name="aggregated-metrics"></a>집계 메트릭

Purview 계정의 메트릭은 Azure Portal에서 액세스할 수 있습니다. Purview 계정의 메트릭에 대한 액세스는 역할 할당으로 제어됩니다. 사용자가 메트릭을 보려면 Azure Purview의 "모니터링 읽기 권한자" 역할의 일부여야 합니다. 역할 액세스 수준에 대한 자세한 내용은 [모니터링 읽기 권한자 역할 사용 권한](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles)을 참조하세요.

Purview 계정을 만든 사람에게는 메트릭을 볼 수 있는 권한이 자동으로 부여됩니다. 다른 사용자가 메트릭을 보려면 다음 단계를 수행하여 **모니터링 읽기 권한자** 역할에 해당 메트릭을 추가합니다.

### <a name="add-a-user-to-the-monitoring-reader-role"></a>모니터링 읽기 권한자 역할에 사용자 추가

**모니터링 읽기 권한자** 역할에 사용자를 추가하려면, Purview 계정 소유자 또는 구독 소유자가 다음 단계를 수행할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)로 이동하여 Azure Purview 계정 이름을 검색합니다.

2. **액세스 제어(IAM)** 를 선택합니다.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/access-iam.png" alt-text="IAM에 액세스하는 방법을 보여 주는 스크린샷":::

3. **역할 할당 추가** 를 선택합니다.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-role-assignment.png" alt-text="역할 할당을 추가하는 방법을 보여 주는 스크린샷":::

4. **모니터링 읽기 권한자** 역할을 선택하고 **Azure AD 사용자, 그룹 또는 서비스 주체** 로 액세스 할당을 설정합니다. 그리고 메트릭에 액세스하는 AAD 계정을 할당합니다.  

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-monitoring-reader.png" alt-text="모니터링 읽기 권한자 역할을 추가하는 방법을 보여 주는 스크린샷":::

## <a name="metrics-visualization"></a>메트릭 시각화

**모니터링 읽기 권한자** 역할의 사용자는 Azure Monitor로 전송된 집계 메트릭 및 진단 로그를 볼 수 있습니다. 해당 Purview 계정의 메트릭은 Azure Portal에 나열됩니다. Azure Portal에서 메트릭 섹션을 선택하여 사용 가능한 모든 메트릭의 목록을 표시합니다.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/purview-metrics.png" alt-text="사용 가능한 Purview 메트릭 섹션을 보여 주는 스크린샷" lightbox="./media/how-to-monitor-with-azure-monitor/purview-metrics.png":::

Azure Purview 사용자는 Azure Purview 계정의 관리 센터에서 직접 메트릭 페이지에 액세스할 수도 있습니다. Purview management center의 기본 페이지에서 관리 센터를 선택하여 Azure Portal를 시작합니다.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png" alt-text="관리 센터에서 Purview 메트릭을 시작하는 스크린샷" lightbox="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png":::

### <a name="available-metrics"></a>사용 가능한 메트릭

Azure Portal에서 메트릭 섹션을 사용하는 방법에 대한 자세한 내용을 보려면 다음 두 문서를 미리 읽으십시오. [메트릭 탐색기로 시작하기](../azure-monitor/essentials/metrics-getting-started.md) 및 [메트릭 탐색기의 고급 기능](../azure-monitor/essentials/metrics-charts.md).

다음 표에는 Azure Portal에서 탐색할 수 있는 메트릭 목록이 포함되어 있습니다.

| 메트릭 이름 | 메트릭 네임스페이스 | 집계 유형 | Description |
| ------------------- | ------------------- | ------------------- | ----------------- |
| 취소된 검사 | 자동화된 검사 | 합계 <br> 개수 | 기간에 따라 취소된 데이터 원본 검색 집계 |
| 완료된 검사 | 자동화된 검사 | 합계 <br> 개수 | 일정 기간에 완료된 데이터 원본 검사 집계 |
| 실패한 검사 | 자동화된 검사 | 합계 <br> 개수 | 일정 기간에 실패한 데이터 원본 검사 집계 |
| 소요된 검사 시간 | 자동화된 검사 | 최소값 <br> 최대값 <br> 합계 <br> 평균 | 일정 기간에 검사에 소요된 총 시간 집계 |

## <a name="diagnostic-logs-to-azure-storage-account"></a>Azure Storage 계정에 대한 진단 로그

원시 원격 분석 이벤트는 Azure Monitor로 내보내집니다. 추가 분석을 위해 선택한 고객 스토리지 계정에 이벤트를 기록할 수 있습니다. 로그 내보내기는 Azure Portal의 Purview 계정에 대한 진단 설정을 통해 수행됩니다.

단계에 따라 Azure Purview 계정에 대한 진단 설정을 만듭니다.

1. [다른 대상으로 플랫폼 로그 및 메트릭을 보내기 위해 진단 설정 만들기](../azure-monitor/essentials/diagnostic-settings.md) 문서에 따라, 새 진단 설정을 만들어 플랫폼 로그 및 메트릭을 수집합니다. Azure Storage 계정으로만 계정을 선택합니다.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png" alt-text="진단 로그 만들기를 보여 주는 스크린샷" lightbox="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png":::

2. 스토리지 계정에 이벤트를 기록합니다. 진단 로그를 보관하는 데 전용 스토리지 계정이 권장됩니다. 이 문서에 따라 [스토리지 계정을 만듭니다](../storage/common/storage-account-create.md?tabs=azure-portal).

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png" alt-text="진단 로그에 스토리지 계정을 할당하는 것을 보여 주는 스크린샷" lightbox="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png":::

새로 만든 스토리지 계정에서 로그 수신을 시작하는 데 최대 15 분이 소요됩니다. [Azure Storage 계정에서 리소스 로그의 데이터 보존 및 스키마를 참조하세요](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage). 진단 로그가 구성되면 이벤트는 스토리지 계정으로 전달됩니다.

### <a name="scanstatuslogevent"></a>ScanStatusLogEvent

이벤트는 검사 수명 주기를 추적합니다. 검사 작업은 대기 중, 실행 중, 마지막으로 성공 | 실패 | 취소의 터미널 상태와 같은 상태 시퀀스를 통해 진행됩니다. 이벤트는 각 상태 전환에 대해 기록되고, 이벤트의 스키마에는 다음 속성이 포함됩니다.

```JSON
{
  "time": "<The UTC time when the event occurred>",
  "properties": {
    "dataSourceName": "<Registered data source friendly name>",
    "dataSourceType": "<Registered data source type>",
    "scanName": "<Scan instance friendly name>",
    "assetsDiscovered": "<If the resultType is succeeded, count of assets discovered in scan run>",
    "assetsClassified": "<If the resultType is succeeded, count of assets classified in scan run>",
    "scanQueueTimeInSeconds": "<If the resultType is succeeded, total seconds the scan instance in queue>",
    "scanTotalRunTimeInSeconds": "<If the resultType is succeeded, total seconds the scan took to run>",
    "runType": "<How the scan is triggered>",
    "errorDetails": "<Scan failure error>",
    "scanResultId": "<Unique GUID for the scan instance>"
  },
  "resourceId": "<The azure resource identifier>",
  "category": "<The diagnostic log category>",
  "operationName": "<The operation that cause the event Possible values for ScanStatusLogEvent category are: 
                    |AdhocScanRun 
                    |TriggeredScanRun 
                    |StatusChangeNotification>",
  "resultType": "Queued – indicates a scan is queued. 
                 Running – indicates a scan entered a running state. 
                 Succeeded – indicates a scan completed successfully. 
                 Failed – indicates a scan failure event. 
                 Cancelled – indicates a scan was cancelled. ",
  "resultSignature": "<Not used for ScanStatusLogEvent category. >",
  "resultDescription": "<This will have an error message if the resultType is Failed. >",
  "durationMs": "<Not used for ScanStatusLogEvent category. >",
  "level": "<The log severity level. Possible values are:
            |Informational
            |Error >",
  "location": "<The location of the Azure Purview account>",
}
```

이벤트 인스턴스의 예제 로그는 아래 섹션에 나와 있습니다.

```JSON
{
  "time": "2020-11-24T20:25:13.022860553Z",
  "properties": {
    "dataSourceName": "AzureDataExplorer-swD",
    "dataSourceType": "AzureDataExplorer",
    "scanName": "Scan-Kzw-shoebox-test",
    "assetsDiscovered": "0",
    "assetsClassified": "0",
    "scanQueueTimeInSeconds": "0",
    "scanTotalRunTimeInSeconds": "0",
    "runType": "Manual",
    "errorDetails": "empty_value",
    "scanResultId": "0dc51a72-4156-40e3-8539-b5728394561f"
  },
  "resourceId": "/SUBSCRIPTIONS/111111111111-111-4EB2/RESOURCEGROUPS/FOOBAR-TEST-RG/PROVIDERS/MICROSOFT.PURVIEW/ACCOUNTS/FOOBAR-HEY-TEST-NEW-MANIFEST-EUS",
  "category": "ScanStatusLogEvent",
  "operationName": "TriggeredScanRun",
  "resultType": "Delayed",
  "resultSignature": "empty_value",
  "resultDescription": "empty_value",
  "durationMs": 0,
  "level": "Informational",
  "location": "eastus",
}
```

## <a name="next-steps"></a>다음 단계

[자산 인사이트 보기](asset-insights.md)