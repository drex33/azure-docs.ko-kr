---
title: Azure HPC Cache에서 NFS Blob Storage 사용
description: Azure HPC Cache에서 ADLS-NFS Blob Storage를 사용하는 경우의 절차 및 제한 사항에 대해 설명합니다.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/12/2021
ms.author: v-erkel
ms.openlocfilehash: e9d31ae4ccaa7bffaf1ce38a9f044993277f5f2f
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130041674"
---
# <a name="use-nfs-mounted-blob-storage-with-azure-hpc-cache"></a>Azure HPC Cache에서 NFS 탑재 Blob Storage 사용

Azure HPC Cache에서 NFS가 탑재된 Blob 컨테이너를 사용할 수 있습니다. Blob Storage 설명서 사이트의 [Azure Blob Storage에서 NFS 3.0 프로토콜 지원](../storage/blobs/network-file-system-protocol-support.md)에 대해 자세히 알아보세요.

Azure HPC Cache는 ADLS-NFS 스토리지 대상 유형에서 NFS 지원 Blob Storage를 사용합니다. 이러한 스토리지 대상은 일반적인 NFS 스토리지 대상과 비슷하지만 일반적인 Azure Blob 대상과 약간의 겹치는 부분이 있습니다.

이 문서에서는 ADLS-NFS 스토리지 대상을 사용할 때 이해해야 하는 전략과 제한 사항을 설명합니다.

또한 NFS Blob 설명서, 특히 호환 및 비호환 시나리오를 설명하는 다음 섹션을 읽어야 합니다.

* [기능 개요](../storage/blobs/network-file-system-protocol-support.md)
* [성능 고려 사항](../storage/blobs/network-file-system-protocol-support-performance.md)
* [알려진 문제 및 제한 사항](../storage/blobs/network-file-system-protocol-known-issues.md)

## <a name="understand-consistency-requirements"></a>일관성 요구 사항 이해

HPC 캐시는 ADLS-NFS 스토리지 대상에 대해 강력한 일관성을 요구합니다. 기본적으로 NFS 지원 Blob Storage는 파일 메타데이터를 엄격하게 업데이트하지 않으므로 HPC 캐시가 파일 버전을 정확하게 비교하지 못합니다.

이러한 차이를 해결하기 위해 Azure HPC Cache는 스토리지 대상으로 사용되는 모든 NFS 지원 Blob 컨테이너에서 NFS 특성 캐싱을 자동으로 사용하지 않도록 설정합니다.

이 설정은 캐시에서 제거해도 컨테이너의 수명 기간 동안 지속됩니다.

## <a name="pre-load-data-with-nfs-protocol"></a>NFS 프로토콜을 사용하여 데이터 미리 로드
<!-- cross-referenced from hpc-cache-ingest.md and here -->

