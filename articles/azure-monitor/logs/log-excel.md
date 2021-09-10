---
title: Log Analytics 및 Excel 통합
description: Log Analytics 쿼리를 Excel로 가져오고 Excel 내에서 결과를 새로 고칩니다.
ms.topic: conceptual
author: roygalMS
ms.author: roygal
ms.date: 06/10/2021
ms.openlocfilehash: 7cfe8ac2badd292b73b77dd13d7e7ca4bf24a428
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114474128"
---
# <a name="integrate-log-analytics-and-excel"></a>Log Analytics 및 Excel 통합

M 쿼리 및 Log Analytics API를 사용하여 Azure Monitor Log Analytics 및 Microsoft Excel을 통합할 수 있습니다. 이 통합을 사용하면 특정 수의 레코드와 수 MB의 데이터를 보낼 수 있습니다. 이러한 제한은 Azure Portal 섹션의 [Azure Monitor Log Analytics 작업 영역 제한](../service-limits.md#log-analytics-workspaces)에 설명되어 있습니다. 

> [!NOTE]
> Excel은 로컬 클라이언트 애플리케이션이므로, 로컬 하드웨어 및 소프트웨어 제한이 대규모 데이터 집합을 처리하는 성능 및 기능에 영향을 미칩니다.

## <a name="create-your-m-query-in-log-analytics"></a>Log Analytics에서 M 쿼리 생성하기 

1. 일반적으로 Log analytics에서 **쿼리를 생성하고 실행** 합니다. 사용자 인터페이스의 레코드 수 제한에 도달해도 괜찮습니다.  상대 날짜(예: 'ago' 함수 또는 UI 시간 선택기)를 사용해야 Excel이 올바른 데이터 집합을 새로 고칩니다.
  
2. **쿼리 내보내기** -쿼리 및 해당 결과가 만족스러우면 *내보내기* 메뉴에서 Log Analytics **Power BI (M 쿼리)로 내보내기** 메뉴를 사용하여 쿼리를 M으로 내보냅니다.

:::image type="content" source="media/log-excel/export-query.png" alt-text="데이터 및 내보내기 옵션을 사용한 Log Analytics 쿼리" border="true":::



이 옵션을 선택하면 Excel 사용할 수 있는 M 코드가 포함된 .txt 파일이 다운로드됩니다.

위에 표시된 쿼리는 다음 M 코드를 내보냅니다. 다음은 예제에서 쿼리에 대해 내보낸 M 코드의 예입니다.

```m
/*
The exported Power Query Formula Language (M Language ) can be used with Power Query in Excel
and Power BI Desktop.
For Power BI Desktop follow the instructions below: 
1) Download Power BI Desktop from https://powerbi.microsoft.com/desktop/
2) In Power BI Desktop select: 'Get Data' -> 'Blank Query'->'Advanced Query Editor'
3) Paste the M Language script into the Advanced Query Editor and select 'Done'
*/

let AnalyticsQuery =
let Source = Json.Document(Web.Contents("https://api.loganalytics.io/v1/workspaces/ddcfc599-cae0-48ee-9026-fffffffffffff/query", 
[Query=[#"query"="

Heartbeat 
| summarize dcount(ComputerIP) by bin(TimeGenerated, 1h)    
| render timechart
",#"x-ms-app"="OmsAnalyticsPBI",#"timespan"="P1D",#"prefer"="ai.response-thinning=true"],Timeout=#duration(0,0,4,0)])),
TypeMap = #table(
{ "AnalyticsTypes", "Type" }, 
{ 
{ "string",   Text.Type },
{ "int",      Int32.Type },
{ "long",     Int64.Type },
{ "real",     Double.Type },
{ "timespan", Duration.Type },
{ "datetime", DateTimeZone.Type },
{ "bool",     Logical.Type },
{ "guid",     Text.Type },
{ "dynamic",  Text.Type }
}),
DataTable = Source[tables]{0},
Columns = Table.FromRecords(DataTable[columns]),
ColumnsWithType = Table.Join(Columns, {"type"}, TypeMap , {"AnalyticsTypes"}),
Rows = Table.FromRows(DataTable[rows], Columns[name]), 
Table = Table.TransformColumnTypes(Rows, Table.ToList(ColumnsWithType, (c) => { c{0}, c{3}}))
in
Table
in AnalyticsQuery
```

## <a name="connect-query-to-excel"></a>Excel에 쿼리 연결 

쿼리를 가져오려면 다음을 수행합니다. 

1. Microsoft Excel을 엽니다. 
1. 리본에서 **데이터** 메뉴로 이동합니다. **데이터 가져오기** 를 선택합니다. **다른 원본** 에서 **빈 쿼리** 를 선택합니다.
 
   :::image type="content" source="media/log-excel/excel-import-blank-query.png" alt-text="Excel에서 공백 가져오기 옵션" border="true":::

1. 파워 쿼리 창에서 **고급 편집기** 를 선택합니다.

   :::image type="content" source="media/log-excel/advanced-editor.png" alt-text="Excel 고급 쿼리 편집기" border="true":::

 
1. 고급 편집기에서 텍스트를 Log Analytics에서 내보낸 쿼리로 대체합니다.

   :::image type="content" source="media/log-excel/advanced-editor-2.png" alt-text="빈 쿼리 생성하기" border="true":::
 
1. **완료** 를 선택한 다음 **로드 및 닫기** 를 선택합니다. Log Analytics API 및 결과 집합을 사용하여 Excel이 쿼리를 실행합니다.
 

   :::image type="content" source="media/log-excel/excel-query-result.png" alt-text="Excel의 쿼리 결과" border="true":::

> [!Note]
> 레코드 수가 예상보다 적으면 결과 볼륨이 61MiB 제한을 초과할 수 있습니다. 쿼리에서 `project` 또는 `project-away`를 사용하여 열을 필요한 열로 제한합니다.

##  <a name="refreshing--data"></a>데이터 새로 고침

Excel에서 직접 데이터를 새로 고칠 수 있습니다. Excel 리본의 **데이터** 메뉴 그룹에서 **새로 고침** 단추를 선택합니다.
 
## <a name="next-steps"></a>다음 단계

Excel과 외부 데이터 원본의 통합에 대한 자세한 내용은 [외부 데이터 원본에서 데이터 가져오기(파워 쿼리)](https://support.office.com/article/import-data-from-external-data-sources-power-query-be4330b3-5356-486c-a168-b68e9e616f5a)를 참조하세요
