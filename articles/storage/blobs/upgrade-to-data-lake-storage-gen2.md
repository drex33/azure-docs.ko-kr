---
title: Azure Data Lake Storage Gen2로 Azure Blob Storage 업그레이드 | Microsoft Docs
description: 설명은 여기에 있습니다.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/04/2021
ms.author: normesta
ms.openlocfilehash: 7f5c2397e2b8d6e19e912dd46eec39f432e27434
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129459195"
---
# <a name="upgrading-azure-blob-storage-with-azure-data-lake-storage-gen2-capabilities"></a>Azure Data Lake Storage Gen2 기능으로 Azure Blob Storage 업그레이드

이 문서는 파일 및 디렉터리 수준 보안과 같은 기능의 잠금을 해제 하는 데 도움이 됩니다. 이러한 기능은 빅 데이터 분석 워크 로드에서 널리 사용 되며 Azure Data Lake Storage Gen2로 통칭 됩니다. 가장 인기 있는 기능은 다음과 같습니다.

- 높은 처리량, IOPS (초당 입력/출력 작업 수) 및 저장소 용량 한도가 있습니다.

- 개별 노드 Uri에 대해 작업을 수행할 수 있기 때문에 작업 (예: 이름 바꾸기 작업)이 더 빠릅니다.
 
- 지정 된 작업을 수행 하는 데 필요한 데이터만 전송 하는 효율적인 쿼리 엔진입니다.

- 컨테이너, 디렉터리 및 파일 수준에서의 보안.

자세히 알아보려면 [Azure Data Lake Storage Gen2 소개](data-lake-storage-introduction.md)를 참조 하세요.

이 문서는 작업, 응용 프로그램, 비용, 서비스 통합, 도구, 기능 및 설명서에 미치는 영향을 평가 하는 데 도움이 됩니다. 이러한 영향을 신중 하 게 검토 해야 합니다. 계정을 업그레이드할 준비가 된 경우이 단계별 가이드: [Azure Data Lake Storage Gen2 기능으로 Azure Blob Storage 업그레이드](upgrade-to-data-lake-storage-gen2-how-to.md)를 참조 하세요.

> [!IMPORTANT]
> 업그레이드는 단방향입니다. 업그레이드를 수행한 후에는 계정을 되돌릴 방법이 없습니다. 비프로덕션 환경에서 업그레이드의 유효성을 검사 하는 것이 좋습니다.

## <a name="impact-on-availability"></a>가용성에 미치는 영향

업그레이드 프로세스가 완료 되는 동안 계정의 가동 중지 시간에 대해 계획 해야 합니다. 계정을 업그레이드 하는 동안에는 쓰기 작업을 사용할 수 없습니다. 읽기 작업은 사용 하지 않도록 설정 되지만 이러한 작업으로 인해 업그레이드 프로세스가 불안정 해질 수 있으므로 읽기 작업을 일시 중단 하는 것이 좋습니다.

## <a name="impact-on-workloads-and-applications"></a>워크 로드 및 응용 프로그램에 미치는 영향

Blob Api는 계층 네임 스페이스가 있는 계정으로 작동 하므로 이러한 Api를 사용 하 여 계정과 상호 작용 하는 대부분의 응용 프로그램은 수정 없이 계속 작동 합니다.

