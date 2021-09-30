---
title: Azure Blob Storage 모니터링 데이터 참조 | Microsoft Docs
description: Azure Blob Storage의 모니터링 데이터를 위한 로그 및 메트릭 참조입니다.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: subject-monitoring
ms.openlocfilehash: 866b56446ce962a47ac7235ac260e54e5fcacf76
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129275699"
---
# <a name="azure-blob-storage-monitoring-data-reference"></a>Azure Blob Storage 모니터링 데이터 참조

Azure Storage의 모니터링 데이터를 수집하고 분석하는 방법은 [Azure Storage 모니터링](monitor-blob-storage.md)을 참조하세요.

## <a name="metrics"></a>메트릭

다음 표에는 Azure Storage에 대해 수집된 플랫폼 메트릭이 나와 있습니다.

### <a name="capacity-metrics"></a>용량 메트릭

용량 메트릭 값은 매일(최대 24시간) 새로 고쳐집니다. 시간 조직은 메트릭 값이 표시되는 시간 간격을 정의합니다. 모든 용량 메트릭에 대해 지원되는 시간 조직은 1시간(PT1H)입니다.

Azure Storage는 Azure Monitor에서 다음과 같은 용량 메트릭을 제공합니다.

#### <a name="account-level"></a>계정 수준

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="blob-storage"></a>Blob Storage

다음 표에서는 [Blob 스토리지 메트릭](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsblobservices)을 보여줍니다.

