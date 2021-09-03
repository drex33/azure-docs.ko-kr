---
title: 진단 로깅
titleSuffix: Azure Cognitive Services
description: 이 가이드에서는 Azure Cognitive Service에 대한 진단 로깅을 사용하는 단계별 지침을 제공합니다. 이러한 로그는 문제를 식별하고 디버그하는 데 사용되는 리소스의 작업에 대한 풍부하고 빈번한 데이터를 제공합니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: erhopf
ms.openlocfilehash: deb5db6f205c02812330539c403fbe7b80d4784c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536730"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Azure Cognitive Services에 대한 진단 로깅 사용

이 가이드에서는 Azure Cognitive Service에 대한 진단 로깅을 사용하는 단계별 지침을 제공합니다. 이러한 로그는 문제를 식별하고 디버그하는 데 사용되는 리소스의 작업에 대한 풍부하고 빈번한 데이터를 제공합니다. 계속하기 전에 Azure 계정에서 [Speech Services](./speech-service/overview.md) 또는 [LUIS](./luis/what-is-luis.md)와 같이 Cognitive Service 중 하나 이상을 구독해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

진단 로깅을 사용 설정하려면 로그 데이터를 저장할 위치에 있어야 합니다. 이 자습서에서는 Azure Storage와 Log Analytics를 사용합니다.

* [Azure storage](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) - 정책 감사, 정적 분석 또는 백업에 대한 진단 로그를 유지합니다. 설정을 구성하는 사용자가 두 구독에 대한 적절한 Azure RBAC 액세스를 가진 경우 스토리지 계정은 로그를 내보내는 리소스와 동일한 구독을 가지고 있지 않아도 됩니다.
* [Log Analytics](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) - Azure 리소스에서 생성된 원시 로그를 분석할 수 있는 유연한 로그 검색 및 분석 도구입니다.

> [!NOTE]
> * 추가 구성 옵션이 사용 가능합니다. 자세한 내용은 [Azure 리소스에서 로그 데이터 수집 및 사용](../azure-monitor/essentials/platform-logs-overview.md)을 참조하세요.
> * 진단 로깅의 "추적"은 [사용자 지정 질문 답변](./qnamaker/how-to/get-analytics-knowledge-base.md?tabs=v2)에만 사용할 수 있습니다.

## <a name="enable-diagnostic-log-collection"></a>진단 로그 수집 사용 설정  

먼저 Azure Portal을 사용하여 진단 로깅을 사용하도록 설정합니다.

> [!NOTE]
> PowerShell 또는 Azure CLI를 사용하여 이 기능을 사용 설정하려면 [Azure 리소스에서 로그 데이터 수집 및 소비](../azure-monitor/essentials/platform-logs-overview.md)에 제공된 지침을 사용하세요.

1. Azure Portal로 이동합니다. 그런 다음, Cognitive Services 리소스를 찾아 선택합니다. 예를 들어, Speech Services에 대한 구독입니다.   
2. 다음으로 왼쪽 탐색 메뉴에서 **모니터링** 을 찾고, **진단 설정** 을 선택합니다. 이 화면에는 이 리소스에 대해 이전에 만든 모든 진단 설정이 포함되어 있습니다.
3. 이전에 만든 리소스를 사용하려는 경우 지금 선택할 수 있습니다. 그렇지 않은 경우 **+진단 설정 추가** 를 선택합니다.
4. 설정의 이름을 입력합니다. 그런 다음, **스토리지 계정에 보관** 및 **Log Analytics에 보내기** 를 선택합니다.
5. 구성 메시지가 표시되면 진단 로그를 저장하는 데 사용할 스토리지 계정 및 OMS 작업 영역을 선택합니다. **참고**: 스토리지 계정이나 OMS 작업 영역이 없는 경우 표시되는 메시지에 따라 만듭니다.
6. **Audit**, **RequestResponse**, **AllMetrics** 를 선택합니다. 그런 다음, 진단 로그 데이터에 대한 보존 기간을 설정합니다. 보존 정책이 0으로 설정된 경우 해당 로그 범주에 대한 이벤트는 무기한으로 저장됩니다.
7. **저장** 을 클릭합니다.

