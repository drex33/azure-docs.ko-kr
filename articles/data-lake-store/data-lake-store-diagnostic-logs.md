---
title: Azure Data Lake Storage Gen1에 대한 진단 로그 보기 | Microsoft Docs
description: 'Azure Data Lake Storage Gen1에 대한 진단 로그를 설정하고 액세스하는 방법을 이해합니다. '
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 03/26/2018
ms.author: normesta
ms.openlocfilehash: 325bca316aaf4add854ea473f71c9f9883fa42eb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602938"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1에 대한 진단 로그 액세스
Azure Data Lake Storage Gen1 계정에 대해 진단 로깅을 사용하도록 설정하는 방법 및 계정에 대해 수집된 로그를 보는 방법을 알아봅니다.

조직은 Azure Data Lake Storage Gen1 계정에 대해 진단 로깅을 사용하도록 설정하여 데이터에 액세스하는 사용자 목록, 데이터가 액세스되는 빈도, 계정에 저장된 데이터 양과 같은 정보를 제공하는 데이터 액세스 감사 추적을 수집할 수 있습니다. 사용하도록 설정되면 진단 및 요청은 최대한 가능한 한도 내에서 로그됩니다. 서비스 엔드포인트에 대한 요청이 있는 경우에만 요청 및 진단 로그 항목이 만들어집니다.

## <a name="prerequisites"></a>사전 요구 사항
* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* **Azure Data Lake Storage Gen1 계정**. [Azure Portal을 사용하여 Azure Data Lake Storage Gen1 시작](data-lake-store-get-started-portal.md)에 있는 지침을 따릅니다.

## <a name="enable-diagnostic-logging-for-your-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1 계정에 대해 진단 로깅 사용
1. 새로운 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Data Lake Storage Gen1 계정을 열고, Data Lake Storage Gen1 계정 블레이드에서 **진단 설정** 을 클릭합니다.
3. **진단 설정** 블레이드에서 **진단 켜기** 를 클릭합니다.

    ![진단 설정 옵션과 진단 켜기 옵션이 강조 표시된 Data Lake Storage Gen 1 계정의 스크린샷.](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "진단 로그 활성화")

3. **진단 설정** 블레이드에서 다음과 같이 변경하여 진단 로깅을 구성합니다.
   
    ![이름 텍스트 상자와 저장 옵션이 강조 표시된 진단 설정 섹션의 스크린샷.](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "진단 로그 활성화")
   
   * **이름** 으로 진단 로그 구성에 대한 값을 입력합니다.
   * 다양한 방법으로 데이터를 저장/처리하도록 선택할 수 있습니다.
     
        * Azure Storage 계정에 로그를 저장하려면 **스토리지 계정에 보관** 옵션을 선택합니다. 나중에 배치로 처리할 데이터를 보관하려는 경우 이 옵션을 사용합니다. 이 옵션을 선택하는 경우 Azure Storage 계정을 제공하여 로그를 저장해야 합니다.
        
        * Azure Event Hub에 로그 데이터를 스트리밍하려면 **이벤트 허브로 스트리밍** 옵션을 선택합니다. 들어오는 로그를 실시간으로 분석하는 다운스트림 처리 파이프라인을 사용하는 경우 대개 이 옵션을 사용합니다. 이 옵션을 선택하는 경우 사용하려는 Azure 이벤트 허브에 대한 세부 정보를 제공해야 합니다.

        * Azure Monitor 서비스를 사용하여 생성된 로그 데이터를 분석하려면 **Log Analytics로 전송** 옵션을 선택합니다. 이 옵션을 선택하는 경우 로그 분석을 수행하는 데 사용하는 Log Analytics 작업 영역에 세부 정보를 제공해야 합니다. Azure Monitor 로그 사용에 대한 자세한 내용은 [Azure Monitor 로그 검색을 사용하여 수집한 데이터 보기 또는 분석](../azure-monitor/logs/log-analytics-tutorial.md)을 참조하세요.
     
   * 감사 로그 또는 요청 로그를 가져올지, 혹은 둘 모두를 가져올지를 지정합니다.
   * 데이터를 유지해야 하는 일 수를 지정합니다. Azure Storage 계정을 사용하여 로그 데이터를 보관하는 경우에만 보존 기능이 적용됩니다.
   * **저장** 을 클릭합니다.