NFS 지원 Blob 컨테이너에서는 *파일을 만들 때 사용한 것과 동일한 프로토콜을 통해서만 파일을 편집할 수 있습니다*. 즉, Azure REST API를 사용하여 컨테이너를 채우면 NFS를 사용하여 해당 파일을 업데이트할 수 없습니다. Azure HPC Cache는 NFS만 사용하기 때문에 Azure REST API를 사용하여 만든 파일은 편집할 수 없습니다. ([Blob 스토리지 API의 알려진 문제](../storage/blobs/data-lake-storage-known-issues.md#blob-storage-apis)에 대해 자세히 알아보기)

컨테이너가 비어 있거나 파일이 NFS를 사용하여 만들어진 경우에는 캐시에 문제가 되지 않습니다.

컨테이너의 파일이 NFS 대신 Azure Blob REST API 사용하여 만들어진 경우 Azure HPC Cache 원래 파일에서 이러한 작업으로 제한됩니다.

* 디렉터리에 파일을 나열합니다.
* 파일을 읽고 후속 읽기를 위해 캐시에 보관합니다.
* 파일을 삭제합니다.
* 파일이 비어 있습니다(0으로 자르기).
* 파일의 복사본을 저장합니다. 이 복사본은 NFS에서 만든 파일로 표시되며 NFS를 사용하여 편집할 수 있습니다.

**Azure HPC Cache는 REST를 사용하여 만든 파일의 콘텐츠를 편집할 수 없습니다**. 즉, 캐시가 변경된 파일을 클라이언트에서 스토리지 대상으로 다시 저장할 수 없습니다.

NFS를 사용하여 만들지 않은 파일에 대한 읽기/쓰기 캐싱 사용 모델을 사용하는 경우 데이터 무결성 문제가 발생할 수 있으므로 이러한 제한을 이해하는 것이 중요합니다.

> [!TIP]
> 읽기 및 쓰기 캐싱에 대한 자세한 내용은 [캐시 사용 모델 이해](cache-usage-models.md)를 참조하세요.

### <a name="write-caching-scenarios"></a>쓰기 캐싱 시나리오

이러한 캐시 사용 모델에는 쓰기 캐싱이 포함됩니다.

* **15%보다 큰 쓰기**
* **15%보다 큰 쓰기, 변경 내용이 있는지 30초마다 지원 서버를 확인합니다.**
* **15%보다 큰 쓰기, 변경 내용이 있는지 60초마다 지원 서버를 확인합니다.**
* **15%보다 큰 쓰기, 30초마다 서버에 쓰기 저장합니다.**

쓰기 캐싱 사용 모델은 NFS를 사용하여 만든 파일에만 사용해야 합니다.

REST에서 만든 파일에 대한 쓰기 캐싱을 사용하려고 하면 파일의 변경 내용이 손실될 수 있습니다. 이는 캐시가 스토리지 컨테이너에 파일 편집 내용을 즉시 저장하려고 시도하지 않기 때문입니다.

다음은 REST에서 만든 파일에 대한 쓰기를 캐시하는 것이 데이터를 위험에 노출시키는 이유를 설명합니다.

1. 캐시는 클라이언트의 편집 내용을 수락하고 각 변경에 대한 성공 메시지를 반환합니다.
1. 캐시는 변경된 파일을 스토리지에 보관하고 추가 변경 작업을 기다립니다.
1. 시간이 지난 후 캐시는 변경된 파일을 백 엔드 컨테이너에 저장하려고 합니다. 이 시점에서 오류 메시지가 표시되는 것은 REST에서 만든 파일에 NFS를 사용하여 쓰려고 하기 때문입니다.

   클라이언트 컴퓨터에 변경 내용이 수락되지 않은 것을 알리기에는 너무 늦었으며 캐시는 원본 파일을 업데이트할 방법이 없습니다. 따라서 클라이언트의 변경 내용이 손실됩니다.

### <a name="read-caching-scenarios"></a>읽기 캐싱 시나리오

읽기 캐싱 시나리오는 NFS 또는 Azure Blob REST API를 사용하여 만든 파일에 적합합니다.

다음과 같은 사용 모델은 읽기 캐싱만 사용합니다.

* **대량의 드문 쓰기 읽기**
* **클라이언트가 캐시를 무시하면서 NFS 대상에 쓰기**
* **대량 읽기, 3시간마다 지원 서버 확인**

REST API 또는 NFS에서 만든 파일에 이러한 사용 모델을 사용할 수 있습니다. 클라이언트에서 백 엔드 컨테이너로 전송된 NFS 쓰기는 여전히 실패하지만 즉시 실패하고 클라이언트에 오류 메시지를 반환합니다.

읽기 캐싱 워크플로는 캐시되지 않은 한 파일의 변경 내용을 여전히 포함할 수 있습니다. 예를 들어 클라이언트는 컨테이너에서 파일에 액세스할 수 있지만 변경 내용을 새 파일로 다시 작성할 수 있습니다. 또는 수정된 파일을 다른 위치에 저장할 수 있습니다.

## <a name="recognize-network-lock-manager-nlm-limitations"></a>NLM(네트워크 잠금 관리자) 제한 사항 인식

NFS 지원 Blob 컨테이너는 파일의 충돌을 방지하는 데 일반적으로 사용되는 NFS 프로토콜인 NLM(네트워크 잠금 관리자)를 지원하지 않습니다.

NFS 워크플로가 원래 하드웨어 스토리지 시스템용으로 작성된 경우 클라이언트 애플리케이션에는 NLM 요청이 포함될 수 있습니다. NFS 지원 Blob Storage로 프로세스를 이동할 때 이 제한 사항을 해결하려면 클라이언트에서 캐시를 탑재할 때 NLM을 사용하지 않도록 설정해야 합니다.

NLM을 사용하지 않도록 설정하려면 클라이언트의 ``mount`` 명령에서 ``-o nolock`` 옵션을 사용합니다. 이 옵션은 클라이언트의 NLM 잠금 요청을 차단하고 응답에서 오류를 수신하지 못하도록 합니다. ``nolock`` 옵션은 운영 체제마다 다르게 구현됩니다. 자세한 내용은 클라이언트 OS 설명서(man 5 nfs)를 확인하세요.

## <a name="streamline-writes-to-nfs-enabled-containers-with-hpc-cache"></a>HPC 캐시로 NFS 지원 컨테이너에 대한 쓰기 간소화

Azure HPC Cache는 ADLS-NFS 스토리지 대상에 대한 변경 내용 쓰기를 포함하는 워크로드의 성능을 향상시킬 수 있습니다.

> [!NOTE]
> Azure HPC Cache를 통해 파일을 수정하려면 NFS를 사용하여 ADLS-NFS 스토리지 컨테이너를 채워야 합니다.

NFS 지원 Blob [성능 고려 사항 문서](../storage/blobs/network-file-system-protocol-support-performance.md)에 설명된 제한 사항 중 하나는 기존 파일을 덮어쓸 때 ADLS-NFS 스토리지가 그다지 효율적이지 않다는 것입니다. NFS가 탑재된 Blob Storage와 함께 Azure HPC Cache를 사용하는 경우 클라이언트가 활성 파일을 수정할 때 캐시가 간헐적인 다시 쓰기를 처리합니다. 백 엔드 컨테이너에 파일을 쓰는 경우의 대기 시간은 클라이언트에게서 숨겨집니다.

위의 [NFS 프로토콜을 사용하여 데이터 미리 로드](#pre-load-data-with-nfs-protocol)에서 설명한 제한 사항에 유의하세요.

## <a name="next-steps"></a>다음 단계

* [ADLS-NFS 스토리지 대상 필수 조건](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements) 알아보기
* [NFS 지원 Blob Storage 계정](../storage/blobs/network-file-system-protocol-support-how-to.md) 만들기
