---
title: Azure Blob Storage 모니터링에 대한 모범 사례
description: 모범 사례 지침 및 메트릭과 로그를 사용하여 Azure Blob Storage를 모니터링하는 방법에 대해 알아봅니다.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 07/30/2021
ms.custom: monitoring
ms.openlocfilehash: 54155f2bacd9a593a1288c8d1f95a5843dca2602
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132134783"
---
# <a name="best-practices-for-monitoring-azure-blob-storage"></a>Azure Blob Storage 모니터링에 대한 모범 사례

이 문서에서는 여러 개의 일반적인 스토리지 모니터링 시나리오를 제공하고 이를 수행하기 위한 모범 사례 지침을 제공합니다.

## <a name="identify-storage-accounts-with-no-or-low-use"></a>사용하지 않거나 사용률이 낮은 스토리지 계정 식별

Storage Insights는 Azure Storage 메트릭과 로그를 기반으로 하는 대시보드입니다. Storage Insights를 사용하여 모든 계정의 트랜잭션 볼륨과 사용 용량을 검사할 수 있습니다. 이 정보를 사용하면 사용 중지하려는 계정을 결정할 수 있습니다. Storage Insights를 구성하려면 [Azure Monitor Storage 인사이트를 사용하여 스토리지 서비스 모니터링](../common/storage-insights-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json)을 참조하세요.

### <a name="analyze-transaction-volume"></a>트랜잭션 볼륨 분석