진단 설정을 사용하도록 설정했으면 **진단 로그** 탭에서 로그를 볼 수 있습니다.

## <a name="view-diagnostic-logs-for-your-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1 계정에 대한 진단 로그 보기
두 가지 방법으로 Data Lake Storage Gen1 계정에 대한 로그 데이터를 볼 수 있습니다.

* Data Lake Storage Gen1 계정 설정 보기에서
* 데이터가 저장된 Azure Storage 계정에서

### <a name="using-the-data-lake-storage-gen1-settings-view"></a>Data Lake Storage Gen1 설정 보기 사용
1. Data Lake Storage Gen1 계정 **설정** 블레이드에서 **진단 로그** 를 클릭합니다.
   
    ![진단 로그 보기](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "진단 로그 보기") 
2. **진단 로그** 블레이드에 **감사 로그** 및 **요청 로그** 로 분류된 로그가 표시됩니다.
   
   * 요청 로그는 Data Lake Storage Gen1 계정에 대한 모든 API 요청을 캡처합니다.
   * 감사 로그는 요청 로그와 비슷하지만 Data Lake Storage Gen1 계정에 대해 수행된 작업의 세부적인 분석 결과를 제공합니다. 예를 들어, 요청 로그에서 단일 업로드 API 호출은 감사 로그에서 여러 "추가" 작업을 발생시킬 수 있습니다.
3. 로그를 다운로드하려면 각 로그 항목에 대한 **다운로드** 링크를 클릭합니다.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>로그 데이터를 포함하는 Azure Storage 계정에서
1. 로깅을 위해 Data Lake Storage Gen1과 연결된 Azure Storage 계정 블레이드를 열고 Blob을 클릭합니다. **Blob service** 블레이드는 두 개의 컨테이너를 나열합니다.
   
    ![Blob 옵션이 선택되고 Blob 서비스 블레이드에서 2개의 Blob 서비스 이름이 강조 표시된 Data Lake Storage Gen 1 블레이드의 스크린샷.](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "진단 로그 보기")
   
   * **insights-logs-audit** 컨테이너는 감사 로그를 포함합니다.
   * **insights-logs-requests** 컨테이너는 요청 로그를 포함합니다.
