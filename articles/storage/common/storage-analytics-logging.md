---
title: Azure 스토리지 분석 로깅
description: 스토리지 분석을 사용하여 Azure Storage 요청에 대한 세부 정보를 로그합니다. 로그되는 요청, 로그를 저장하는 방법, 스토리지 로깅을 사용하도록 설정하는 방법 등을 참조하세요.
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: c536e8749ce41f51f161d9659beca3ab0ccd30ae
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123032256"
---
# <a name="azure-storage-analytics-logging"></a>Azure 스토리지 분석 로깅

스토리지 분석은 Storage 서비스에 대해 성공한 요청과 실패한 요청 관련 상세 정보를 기록합니다. 이 정로를 사용하면 개별 요청을 모니터링하고 스토리지 서비스의 문제를 진단할 수 있습니다. 요청은 최상의 노력을 기준으로 기록됩니다. 즉, 대부분의 요청은 로그 레코드를 생성하지만 스토리지 분석 로그의 완성도와 적시성은 보장되지 않습니다. 

> [!NOTE]
> Azure Monitor에서 스토리지 분석 로그 대신 Azure 스토리지 로그를 사용하는 것이 좋습니다. Azure Monitor의 Azure Storage 로그는 현재 공개 미리 보기이며 모든 퍼블릭 클라우드 지역에서 미리 보기 테스트에 사용할 수 있습니다. 이 미리 보기는 Blob(Azure Data Lake Storage Gen2 포함), 파일, 큐, 테이블에 대한 로그를 지원합니다. 다음 문서에서 자세한 내용을 참조하세요.
>
> - [Azure Blob Storage 모니터링](../blobs/monitor-blob-storage.md)
> - [Azure Files 모니터링](../files/storage-files-monitoring.md)
> - [Azure Queue Storage 모니터링](../queues/monitor-queue-storage.md)
> - [Azure Table Storage 모니터링](../tables/monitor-table-storage.md)

 스토리지 분석 로깅은 Storage 계정에 대해 기본적으로 사용하지 않도록 설정됩니다. [Azure Portal](https://portal.azure.com/)에서, 또는 PowerShell이나 Azure CLI를 통해 사용 설정할 수 있습니다. 단계별 안내는 [Azure 스토리지 분석 로그(클래식) 설정 및 관리](manage-storage-analytics-logs.md)를 참조하세요. 

REST API 또는 클라이언트 라이브러리를 통해 프로그래밍 방식으로 스토리지 분석 로그를 사용하도록 설정할 수도 있습니다. 또한 [Blob Service 속성 가져오기](/rest/api/storageservices/Blob-Service-REST-API), [큐 서비스 속성 가져오기](/rest/api/storageservices/Get-Queue-Service-Properties) 및 [테이블 서비스 속성 가져오기](/rest/api/storageservices/Get-Table-Service-Properties) 작업을 사용하여 각 서비스에 대해 스토리지 분석을 사용하도록 설정할 수 있습니다. .NET을 사용하여 스토리지 분석 로그를 사용 설정하는 예제를 보려면 [로그 사용](manage-storage-analytics-logs.md)을 참조하세요.

 서비스 엔드포인트에 대한 요청이 있는 경우에만 로그 항목이 만들어집니다. 예를 들어 스토리지 계정의 활동이 Blob 엔드포인트에는 있지만 테이블 또는 큐 엔드포인트에는 없는 경우 Blob service와 관련된 로그만 만들어집니다.

> [!NOTE]
>  스토리지 분석 로깅은 현재 Blob, 큐 및 Table Service에서만 사용할 수 있습니다. 스토리지 분석 로깅은 프리미엄 성능 [BlockBlobStorage](./storage-account-create.md) 계정에도 사용할 수 있습니다. 그러나 프리미엄 성능을 사용하는 범용 v2 계정에는 사용할 수 없습니다.

## <a name="requests-logged-in-logging"></a>로깅에 로그인된 요청
### <a name="logging-authenticated-requests"></a>인증된 요청 로깅

 다음과 같은 유형의 인증된 요청이 기록됩니다.

- 성공한 요청
- 실패한 요청(시간 제한, 제한, 네트워크, 권한 부여 및 기타 오류)
- 실패한 요청 및 성공한 요청을 포함하는 SAS(공유 액세스 서명) 또는 OAuth를 사용하는 요청
- 분석 데이터에 대한 요청

  로그 만들기/삭제 등 스토리지 분석 자체에서 수행한 요청은 기록되지 않습니다. 기록되는 데이터의 전체 목록은 [스토리지 분석에서 기록한 작업 및 상태 메시지](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) 및 [스토리지 분석 로그 형식](/rest/api/storageservices/storage-analytics-log-format) 항목에 나와 있습니다.

### <a name="logging-anonymous-requests"></a>익명 요청 로깅

 다음과 같은 유형의 익명 요청이 기록됩니다.

- 성공한 요청
- 서버 오류
- 클라이언트와 서버에 대한 시간 초과 오류
- 오류 코드가 304(수정되지 않음)인 실패한 GET 요청

  기타 모든 실패한 익명 요청은 기록되지 않습니다. 기록되는 데이터의 전체 목록은 [스토리지 분석에서 기록한 작업 및 상태 메시지](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) 및 [스토리지 분석 로그 형식](/rest/api/storageservices/storage-analytics-log-format) 항목에 나와 있습니다.
  
> [!NOTE]
> Storage Analytics는 데이터 평면에 대한 모든 내부 호출을 로그합니다. Azure Storage 리소스 공급자의 호출도 로그됩니다. 이러한 요청을 식별하려면 요청 URL에서 쿼리 문자열 `<sk=system-1>`을 찾습니다.

## <a name="how-logs-are-stored"></a>로그 저장 방법

모든 로그는 스토리지 계정에 대해 스토리지 분석을 사용하도록 설정할 때 자동으로 생성되는 `$logs`라는 컨테이너의 블록 Blob에 저장됩니다. `$logs` 컨테이너는 스토리지 계정의 Blob 네임스페이스에 있습니다. 예: `http://<accountname>.blob.core.windows.net/$logs` Storage 계정을 사용하도록 설정한 후에는 이 컨테이너를 삭제할 수 없지만 해당 콘텐츠는 삭제할 수 있습니다. 스토리지 검색 도구를 사용하여 컨테이너로 직접 이동하는 경우 로깅 데이터를 포함하는 모든 Blob이 표시됩니다.

> [!NOTE]
>  `$logs` 컨테이너는 컨테이너 나열 작업과 같은 컨테이너 나열 작업을 수행할 때는 표시되지 않습니다. 직접 액세스해야 합니다. 예를 들어 Blob 나열 작업을 사용하여 `$logs` 컨테이너의 Blob에 액세스할 수 있습니다.

요청을 기록할 때 스토리지 분석은 중간 결과를 블록으로 업로드합니다. 그리고 이러한 블록을 정기적으로 커밋하여 Blob로 제공합니다. 스토리지 서비스가 로그 기록기를 플러시하는 빈도 때문에 로그 데이터가 **$logs** 컨테이너의 Blob에 나타나기까지 최대 1시간 정도 걸릴 수 있습니다. 같은 시간에 생성되는 로그의 경우 중복 레코드가 있을 수 있습니다. **RequestId** 및 **Operation** 번호를 검사하여 레코드 중복 여부를 확인할 수 있습니다.

매시간 여러 파일이 포함된 다량의 로그 데이터가 발생하는 경우 Blob 메타데이터를 사용하여 Blob 메타데이터 필드를 검토함으로써 로그에 포함된 데이터를 확인할 수 있습니다. 또한 이는 데이터가 로그 파일에 기록되는 동안 지연이 발생할 수 있으므로 유용합니다. Blob 메타데이터는 Blob 이름보다 Blob 콘텐츠를 보다 정확하게 표시합니다.

대부분의 스토리지 검색 도구를 사용하여 Blob의 메타데이터를 볼 수 있습니다. 또한 PowerShell을 사용하거나 프로그래밍 방식으로 이 정보를 읽을 수 있습니다. 다음 PowerShell 코드 조각은 로그 Blob 목록을 이름으로 필터링하여 시간을 지정하고, 메타데이터로 필터링하여 **write** 작업이 포함된 해당 로그만 식별하는 예제입니다.  

 ```powershell
 Get-AzStorageBlob -Container '$logs' |  
 Where-Object {  
     $_.Name -match 'blob/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 ForEach-Object {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

프로그래밍 방식으로 Blob을 나열하는 방법에 대한 자세한 내용은 [Blob 리소스 열거](/rest/api/storageservices/Enumerating-Blob-Resources) 및 [Blob 리소스의 속성과 메타데이터 설정 및 검색](/rest/api/storageservices/Setting-and-Retrieving-Properties-and-Metadata-for-Blob-Resources)을 참조하세요.  

### <a name="log-naming-conventions"></a>로그 명명 규칙

 각 로그는 다음 형식으로 작성됩니다.

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 다음 표에서는 로그 이름의 각 특성에 대해 설명합니다.

|attribute|Description|
|---------------|-----------------|
|`<service-name>`|스토리지 서비스의 이름입니다. 예: `blob`, `table` 또는 `queue`|
|`YYYY`|로그의 4자리 연도입니다. 예: `2011`|
|`MM`|로그의 2자리 월입니다. 예: `07`|
|`DD`|로그의 2자리 일입니다. 예: `31`|
|`hh`|로그의 시작 시간을 나타내는 24시간 UTC 형식의 2자리 시간입니다. 예: `18`|
|`mm`|로그의 시작 분을 나타내는 2자리 숫자입니다. **참고:**  현재 스토리지 분석 버전에서는 이 값이 지원되지 않으며 해당 값은 항상 `00`입니다.|
|`<counter>`|1시간 동안 스토리지 서비스에 대해 생성되는 로그 Blob의 수를 나타내는 0부터 시작되는 6자리 카운터입니다. 이 카운터는 `000000`에서 시작됩니다. 예: `000001`|

 다음은 위 예를 결합한 완전한 예제 로그 이름입니다.

 `blob/2011/07/31/1800/000001.log`

 다음은 위 로그에 액세스하는 데 사용할 수 있는 예제 URI입니다.

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 스토리지 요청이 기록되면 생성되는 로그 이름과 요청한 작업이 완료된 시간 간의 상관 관계가 지정됩니다. 예를 들어 GetBlob 요청이 2011년 7월 31일 오후 6:30에 완료되었으면 다음 접두사를 사용해서 로그가 기록됩니다. `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>로그 메타데이터

 모든 로그 Blob는 해당 Blob에 포함된 로깅 데이터를 식별하는 데 사용할 수 있는 메타데이터와 함께 저장됩니다. 아래 표에서 각 메타데이터 특성에 대해 설명합니다.

|attribute|Description|
|---------------|-----------------|
|`LogType`|로그에 읽기, 쓰기 또는 삭제 작업과 관련된 정보가 포함되는지 여부를 설명합니다. 이 값에는 한 가지 형식이 포함될 수도 있고 3개 형식이 모두 조합(쉼표로 구분)되어 포함될 수도 있습니다.<br /><br /> 예 1: `write`<br /><br /> 예 2: `read,write`<br /><br /> 예 3: `read,write,delete`|
|`StartTime`|`YYYY-MM-DDThh:mm:ssZ` 형식을 사용해서 표시되는 로그 항목의 초기 시간입니다. 예: `2011-07-31T18:21:46Z`|
|`EndTime`|`YYYY-MM-DDThh:mm:ssZ` 형식을 사용해서 표시되는 로그 항목의 마지막 시간입니다. 예: `2011-07-31T18:22:09Z`|
|`LogVersion`|로그 형식의 버전입니다.|

 다음 목록에서는 위 예제를 사용한 전체 예제 메타데이터를 보여줍니다.

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`


## <a name="next-steps"></a>다음 단계

* [Azure 스토리지 분석 로그(클래식) 사용 및 관리](manage-storage-analytics-logs.md)
* [스토리지 분석 로그 형식](/rest/api/storageservices/storage-analytics-log-format)
* [스토리지 분석에서 기록한 작업 및 상태 메시지](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [스토리지 분석 메트릭(클래식)](storage-analytics-metrics.md)