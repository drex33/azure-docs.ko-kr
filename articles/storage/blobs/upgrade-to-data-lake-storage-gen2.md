---
title: Azure Blob Storage Azure Data Lake Storage Gen2 | 업그레이드 Microsoft Docs
description: 설명은 여기로 이동합니다.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/12/2021
ms.author: normesta
ms.openlocfilehash: 5dfdddb3458d552c0c3336c9d7609cfe2fdc6de3
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130003499"
---
# <a name="upgrading-azure-blob-storage-with-azure-data-lake-storage-gen2-capabilities"></a>Azure Data Lake Storage Gen2 기능으로 Azure Blob Storage 업그레이드

이 문서는 파일 및 디렉터리 수준 보안 및 더 빠른 작업과 같은 기능의 잠금을 해제하는 데 도움이 됩니다. 이러한 기능은 빅 데이터 분석 워크로드에서 널리 사용되며 전체적으로 Azure Data Lake Storage Gen2라고 합니다. 가장 인기 있는 기능은 다음과 같습니다.

- 더 높은 처리량, IOPS(초당 입출력 작업 수) 및 스토리지 용량 제한

- 개별 노드 URI에서 작동할 수 있으므로 더 빠른 작업(예: 이름 바꾸기 작업).
 
- 지정된 작업을 수행하는 데 필요한 데이터만 전송하는 효율적인 쿼리 엔진입니다.

- 컨테이너, 디렉터리 및 파일 수준의 보안.

자세한 내용은 [Azure Data Lake Storage Gen2 소개를 참조하세요.](data-lake-storage-introduction.md)

이 문서는 워크로드, 애플리케이션, 비용, 서비스 통합, 도구, 기능 및 설명서에 미치는 영향을 평가하는 데 도움이 됩니다. 이러한 영향을 신중하게 검토해야 합니다. 계정을 업그레이드할 준비가 되면 Azure Data Lake Storage [Gen2 기능을 사용하여 Azure Blob Storage 업그레이드](upgrade-to-data-lake-storage-gen2-how-to.md)단계별 가이드를 참조하세요.

> [!IMPORTANT]
> 업그레이드는 단방향입니다. 업그레이드를 수행한 후에는 계정을 되돌릴 방법이 없습니다. 비프로덕션 환경에서 업그레이드의 유효성을 검사하는 것이 좋습니다.

## <a name="impact-on-availability"></a>가용성에 미치는 영향

업그레이드 프로세스가 완료된 동안 계정에서 가동 중지 시간을 계획해야 합니다. 계정을 업그레이드하는 동안 쓰기 작업이 비활성화됩니다. 읽기 작업은 사용하지 않도록 설정되지 않지만 해당 작업으로 업그레이드 프로세스가 불안정해질 수 있으므로 읽기 작업을 일시 중단하는 것이 좋습니다.

## <a name="impact-on-workloads-and-applications"></a>워크로드 및 애플리케이션에 미치는 영향

Blob API는 계층 구조 네임스페이스가 있는 계정에서 작동하므로 이러한 API를 사용하여 계정과 상호 작용하는 대부분의 애플리케이션은 수정 없이 계속 작동합니다.