2. 이러한 컨테이너 내에서 로그는 다음 구조로 저장됩니다.
   
    ![컨테이너에 저장된 로그 구조의 스크린샷.](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "진단 로그 보기")
   
    예를 들어, 감사 로그에 대한 전체 경로는 `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    마찬가지로 요청 로그에 대한 전체 경로는 `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=14/m=00/PT1H.json`과 같을 수 있습니다.

## <a name="understand-the-structure-of-the-log-data"></a>로그 데이터의 구조 이해
감사 로그 및 요청 로그는 JSON 형식입니다. 이 섹션에서는 요청 로그 및 감사 로그에 대한 JSON의 구조를 살펴봅니다.

### <a name="request-logs"></a>요청 로그
다음은 JSON 형식인 요청 로그의 샘플 항목입니다. 각 Blob는 **레코드** 라는 하나의 루트 개체를 포함하며 여기에는 로그 개체의 배열이 포함됩니다.

```json
{
"records": 
  [        
    . . . .
    ,
    {
        "time": "2016-07-07T21:02:53.456Z",
        "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
        "category": "Requests",
        "operationName": "GETCustomerIngressEgress",
        "resultType": "200",
        "callerIpAddress": "::ffff:1.1.1.1",
        "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
        "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
        "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"StoreIngressSize":0 ,"StoreEgressSize":4096,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z","QueryParameters":"api-version=<version>&op=<operationName>"}
    }
    ,
    . . . .
  ]
}
```

#### <a name="request-log-schema"></a>요청 로그 스키마
| 이름 | 유형 | Description |
| --- | --- | --- |
| time |String |로그의 타임스탬프(UTC) |
| resourceId |String |작업이 수행되는 리소스의 ID |
| category |String |로그 범주 예: **Requests** |
| operationName |String |기록된 작업의 이름 예를 들어 getfilestatus |
| resultType |String |작업의 상태, 예를 들어 200 |
| callerIpAddress |String |요청한 클라이언트의 IP 주소 |
| correlationId |String |관련된 로그 항목의 집합을 그룹화하는 데 사용할 수 있는 로그의 ID |
| identity |Object |로그를 생성하는 ID |
| properties |JSON |자세한 내용은 다음을 참조하세요. |

#### <a name="request-log-properties-schema"></a>요청 로그 속성 스키마
| 이름 | 유형 | 설명 |
| --- | --- | --- |
| HttpMethod |String |작업에 사용된 HTTP 메서드 예를 들어 GET |
| 경로 |String |작업이 수행된 경로 |
| RequestContentLength |int |HTTP 요청의 콘텐츠 길이 |
| ClientRequestId |String |이 요청을 고유하게 식별하는 ID |
| StartTime |String |서버가 요청을 받은 시간 |
| EndTime |String |서버가 응답을 전송한 시간 |
| StoreIngressSize |long |Data Lake Store에 수신되는 크기(바이트) |
| StoreEgressSize |long |Data Lake Store에서 송신되는 크기(바이트) |
| QueryParameters |String |설명: http 쿼리 매개 변수입니다. 예 1: api-version=2014-01-01&op=getfilestatus 예 2: op=APPEND&append=true&syncFlag=DATA&filesessionid=bee3355a-4925-4435-bb4d-ceea52811aeb&leaseid=bee3355a-4925-4435-bb4d-ceea52811aeb&offset=28313319&api-version=2017-08-01 |

### <a name="audit-logs"></a>감사 로그
다음은 JSON 형식인 감사 로그의 샘플 항목입니다. 각 Blob는 **레코드** 라는 하나의 루트 개체를 포함하며 여기에는 로그 개체의 배열이 포함됩니다.

```json
{
"records": 
  [        
    . . . .
    ,
    {
        "time": "2016-07-08T19:08:59.359Z",
        "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
        "category": "Audit",
        "operationName": "SeOpenStream",
        "resultType": "0",
        "resultSignature": "0",
        "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
        "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
        "properties": {"StreamName":"adl://<data_lake_storage_gen1_account_name>.azuredatalakestore.net/logs.csv"}
    }
    ,
    . . . .
  ]
}
```

#### <a name="audit-log-schema"></a>감사 로그 스키마
| 이름 | 유형 | Description |
| --- | --- | --- |
| time |String |로그의 타임스탬프(UTC) |
| resourceId |String |작업이 수행되는 리소스의 ID |
| category |String |로그 범주 예: **Audit**. |
| operationName |String |기록된 작업의 이름 예를 들어 getfilestatus |
| resultType |String |작업의 상태, 예를 들어 200 |
| resultSignature |String |작업에 대한 추가 세부 정보입니다. |
| correlationId |String |관련된 로그 항목의 집합을 그룹화하는 데 사용할 수 있는 로그의 ID |
| identity |Object |로그를 생성하는 ID |
| properties |JSON |자세한 내용은 다음을 참조하세요. |

#### <a name="audit-log-properties-schema"></a>감사 로그 속성 스키마
| 이름 | 유형 | 설명 |
| --- | --- | --- |
| StreamName |String |작업이 수행된 경로 |

## <a name="samples-to-process-the-log-data"></a>로그 데이터를 처리하는 샘플
Azure Data Lake Storage Gen1에서 Azure Monitor 로그로 로그를 보낼 때(Azure Monitor 로그를 사용하는 방법에 대한 자세한 내용은 [Azure Monitor 로그 검색을 사용하여 수집한 데이터 보기 및 분석](../azure-monitor/logs/log-analytics-tutorial.md) 참조) 다음 쿼리는 시각적 개체 차트와 함께 사용자 표시 이름 목록, 이벤트 시간 및 이벤트 시간의 이벤트 개수를 포함하는 테이블을 반환합니다. 사용자 GUID 또는 기타 특성을 표시하도록 쉽게 수정할 수 있습니다.

```
search *
| where ( Type == "AzureDiagnostics" )
| summarize count(TimeGenerated) by identity_s, TimeGenerated
```


Azure Data Lake Storage Gen1에서는 로그 데이터를 처리하고 분석하는 방법에 대한 샘플을 제공합니다. [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample)에서 샘플을 찾을 수 있습니다. 

## <a name="see-also"></a>참고 항목
* [Azure Data Lake Storage Gen1 개요](data-lake-store-overview.md)
* [Data Lake Storage Gen1의 데이터 보호](data-lake-store-secure-data.md)