| 메트릭 | Description |
| ------------------- | ----------------- |
| BlobCapacity | 스토리지 계정에 사용한 Blob Storage의 총계입니다. <br/><br/> 단위: 바이트 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 <br/> 차원: **BlobType** 및 **BlobTier**([정의](#metrics-dimensions)) |
| BlobCount    | 스토리지 계정에 저장된 Blob 개체 수입니다. <br/><br/> 단위: 개수 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 <br/> 차원: **BlobType** 및 **BlobTier**([정의](#metrics-dimensions)) |
| BlobProvisionedSize | 스토리지 계정에 프로비전된 스토리지 양입니다. 이 메트릭은 프리미엄 스토리지 계정에만 적용됩니다. <br/><br/> 단위: 바이트 <br/> 집계 유형: 평균 |
| ContainerCount    | 스토리지 계정의 컨테이너 수입니다. <br/><br/> 단위: 개수 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 |
| IndexCapacity     | ADLS Gen2 계층적 인덱스에 사용한 스토리지 양입니다. <br/><br/> 단위: 바이트 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 |

### <a name="transaction-metrics"></a>트랜잭션 메트릭

트랜잭션 메트릭은 스토리지 계정에 대한 모든 요청이 있을 때 Azure Storage에서 Azure Monitor로 내보내집니다. 스토리지 계정에 작업이 없는 경우 해당 기간에 트랜잭션 메트릭에도 데이터가 없습니다. 모든 트랜잭션 메트릭은 계정 및 Blob 스토리지 서비스 수준에서 모두 사용할 수 있습니다. 시간 조직은 메트릭 값이 표시되는 시간 간격을 정의합니다. 모든 트랜잭션 메트릭에 대해 지원되는 시간 조직은 PT1H 및 PT1M입니다.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>메트릭 차원

Azure Storage는 Azure Monitor의 메트릭에 대해 다음과 같은 차원을 지원합니다.

### <a name="dimensions-available-to-all-storage-services"></a>모든 스토리지 서비스에 사용할 수 있는 차원

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

### <a name="dimensions-specific-to-blob-storage"></a>Blob 스토리지와 관련된 차원

| 차원 이름 | Description |
| ------------------- | ----------------- |
| **BlobType** | Blob 메트릭용 Blob 형식만. 지원되는 값은 **BlockBlob**, **PageBlob** 및 **Azure Data Lake Storage** 입니다. 추가 Blob은 **BlockBlob** 에 포함됩니다. |
| **BlobTier** | Azure Storage는 가장 비용 효율적인 방식으로 Blob 개체 데이터를 저장할 수 있도록 여러 액세스 계층을 제공합니다. [Azure Storage Blob 계층](../blobs/access-tiers-overview.md)을 참조하세요. 지원되는 값은 다음과 같습니다. <br/> <li>**핫**: 핫 액세스 계층</li> <li>**쿨**: 쿨 액세스 계층</li> <li>**보관**: 보관 액세스 계층</li> <li>**프리미엄**: 블록 Blob 프리미엄 계층</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60**: 프리미엄 페이지 Blob 계층 유형</li> <li>**표준**: 표준 페이지 Blob 계층 유형</li> <li>**계층 없음**: 범용 v1 스토리지 계정 계층 유형</li> |

메트릭 지원 차원의 경우 해당 메트릭 값을 보려면 차원 값을 지정해야 합니다. 예를 들어 성공적인 응답에 대한 **트랜잭션** 값을 조사하는 경우 **성공** 을 포함한 **ResponseType** 차원을 필터링해야 합니다. 블록 Blob에 대한 **BlobCount** 값을 조사하는 경우 **BlockBlob** 을 포함한 **BlobType** 차원을 필터링해야 합니다.

## <a name="resource-logs-preview"></a>리소스 로그(미리 보기)

> [!NOTE]
> Azure Monitor Azure Storage 로그는 공개 미리 보기로 제공되며 모든 퍼블릭 및 미국 정부 클라우드 지역에서 미리 보기 테스트에 사용할 수 있습니다. 이 미리 보기에서는 BLOB(Azure Data Lake Storage Gen2 포함), 파일, 큐, 테이블, 범용 v1의 프리미엄 스토리지 계정, 범용 v2 스토리지 계정에 로그를 사용할 수 있습니다. 클래식 스토리지 계정은 지원되지 않습니다.

다음 표에는 Azure Monitor Logs 또는 Azure Storage에서 수집되는 경우 Azure Storage 리소스 로그의 속성이 나와 있습니다. 속성은 작업, 서비스, 작업을 수행하는 데 사용된 인증 유형을 설명합니다.

### <a name="fields-that-describe-the-operation"></a>작업을 설명하는 필드

```json
{
    "time": "2019-02-28T19:10:21.2123117Z",
    "resourceId": "/subscriptions/12345678-2222-3333-4444-555555555555/resourceGroups/mytestrp/providers/Microsoft.Storage/storageAccounts/testaccount1/blobServices/default",
    "category": "StorageWrite",
    "operationName": "PutBlob",
    "operationVersion": "2017-04-17",
    "schemaVersion": "1.0",
    "statusCode": 201,
    "statusText": "Success",
    "durationMs": 5,
    "callerIpAddress": "192.168.0.1:11111",
    "correlationId": "ad881411-201e-004e-1c99-cfd67d000000",
    "location": "uswestcentral",
    "uri": "http://mystorageaccount.blob.core.windows.net/cont1/blobname?timeout=10"
}
```

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>작업이 인증된 방식을 설명하는 필드

```json
{
    "identity": {
        "authorization": [
            {
                "action": "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
                "principals": [
                    {
                        "id": "fde5ba15-4355-4223-b811-cccccccccccc",
                        "type": "User"
                    }
                ],
                "roleAssignmentId": "ecf75cb8-491c-4a25-ad6e-aaaaaaaaaaaa",
                "roleDefinitionId": "b7e6dc6d-f1e8-4753-8033-ffffffffffff"
            }
        ],
        "requester": {
            "appId": "691458b9-1327-4635-9f55-bbbbbbbbbbbb",
            "audience": "https://storage.azure.com/",
            "objectId": "fde5ba15-4355-4223-b811-cccccccccccc",
            "tenantId": "72f988bf-86f1-41af-91ab-dddddddddddd",
            "tokenIssuer": "https://sts.windows.net/72f988bf-86f1-41af-91ab-eeeeeeeeeeee/"
           },
        "type": "OAuth"
    },
}

```

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>서비스를 설명하는 필드

```json
{
    "properties": {
        "accountName": "testaccount1",
        "requestUrl": "https://testaccount1.blob.core.windows.net:443/upload?restype=container&comp=list&prefix=&delimiter=%2F&marker=&maxresults=30&include=metadata&_=1551405598426",
        "userAgentHeader": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.140 Safari/537.36 Edge/17.17134",
        "referrerHeader": "blob:https://ms.portal.azure.com/6f50025f-3b88-488d-b29e-3c592a31ddc9",
        "clientRequestId": "",
        "etag": "",
        "serverLatencyMs": 63,
        "serviceType": "blob",
        "operationCount": 0,
        "requestHeaderSize": 2658,
        "requestBodySize": 0,
        "responseHeaderSize": 295,
        "responseBodySize": 2018,
        "contentLengthHeader": 0,
        "requestMd5": "",
        "serverMd5": "",
        "lastModifiedTime": "",
        "conditionsUsed": "",
        "smbTreeConnectID" : "0x3",
        "smbPersistentHandleID" : "0x6003f",
        "smbVolatileHandleID" : "0xFFFFFFFF00000065",
        "smbMessageID" : "0x3b165",
        "smbCreditsConsumed" : "0x3",
        "smbCommandDetail" : "0x2000 bytes at offset 0xf2000",
        "smbFileId" : " 0x9223442405598953",
        "smbSessionID" : "0x8530280128000049",
        "smbCommandMajor" : "0x6",
        "smbCommandMinor" : "DirectoryCloseAndDelete"
    }

}
```

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>참고 항목

- Azure Storage 모니터링에 대한 설명은 [Azure Storage 모니터링](monitor-blob-storage.md)을 참조하세요.
- Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../../azure-monitor/essentials/monitor-azure-resource.md)을 참조하세요.