문제 및 해결 방법에 대 한 전체 목록은 [Blob Storage api의 알려진 문제](data-lake-storage-known-issues.md#blob-storage-apis)를 참조 하세요.

Windows Azure Storage Blob 드라이버 또는 [WASB](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) 드라이버를 사용 하는 모든 Hadoop 작업은 [Azure blob 파일 시스템 (abfs)](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html) 드라이버를 사용 하도록 수정 해야 합니다. **Blob service** 끝점에 요청을 수행 하는 WASB 드라이버와는 달리, abfs 드라이버는 계정의 **Data Lake Storage** 끝점에 요청을 만듭니다.

### <a name="data-lake-storage-endpoint"></a>Data Lake Storage 끝점

업그레이드 된 계정에는 Data Lake 저장소 끝점이 있습니다. 계정에 대 한 **속성** 페이지를 열어 Azure Portal에서이 끝점의 URL을 찾을 수 있습니다.

> [!div class="mx-imgBorder"]
> ![범용 v2 범주](./media/upgrade-to-data-lake-storage-gen2/data-lake-storage-endpoint.png)

해당 끝점을 사용 하도록 기존 응용 프로그램 및 워크 로드를 수정할 필요가 없습니다. [Data Lake Storage의 멀티 프로토콜 액세스](data-lake-storage-multi-protocol-access.md) 를 사용 하면 Blob service 끝점 또는 Data Lake 저장소 끝점을 사용 하 여 데이터와 상호 작용할 수 있습니다. 

Azure 서비스 및 도구 (예: AzCopy)는 저장소 계정의 데이터와 상호 작용 하기 위해 Data Lake 저장소 끝점을 사용할 수 있습니다. 또한 [Data Lake Storage Gen2 sdk](data-lake-storage-directory-file-acl-dotnet.md), [PowerShell 명령](data-lake-storage-directory-file-acl-powershell.md)또는 [Azure CLI 명령을](data-lake-storage-directory-file-acl-cli.md)사용 하 여 수행 하는 모든 작업에 대해이 새 끝점을 사용 해야 합니다. 

### <a name="directories"></a>디렉터리

계층적 네임 스페이스가 없는 Blob storage 계정은 계층적 패러다임 대신 플랫 패러다임으로 파일을 구성 합니다. Blob은 폴더 구조를 모방 하기 위해 가상 디렉터리로 구성 됩니다. 가상 디렉터리는 Blob 이름의 일부를 형성하며 구분 기호 문자로 표시됩니다. 가상 디렉터리는 blob 이름에 포함 되어 있으므로 실제로는 독립적 개체로 존재 하지 않습니다.

새 계정에 계층 네임 스페이스가 있습니다. 즉, 디렉터리가 가상이 아닙니다. 직접 작업할 수 있는 구체적이 고 독립적인 개체입니다. 디렉터리는 파일을 포함 하지 않고 있을 수 있습니다. 디렉터리를 삭제 하면 해당 디렉터리에 있는 모든 파일이 제거 됩니다. 디렉터리가 사라지기 전에 각 개별 blob을 더 이상 삭제할 필요가 없습니다. 

### <a name="blob-metadata"></a>Blob 메타데이터

마이그레이션하기 전에 blob 메타 데이터는 전체 가상 경로와 함께 blob 이름과 연결 됩니다. 마이그레이션 후 메타 데이터는 blob에만 연결 됩니다. Blob에 대 한 가상 경로는 디렉터리의 컬렉션이 됩니다. Blob의 메타 데이터는 이러한 디렉터리에 적용 되지 않습니다. 

### <a name="put-operations"></a>Put 작업

Blob을 업로드 하는 경우 지정한 경로에 존재 하지 않는 디렉터리가 포함 되어 있으면 작업에서 해당 디렉터리를 만든 다음 blob을 추가 합니다. 이 동작은 계층 구조 폴더 구조의 컨텍스트에서 논리적입니다. 계층 네임 스페이스가 없는 Blob storage 계정에서 작업은 디렉터리를 만들지 않습니다. 대신, 디렉터리 이름이 blob의 네임 스페이스에 추가 됩니다. 

### <a name="list-operations"></a>작업 나열

[Blob 나열](/rest/api/storageservices/list-blobs) 작업은 디렉터리와 파일을 모두 반환 합니다. 각각은 별도로 나열 됩니다. 디렉터리는 목록에서 길이가 0 인 blob으로 표시 됩니다. 계층 네임 스페이스가 없는 Blob storage 계정에서 blob [나열](/rest/api/storageservices/list-blobs) 작업은 디렉터리가 아닌 blob만 반환 합니다. Data Lake Storage Gen2 [경로 목록](/rest/api/storageservices/datalakestoragegen2/path/list) 작업을 사용 하는 경우 디렉터리는 길이가 0 인 blob이 아닌 디렉터리 항목으로 표시 됩니다.

목록 순서도 다릅니다. 디렉터리와 파일 *은 깊이 우선 검색* 순서로 표시 됩니다. 계층 네임 스페이스가 없는 Blob storage 계정에는 blob이 *사전순* 으로 나열 됩니다. 

### <a name="operations-to-rename-blobs"></a>Blob 이름 바꾸기 작업

클라이언트 응용 프로그램은 단일 작업으로 blob의 이름을 바꿀 수 있으므로 blob의 이름을 변경 하는 것이 훨씬 효율적입니다. 계층 네임 스페이스가 *없는 계정* 에서 도구 및 응용 프로그램은 blob을 복사한 다음 원본 blob을 삭제 해야 합니다.  

> [!NOTE]
> Blob의 이름을 바꾸면 blob의 마지막 수정 시간이 업데이트 되지 않습니다. Blob의 내용이 변경 되지 않기 때문입니다. 

## <a name="impact-on-costs"></a>비용에 미치는 영향

Storage 비용은 영향을 받지 않지만 트랜잭션 비용은 영향을 받습니다. 이러한 페이지를 사용 하 여 비교 비용을 평가 합니다. 

- [블록 blob 가격 책정](https://azure.microsoft.com/pricing/details/storage/blobs/).

- [가격 책정을 Azure Data Lake Storage Gen2](https://azure.microsoft.com/pricing/details/storage/data-lake/)합니다.

또한 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/) 의 **Storage 계정** 옵션을 사용 하 여 업그레이드 후의 비용에 미치는 영향을 예측할 수 있습니다. 

가격 책정을 변경 하는 것 외에 Data Lake Storage Gen2 기능과 관련 된 비용 절감 액을 고려해 야 합니다. 총 소유 비용 비용은 일반적으로 처리량이 높고 최적화 된 작업 때문에 거부 됩니다. 처리량이 높을수록 더 많은 데이터를 더 자주 전송할 수 있습니다. 계층적 네임 스페이스는 작업의 효율성을 향상 시킵니다.  

## <a name="impact-on-service-integrations"></a>서비스 통합에 미치는 영향

대부분의 Azure 서비스 통합은 이러한 기능을 사용 하도록 설정한 후에도 계속 작동 하지만 일부는 미리 보기 상태로 유지 되거나 아직 지원 되지 않습니다. Data Lake Storage Gen2와의 azure 서비스 통합에 대 한 현재 지원을 이해 하려면 [Azure Data Lake Storage Gen2를 지 원하는 azure 서비스](data-lake-storage-supported-azure-services.md) 를 참조 하세요.

## <a name="impact-on-tools-features-and-documentation"></a>도구, 기능 및 설명서에 미치는 영향

업그레이드 후 일부 기능과 상호 작용 하는 방식이 변경 됩니다. 이 섹션에서는 이러한 변경 내용에 대해 설명 합니다.

### <a name="blob-storage-feature-support"></a>Blob Storage 기능 지원

이러한 기능을 사용 하도록 설정한 후에는 대부분의 Blob 저장소 기능이 계속 작동 하지만, 이러한 기능 중 일부는 미리 보기 상태를 유지 하거나 아직 지원 되지 않습니다. 

[Azure Data Lake Storage Gen2에서 제공 되는 Blob Storage 기능](data-lake-storage-supported-blob-storage-features.md) 을 참조 하 여 Data Lake Storage Gen2에서 Blob 저장소 기능에 대 한 현재 지원을 이해 합니다. 

### <a name="diagnostic-logs"></a>진단 로그

[Storage 분석 로깅을](../common/storage-analytics-logging.md)사용 하도록 설정 하면 이제 버전 2.0 로그 형식을 사용할 수 있습니다.  

새 버전을 사용할 필요가 없습니다. 그러나 Data Lake 저장소 끝점에 적용 되는 모든 작업은 버전 2.0 로그에만 기록 됩니다. 사용자가 사용 하는 일부 서비스 및 도구 (예: AzCopy)는 해당 끝점을 사용 하 여 계정에 대 한 작업을 수행 합니다. 모든 작업에서 로깅 정보를 캡처하려면 버전 2.0 로그 형식을 사용 하는 것이 좋습니다. 

### <a name="azure-lifecycle-management"></a>Azure 수명 주기 관리

디렉터리의 모든 blob을 이동 하거나 삭제 하는 정책은 다음 날까지 해당 blob이 포함 된 디렉터리를 삭제 하지 않습니다. 디렉터리를 삭제 하려면 먼저 해당 디렉터리에 있는 모든 blob을 먼저 제거 해야 합니다. 다음 날에는 디렉터리가 제거 됩니다. 

### <a name="event-grid"></a>Event Grid

새 계정에는 Data Lake 저장소 끝점과 Blob service 끝점의 두 끝점이 있습니다. 서비스, 도구 및 응용 프로그램은 두 끝점 중 하나를 사용 하 여 데이터에 대해 작업을 수행할 수 있습니다. 따라서 Event Grid에서 반환 되는 이벤트 응답은 해당 blob을 설명 하는 **url** 필드에 이러한 두 끝점 중 하나를 표시할 수 있습니다. 

다음 JSON은 Blob service 끝점을 사용 하 여 blob를 만들 때 이벤트 응답에 표시 되는 blob의 url을 보여 줍니다.

:::code language="json" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ConfigurationFiles/change-feed-logs.json" ID="Snippet_BlobURL" highlight="15":::

다음 JSON은 Data Lake 저장소 끝점을 사용 하 여 blob를 만들 때 이벤트 응답에 표시 되는 blob의 url을 보여 줍니다.

:::code language="json" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ConfigurationFiles/change-feed-logs.json" ID="Snippet_DataLakeURL" highlight="16":::

응용 프로그램에서 Event Grid 사용 하는 경우 해당 응용 프로그램을 수정 하 여이를 고려해 야 할 수 있습니다. 

### <a name="storage-explorer"></a>Storage Explorer

다음 단추는 아직 Azure Storage Explorer의 리본에 표시 되지 않습니다.

|단추|이유|
|--|--|
|URL 복사|아직 구현 되지 않음|
|스냅샷 관리|아직 구현 되지 않음|
|삭제 취소|Data Lake Storage Gen2에서 아직 지원 되지 않는 Blob storage 기능에 종속 |


다음 단추는 새 계정에서 다르게 동작 합니다.

|단추|Blob storage 동작|Data Lake Storage Gen2 동작|
|---|---|---|
|폴더|폴더는 가상 폴더에 파일을 추가 하지 않은 경우에만 사라집니다. |파일이 추가 되지 않은 경우에도 폴더가 존재 합니다.| 
|이름 바꾸기|복사한 후 원본 blob 삭제|동일한 blob의 이름을 바꿉니다. 훨씬 더 효율적입니다.|

### <a name="documentation"></a>문서

Data Lake Storage Gen2 기능 사용에 대 한 지침은 [Azure Data Lake Storage Gen2 소개를](data-lake-storage-introduction.md)참조 하세요. 

모든 기존 Blob 저장소 기능에 대 한 지침을 찾을 수 있는 위치와 관련 하 여 아무 것도 변경 되지 않았습니다. 해당 지침은 [Azure Blob Storage 소개](storage-blobs-introduction.md)를 참조 하세요. 

콘텐츠 집합 간에 이동할 때 약간의 용어 차이점이 있습니다. 예를 들어 Data Lake Storage Gen2 콘텐츠에 있는 콘텐츠는 *blob* 및 *컨테이너* 대신 용어 *파일* 및 *파일 시스템* 을 사용할 수 있습니다. *파일* 및 *파일 시스템* 이라는 용어는 Data Lake 저장소에 긴 기록이 있는 빅 데이터 분석 분야에서 가장 많이 사용 됩니다. 콘텐츠에는 이러한 용어를 포함 하 여 이러한 대상 그룹을 쉽게 파악할 수 있습니다. 이러한 용어는 별도의 *작업* 을 설명 하지 않습니다.

## <a name="next-steps"></a>다음 단계

Data Lake Storage Gen2 기능을 포함 하도록 저장소 계정을 업그레이드할 준비가 된 경우이 단계별 가이드를 참조 하세요. 
> [!div class="nextstepaction"]
> [Azure Data Lake Storage Gen2 기능으로 Azure Blob Storage 업그레이드](upgrade-to-data-lake-storage-gen2-how-to.md)