로깅 데이터를 쿼리 및 분석에 사용할 수 있을 때까지 최대 2시간이 걸릴 수 있습니다. 바로 표시되지 않더라도 걱정하지 마세요.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Azure Storage에서 진단 데이터 보기 및 내보내기

Azure Storage는 대량의 비정형 데이터를 저장하는 데 최적화된 강력한 개체 스토리지 솔루션입니다. 이 섹션에서는 스토리지 계정에 30일 기간 동안의 총 트랜잭션을 쿼리하고 Excel로 데이터를 내보내는 방법을 알아봅니다.

1. Azure Portal에서 지난 섹션에서 만든 Azure Storage 리소스를 찾습니다.
2. 왼쪽 탐색 메뉴에서 **모니터링** 을 찾고, **메트릭** 을 선택합니다.
3. 사용 가능한 드롭다운을 사용하여 쿼리를 구성합니다. 이 예에서는 시간 범위를 **최근 30일** 로 설정하고 메트릭을 **트랜잭션** 으로 설정합니다.
4. 쿼리가 완료되면 지난 30일 동안의 트랜잭션 시각화가 표시됩니다. 이 데이터를 내보내려면 페이지 상단에 있는 **Excel로 내보내기** 단추를 사용합니다.

[Azure Storage](../storage/blobs/storage-blobs-introduction.md)에서 진단 데이터로 수행할 수 있는 작업에 대해 자세히 알아보세요.

## <a name="view-logs-in-log-analytics"></a>Log Analytics에서 로그 보기

지침에 따라 리소스에 대한 로그 분석 데이터를 탐색합니다.

1. Azure Portal의 왼쪽 탐색 메뉴에서 **Log Analytics** 를 찾아 선택합니다.
2. 진단을 사용 설정할 때 만든 리소스를 찾아 선택합니다.
3. **일반** 에서 **로그** 를 찾아 선택합니다. 이 페이지에서 로그에 대해 쿼리를 실행할 수 있습니다.

### <a name="sample-queries"></a>샘플 쿼리

로그 데이터를 탐색하는 데 사용할 수 있는 몇 가지 기본 Kusto 쿼리가 있습니다.

지정된 기간 동안 Azure Cognitive Services의 모든 진단 로그에 대해 이 쿼리를 실행합니다.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

이 쿼리를 실행하여 최근 10개 로그를 확인합니다.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

이 쿼리를 실행하여 **리소스** 별로 작업을 그룹화합니다.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
이 쿼리를 실행하여 작업을 수행하는 데 걸리는 평균 시간을 확인합니다.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

이 쿼리를 실행하여 10초마다 범주화된 수를 통해 OperationName에 따라 분할된 시간에 따라 작업 볼륨을 볼 수 있습니다.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>다음 단계

* 로깅을 사용하도록 설정하는 방법뿐 아니라 여러 Azure 서비스에서 지원하는 메트릭 및 로그 범주를 이해하려면 [Microsoft Azure의 메트릭 개요](../azure-monitor/data-platform.md) 및 [Azure 진단 로그 개요](../azure-monitor/essentials/platform-logs-overview.md) 문서를 읽어보세요.
* Event Hubs에 대한 자세한 내용은 다음 문서를 참조하세요.
  * [Azure Event Hubs란?](../event-hubs/event-hubs-about.md)
  * [Event Hubs 시작](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
* [Azure Storage에서 메트릭 및 진단 로그 다운로드](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs)를 읽어보세요.
* [Azure Monitor 로그의 로그 검색 이해](../azure-monitor/logs/log-query-overview.md)를 읽어보세요.