---
title: Azure Data Explorer를 사용하여 Azure Monitor에서 내보낸 데이터 쿼리하기
description: Azure Data Explorer를 사용하여 Log Analytics 작업 영역에서 Azure Storage 계정으로 내보낸 데이터를 쿼리할 수 있습니다.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 10/13/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b50badbf1353ffbb4c5f6fb347cb3c8b1168c97c
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407079"
---
# <a name="query-exported-data-from-azure-monitor-using-azure-data-explorer"></a>Azure Data Explorer를 사용하여 Azure Monitor에서 내보낸 데이터 쿼리하기
Azure Monitor에서 Azure Storage 계정으로 데이터를 내보내면 저렴한 가격으로 보존이 가능하며 여러 지역에 로그를 다시 할당할 수 있습니다. Azure Data Explorer를 사용하여 Log Analytics 작업 영역에서 내보낸 데이터를 쿼리할 수 있습니다. 구성된 후에는 작업 영역에서 Azure Storage 계정으로 전송된 지원하는 테이블을 Azure Data Explorer의 데이터 원본으로 사용할 수 있습니다.

프로세스는 다음과 같습니다. 

1.  Log Analytics 작업 영역에서 Azure Storage 계정으로 데이터를 내보냅니다.
2.  Azure Data Explorer 클러스터에 외부 테이블을 만들고 데이터 형식에 대한 매핑을 만듭니다.
3.  Azure Data Explorer에서 데이터를 쿼리합니다.

:::image type="content" source="media/azure-data-explorer-query-storage/exported-data-query.png" alt-text="Azure Data Explorer가 데이터 쿼리 흐름을 내보냅니다.":::



## <a name="send-data-to-azure-storage"></a>Azure Storage로 데이터 보내기
다음 옵션 중 하나를 사용하여 Azure Monitor 로그를 Azure Storage 계정으로 내보낼 수 있습니다.

