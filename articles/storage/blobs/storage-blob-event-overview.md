---
title: Azure Blob Storage 이벤트에 대응 | Microsoft Docs
description: Azure Event Grid를 사용 하 여 Blob 저장소 이벤트를 구독 하 고 대응 합니다. 이벤트 모델, 필터링 이벤트 및 이벤트 사용에 대 한 사례를 이해 합니다.
author: normesta
ms.author: normesta
ms.date: 04/06/2020
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: dineshm
ms.openlocfilehash: 8ac0e678963cadf43228ff4d89bbafe98644d00d
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132938763"
---
# <a name="reacting-to-blob-storage-events"></a>Blob Storage 이벤트에 대응

Azure Storage 이벤트를 사용 하면 응용 프로그램에서 blob의 생성 및 삭제와 같은 이벤트에 대응할 수 있습니다. 복잡한 코드나 비용이 많이 들고 비효율적인 폴링 서비스가 없어도 이렇게 할 수 있습니다. 가장 좋은 부분은 사용한 양만큼만 요금을 지불 하는 것입니다.

Blob 저장소 이벤트는 Azure Functions, Azure Logic Apps, 사용자의 http 수신기에 대 한 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) 를 사용 하 여 푸시 됩니다. Event Grid은 풍부한 재시도 정책 및 배달 못 한 편지를 통해 애플리케이션에 안정적으로 이벤트를 배달합니다.

