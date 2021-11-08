---
title: 논리 앱을 사용하여 Log Analytics 작업 영역에서 Azure Storage로 데이터 보관
description: Azure Logic Apps 사용하여 Log Analytics 작업 영역에서 데이터를 쿼리하고 Azure Storage 보내는 방법을 설명합니다.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/02/2020
ms.openlocfilehash: c5f73bbb960cd5bdce4a55a2c090ad509c1a9106
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997821"
---
# <a name="archive-data-from-log-analytics-workspace-to-azure-storage-using-logic-app"></a>논리 앱을 사용하여 Log Analytics 작업 영역에서 Azure Storage로 데이터 보관
이 문서에서는 Azure Logic Apps 사용하여 [Azure Monitor](../../logic-apps/index.yml) Log Analytics 작업 영역에서 데이터를 쿼리하고 Azure Storage 보내는 방법을 설명합니다. 감사 및 규정 준수 시나리오를 위해 Azure Monitor 로그 데이터를 내보내거나 다른 서비스에서 이 데이터를 검색하도록 허용해야 하는 경우 이 프로세스를 사용합니다.  

## <a name="other-export-methods"></a>기타 내보내기 메서드
이 문서에서 설명하는 메서드는 논리 앱을 사용하여 로그 쿼리에서 예약된 내보내기를 설명합니다. 특정 시나리오에 대해 데이터를 내보내기 위한 기타 옵션은 다음과 같습니다.

- Log Analytics 작업 영역에서 Azure Storage 계정 또는 이벤트 허브로 데이터를 내보내려면 Azure Monitor Logs의 Log Analytics 작업 영역 데이터 내보내기 기능을 사용합니다. [Azure Monitor에서 Log Analytics 작업 영역 데이터 내보내기(미리 보기)](logs-data-export.md)를 참조하세요.
- 논리 앱을 사용한 일회성 내보내기 [Logic Apps와 Power Automate용 Azure Monitor Logs 커넥터](logicapp-flow-connector.md)를 참조하세요.
- PowerShell 스크립트를 사용하여 로컬 컴퓨터에 일회성 내보내기 [Invoke-AzOperationalInsightsQueryExport](를 https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport) 참조하세요.

## <a name="overview"></a>개요
이 절차에서는 논리 앱에서 로그 쿼리를 실행하고 워크플로의 다른 작업에서 해당 출력을 사용할 수 있는 [Azure Monitor Logs 커넥터를](/connectors/azuremonitorlogs/) 사용합니다. 이 절차에서는 [Azure Blob Storage 커넥터를](/connectors/azureblob/) 사용하여 쿼리 출력을 Azure Storage로 보냅니다. 다른 작업은 아래 섹션에 설명되어 있습니다.

![논리 앱 개요](media/logs-export-logic-app/logic-app-overview.png)

Log Analytics 작업 영역에서 데이터를 내보낼 때는 로그 데이터를 필터링 및 집계하고 쿼리를 최적화하여 Logic App 워크플로에서 처리하는 데이터의 양을 필요한 데이터로 제한해야 합니다. 예를 들어 로그인 이벤트를 보관해야 하는 경우 필수 이벤트를 필터링하고 다음 쿼리를 사용하여 필수 필드만 프로젝터할 수 있습니다. 

```json
SecurityEvent
| where EventID == 4624 or EventID == 4625
| project TimeGenerated , Account , AccountType , Computer
```