문제 및 해결 방법의 전체 목록은 [Blob Storage API의 알려진 문제를 참조하세요.](data-lake-storage-known-issues.md#blob-storage-apis)

Windows Azure Storage Blob 드라이버 또는 [WASB](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) 드라이버를 사용하는 Hadoop 워크로드는 [ABFS(Azure Blob File System)](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html) 드라이버를 사용하도록 수정해야 합니다. **Blob service** 엔드포인트를 요청하는 WASB 드라이버와 달리 ABFS 드라이버는 계정의 Data Lake **Storage** 엔드포인트를 요청합니다.

### <a name="data-lake-storage-endpoint"></a>Data Lake Storage 엔드포인트

업그레이드된 계정에는 Data Lake Storage 엔드포인트가 있습니다. 계정의 **속성** 페이지를 열어 Azure Portal 이 엔드포인트의 URL을 찾을 수 있습니다.

> [!div class="mx-imgBorder"]
> ![범용 v2 범주](./media/upgrade-to-data-lake-storage-gen2/data-lake-storage-endpoint.png)

해당 엔드포인트를 사용하기 위해 기존 애플리케이션 및 워크로드를 수정할 필요가 없습니다. [Data Lake Storage 다중 프로토콜 액세스를](data-lake-storage-multi-protocol-access.md) 사용하면 Blob service 엔드포인트 또는 Data Lake Storage 엔드포인트를 사용하여 데이터와 상호 작용할 수 있습니다. 

Azure 서비스 및 도구(예: AzCopy)는 Data Lake Storage 엔드포인트를 사용하여 스토리지 계정의 데이터와 상호 작용할 수 있습니다. 또한 Data Lake Storage [Gen2 SDK,](data-lake-storage-directory-file-acl-dotnet.md) [PowerShell 명령](data-lake-storage-directory-file-acl-powershell.md)또는 Azure CLI 명령을 사용하여 수행하는 모든 작업에 이 새 엔드포인트를 사용해야 [합니다.](data-lake-storage-directory-file-acl-cli.md) 

### <a name="directories"></a>디렉터리

계층 구조 네임스페이스가 없는 Blob Storage 계정은 계층적 패러다임이 아닌 플랫 패러다임으로 파일을 구성합니다. Blob은 폴더 구조를 모방하기 위해 가상 디렉터리로 구성됩니다. 가상 디렉터리는 Blob 이름의 일부를 형성하며 구분 기호 문자로 표시됩니다. 가상 디렉터리 Blob 이름의 일부이기 때문에 실제로는 독립적인 개체로 존재하지 않습니다.

새 계정에 계층 구조 네임스페이스가 있습니다. 즉, 디렉터리 가상이 아닙니다. 직접 작동할 수 있는 구체적이고 독립적인 개체입니다. 파일이 포함되지 않은 디렉터리도 존재할 수 있습니다. 디렉터리를 삭제하면 해당 디렉터리에 있는 모든 파일이 제거됩니다. 디렉터리를 사라지기 전에 더 이상 각 개별 Blob을 삭제할 필요가 없습니다. 

### <a name="blob-metadata"></a>Blob 메타데이터

마이그레이션하기 전에 Blob 메타데이터는 전체 가상 경로와 함께 Blob 이름과 연결됩니다. 마이그레이션 후 메타데이터는 Blob에만 연결됩니다. Blob에 대한 가상 경로는 디렉터리 컬렉션이 됩니다. Blob의 메타데이터는 해당 디렉터리에 적용되지 않습니다. 

### <a name="put-operations"></a>작업 배치

Blob을 업로드하고 지정한 경로에 존재하지 않는 디렉터리를 포함하는 경우 작업은 해당 디렉터리를 만든 다음 Blob을 추가합니다. 이 동작은 계층적 폴더 구조의 컨텍스트에서 논리적입니다. 계층 구조 네임스페이스가 없는 Blob Storage 계정에서 작업은 디렉터리를 만들지 않습니다. 대신 디렉터리 이름이 Blob의 네임스페이스에 추가됩니다. 

### <a name="list-operations"></a>작업 나열

[Blob 나열](/rest/api/storageservices/list-blobs) 작업은 디렉터리와 파일을 모두 반환합니다. 각각은 별도로 나열됩니다. 디렉터리를 목록에 길이가 0인 Blob으로 표시합니다. 계층 구조 네임스페이스가 없는 Blob Storage 계정에서 [Blob 나열](/rest/api/storageservices/list-blobs) 작업은 디렉터리를 반환하지 않고 Blob만 반환합니다. Data Lake Storage Gen2 [경로 - 나열](/rest/api/storageservices/datalakestoragegen2/path/list) 작업을 사용하는 경우 디렉터리 는 길이가 0인 Blob이 아닌 디렉터리 항목으로 표시됩니다.

목록 순서도 다릅니다. 디렉터리 및 파일은 *깊이 우선 검색* 순서로 표시됩니다. 계층 구조 네임스페이스가 없는 Blob Storage 계정은 Blob을 *어휘* 순서대로 나열합니다. 

### <a name="operations-to-rename-blobs"></a>Blob 이름 바꾸기 작업

Blob 이름 바꾸기는 클라이언트 애플리케이션이 단일 작업에서 Blob의 이름을 바꿀 수 있기 때문에 훨씬 더 효율적입니다. 계층 구조 네임스페이스가 *없는* 계정에서 도구와 애플리케이션은 Blob을 복사한 다음 원본 Blob을 삭제해야 합니다.  

> [!NOTE]
> Blob의 이름을 바꾸면 Blob의 마지막 수정 시간이 업데이트되지 않습니다. Blob의 내용이 변경되지 않기 때문입니다. 

## <a name="impact-on-costs"></a>비용에 미치는 영향

Storage 비용은 영향을 받지 않지만 트랜잭션 비용은 영향을 미칩니다. 이러한 페이지를 사용하여 비용 비교를 평가합니다. 

- [블록 Blob 가격 책정.](https://azure.microsoft.com/pricing/details/storage/blobs/)

- [Azure Data Lake Storage Gen2 가격 책정.](https://azure.microsoft.com/pricing/details/storage/data-lake/)

[Azure 가격 계산기에서](https://azure.microsoft.com/pricing/calculator/) **Storage 계정** 옵션을 사용하여 업그레이드 후 비용의 영향을 예상할 수도 있습니다. 

가격 책정 변경 외에도 Data Lake Storage Gen2 기능과 관련된 비용 절감을 고려합니다. 일반적으로 높은 처리량 및 최적화된 작업으로 인해 총 소유 비용이 감소합니다. 처리량이 높을수록 더 적은 시간 안에 더 많은 데이터를 전송할 수 있습니다. 계층 구조 네임스페이스는 작업의 효율성을 향상시킵니다.  

## <a name="impact-on-service-integrations"></a>서비스 통합에 미치는 영향

이러한 기능을 사용하도록 설정한 후에도 대부분의 Azure 서비스 통합이 계속 작동하지만, 그 중 일부는 미리 보기로 유지되거나 아직 지원되지 않습니다. Data [Lake Storage Gen2와의](data-lake-storage-supported-azure-services.md) Azure 서비스 통합에 대한 현재 지원을 이해하려면 Azure Data Lake Storage Gen2를 지원하는 Azure 서비스를 참조하세요.

## <a name="impact-on-tools-features-and-documentation"></a>도구, 기능 및 설명서에 미치는 영향

업그레이드 후에는 일부 기능과 상호 작용하는 방식이 변경됩니다. 이 섹션에서는 이러한 변경 내용에 대해 설명합니다.

### <a name="blob-storage-feature-support"></a>Blob Storage 기능 지원

이러한 기능을 사용하도록 설정한 후에도 대부분의 Blob Storage 기능이 계속 작동하지만, 그 중 일부는 미리 보기로 유지되거나 아직 지원되지 않습니다. 

Data Lake Storage Gen2를 사용하여 Blob Storage 기능에 대한 현재 지원을 이해하려면 [Azure Data Lake Storage Gen2에서 사용할 수](data-lake-storage-supported-blob-storage-features.md) 있는 Blob Storage 기능을 참조하세요. 

### <a name="diagnostic-logs"></a>진단 로그

[Storage 분석 로깅을](../common/storage-analytics-logging.md)사용하도록 설정하면 버전 2.0 로그 형식을 사용할 수 있습니다.  

이 새 버전을 사용할 필요가 없습니다. 그러나 Data Lake Storage 엔드포인트에 적용되는 모든 작업은 버전 2.0 로그에만 기록됩니다. 사용하는 일부 서비스 및 도구(예: AzCopy)는 해당 엔드포인트를 사용하여 계정에 대한 작업을 수행합니다. 모든 활동에서 로깅 정보를 캡처하려면 버전 2.0 로그 형식을 사용하는 것이 좋습니다. 

### <a name="azure-lifecycle-management"></a>Azure 수명 주기 관리

디렉터리의 모든 Blob을 이동하거나 삭제하는 정책은 다음 날까지 해당 Blob이 포함된 디렉터리를 삭제하지 않습니다. 디렉터리에 있는 모든 Blob이 먼저 제거될 때까지 디렉터리를 삭제할 수 없기 때문입니다. 다음 날 디렉터리를 제거합니다. 

### <a name="event-grid"></a>Event Grid

새 계정에는 Data Lake Storage 엔드포인트와 Blob service 엔드포인트라는 두 개의 엔드포인트가 있습니다. 서비스, 도구 및 애플리케이션은 두 엔드포인트 중 하나를 사용하여 데이터에 대해 작동할 수 있습니다. 따라서 Event Grid 반환되는 이벤트 응답은 영향을 받는 Blob을 설명하는 **URL** 필드에 이러한 두 엔드포인트 중 하나를 표시할 수 있습니다. 

다음 JSON은 Blob service 엔드포인트를 사용하여 Blob을 만들 때 이벤트 응답에 표시되는 Blob의 URL을 보여줍니다.

:::code language="json" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ConfigurationFiles/change-feed-logs.json" ID="Snippet_BlobURL" highlight="15":::

다음 JSON은 Data Lake Storage 엔드포인트를 사용하여 Blob을 만들 때 이벤트 응답에 표시되는 Blob의 URL을 보여줍니다.

:::code language="json" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ConfigurationFiles/change-feed-logs.json" ID="Snippet_DataLakeURL" highlight="16":::

애플리케이션에서 Event Grid 사용하는 경우 이를 고려하도록 해당 애플리케이션을 수정해야 할 수 있습니다. 

### <a name="storage-explorer"></a>Storage Explorer

다음 단추는 Azure Storage Explorer 리본에 아직 표시되지 않습니다.

|단추|이유|
|--|--|
|URL 복사|아직 구현되지 않음|
|스냅샷 관리|아직 구현되지 않음|
|삭제 취소|Data Lake Storage Gen2에서 아직 지원되지 않는 Blob Storage 기능에 따라 다름 |


다음 단추는 새 계정에서 다르게 동작합니다.

|단추|Blob Storage 동작|Data Lake Storage Gen2 동작|
|---|---|---|
|폴더|폴더는 가상이며 파일을 추가하지 않으면 사라집니다. |폴더는 파일이 추가되지 않은 경우에도 존재합니다.| 
|이름 바꾸기|원본 Blob을 복사한 다음 삭제합니다.|동일한 Blob의 이름을 바꿉니다. 훨씬 더 효율적입니다.|

### <a name="documentation"></a>설명서

Data Lake Storage Gen2 기능 사용에 대한 지침은 [Azure Data Lake Storage Gen2 소개에서](data-lake-storage-introduction.md)찾을 수 있습니다. 

모든 기존 Blob Storage 기능에 대한 지침을 찾을 수 있는 위치와 관련하여 아무것도 변경되지 않았습니다. 이 지침은 [Azure Blob Storage 소개입니다.](storage-blobs-introduction.md) 

콘텐츠 집합 간에 이동할 때 약간의 용어 차이가 있습니다. 예를 들어 Data Lake Storage Gen2 콘텐츠에 포함된 콘텐츠는 *Blob* 및 *컨테이너* 대신 *파일* 및 *파일 시스템이라는* 용어를 사용할 수 있습니다. *파일* 및 *파일 시스템이라는* 용어는 Data Lake Storage가 오랜 기록을 가진 빅 데이터 분석의 세계에 깊이 있습니다. 콘텐츠에는 이러한 대상과 관련성을 유지하기 위한 이러한 용어가 포함되어 있습니다. 이러한 용어는 별도의 *를* 설명하지 않습니다.

## <a name="next-steps"></a>다음 단계

Data Lake Storage Gen2 기능을 포함하도록 스토리지 계정을 업그레이드할 준비가 되면 이 단계별 가이드를 참조하세요. 
> [!div class="nextstepaction"]
> [Azure Data Lake Storage Gen2 기능을 통해 Azure Blob Storage 업그레이드](upgrade-to-data-lake-storage-gen2-how-to.md)