Blob storage에서 지 원하는 이벤트의 전체 목록을 보려면 [blob 저장소 이벤트 스키마](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 문서를 참조 하세요.

일반적인 Blob Storage 이벤트 시나리오에는 이미지 또는 비디오 처리, 검색 인덱싱, 또는 파일 중심의 워크플로가 포함됩니다. 비동기 파일 업로드는 이벤트에 매우 적합합니다. 변경 빈도가 낮더라도 즉각적인 대응이 필요한 시나리오에서는 이벤트 기반 아키텍처가 특히 효율적일 수 있습니다.

Blob storage 이벤트를 시도 하려면 다음 빠른 시작 문서를 참조 하세요.

|이 도구를 사용하려면 다음을 수행합니다.    |참조 문서: |
|--|-|
|Azure portal    |[빠른 시작: Azure Portal을 사용하여 Blob Storage 이벤트를 웹 엔드포인트로 라우팅](../../event-grid/blob-event-quickstart-portal.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[빠른 시작: PowerShell을 사용하여 스토리지 이벤트를 웹 엔드포인트로 라우팅](./storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure CLI    |[빠른 시작: Azure CLI를 사용하여 웹 엔드포인트에 스토리지 이벤트 라우팅](./storage-blob-event-quickstart.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

Azure 함수를 사용 하 여 Blob 저장소 이벤트에 대응 하는 자세한 예제를 보려면 다음 문서를 참조 하세요.

- [자습서: Azure Data Lake Storage Gen2 이벤트를 사용 하 여 Databricks 델타 테이블을 업데이트](data-lake-storage-events.md)합니다.
- [자습서: Event Grid를 사용하여 업로드된 이미지 크기 자동 조정](../../event-grid/resize-images-on-storage-blob-upload-event.md?tabs=dotnet)

> [!NOTE]
> **스토리지(범용 v1)** 는 Event Grid와의 통합을 지원하지 *않습니다*.

## <a name="the-event-model"></a>이벤트 모델

Event Grid는 [이벤트 구독](../../event-grid/concepts.md#event-subscriptions)을 사용하여 이벤트 메시지를 구독자에게 라우팅합니다. 이 이미지는 이벤트 게시자, 이벤트 구독 및 이벤트 처리기 간 관계를 보여 줍니다.

![Event Grid 모델](./media/storage-blob-event-overview/event-grid-functional-model.png)

먼저 이벤트에 엔드포인트를 구독합니다. 그러면 이벤트가 트리거될 때 Event Grid 서비스는 해당 이벤트에 대한 데이터를 엔드포인트로 보냅니다.

보려면 [Blob storage 이벤트 스키마](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 문서를 참조 하세요.

> [!div class="checklist"]
> - Blob 저장소 이벤트의 전체 목록과 각 이벤트가 트리거되는 방법에 대해 설명 합니다.
> - 이러한 각 이벤트에 대해 Event Grid에서 전송하는 데이터 예제
> - 데이터에 표시되는 각 키 값 쌍의 용도

## <a name="filtering-events"></a>이벤트 필터링

Blob 이벤트는 이벤트 유형, 컨테이너 이름 또는 생성/삭제 된 개체의 이름으로 [필터링 할 수 있습니다](/cli/azure/eventgrid/event-subscription) . Event Grid 필터는 주체의 시작 또는 끝과 일치 하므로 일치 하는 제목이 있는 이벤트는 구독자에 게 전달 됩니다.

필터를 적용하는 방법에 대한 자세한 내용은 [Event Grid에 대한 이벤트 필터링](../../event-grid/how-to-filter-events.md)을 참조하세요.

Blob Storage 이벤트의 제목은 다음 형식을 사용합니다.

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

스토리지 계정의 모든 이벤트와 일치하는 항목을 찾으려면 제목 필터를 비워 둡니다.

접두사를 공유하는 컨테이너 집합에서 생성된 Blob의 이벤트와 일치하는 항목을 찾으려면 다음과 같은 `subjectBeginsWith` 필터를 사용합니다.

```
/blobServices/default/containers/containerprefix
```

특정 컨테이너에서 생성된 Blob의 이벤트와 일치하는 항목을 찾으려면 다음과 같은 `subjectBeginsWith` 필터를 사용합니다.

```
/blobServices/default/containers/containername/
```

Blob 이름 접두사를 공유하는 특정 컨테이너에서 생성된 Blob의 이벤트와 일치하는 항목을 찾으려면 다음과 같은 `subjectBeginsWith` 필터를 사용합니다.

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Blob 접미사를 공유하는 특정 컨테이너에 만들어진 Blob의 이벤트와 일치시키려면 `subjectEndsWith` 필터(예: ".log" 또는 ".jpg")를 사용합니다. 자세한 내용은 [Event Grid 개념](../../event-grid/concepts.md#event-subscriptions)을 참조하세요.

## <a name="practices-for-consuming-events"></a>이벤트 사용에 관한 지침

Blob Storage 이벤트를 처리하는 애플리케이션은 아래 권장되는 몇 가지 지침을 따라야 합니다.
> [!div class="checklist"]
> - 동일한 이벤트 처리기로 이벤트를 라우팅하도록 여러 구독이 구성될 수 있으므로, 이벤트가 특정 원본에서 온 것이라고 가정하지 않고 메시지의 토픽을 확인하여 예상하는 스토리지 계정에서 왔음을 확실히 아는 것이 중요합니다.
> - 마찬가지로, eventType이 본인이 처리하려는 형식인지 확인하고, 수신된 모든 이벤트가 예상하는 형식일 것이라고 간주하지 않도록 합니다.
> - 잠시 후 메시지가 도착할 수 있으므로 etag 필드를 사용 하 여 개체에 대 한 정보가 아직 최신 상태 인지를 파악 합니다. Etag 필드를 사용 하는 방법을 알아보려면 [Blob storage에서 동시성 관리](./concurrency-manage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#managing-concurrency-in-blob-storage)를 참조 하세요.
> - 메시지가 잘못 된 순서로 도착할 수 있으므로 sequencer 필드를 사용 하 여 특정 개체에 대 한 이벤트의 순서를 이해 합니다. Sequencer 필드는 특정 blob 이름에 대 한 이벤트의 논리적 시퀀스를 나타내는 문자열 값입니다. 표준 문자열 비교를 사용 하 여 동일한 blob 이름에서 두 이벤트의 상대 시퀀스를 이해할 수 있습니다.
> - Storage 이벤트는 구독자에 게 한 번 이상 배달 되도록 보장 하 여 모든 메시지가 출력 되도록 합니다. 그러나 백 엔드 노드 및 서비스 간 재시도 또는 구독의 가용성 때문에 중복 메시지가 발생할 수 있습니다. 메시지 배달 및 다시 시도에 대한 자세한 내용은 [Event Grid 메시지 배달 및 다시 시도](../../event-grid/delivery-and-retry.md)를 참조하세요.
> - blobType 필드를 사용하여 Blob에 허용되는 작업 형식을 파악하고 Blob에 액세스하는 데 사용해야 하는 클라이언트 라이브러리 형식을 확인합니다. 유효한 값은 `BlockBlob` 또는 `PageBlob`입니다.
> - Blob에 액세스하려면 `CloudBlockBlob` 및 `CloudAppendBlob` 생성자에 URL 필드를 사용합니다.
> - 이해할 수 없는 필드는 무시합니다. 이 지침은 나중에 추가될 수 있는 새로운 기능에 적용하는 데도 도움이 됩니다.
> - Microsoft. Storage를 확인 하려는 경우 **Blobcreated** 이벤트는 블록 Blob이 완전히 커밋되면 `CopyBlob` , `PutBlob` `PutBlockList` 또는 `FlushWithClose` REST API 호출에 대 한 이벤트를 필터링 하는 경우에만 트리거됩니다. 해당 API 호출은 데이터가 블록 Blob에 완전히 커밋된 후에만 **Microsoft.Storage.BlobCreated** 이벤트를 트리거합니다. 필터를 만드는 방법은 [Event Grid에 대한 필터 이벤트](../../event-grid/how-to-filter-events.md)를 참조하세요.

## <a name="feature-support"></a>기능 지원

이 표에서는 사용자 계정에서 이 기능이 지원되는 방법과 특정 기능을 활성화할 때 지원에 미치는 영향을 보여 줍니다.

| Storage 계정 유형 | Blob Storage(기본 지원) | Data Lake Storage Gen2 <sup>1</sup> | NFS 3.0 <sup>1</sup> | SFTP <sup>1</sup> |
|--|--|--|--|--|
| 표준 범용 v2 | ![예](../media/icons/yes-icon.png) |![예](../media/icons/yes-icon.png) <sup>2</sup>  | ![아니요](../media/icons/no-icon.png) |  ![아니요](../media/icons/no-icon.png) |
| Premium 블록 Blob          | ![예](../media/icons/yes-icon.png) |![예](../media/icons/yes-icon.png) <sup>2</sup> | ![아니요](../media/icons/no-icon.png) |  ![아니요](../media/icons/no-icon.png) |

<sup>1</sup> Data Lake Storage Gen2와 NFS(네트워크 파일 시스템) 3.0 프로토콜 모두에는 계층 구조 네임스페이스를 사용하는 스토리지 계정이 필요합니다.

<sup>1</sup> Data Lake Storage Gen2, NFS (네트워크 파일 시스템) 3.0 프로토콜 및 SFTP (SSH 파일 전송 프로토콜)는 계층 네임 스페이스가 사용 하도록 설정 된 저장소 계정이 필요 합니다.

## <a name="next-steps"></a>다음 단계

Event Grid에 대해 자세히 알아보고 Blob Storage 이벤트를 사용해보세요.

- [Event Grid 정보](../../event-grid/overview.md)
- [Blob 저장소 이벤트 스키마](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Blob 저장소 이벤트를 사용자 지정 웹 끝점으로 라우팅](storage-blob-event-quickstart.md)
