---
title: Azure Monitor 로그를 사용한 Azure Functions 모니터링
description: Azure Functions를 Azure Monitor와 함께 사용하여 함수 실행을 모니터링하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 8323f3a917cfe51fdc358d28b865db3055f28172
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130178223"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Azure Monitor 로그를 사용한 Azure Functions 모니터링

Azure Functions는 [Azure Monitor 로그](../azure-monitor/logs/data-platform-logs.md)와의 통합을 제공하여 함수를 모니터링합니다. 이 문서에서는 시스템 생성 및 사용자 생성 로그를 Azure Monitor 로그에 보내도록 Azure Functions를 구성하는 방법을 보여줍니다.

Azure Monitor 로그는 동일한 작업 영역에 있는 여러 리소스의 로그를 통합하는 기능을 제공합니다. 이를 통해 [쿼리](../azure-monitor/logs/log-query-overview.md)로 분석하여 수집된 데이터를 신속하게 검색, 통합 및 분석할 수 있습니다.  Azure Portal에서 [Log Analytics](../azure-monitor/logs/log-query-overview.md)를 사용하여 쿼리를 만들고 테스트한 다음, 이러한 도구를 사용하여 데이터를 직접 분석하거나 [시각화](../azure-monitor/best-practices-analysis.md) 또는 [경고 규칙](../azure-monitor/alerts/alerts-overview.md)에 사용하기 위해 쿼리를 저장할 수 있습니다.

Azure Monitor는 Azure Data Explorer에서 사용되는 [Kusto 쿼리 언어](/azure/kusto/query/)의 한 버전을 사용합니다. Kusto 쿼리 언어는 단순 로그 쿼리에 적합하지만 집계, 조인, 스마트 분석 등의 고급 기능도 포함합니다. [여러 강좌](../azure-monitor/logs/get-started-queries.md)를 통해 쿼리 언어를 빠르게 배울 수 있습니다.

> [!NOTE]
> Azure Monitor 로그 통합은 현재 퍼블릭 미리 보기 상태입니다. [버전 1.x](functions-versions.md)에서 실행되는 함수 앱에는 지원되지 않습니다.

## <a name="setting-up"></a>설치

1. [Azure Portal](https://portal.azure.com)에 있는 함수 앱의 **모니터링** 섹션에서 **진단 설정** 을 선택한 다음, **진단 설정 추가** 를 선택합니다.

   :::image type="content" source="media/functions-monitor-log-analytics/diagnostic-settings-add.png" alt-text="진단 설정 선택":::

1. **진단 설정** 페이지의 **범주 세부 정보** 및 **로그** 에서 **FunctionAppLogs** 를 선택합니다.

   **FunctionAppLogs** 테이블에는 원하는 로그가 포함되어 있습니다.

1. **대상 세부 정보** 에서 **Log Analytics에 보내기** 를 선택한 다음, **Log Analytics 작업 영역** 을 선택합니다. 

1. **진단 설정 이름** 을 입력한 다음, **저장** 을 선택합니다.

   :::image type="content" source="media/functions-monitor-log-analytics/choose-table.png" alt-text="진단 설정 추가":::

## <a name="user-generated-logs"></a>사용자 생성 로그

사용자 지정 로그를 생성하려면 해당 언어와 관련된 로깅 문을 사용합니다. 샘플 코드 조각은 다음과 같습니다.


# <a name="c"></a>[C#](#tab/csharp)

```csharp
log.LogInformation("My app logs here.");
```

# <a name="java"></a>[Java](#tab/java)

```java
context.getLogger().info("My app logs here.");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
context.log('My app logs here.');
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="python"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>로그 쿼리

생성된 로그를 쿼리하려면 다음을 수행합니다.
 
1. 함수 앱에서 **진단 설정** 을 선택합니다. 

1. **진단 설정** 목록에서 함수 로그를 보내도록 구성된 Log Analytics 작업 영역을 선택합니다. 

1. **Log Analytics 작업 영역** 페이지에서 **로그** 를 선택합니다.

   Azure Functions는 **LogManagement** 아래의 **FunctionAppLogs** 테이블에 모든 로그를 기록합니다. 

   :::image type="content" source="media/functions-monitor-log-analytics/querying.png" alt-text="Log Analytics 작업 영역의 쿼리 창":::

몇 가지 샘플 쿼리는 다음과 같습니다.

### <a name="all-logs"></a>모든 로그

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="specific-function-logs"></a>특정 함수 로그

```

FunctionAppLogs
| where FunctionName == "<Function name>" 

```

### <a name="exceptions"></a>예외

```

FunctionAppLogs
| where ExceptionDetails != ""  
| order by TimeGenerated asc

```

## <a name="next-steps"></a>다음 단계

- [Azure Functions 개요](functions-overview.md)를 검토합니다.
- [Azure Monitor 로그](../azure-monitor/logs/data-platform-logs.md)에 대해 자세히 알아봅니다.
- [쿼리 언어](../azure-monitor/logs/get-started-queries.md)에 대해 자세히 알아보기
