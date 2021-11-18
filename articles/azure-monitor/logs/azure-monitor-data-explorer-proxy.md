---
title: Azure Monitor를 사용한 Azure Data Explorer 리소스 간 쿼리
description: Azure Monitor를 사용하여 Azure Data Explorer와 Log Analytics 작업 영역, 그리고 Azure Monitor 내 클래식 Application Insights 애플리케이션 사이에서 교차 제품 쿼리를 수행합니다.
author: osalzberg
ms.author: orens
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: be0ee4853bc47e2f52d960f28e7d2f151c73861b
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132715362"
---
# <a name="cross-resource-query-azure-data-explorer-by-using-azure-monitor"></a>Azure Monitor를 사용한 Azure Data Explorer 리소스 간 쿼리
Azure Monitor는 Azure Data Explorer, [Application Insights](../app/app-insights-overview.md) 및 [Log Analytics](../logs/data-platform-logs.md) 사이의 교차 서비스 쿼리를 지원합니다. 그다음 Log Analytics/Application Insights 도구를 사용하여 Azure Data Explorer 클러스터를 쿼리하고 교차 서비스 쿼리에서 이를 참조할 수 있습니다. 이 아티클에서는 서비스 간 쿼리를 만드는 방법을 보여 줍니다.

다음 다이어그램은 Azure Monitor 서비스 간 흐름을 보여 줍니다.

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-monitor-data-explorer-flow.png" alt-text="사용자, Azure Monitor, 프록시 및 Azure Data Explorer 간의 쿼리 흐름을 보여 주는 다이어그램입니다.":::

## <a name="cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer"></a>Log Analytics 또는 Application Insights 리소스 및 Azure Data Explorer 간 쿼리를 진행하세요.

Kusto 쿼리를 지원하는 클라이언트 도구를 사용하여 리소스 간 쿼리를 실행할 수 있습니다. 관련 도구의 예로 Log Analytics 웹 UI, 통합 문서, PowerShell 및 REST API가 있습니다.

`adx` 패턴 내의 쿼리에 Azure Data Explorer 클러스터의 식별자와 데이터베이스 이름 및 테이블을 차례로 입력합니다.

```kusto
adx('https://help.kusto.windows.net/Samples').StormEvents
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-cross-service-query-example.png" alt-text="서비스 간 쿼리 예제를 보여 주는 스크린샷":::

> [!NOTE]
>* 데이터베이스 이름은 대/소문자를 구분합니다.
>* 경고용 리소스 간 쿼리는 지원되지 않습니다.
>* 클러스터에서 타임스탬프 열을 식별하는 것은 지원되지 않으며, Log Analytics 쿼리 API는 시간 필터를 전달하지 않습니다.

## <a name="combine-azure-data-explorer-cluster-tables-with-a-log-analytics-workspace"></a>Azure Data Explorer 클러스터 테이블과 Log Analytics 작업 영역을 결합합니다.

`union` 명령을 사용하여 클러스터 테이블을 Log Analytics 작업 영역과 결합합니다.

```kusto
union customEvents, adx('https://help.kusto.windows.net/Samples').StormEvents
| take 10
```
```kusto
let CL1 = adx('https://help.kusto.windows.net/Samples').StormEvents;
union customEvents, CL1 | take 10
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-union-cross-query.png" alt-text="Union 명령을 사용하여 서비스 간 쿼리 예제를 보여 주는 스크린샷":::

> [!Tip]
> 줄임 형식은 허용됩니다. *ClusterName*/*InitialCatalog*. 예를 들어 `adx('help/Samples')`은 `adx('help.kusto.windows.net/Samples')`로 변환됩니다.

>[!Note]
> 
>* 공용 구조체가 아니라 [`join` 연산자](/azure/data-explorer/kusto/query/joinoperator)를 사용하면 Azure Data Explorer 클러스터의 데이터를 Log Analytics Workspace와 결합하기 위해 [`hint`](/azure/data-explorer/kusto/query/joinoperator#join-hints)를 사용해야 합니다.
>* Hint.remote={Direction of the Log Analytics Workspace}를 사용합니다. 예를 들어 다음과 같습니다.
>```kusto
>AzureDiagnostics
>| join hint.remote=left adx("cluster=ClusterURI").AzureDiagnostics on (ColumnName)
>```

## <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>다른 테넌트에서 Azure Monitor 리소스를 사용하여 한 테넌트의 Azure Data Explorer 클러스터에서 데이터 조인

서비스 사이에서 테넌트 간 쿼리는 지원되지 않습니다. 양쪽 리소스 모두로 확장되는 쿼리를 실행하기 위해 단일 테넌트에 로그인되어 있습니다.

Azure Data Explorer 리소스가 테넌트 A에 위치하고 Log Analytics 작업 영역이 테넌트 B에 위치한 경우, 다음 두 가지 방법 중 하나를 사용합니다.

*  Azure Data Explorer를 사용하면 다른 테넌트의 보안 주체에 대한 역할을 추가할 수 있습니다. 테넌트 B의 사용자 ID를 Azure Data Explorer 클러스터에 대한 권한이 있는 사용자로 추가합니다. Azure Data Explorer 클러스터의 [TrustedExternalTenant](/powershell/module/az.kusto/update-azkustocluster) 속성에 Tenant B가 포함되어 있는지 확인합니다. Tenant B에서 완전히 쿼리를 실행합니다.
*  [Lighthouse](../../lighthouse/index.yml)를 사용하여 Azure Monitor 리소스를 테넌트 A에 프로젝션합니다.

## <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>다른 테넌트에서 Azure Data Explorer 클러스터에 연결

Kusto Explorer는 사용자 계정이 원래 속한 테넌트에 자동으로 로그인합니다. 동일한 사용자 계정을 사용하여 다른 테넌트의 리소스에 액세스하려면 다음과 같이 연결 문자열에서 `TenantId`을 명시적으로 지정해야 합니다.

`Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=TenantId`

## <a name="next-steps"></a>다음 단계
* [쿼리 작성](/azure/data-explorer/write-queries)
* [Azure Data Explorer를 사용하여 Azure Monitor에서 데이터 쿼리](/azure/data-explorer/query-monitor-data)
* [Azure Monitor에서 리소스 간 로그 쿼리 수행](../logs/cross-workspace-query.md)