일정에 따라 데이터를 내보내는 경우 쿼리에서 ingestion_time() 함수를 사용하여 늦게 도착하는 데이터를 놓치지 않도록 합니다. 네트워크 또는 플랫폼 문제로 인해 데이터가 지연되는 경우 수집 시간을 사용하면 다음 논리 앱 실행에 포함됩니다. 예제는 [Azure Monitor 로그 추가 작업을](#add-azure-monitor-logs-action) 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소
다음은 이 절차를 완료하기 전에 완료해야 하는 필수 조건입니다.

- Log Analytics 작업 영역. 논리 앱을 만드는 사용자에게는 적어도 작업 영역에 대한 읽기 권한이 있어야 합니다. 
- Azure Storage 계정. 스토리지 계정이 Log Analytics 작업 영역과 동일한 구독에 있을 필요는 없습니다. 논리 앱을 만드는 사용자는 스토리지 계정에 대한 쓰기 권한이 있어야 합니다. 


## <a name="connector-limits"></a>커넥터 제한
log Analytics 작업 영역 및 Azure Monitor 로그 쿼리는 고객을 보호 및 격리하고 서비스 품질을 유지하는 제한을 포함하는 다중테넌트 서비스입니다. 많은 양의 데이터를 쿼리할 때 논리 앱 되풀이 및 로그 쿼리를 구성하는 방법에 영향을 줄 수 있는 다음 제한을 고려해야 합니다.

- 로그 쿼리는 500,000개 이상의 행을 반환할 수 없습니다.
- 로그 쿼리는 64,000,000바이트를 초과하여 반환할 수 없습니다.
- 로그 쿼리는 기본적으로 10분 이상 실행할 수 없습니다. 
- Log Analytics 커넥터는 분당 100개 호출로 제한됩니다.


## <a name="create-container-in-the-storage-account"></a>스토리지 계정에 컨테이너 만들기
[컨테이너 만들기의](../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) 절차를 사용하여 내보낸 데이터를 보관할 컨테이너를 스토리지 계정에 추가합니다. 이 문서의 컨테이너에 사용되는 이름은 **은(는) 입니다.** 하지만 어떤 이름도 사용할 수 있습니다.


## <a name="create-logic-app"></a>논리 앱 만들기

Azure Portal에서 **Logic Apps** 로 이동하고 **추가** 를 클릭합니다. **구독**, **리소스 그룹** 및 **지역** 을 선택하여 새 논리 앱을 저장한 다음 고유한 이름을 지정합니다. [Azure Monitor 로그 설정 및 Azure Logic Apps에 대한 진단 데이터 수집](../../logic-apps/monitor-logic-apps-log-analytics.md)에 설명된 대로 **Log Analytics** 설정을 켜서 런타임 데이터 및 이벤트에 대한 정보를 수집할 수 있습니다. 이 설정은 Azure Monitor Logs 커넥터를 사용하는 데 필요하지 않습니다.

![논리 앱 만들기](media/logs-export-logic-app/create-logic-app.png)


**검토 + 만들기** 를 클릭한 다음 **만들기** 를 클릭합니다. 배포가 완료되면 **리소스로 이동** 을 클릭하여 **Logic Apps 디자이너** 를 엽니다.

## <a name="create-a-trigger-for-the-logic-app"></a>논리 앱에 대한 트리거 만들기
**공통 트리거로 시작** 에서 **반복** 을 선택합니다. 이렇게 하면 일정한 간격으로 자동으로 실행되는 논리 앱이 만들어집니다. 작업의 **빈도** 상자에서 **일** 을 선택하고 **간격** 상자에 **1** 을 입력하여 하루에 한 번 워크플로를 실행합니다.

![반복 작업](media/logs-export-logic-app/recurrence-action.png)


### <a name="add-azure-monitor-logs-action"></a>Azure Monitor 로그 작업 추가
**+ 새 단계** 를 클릭하여 반복 작업 후에 실행되는 작업을 추가합니다. **작업 선택** 에서 **azure monitor** 를 입력한 다음 **Azure Monitor 로그** 를 선택합니다.

![Azure Monitor 로그 작업](media/logs-export-logic-app/select-azure-monitor-connector.png)

**Azure Log Analytics – 쿼리 실행 및 결과 나열을** 클릭합니다.

![논리 앱 디자이너의 단계에 추가되는 새 작업의 스크린샷. 작업 선택에서 Azure Monitor 로그가 강조 표시됩니다.](media/logs-export-logic-app/select-query-action-list.png)

테넌트 선택 및 워크플로에서 쿼리를 실행하는 데 사용할 계정으로 Log Analytics 작업 영역에 대한 액세스 권한을 부여하라는 메시지가 표시됩니다.


## <a name="add-azure-monitor-logs-action"></a>Azure Monitor 로그 작업 추가
Azure Monitor 로그 작업을 사용하면 실행할 쿼리를 지정할 수 있습니다. 이 예제에 사용된 로그 쿼리는 시간별 되풀이에 최적화되어 있으며 특정 실행 시간에 대해 수집된 데이터를 수집합니다. 예를 들어 워크플로가 4:35에 실행되는 경우 시간 범위는 4:00에서 5:00이 됩니다. 다른 빈도로 실행하도록 논리 앱을 변경하는 경우 쿼리도 변경해야 합니다. 예를 들어 되풀이를 매일 실행하도록 설정하면 쿼리에서 startTime을 startofday(make_datetime(year,month,day,0,0))로 설정합니다. 

Log Analytics 작업 영역에 대한 **구독** 및 **리소스 그룹** 을 선택합니다. **리소스 유형** 으로 *Log Analytics 작업 영역* 을 선택한 다음 **리소스 이름** 에서 작업 영역 이름을 선택합니다.

다음 로그 쿼리를 **쿼리** 창에 추가합니다.  

```Kusto
let dt = now();
let year = datetime_part('year', dt);
let month = datetime_part('month', dt);
let day = datetime_part('day', dt);
let hour = datetime_part('hour', dt);
let startTime = make_datetime(year,month,day,hour,0)-1h;
let endTime = startTime + 1h - 1tick;
AzureActivity
| where ingestion_time() between(startTime .. endTime)
| project 
    TimeGenerated,
    BlobTime = startTime, 
    OperationName ,
    OperationNameValue ,
    Level ,
    ActivityStatus ,
    ResourceGroup ,
    SubscriptionId ,
    Category ,
    EventSubmissionTimestamp ,
    ClientIpAddress = parse_json(HTTPRequest).clientIpAddress ,
    ResourceId = _ResourceId 
```

**시간 범위는** **TimeGenerated** 열을 기반으로 쿼리에 포함할 레코드를 지정합니다. 쿼리에서 선택한 시간 범위보다 크거나 같은 값으로 설정해야 합니다. 이 쿼리는 **TimeGenerated** 열을 사용하지 않기 때문에 **쿼리에서 설정** 옵션을 사용할 수 없습니다. 시간 범위에 대한 자세한 내용은 [쿼리](./scope.md) 범위를 참조하세요. 

**시간 범위** 에 대해 **지난 4시간을** 선택합니다. 이렇게 하면 **TimeGenerated보다** 큰 검색 시간이 있는 모든 레코드가 결과에 포함됩니다.
   
![쿼리 실행 및 결과 시각화라는 새 Azure Monitor Logs 작업에 대한 설정 스크린샷.](media/logs-export-logic-app/run-query-list-action.png)


### <a name="add-parse-json-activity-optional"></a>JSON 구문 분석 작업 추가(선택 사항)
쿼리 실행 **및 결과 나열** 작업의 출력은 JSON으로 형식이 지정됩니다. 이 데이터를 구문 분석하고 **작성** 작업 준비의 일부로 조작할 수 있습니다. 

수신할 페이로드를 설명하는 JSON 스키마를 제공할 수 있습니다. 이 디자이너는 이 스키마를 사용하여 JSON 콘텐츠를 구문 분석하고 JSON 콘텐츠의 속성을 나타내는 친숙한 토큰을 생성합니다. 그 후 이러한 속성을 논리 앱 워크플로 전체에서 간편하게 참조하고 사용할 수 있습니다. 


**+ 새 단계** 를 클릭한 다음 **+작업 추가** 를 클릭합니다. **작업 선택에서** **json을** 입력한 **다음, JSON 구문 분석 을** 선택합니다.

![JSON 구문 분석 작업 선택](media/logs-export-logic-app/select-parse-json.png)

**콘텐츠** 상자를 클릭하여 이전 활동의 값 목록을 표시합니다. 쿼리 실행 **및 결과 나열** 작업에서 **본문을** 선택합니다. 로그 쿼리의 출력입니다.

[![본문 선택](media/logs-export-logic-app/select-body.png)](media/logs-export-logic-app/select-body.png#lightbox)

5.  **샘플 페이로드를 사용하여 스키마 생성을** 클릭합니다. 로그 쿼리를 실행하고 샘플 페이로드에 사용할 출력을 복사합니다.  여기서 샘플 쿼리의 경우 다음 출력을 사용할 수 있습니다.


```json
{
    "TimeGenerated": "2020-09-29T23:11:02.578Z",
    "BlobTime": "2020-09-29T23:00:00Z",
    "OperationName": "Returns Storage Account SAS Token",
    "OperationNameValue": "MICROSOFT.RESOURCES/DEPLOYMENTS/WRITE",
    "Level": "Informational",
    "ActivityStatus": "Started",
    "ResourceGroup": "monitoring",
    "SubscriptionId": "00000000-0000-0000-0000-000000000000",
    "Category": "Administrative",
    "EventSubmissionTimestamp": "2020-09-29T23:11:02Z",
    "ClientIpAddress": "192.168.1.100",
    "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/monitoring/providers/microsoft.storage/storageaccounts/my-storage-account"
}
```



![JSON 페이로드 구문 분석](media/logs-export-logic-app/parse-json-payload.png)

## <a name="add-the-compose-action"></a>작성 작업 추가
**Compose** 작업은 구문 분석된 JSON 출력을 가져와서 Blob에 저장해야 하는 개체를 만듭니다.

**+ 새 단계** 를 클릭한 다음 **+작업 추가** 를 클릭합니다. **작업 선택에서** **compose를** 입력한 다음 **작성** 작업을 선택합니다.

![작성 작업 선택](media/logs-export-logic-app/select-compose.png)


**입력** 상자를 클릭하면 이전 활동의 값 목록이 표시됩니다. **JSON 구문 분석** 작업에서 **본문을** 선택합니다. 로그 쿼리의 구문 분석된 출력입니다.

[![작성 작업에 대한 본문 선택](media/logs-export-logic-app/select-body-compose.png)](media/logs-export-logic-app/select-body-compose.png#lightbox)


## <a name="add-the-create-blob-action"></a>Blob 만들기 작업 추가
Blob 만들기 작업은 구성된 JSON을 스토리지에 씁니다.

**+ 새 단계** 를 클릭한 다음 **+작업 추가** 를 클릭합니다. **작업 선택에서** **Blob을** 입력한 **다음, Blob 만들기** 작업을 선택합니다.

![Blob 만들기를 선택합니다.](media/logs-export-logic-app/select-create-blob.png)

**연결** 이름에 스토리지 계정에 대한 연결의 이름을 입력한 다음 **폴더 경로** 상자에서 폴더 아이콘을 클릭하여 스토리지 계정의 컨테이너를 선택합니다. **Blob 이름을** 클릭하여 이전 작업의 값 목록을 확인합니다. **식을** 클릭하고 시간 간격과 일치하는 식을 입력합니다. 매시간 실행되는 이 쿼리의 경우 다음 식은 이전 시간당 Blob 이름을 설정합니다. 

```json
subtractFromTime(formatDateTime(utcNow(),'yyyy-MM-ddTHH:00:00'), 1,'Hour')
```

[![Blob 식](media/logs-export-logic-app/blob-expression.png)](media/logs-export-logic-app/blob-expression.png#lightbox)

**Blob 콘텐츠** 상자를 클릭하여 이전 작업의 값 목록을 표시한 **다음, 작성** 섹션에서 **출력을** 선택합니다.


![Blob 식 만들기](media/logs-export-logic-app/create-blob.png)


## <a name="test-the-logic-app"></a>Logic Apps 테스트
**실행을** 클릭하여 워크플로를 테스트합니다. 워크플로에 오류가 있는 경우 문제가 있는 단계에서 표시됩니다. 실행을 보고 각 단계로 드릴인하여 입력 및 출력을 보고 오류를 조사할 수 있습니다. 필요한 경우 [Azure Logic Apps 워크플로 오류 문제 해결 및 진단을](../../logic-apps/logic-apps-diagnosing-failures.md) 참조하세요.

[![실행 기록](media/logs-export-logic-app/runs-history.png)](media/logs-export-logic-app/runs-history.png#lightbox)


## <a name="view-logs-in-storage"></a>Storage 로그 보기
**Azure Portal Storage 계정** 메뉴로 이동하여 스토리지 계정을 선택합니다. **Blob 타일을** 클릭하고 Blob 만들기 작업에서 지정한 컨테이너를 선택합니다. Blob 중 하나를 선택한 **다음, Blob 편집 을** 선택합니다.

[![Blob 데이터](media/logs-export-logic-app/blob-data.png)](media/logs-export-logic-app/blob-data.png#lightbox)

## <a name="next-steps"></a>다음 단계

- [Azure Monitor의 로그 쿼리](./log-query-overview.md)에 대해 자세히 알아봅니다.
- [Logic Apps](../../logic-apps/index.yml)에 대해 자세히 알아봅니다.
- [Power Automate](https://flow.microsoft.com)에 대해 자세히 알아봅니다.
