---
title: Azure Logic Apps와 Power Automate로 Azure Monitor 로그 사용
description: Azure Logic Apps 및 Power Automate를 사용하여 Azure Monitor 커넥터를 통해 반복 가능한 프로세스를 빠르게 자동화하는 방법을 알아봅니다.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/13/2020
ms.openlocfilehash: 8f44bf2610096db94b1c846dcab1eeacbf121a07
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494827"
---
# <a name="azure-monitor-logs-connector-for-logic-apps-and-power-automate"></a>Logic Apps 및 Power Automate용 Azure Monitor 로그 커넥터
[Azure Logic Apps](../../logic-apps/index.yml) 및 [Power Automate](https://flow.microsoft.com)를 사용하면 다양한 서비스에 대해 수백 가지 작업을 사용하여 자동화된 워크플로를 만들 수 있습니다. Azure Monitor 로그 커넥터를 사용하면 Azure Monitor의 Log Analytics 작업 영역 또는 Application Insights 애플리케이션에서 데이터를 검색하는 워크플로를 빌드할 수 있습니다. 이 문서에서는 커넥터에 포함된 작업에 대해 설명하고 이 데이터를 사용하여 워크플로를 빌드하기 위한 연습을 제공합니다.

예를 들어, 논리 앱을 만들어 Office 365의 이메일 알림에서 Azure Monitor 로그 데이터를 사용하거나, Azure DevOps에서 버그를 만들거나, Slack 메시지를 게시할 수 있습니다.  메일이나 트윗이 수신될 때와 같이 연결된 서비스의 일부 작업 또는 단순 일정에서 워크플로를 트리거할 수 있습니다. 

## <a name="connector-limits"></a>커넥터 제한
Azure Monitor 로그 커넥터에는 다음과 같은 제한이 있습니다.
* 최대 쿼리 응답 크기는 16.7MB(16 MiB)입니다. 커넥터 인프라는 제한이 쿼리 API 제한보다 낮게 설정되어 있음을 나타냅니다.
* 최대 레코드 수: 500,000
* 최대 쿼리 제한 시간 110초
* 커넥터와 로그 페이지가 현재 동일한 차트 라이브러리를 사용하지 않기 때문에 차트 시각화는 로그 페이지에서 사용할 수 있고 커넥터에서 누락될 수 있습니다.

커넥터는 사용하는 쿼리 및 결과의 크기에 따라 제한에 도달할 수 있습니다. 더 작은 시간 범위에서 더 자주 실행하도록 흐름 되풀이를 조정하거나 결과 크기를 줄이기 위해 데이터를 집계하여 이러한 경우를 방지할 수 있습니다. 캐싱으로 인해 120초보다 낮은 간격으로 자주 쿼리하는 것은 권장되지 않습니다.

## <a name="actions"></a>동작
다음 표에서는 Azure Monitor Logs 커넥터에 포함된 작업을 설명합니다. 둘 다 Log Analytics 작업 영역 또는 Application Insights 애플리케이션에 대해 로그 쿼리를 실행할 수 있습니다. 차이점은 데이터가 반환되는 방식에 있습니다.

> [!NOTE]
> Azure Monitor Logs 커넥터는 [Azure Log Analytics 커넥터](/connectors/azureloganalytics/) 및 [Azure Application Insights 커넥터](/connectors/applicationinsights/)를 대체합니다. 이 커넥터는 다른 커넥터와 동일한 기능을 제공하며 Log Analytics 작업 영역 또는 Application Insights 애플리케이션에 대해 쿼리를 실행하는 기본 방법입니다.


| 작업 | 설명 |
|:---|:---|
| [쿼리 실행 및 결과 나열](/connectors/azuremonitorlogs/#run-query-and-list-results) | 각 행을 자체 개체로 반환합니다. 나머지 워크플로에서 각 행을 별도로 작업하려는 경우 이 작업을 사용합니다. 일반적으로 작업 뒤에는 [각 작업](../../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)이 옵니다. |
| [쿼리 실행 및 결과 시각화](/connectors/azuremonitorlogs/#run-query-and-visualize-results) | 결과 집합의 모든 행을 서식이 지정된 단일 개체로 반환합니다. 결과를 메일로 보내는 것과 같이 나머지 워크플로에서 결과 집합을 함께 사용하려는 경우 이 작업을 사용합니다.  |

## <a name="walkthroughs"></a>연습
다음 자습서에서는 Azure Logic Apps에서 Azure Monitor 커넥터를 사용하는 방법을 보여 줍니다. Power Automate를 사용하여 이와 동일한 예를 수행할 수 있습니다. 유일한 차이점은 초기 워크플로를 만들고 완료되면 실행하는 방법뿐입니다. 워크플로 및 작업의 구성은 둘 다 동일합니다. 시작하려면 [Power Automate의 템플릿에서 흐름 만들기](/power-automate/get-started-logic-template)를 참조하세요.


### <a name="create-a-logic-app"></a>논리 앱 만들기

Azure Portal에서 **Logic Apps** 로 이동하고 **추가** 를 클릭합니다. **구독**, **리소스 그룹** 및 **지역** 을 선택하여 새 논리 앱을 저장한 다음 고유한 이름을 지정합니다. [Azure Monitor 로그 설정 및 Azure Logic Apps에 대한 진단 데이터 수집](../../logic-apps/monitor-logic-apps-log-analytics.md)에 설명된 대로 **Log Analytics** 설정을 켜서 런타임 데이터 및 이벤트에 대한 정보를 수집할 수 있습니다. 이 설정은 Azure Monitor Logs 커넥터를 사용하는 데 필요하지 않습니다.

![논리 앱 만들기](media/logicapp-flow-connector/create-logic-app.png)


**검토 + 만들기** 를 클릭한 다음 **만들기** 를 클릭합니다. 배포가 완료되면 **리소스로 이동** 을 클릭하여 **Logic Apps 디자이너** 를 엽니다.

### <a name="create-a-trigger-for-the-logic-app"></a>논리 앱에 대한 트리거 만들기
**공통 트리거로 시작** 에서 **반복** 을 선택합니다. 이렇게 하면 일정한 간격으로 자동으로 실행되는 논리 앱이 만들어집니다. 작업의 **빈도** 상자에서 **일** 을 선택하고 **간격** 상자에 **1** 을 입력하여 하루에 한 번 워크플로를 실행합니다.

![반복 작업](media/logicapp-flow-connector/recurrence-action.png)

## <a name="walkthrough-mail-visualized-results"></a>연습: 메일 시각화 결과
다음 자습서에서는 Azure Monitor 로그 쿼리의 결과를 이메일로 보내는 논리 앱을 만드는 방법을 보여 줍니다. 

### <a name="add-azure-monitor-logs-action"></a>Azure Monitor 로그 작업 추가
**+ 새 단계** 를 클릭하여 반복 작업 후에 실행되는 작업을 추가합니다. **작업 선택** 에서 **azure monitor** 를 입력한 다음 **Azure Monitor 로그** 를 선택합니다.

![Azure Monitor 로그 작업](media/logicapp-flow-connector/select-azure-monitor-connector.png)

**Azure Log Analytics - 쿼리 실행 및 결과 시각화** 를 클릭합니다.

![논리 앱 디자이너의 단계에 추가되는 새 작업의 스크린샷. 작업 선택에서 Azure Monitor 로그가 강조 표시됩니다.](media/logicapp-flow-connector/select-query-action-visualize.png)


### <a name="add-azure-monitor-logs-action"></a>Azure Monitor 로그 작업 추가

Log Analytics 작업 영역에 대한 **구독** 및 **리소스 그룹** 을 선택합니다. **리소스 유형** 으로 *Log Analytics 작업 영역* 을 선택한 다음 **리소스 이름** 에서 작업 영역 이름을 선택합니다.

다음 로그 쿼리를 **쿼리** 창에 추가합니다.  

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1day)
| summarize TotalErrors=count() by Computer
| sort by Computer asc   
```

**시간 범위** 는 *쿼리에서 설정* 을 선택하고 **차트 유형** 은 **HTML 표** 를 선택합니다.
   
![쿼리 실행 및 결과 시각화라는 새 Azure Monitor Logs 작업에 대한 설정 스크린샷.](media/logicapp-flow-connector/run-query-visualize-action.png)

메일은 현재 연결과 연결된 계정으로 전송됩니다. **연결 변경** 을 클릭하여 다른 계정을 지정할 수 있습니다.

### <a name="add-email-action"></a>이메일 작업 추가

**+ 새 단계** 를 클릭한 다음 **+작업 추가** 를 클릭합니다. **작업 선택** 에서 **outlook** 을 입력한 다음 **Office 365 Outlook** 을 선택합니다.

![Outlook 커넥터 선택](media/logicapp-flow-connector/select-outlook-connector.png)

**이메일 보내기(V2)** 를 선택합니다.

![Office 365 Outlook 선택 창](media/logicapp-flow-connector/select-mail-action.png)

**본문** 상자의 아무 곳이나 클릭하면 논리 앱에서 이전 작업의 값이 포함된 **동적 콘텐츠** 창이 열립니다. **자세히 보기** 를 선택한 다음 Log Analytics 작업의 쿼리 결과인 **본문** 을 선택합니다.

![본문 선택](media/logicapp-flow-connector/select-body.png)

**받는 사람** 창에서 수신자의 이메일 주소 및 **제목** 에서 전자 메일에 대한 제목을 지정합니다. 

![메일 작업](media/logicapp-flow-connector/mail-action.png)


### <a name="save-and-test-your-logic-app"></a>논리 앱 저장 및 테스트
**저장** 을 클릭한 다음 **실행** 을 클릭하여 논리 앱의 테스트 실행을 수행합니다.

![저장 및 실행](media/logicapp-flow-connector/save-run.png)


논리 앱이 완료되면 사용자가 지정한 수신자의 이메일을 확인합니다.  다음과 유사한 본문이 있는 메일을 받아야 합니다.

![샘플 메일](media/logicapp-flow-connector/sample-mail.png)



## <a name="next-steps"></a>다음 단계

- [Azure Monitor의 로그 쿼리](./log-query-overview.md)에 대해 자세히 알아봅니다.
- [Logic Apps](../../logic-apps/index.yml)에 대해 자세히 알아봅니다.
- [Power Automate](https://flow.microsoft.com)에 대해 자세히 알아봅니다.