- Log Analytics 작업 영역의 모든 데이터를 Azure Storage 계정 또는 이벤트 허브로 내보내려면 Azure Monitor Logs의 Log Analytics 작업 영역 데이터 내보내기 기능을 사용합니다. [Azure Monitor에서 Log Analytics 작업 영역 데이터 내보내기](./logs-data-export.md)를 참조하세요.
- 논리 앱을 사용하여 로그 쿼리에서 예약된 내보내기. 이는 데이터 내보내기 기능과 유사하지만 필터링된 데이터 또는 집계된 데이터를 Azure Storage로 보낼 수 있습니다. 그러나 이 방법에는 [로그 쿼리 한도](../service-limits.md#log-analytics-workspaces)가 적용됩니다. [논리 앱을 사용하여 Log Analytics 작업 영역에서 Azure Storage로 데이터 보관](./logs-export-logic-app.md)을 참조하세요.
- 논리 앱을 사용한 일회성 내보내기 [Logic Apps와 Power Automate용 Azure Monitor Logs 커넥터](./logicapp-flow-connector.md)를 참조하세요.
- PowerShell 스크립트를 사용하여 로컬 컴퓨터에 일회성 내보내기 [Invoke-AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport)를 참조하세요.

> [!TIP]
> 기존 Azure Data Explorer 클러스터를 사용하거나 필요한 구성을 사용하여 새 전용 클러스터를 만들 수 있습니다.

## <a name="create-an-external-table-located-in-azure-blob-storage"></a>Azure blob storage에 있는 외부 테이블 만들기
[외부 테이블](/azure/data-explorer/kusto/query/schema-entities/externaltables)을 사용하여 Azure Data Explorer를 Azure Storage 계정에 연결합니다. 외부 테이블은 Kusto 데이터베이스 외부에 저장된 데이터를 참조하는 Kusto 스키마 엔터티입니다. 테이블과 마찬가지로 외부 테이블에는 잘 정의된 스키마가 있습니다. 테이블과 달리 데이터는 Kusto 클러스터 외부에 저장되고 관리됩니다. 이전 섹션에서 내보낸 데이터는 JSON 줄에 저장됩니다.

참조를 만들려면 내보낸 테이블의 스키마가 필요합니다. Log Analytics에서 [getschema](/azure/data-explorer/kusto/query/getschemaoperator) 연산자를 사용하여 테이블의 열과 해당 데이터 형식을 포함 하는 정보를 검색합니다.

:::image type="content" source="media\azure-data-explorer-query-storage\exported-data-map-schema.jpg" alt-text="Log Analytics 테이블 스키마":::

이제 출력을 사용하여 외부 테이블을 빌드하기 위한 Kusto 쿼리를 만들 수 있습니다.
[Azure Storage 또는 Azure Data Lake에서 외부 테이블 만들기 및 변경](/azure/data-explorer/kusto/management/external-tables-azurestorage-azuredatalake)의 참고 자료에 따라 JSON 형식으로 외부 테이블을 만든 다음 Azure Data Explorer 데이터베이스에서 쿼리를 실행합니다.

>[!NOTE]
>두 개의 프로세스에서 외부 테이블 만들기가 빌드됩니다. 첫 번째는 외부 테이블을 만드는 것이고 두 번째는 매핑을 만드는 것입니다.

다음 PowerShell 스크립트는 테이블 및 매핑에 대한 [만들기](/azure/data-explorer/kusto/management/external-tables-azurestorage-azuredatalake#create-external-table-mapping) 명령을 만듭니다.

```powershell
PARAM(
    $resourcegroupname, #The name of the Azure resource group
    $TableName, # The Log Analytics table you wish to convert to external table
    $MapName, # The name of the map
    $subscriptionId, #The ID of the subscription
    $WorkspaceId, # The Log Analytics WorkspaceId
    $WorkspaceName, # The Log Analytics workspace name
    $BlobURL, # The Blob URL where the data is saved
    $ContainerAccessKey, # The blob container Access Key (Option to add a SAS url)
    $ExternalTableName = $null # The External Table name, null to use the same name
)

if($null -eq $ExternalTableName) {
    $ExternalTableName = $TableName
}

$query = $TableName + ' | getschema | project ColumnName, DataType'

$output = (Invoke-AzOperationalInsightsQuery -WorkspaceId $WorkspaceId -Query $query).Results

$FirstCommand = @()
$SecondCommand = @()

foreach ($record in $output) {
    if ($record.DataType -eq 'System.DateTime') {
        $dataType = 'datetime'
    } elseif ($record.DataType -eq 'System.Int32') {
        $dataType = 'int32'
    } elseif ($record.DataType -eq 'System.Double') {
        $dataType = 'double'
    } else {
        $dataType = 'string'
    }
    $FirstCommand += $record.ColumnName + ":" + "$dataType" + ","
    $SecondCommand += "{`"column`":" + "`"" + $record.ColumnName + "`"," + "`"datatype`":`"$dataType`",`"path`":`"$." + $record.ColumnName + "`"},"
}
$schema = ($FirstCommand -join '') -replace ',$'
$mapping = ($SecondCommand -join '') -replace ',$'

$CreateExternal = @'
.create external table {0} ({1})
kind=blob
partition by (TimeGeneratedPartition:datetime = bin(TimeGenerated, 1min))
pathformat = (datetime_pattern("'y='yyyy'/m='MM'/d='dd'/h='HH'/m='mm", TimeGeneratedPartition))
dataformat=multijson
(
   h@'{2}/WorkspaceResourceId=/subscriptions/{4}/resourcegroups/{6}/providers/microsoft.operationalinsights/workspaces/{5};{3}'
)
with
(
   docstring = "Docs",
   folder = "ExternalTables"
)
'@ -f $TableName, $schema, $BlobURL, $ContainerAccessKey, $subscriptionId, $WorkspaceName.ToLower(), $resourcegroupname.ToLower(),$WorkspaceId

$createMapping = @'
.create external table {0} json mapping "{1}" '[{2}]'
'@ -f $ExternalTableName, $MapName, $mapping

Write-Host -ForegroundColor Red 'Copy and run the following commands (one by one), on your Azure Data Explorer cluster query window to create the external table and mappings:'
write-host -ForegroundColor Green $CreateExternal
Write-Host -ForegroundColor Green $createMapping
```

다음 이미지는 예상된 출력의 예를 보여 줍니다.

:::image type="content" source="media/azure-data-explorer-query-storage/external-table-create-command-output.png" alt-text="ExternalTable 만들기 명령 출력.":::

[![예제 출력](media/azure-data-explorer-query-storage/external-table-create-command-output.png)](media/azure-data-explorer-query-storage/external-table-create-command-output.png#lightbox)

>[!TIP]
>* Azure Data Explorer 클라이언트 도구에서 스크립트의 출력을 복사하여 붙여넣고 실행하여 테이블과 매핑을 만듭니다.
>* 컨테이너 내의 모든 데이터를 사용하려면 스크립트를 변경하고 URL을 'https://your.blob.core.windows.net/containername;SecKey'로 변경합니다.

## <a name="query-the-exported-data-from-azure-data-explorer"></a>Azure Data Explorer에서 내보낸 데이터 쿼리 

매핑을 구성한 후 Azure Data Explorer에서 내보낸 데이터를 쿼리할 수 있습니다. 쿼리에는 다음 예제와 같은 [external_table](/azure/data-explorer/kusto/query/externaltablefunction) 함수가 필요합니다.

```kusto
external_table("HBTest","map") | take 10000
```

[![내보낸 데이터 Log Analytics 쿼리](media/azure-data-explorer-query-storage/external-table-query.png)](media/azure-data-explorer-query-storage/external-table-query.png#lightbox)

## <a name="next-steps"></a>다음 단계

- [Azure Data Explorer에서 쿼리를 작성](/azure/data-explorer/write-queries)하는 방법을 알아봅니다.