[Azure 모니터의 Storage Insights 보기](../common/storage-insights-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json#view-from-azure-monitor)에서 **트랜잭션** 열을 사용하여 계정을 오름차순으로 정렬합니다. 다음 이미지는 지정된 기간 동안 트랜잭션 볼륨이 낮은 계정을 보여 줍니다.

> [!div class="mx-imgBorder"]
> ![Storage Insights의 트랜잭션 볼륨](./media/blob-storage-monitoring-scenarios/storage-insights-transaction-volume.png)

해당 트랜잭션에 대한 자세한 내용을 보려면 계정 링크를 클릭합니다. 이 예제에서 대부분의 요청은 Blob Storage 서비스에 대해 수행됩니다.

> [!div class="mx-imgBorder"]
> ![서비스 유형별 트랜잭션](./media/blob-storage-monitoring-scenarios/storage-insights-transactions-by-storage-type.png)

수행되는 요청의 유형을 확인하려면 **API 이름별 트랜잭션** 차트를 자세히 살펴보세요.

> [!div class="mx-imgBorder"]
> ![스토리지 트랜잭션 API](./media/blob-storage-monitoring-scenarios/storage-insights-transaction-apis.png)

이 예제에서 모든 요청은 목록 작업 또는 계정 속성 정보에 대한 요청입니다. 읽기 및 쓰기 트랜잭션이 없습니다. 따라서 계정이 의미 있는 방식으로 사용되지 않는다고 생각할 수 있습니다.

### <a name="analyze-used-capacity"></a>사용된 용량 분석

[Azure monitor의 Storage Insights 보기](../common/storage-insights-overview.md#view-from-azure-monitor)의 **용량** 탭에서 **Account used Capacity** 열을 사용 하 여 계정을 오름차순으로 정렬 합니다. 다음 이미지는 다른 계정보다 용량 볼륨이 낮은 계정을 보여 줍니다.

> [!div class="mx-imgBorder"]
> ![사용된 스토리지 용량](./media/blob-storage-monitoring-scenarios/storage-insights-capacity-used.png)

이 사용된 용량과 연결된 Blob을 검사하기 위해 Storage Explorer를 사용할 수 있습니다. Blob이 많은 경우 [Blob 인벤토리 정책](blob-inventory.md)을 사용하여 보고서를 생성하는 것이 좋습니다.

## <a name="monitor-the-use-of-a-container"></a>컨테이너 사용 모니터링

고객의 데이터를 컨테이너별로 분할하면 각 고객이 사용하는 용량을 모니터링할 수 있습니다. Azure Storage blob 인벤토리를 사용하여 크기 정보가 있는 blob의 인벤토리를 가져올 수 있습니다. 그런 다음, 컨테이너 수준에서 크기와 개수를 집계할 수 있습니다. 예를 들어 [Azure Storage 인벤토리를 사용하여 컨테이너당 Blob 수 및 총 크기 계산](calculate-blob-count-size.md)을 참조하세요.

로그를 쿼리하여 컨테이너 수준에서 트래픽을 평가할 수도 있습니다. Log Analytics 쿼리 작성에 대한 자세한 내용은 [Log Analytics](../../azure-monitor/logs/log-analytics-tutorial.md)를 참조하세요. 스토리지 로그 스키마에 대한 자세한 내용은 [Azure Blob Storage 모니터링 데이터 참조](monitor-blob-storage-reference.md#resource-logs-preview)를 참조하세요.

다음은 읽기 트랜잭션 수와 각 컨테이너에서 읽은 바이트 수를 가져오는 쿼리입니다.

```kusto
StorageBlobLogs
| where OperationName  == "GetBlob"
| extend ContainerName = split(parse_url(Uri).Path, "/")[1]
| summarize ReadSize = sum(ResponseBodySize), ReadCount = count() by tostring(ContainerName)
```

다음 쿼리에서는 유사한 쿼리를 사용하여 쓰기 작업에 대한 정보를 가져옵니다.

```kusto
StorageBlobLogs
| where OperationName == "PutBlob" or
  OperationName == "PutBlock" or
  OperationName == "PutBlockList" or
  OperationName == "AppendBlock" or
  OperationName == "SnapshotBlob" or
  OperationName == "CopyBlob" or
  OperationName == "SetBlobTier"
| extend ContainerName = split(parse_url(Uri).Path, "/")[1]
| summarize WriteSize = sum(RequestBodySize), WriteCount = count() by tostring(ContainerName)
```

두 가지 이상의 작업 유형이 쓰기 작업으로 간주될 수 있으므로 위의 쿼리는 여러 작업의 이름을 참조합니다. 읽기 및 쓰기 작업으로 간주되는 작업에 대해 자세히 알아보려면 [Azure Blob Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/blobs/) 또는 [Azure Data Lake Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/data-lake/)을 참조하세요.

## <a name="audit-account-activity"></a>계정 작업 감사

대부분의 경우, 보안 및 규정 준수를 위해 스토리지 계정의 활동을 감사해야 합니다. 스토리지 계정에 대한 작업은 *컨트롤 플레인* 과 *데이터 평면* 의 두 가지 범주로 나뉩니다.

컨트롤 플레인 작업은 스토리지 계정을 만들거나 기존 스토리지 계정의 속성을 업데이트하기 위한 모든 Azure Resource Manager 요청입니다. 자세한 내용은 [Azure Resource Manager](../../azure-resource-manager/management/overview.md)를 참조하세요.

데이터 평면 작업은 스토리지 서비스 엔드포인트에 대한 요청 때문에 발생하는 스토리지 계정의 데이터에 대한 작업입니다. 예를 들어 스토리지 계정에 Blob을 업로드하거나 스토리지 계정에서 Blob을 다운로드할 때 데이터 평면 작업이 실행됩니다. 자세한 내용은 [Azure Storage API](/rest/api/storageservices/)를 참조하세요.

이 섹션에서는 컨트롤 플레인 및 데이터 평면 작업의 “언제”, “누가”, “무엇을”, “어떻게” 정보를 식별하는 방법을 보여줍니다.

### <a name="auditing-control-plane-operations"></a>컨트롤 플레인 작업 감사

Resource Manager 작업은 [Azure 활동 로그](../../azure-monitor/essentials/activity-log.md)에 캡처됩니다. 활동 로그를 보려면 Azure Portal에서 스토리지 계정을 연 다음, **활동 로그** 를 선택합니다.

> [!div class="mx-imgBorder"]
> ![활동 로그](./media/blob-storage-monitoring-scenarios/activity-log.png)

모든 로그 항목을 열어 활동을 설명하는 JSON을 표시합니다. 다음 JSON은 컨트롤 플레인 작업의 “언제”, “무엇을”, “어떻게” 정보를 보여줍니다.

> [!div class="mx-imgBorder"]
> ![활동 로그 JSON](./media/blob-storage-monitoring-scenarios/activity-log-json.png)

“누가” 정보의 가용성은 컨트롤 플레인 작업을 수행하는 데 사용된 인증 메서드에 따라 다릅니다. Azure AD 보안 주체가 권한 부여를 수행한 경우 해당 보안 주체의 개체 식별자도 이 JSON 출력에 나타납니다(예: `"http://schemas.microsoft.com/identity/claims/objectidentifier": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx"`). 이메일 주소나 이름과 같은 기타 ID 관련 정보가 항상 표시되는 것은 아니므로 개체 식별자는 항상 보안 주체를 고유하게 식별하는 가장 좋은 방법입니다.

개체 식별자 값을 사용하고 Azure Portal의 Azure AD 페이지에서 보안 주체를 검색하여 친숙한 해당 보안 주체의 이름을 찾을 수 있습니다. 다음 스크린샷에서는 Azure AD의 검색 결과를 보여줍니다.

> [!div class="mx-imgBorder"]
> ![Azure Active Directory 검색](./media/blob-storage-monitoring-scenarios/search-azure-active-directory.png)

### <a name="auditing-data-plane-operations"></a>데이터 평면 작업 감사

데이터 평면 작업은 [스토리지용 Azure 리소스 로그](monitor-blob-storage.md#analyzing-logs)에 캡처됩니다. 기본 쿼리 환경을 위해 Log Analytics 작업 영역으로 로그를 내보내도록 [진단 설정을 구성](monitor-blob-storage.md#send-logs-to-azure-log-analytics)할 수 있습니다.

다음은 로그 항목 목록에서 “언제”, “누가”, “무엇을”, “어떻게” 정보를 검색하는 Log Analytics 쿼리입니다.

```kusto
StorageBlobLogs
| where TimeGenerated > ago(3d)
| project TimeGenerated, AuthenticationType, RequesterObjectId, OperationName, Uri
```

감사의 “언제” 부분에서 `TimeGenerated` 필드는 로그 항목이 기록된 시간을 보여줍니다.

감사의 “무엇” 부분에서 `Uri` 필드는 항목을 수정했거나 읽었는지 보여줍니다.

감사의 “어떻게” 부분에서 `OperationName` 필드는 실행된 작업을 보여줍니다.

감사의 “누구” 부분에서 `AuthenticationType`은 요청에 사용된 인증 유형을 보여줍니다. 이 필드는 계정 키, SAS 토큰 또는 Azure AD(Azure Active Directory) 인증 사용을 포함하여 Azure Storage가 지원하는 인증 유형을 표시할 수 있습니다.

#### <a name="identifying-the-security-principal-used-to-authorize-a-request"></a>요청에 권한을 부여 하는 데 사용 되는 보안 주체 식별

요청이 Azure AD를 사용하여 인증된 경우 `RequesterObjectId` 필드는 보안 주체를 식별하는 가장 안정적인 방법을 제공합니다. `RequesterObjectId` 필드를 사용하고 Azure Portal의 Azure AD 페이지에서 보안 주체를 검색하여 친숙한 해당 보안 주체의 이름을 찾을 수 있습니다. 다음 스크린샷에서는 Azure AD의 검색 결과를 보여 줍니다.

> [!div class="mx-imgBorder"]
> ![Azure Active Directory 검색](./media/blob-storage-monitoring-scenarios/search-azure-active-directory.png)

경우에 따라 사용자 계정 이름 또는 *UPN* 이 로그에 나타날 수 있습니다. 예를 들어, 보안 주체가 Azure AD 사용자이면 UPN이 표시될 수 있습니다. 사용자가 할당한 관리 ID와 같은 다른 보안 주체 유형의 경우 또는 교차 Azure AD 테넌트 인증과 같은 특정 시나리오에서는 UPN이 로그에 표시되지 않습니다.

이 쿼리는 OAuth 보안 주체가 수행한 모든 읽기 작업을 보여 줍니다.

```kusto
StorageBlobLogs
| where TimeGenerated > ago(3d)
  and OperationName == "GetBlob"
  and AuthenticationType == "OAuth"
| project TimeGenerated, AuthenticationType, RequesterObjectId, OperationName, Uri
```

공유 키 및 SAS 인증에서는 개별 ID를 감사할 수단을 제공하지 않습니다. 따라서 ID 기반 감사 기능을 개선하려면 Azure AD로 전환하고 공유 키와 SAS 인증을 방지하는 것이 좋습니다. 공유 키와 SAS 인증을 방지하는 방법을 알아보려면 [Azure Storage 계정에 대한 공유 키 인증 방지](../common/shared-key-authorization-prevent.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=portal)를 참조하세요. Azure AD를 시작 하려면 [Azure Active Directory를 사용 하 여 blob에 대 한 액세스 권한 부여](authorize-access-azure-active-directory.md)를 참조 하세요.

#### <a name="identifying-the-sas-token-used-to-authorize-a-request"></a>요청에 권한을 부여 하는 데 사용 되는 SAS 토큰 식별

SAS 토큰을 사용 하 여 권한이 부여 된 작업을 쿼리할 수 있습니다. 예를 들어이 쿼리는 SAS 토큰을 사용 하 여 권한이 부여 된 모든 쓰기 작업을 반환 합니다.

```kusto
StorageBlobLogs
| where TimeGenerated > ago(3d)
  and OperationName == "PutBlob"
  and AuthenticationType == "SAS"
| project TimeGenerated, AuthenticationType, AuthenticationHash, OperationName, Uri
```

보안상의 이유로 SAS 토큰은 로그에 표시 되지 않습니다. 그러나 SAS 토큰의 SHA-256 해시는이 쿼리에서 반환 되는 필드에 표시 됩니다 `AuthenticationHash` . 

여러 SAS 토큰을 배포 하 고 어떤 SAS 토큰을 사용 하 고 있는지 확인 하려면 각 SAS 토큰을 SHA-256 해시로 변환한 다음 해당 해시를 로그에 표시 되는 해시 값과 비교 해야 합니다.

먼저 각 SAS 토큰 문자열을 디코딩합니다. 다음 예제에서는 PowerShell을 사용 하 여 SAS 토큰 문자열을 디코딩합니다.

```powershell
[uri]::UnescapeDataString("<SAS token goes here>")
```

그런 다음 해당 문자열을 [Get FileHash](/powershell/module/microsoft.powershell.utility/get-filehash) PowerShell cmdlet에 전달할 수 있습니다. 예제는 [예제 4: 문자열의 해시 계산](/powershell/module/microsoft.powershell.utility/get-filehash#example-4--compute-the-hash-of-a-string)을 참조 하세요.

또는 디코딩된 문자열을 kusto 쿼리의 일부로 [hash_sha256 ()](/data-explorer/kusto/query/sha256hashfunction) 함수에 전달할 수 있습니다.

## <a name="optimize-cost-for-infrequent-queries"></a>자주 발생하지 않는 쿼리 비용 최적화

풍부한 네이티브 쿼리 기능을 위해 로그를 Log Analytics로 내보낼 수 있습니다. 스토리지 계정에 많은 트랜잭션이 있는 경우, Log Analytics에서 로그를 사용하는 비용이 높을 수 있습니다. 자세한 내용은 [Azure Log Analytics 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)을 참조 하세요. 가끔 로그를 쿼리하려는 경우(예: 준수 감사에 대한 로그 쿼리), 로그를 스토리지 계정으로 내보낸 다음, 로그 데이터 위에 서버리스 쿼리 솔루션을 사용하여 총 비용을 절감할 수 있습니다(예: Azure Synapse).

Azure Synapse를 사용하면 서버리스 SQL 풀을 만들어 필요할 때 로그 데이터를 쿼리할 수 있습니다. 이렇게 하면 비용을 크게 절감할 수 있습니다.

1. 스토리지 계정으로 로그를 내보냅니다. 자세한 내용은 [진단 설정 만들기](monitor-blob-storage.md#creating-a-diagnostic-setting)를 참조 하세요.

2. Synapse 작업 영역을 만들고 구성합니다. 자세한 내용은 [빠른 시작: Synapse 작업 영역 만들기](../../synapse-analytics/quickstart-create-workspace.md)를 참조 하세요.

2. 로그를 쿼리합니다. 자세한 내용은 [Azure Synapse Analytics에서 서버를 사용 하지 않는 SQL 풀을 사용 하 여 JSON 파일 쿼리](../../synapse-analytics/sql/query-json-files.md)를 참조 하세요.

   예를 들면 다음과 같습니다.

   ```sql
    select
        JSON_VALUE(doc, '$.time') AS time,
        JSON_VALUE(doc, '$.properties.accountName') AS accountName,
        JSON_VALUE(doc, '$.identity.type') AS identityType,
        JSON_VALUE(doc, '$.identity.requester.objectId') AS requesterObjectId,
        JSON_VALUE(doc, '$.operationName') AS operationName,
        JSON_VALUE(doc, '$.callerIpAddress') AS callerIpAddress,
        JSON_VALUE(doc, '$.uri') AS uri
        doc
    from openrowset(
            bulk 'https://demo2uswest4log.blob.core.windows.net/insights-logs-storageread/resourceId=/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/mytestrp/providers/Microsoft.Storage/storageAccounts/demo2uswest/blobServices/default/y=2021/m=03/d=19/h=*/m=*/PT1H.json',
            format = 'csv', fieldterminator ='0x0b', fieldquote = '0x0b'
        ) with (doc nvarchar(max)) as rows
    order by JSON_VALUE(doc, '$.time') desc

   ```

## <a name="see-also"></a>참고 항목

- [Azure Blob Storage 모니터링](monitor-blob-storage.md).
- [Azure Blob Storage 모니터링 데이터 참조](monitor-blob-storage-reference.md)
- [자습서: Azure Data Explorer 및 Azure Monitor에서 Kusto 쿼리 사용](/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer).
- [Azure Monitor에서 로그 쿼리 시작](../../azure-monitor/logs/get-started-queries.md).
