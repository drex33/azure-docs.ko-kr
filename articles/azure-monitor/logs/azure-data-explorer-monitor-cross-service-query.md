---
title: Azure Monitor와 Azure Data Explorer 사이의 서비스 간 쿼리
description: Azure Log Analytics 도구를 통해 Azure Data Explorer 데이터를 쿼리하거나 그 반대로 쿼리하여 모든 데이터를 한 곳에서 조인하고 분석합니다.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: 7556469049e072c6915baef60083c306753f8717
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528345"
---
# <a name="cross-service-query---azure-monitor-and-azure-data-explorer"></a>서비스 간 쿼리 - Azure Monitor와 Azure Data Explorer
[Azure Data Explorer](/azure/data-explorer/) 및 [Application Insights](../app/app-insights-overview.md), [Log Analytics](../logs/data-platform-logs.md) 사이에서 서비스 간 쿼리를 만듭니다.
## <a name="azure-monitor-and-azure-data-explorer-cross-service-querying"></a>Azure Monitor 및 Azure Data Explorer의 서비스 간 쿼리
이 환경을 사용하면 [Azure Data Explorer와 Azure Monitor 사이에서 서비스 간 쿼리를 만들고](/azure/data-explorer/query-monitor-data) [Azure Monitor와 Azure Data Explorer 상에서 서비스 간 쿼리를 만들 수 있습니다](./azure-monitor-data-explorer-proxy.md).

예를 들면 다음과 같습니다(Log Analytics에서 Azure Data Explorer 쿼리).
```kusto
CustomEvents | where aField == 1
| join (adx("Help/Samples").TableName | where secondField == 3) on $left.Key == $right.key
```
외부 쿼리에서 작업 영역에 있는 테이블을 쿼리한 다음, 새 "adx()" 함수를 사용하여 Azure Data Explorer 클러스터의 다른 테이블과 조인하여(이 경우 clustername = help, databasename = samples) 쿼리 텍스트 내에서 다른 작업 영역을 쿼리하는 것과 같은 작업을 수행할 수 있습니다.

## <a name="query-exported-log-analytics-data-from-azure-blob-storage-account"></a>Azure Blob Storage 계정에서 내보낸 Log Analytics 데이터 쿼리

Azure Monitor에서 Azure Storage 계정으로 데이터를 내보내면 저렴한 가격으로 보존이 가능하며 여러 지역에 로그를 다시 할당할 수 있습니다.

Azure Data Explorer를 사용하여 Log Analytics 작업 영역에서 내보낸 데이터를 쿼리할 수 있습니다. 구성된 후에는 작업 영역에서 Azure Storage 계정으로 전송된 지원하는 테이블을 Azure Data Explorer의 데이터 원본으로 사용할 수 있습니다. [Azure Data Explorer를 사용하여 Azure Monitor에서 내보낸 데이터 쿼리](../logs/azure-data-explorer-query-storage.md).

[스토리지 흐름의 Azure Data Explorer 쿼리](media\azure-data-explorer-query-storage\exported-data-query.png)

>[!tip] 
> * Log Analytics 작업 영역의 모든 데이터를 Azure Storage 계정 또는 이벤트 허브로 내보내려면 Azure Monitor Logs의 Log Analytics 작업 영역 데이터 내보내기 기능을 사용합니다. [Azure Monitor에서 Log Analytics 작업 영역 데이터 내보내기를 참조하세요](/azure/data-explorer/query-monitor-data).

## <a name="next-steps"></a>다음 단계
다음에 대해 자세히 알아봅니다.
* [Azure Data Explorer와 Azure Monitor 사이에서 서비스 간 쿼리를 만듭니다](/azure/data-explorer/query-monitor-data). Azure Data Explorer에서 Azure Monitor 데이터 쿼리
* [Azure Monitor와 Azure Data Explorer 사이에서 서비스 간 쿼리를 만듭니다](./azure-monitor-data-explorer-proxy.md). Azure Monitor에서 Azure Data Explorer 데이터 쿼리
* [Azure Monitor에서 Log Analytics 작업 영역 데이터 내보내기](/azure/data-explorer/query-monitor-data) Log Analytics 내보낸 데이터를 사용하여 Azure Blob Storage 계정을 연결하고 쿼리합니다.