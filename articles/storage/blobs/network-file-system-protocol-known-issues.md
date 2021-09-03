---
title: Azure Blob Storage에서 NFS 3.0의 알려진 문제 | Microsoft Docs
description: Azure Blob Storage에서 NFS(네트워크 파일 시스템) 3.0 프로토콜 지원의 제한 사항과 알려진 문제에 관해 알아봅니다.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: normesta
ms.reviewer: yzheng
ms.openlocfilehash: 8af0b4a5c26d70f9fdedc30d07c8953bfee76fb4
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113727133"
---
# <a name="known-issues-with-network-file-system-nfs-30-protocol-support-in-azure-blob-storage"></a>Azure Blob Storage의 NFS(네트워크 파일 시스템) 3.0 프로토콜 지원의 알려진 문제

이 문서에서는 Azure Blob Storage에서 NFS(네트워크 파일 시스템) 3.0 프로토콜 지원의 제한 사항과 알려진 문제를 설명합니다.

## <a name="nfs-30-support"></a>NFS 3.0 지원

- NFS 3.0 지원은 기존 스토리지 계정에서 사용하도록 설정할 수 없습니다.

- NFS 3.0 지원을 사용하도록 설정한 후에는 스토리지 계정에서 사용하지 않도록 설정할 수 없습니다.

## <a name="nfs-30-features"></a>NFS 3.0 기능

다음 NFS 3.0 기능은 아직 지원되지 않습니다.

- UDP를 통한 NFS 3.0. TCP를 통한 NFS 3.0만 지원됩니다.

- NLM(네트워크 잠금 관리자)를 사용한 파일 잠금. 탑재 명령은 `-o nolock` 매개 변수를 포함해야 합니다.

- 하위 디렉터리 탑재. 루트 디렉터리(컨테이너)만 탑재할 수 있습니다.

- 탑재 나열(예: `showmount -a` 명령 사용)

- 내보내기 나열(예: `showmount -e` 명령 사용)

- 하드 링크

- 컨테이너를 읽기 전용으로 내보내기

## <a name="nfs-30-clients"></a>NFS 3.0 클라이언트

NFS용 Windows 클라이언트는 아직 지원되지 않습니다.

## <a name="blob-storage-features"></a>Blob Storage 기능

다음 Blob Storage 기능은 아직 지원되지 않습니다.

- [Azure AD(Active Directory) 보안](../common/authorize-data-access.md?toc=/azure/storage/blobs/toc.json)
- [Azure Backup 통합](../../backup/blob-backup-overview.md)
- [Azure Storage 분석 로깅](../common/storage-analytics-logging.md?toc=/azure/storage/blobs/toc.json)
- [Blob 인덱스 태그](storage-blob-index-how-to.md)
- [Blob Storage 이벤트](storage-blob-event-overview.md)
- [Blob 버전 관리](versioning-enable.md)
- [변경 피드](storage-blob-change-feed.md)
- [고객 관리형 계정 장애 조치(failover)](../common/storage-disaster-recovery-guidance.md?toc=/azure/storage/blobs/toc.json)
- [Azure Storage 암호화를 위한 고객 제공 키](encryption-customer-provided-keys.md) 
- [암호화 범위](encryption-scope-overview.md)
- [수명 주기 관리를 위한 마지막 액세스 시간 추적](storage-lifecycle-management-concepts.md#move-data-based-on-last-accessed-date-preview)
- [블록 Blob에 대한 개체 복제](object-replication-overview.md)
- [페이지 Blob](storage-blobs-introduction.md#blobs)
- [블록 Blob에 대한 지정 시간 복원(미리 보기)](point-in-time-restore-overview.md)
- [Blob에 대한 일시 삭제](soft-delete-blob-overview.md)
- [컨테이너에 대한 일시 삭제](soft-delete-container-overview.md)
- [Blob 스냅샷](snapshots-overview.md)
- [정적 웹 사이트 호스팅](storage-blob-static-website.md)

## <a name="blob-storage-apis"></a>Blob Storage API

NFS 3.0, Blob API 및 Data Lake Storage Gen2 API는 동일한 데이터에서 작업할 수 있습니다. 

이 섹션에서는 NFS 3.0, Blob API 및 Data Lake Storage Gen2 API를 사용하여 동일한 데이터에서 작업하는 경우의 문제와 제한 사항을 설명합니다. 

- NFS 3.0과 Blob API 또는 Data Lake Storage API를 사용하여 한 파일의 동일한 인스턴스에 작성할 수는 없습니다. NFS를 사용하여 파일에 쓰면 해당 파일의 블록은  [블록 목록 가져오기](/rest/api/storageservices/get-block-list) Blob API에 대한 호출에 보이지 않습니다. 오직 덮어쓰기를 사용할 때만 허용됩니다. API를 사용하여 파일/BLOB을 덮어쓰면 됩니다. 잘림 없음 옵션을 사용하여 NFS 3.0으로 덮어쓸 수도 있습니다.

- 구분 기호를 지정하지 않고 [Blob 나열](/rest/api/storageservices/list-blobs) 작업을 사용하면 결과에 디렉터리와 Blob이 모두 포함됩니다. 구분 기호를 사용하려면 슬래시(`/`)만 사용합니다. 이것이 유일하게 지원되는 구분 기호입니다.

- [Blob 삭제](/rest/api/storageservices/delete-blob) API를 사용하여 디렉터리를 삭제하면 디렉터리가 비어있는 경우에만 삭제됩니다. 따라서, Blob API 디렉터리 삭제는 재귀적으로 사용할 수 없습니다.

다음과 같은 Blob REST API는 지원되지 않습니다.

* [Blob 배치(페이지)](/rest/api/storageservices/put-blob)
* [페이지 배치](/rest/api/storageservices/put-page)
* [페이지 범위 가져오기](/rest/api/storageservices/get-page-ranges)
* [Blob 증분 복사](/rest/api/storageservices/incremental-copy-blob)
* [URL에서 페이지 배치](/rest/api/storageservices/put-page-from-url)
* [블록 목록 배치](/rest/api/storageservices/put-block-list)

비관리형 VM 디스크는 계층 구조 네임스페이스가 있는 계정에서 지원되지 않습니다. 스토리지 계정에서 계층 구조 네임스페이스를 활성화하려면, 계층 구조 네임스페이스 기능을 활성화하지 않은 스토리지 계정에 비관리형 VM 디스크를 배치합니다. 

## <a name="see-also"></a>참고 항목

- [Azure Blob Storage의 NFS(네트워크 파일 시스템) 3.0 프로토콜 지원](network-file-system-protocol-support.md)
- [NFS(네트워크 파일 시스템) 3.0 프로토콜을 사용하여 Blob Storage 탑재](network-file-system-protocol-support-how-to.md)