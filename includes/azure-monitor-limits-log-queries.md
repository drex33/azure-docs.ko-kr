---
title: 포함 파일
description: 포함 파일
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: f691f760c9abfed773db8602f878dfc7ba0c08ba
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520736"
---
### <a name="general-query-limits"></a>일반 쿼리 제한

| 제한 | Description |
|:---|:---|
| 쿼리 언어 | Azure Monitor는 Azure Data Explorer와 동일한 [Kusto 쿼리 언어](/azure/kusto/query/)를 사용합니다. Azure Monitor에서 지원되지 않는 KQL 언어 요소는 [Azure Monitor 로그 쿼리 언어 차이](/azure/data-explorer/kusto/query/)를 참조하세요. |
| Azure 지역 | 데이터가 여러 Azure 지역의 Log Analytics 작업 영역에 걸쳐있는 경우 로그 쿼리에 과도한 오버헤드가 발생할 수 있습니다. 자세한 내용은 [쿼리 제한](../articles/azure-monitor/logs/scope.md#query-scope-limits)을 참조하세요. |
| 리소스 간 쿼리 | 100으로 제한된 단일 쿼리에서 Application Insights 리소스 및 Log Analytics 작업 영역의 최대 수<br>뷰 디자이너에서는 리소스 간 쿼리가 지원되지 않습니다.<br>로그 경고의 리소스 간 쿼리는 새 scheduledQueryRules API에서 지원됩니다.<br>자세한 내용은 [리소스 간 쿼리 제한](../articles/azure-monitor/logs/cross-workspace-query.md#cross-resource-query-limits)을 참조하세요. |

### <a name="user-query-throttling"></a>사용자 쿼리 제한
Azure Monitor에는 과도한 쿼리를 보내는 사용자로부터 보호하기 위한 몇 가지 제한 한도가 있습니다. 이러한 동작은 잠재적으로 시스템 백 엔드 리소스에 과부하를 일으키고 서비스 응답성을 저하시킬 수 있습니다. 다음 제한은 중단으로부터 고객을 보호하고 일관된 서비스 수준을 보장하기 위해 마련되었습니다. 사용자 제한은 극단적인 사용 시나리오에만 영향을 주도록 설계되었으며 일반적인 사용과 관련이 없어야 합니다.


| 측정값 | 사용자당 제한 | Description |
|:---|:---|:---|
| 동시 쿼리 | 5 | 사용자는 최대 5개의 동시 쿼리를 실행할 수 있으며, 추가 쿼리는 큐에 추가됩니다. 실행 중인 쿼리 중 하나가 완료되면 큐의 첫 번째 쿼리가 큐에서 끌어와 실행을 시작합니다. 참고: 경고 쿼리는 이 제한에 포함되지 않습니다.
| 동시성 큐의 시간 | 3분 | 쿼리가 시작되지 않고 3분 이상 큐에 있으면 코드 429와 함께 HTTP 오류 응답으로 종료됩니다. |
| 동시성 큐의 총 쿼리 수 | 200 | 큐의 쿼리 수가 200에 도달하면 HTTP 오류 코드 429로 다음 쿼리가 거부됩니다. 이 숫자는 동시에 실행할 수 있는 5개의 쿼리에 추가됩니다. |
| 쿼리 속도 | 30초 당 200개의 쿼리 | 단일 사용자가 모든 작업 영역에 제출할 수 있는 전체 쿼리 비율입니다. 이 제한은 프로그래밍 방식 쿼리 또는 Azure 대시보드 및 Log Analytics 작업 영역 요약 페이지와 같은 시각화 부분에서 시작된 쿼리에 적용됩니다. |

- [Azure Monitor에서 로그 쿼리 최적화](../articles/azure-monitor/logs/query-optimization.md)에 설명된 대로 쿼리를 최적화합니다.
- 대시보드 및 통합 문서에는 로드하거나 새로 고칠 때마다 쿼리 버스트를 생성하는 여러 쿼리가 단일 보기에 포함될 수 있습니다. 요청 시 로드되는 여러 보기로 분할할 것을 고려합니다. 
- Power BI에서는 원시 로그가 아닌 집계된 결과만 추출할 것을 고려합니다.
