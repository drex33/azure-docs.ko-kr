---
title: Azure Storage 계정의 Blob Storage 기능 지원
description: 지정 된 저장소 계정 유형 및 계정에서 사용 하도록 설정 된 설정에 대 한 각 저장소 계정 기능에 대 한 지원 수준을 결정 합니다.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 11/16/2021
ms.author: normesta
ms.openlocfilehash: fe19796d1acc193c29b4ad547ce7e349e637c1f2
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132939465"
---
# <a name="blob-storage-feature-support-in-azure-storage-accounts"></a>Azure Storage 계정의 Blob Storage 기능 지원

이 아티클에서는 기능이 완전히 지원 되는지, 미리 보기 수준에서 지원 되는지 또는 아직 지원 되지 않는지를 표시 합니다. 지원 수준은 저장소 계정 유형 및 계정에 특정 기능 또는 프로토콜이 사용 되는지 여부에 따라 영향을 받습니다.

이러한 표에 표시되는 항목은 이후 지원이 계속 확장됨에 따라 변경될 수 있습니다.

## <a name="standard-general-purpose-v2-accounts"></a>표준 범용 v2 계정

| 스토리지 기능 | Blob Storage(기본 지원) | Data Lake Storage Gen2 <sup>1</sup>   | NFS 3.0 <sup>1</sup>  | SFTP <sup>1</sup> | 
|---------------|-------------------|---|---|--|
| [액세스 계층-보관](access-tiers-overview.md) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) |
| [액세스 계층-콜드](access-tiers-overview.md)    | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png)| ![예](../media/icons/yes-icon.png) |
| [액세스 계층-핫](access-tiers-overview.md) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) |
| [익명 퍼블릭 액세스](anonymous-read-access-configure.md) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png)| ![예](../media/icons/yes-icon.png) |
| [Azure Active Directory 보안](authorize-access-azure-active-directory.md) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [Blob 인벤토리](blob-inventory.md) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png)  <sup>2</sup> | ![예](../media/icons/yes-icon.png)  <sup>2</sup> | ![예](../media/icons/yes-icon.png)  <sup>2</sup> |
| [Blob 인덱스 태그](storage-manage-find-blobs.md) | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [Blob 스냅샷](snapshots-overview.md) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png)  <sup>2</sup> | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [Blob Storage api](reference.md) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png)  <sup>3</sup> | ![예](../media/icons/yes-icon.png) <sup>3</sup>| ![예](../media/icons/yes-icon.png) <sup>3</sup> |
| [Blob Storage Azure CLI 명령](storage-quickstart-blobs-cli.md) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) |
| [Blob Storage 이벤트](storage-blob-event-overview.md) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png)  <sup>3</sup>  | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [Blob Storage PowerShell 명령](storage-quickstart-blobs-powershell.md) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) |
| [Blob 버전 관리](versioning-overview.md) | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [Blobfuse](storage-how-to-mount-container-linux.md) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) |
| [변경 피드](storage-blob-change-feed.md) | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [사용자 지정 도메인](storage-custom-domain-name.md) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png)  <sup>2</sup> | ![예](../media/icons/yes-icon.png)  <sup>2</sup> | ![예](../media/icons/yes-icon.png)  <sup>2</sup> |
| [고객 관리형 계정 장애 조치(failover)](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [고객 관리 키 (암호화)](../common/customer-managed-keys-overview.md?toc=/azure/storage/blobs/toc.json) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [고객이 제공한 키 (암호화)](encryption-customer-provided-keys.md) | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [데이터 중복성 옵션](../common/storage-redundancy.md?toc=/azure/storage/blobs/toc.json) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) |
| [암호화 범위](encryption-scope-overview.md) | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [변경 불가능한 스토리지](immutable-storage-overview.md) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png)  <sup>2</sup> | ![예](../media/icons/yes-icon.png)  <sup>2</sup> | ![예](../media/icons/yes-icon.png)  <sup>2</sup> |
| [수명 주기 관리를 위한 마지막 액세스 시간 추적](lifecycle-management-overview.md#move-data-based-on-last-accessed-time) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) | ![예](../media/icons/yes-icon.png) |
| [수명 주기 관리 정책(Blob 삭제)](./lifecycle-management-overview.md) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) |
| [수명 주기 관리 정책(계층화)](./lifecycle-management-overview.md) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) |
| [Azure Monitor 로그인](./monitor-blob-storage.md) | ![예](../media/icons/yes-icon.png)  <sup>2</sup> | ![예](../media/icons/yes-icon.png)  <sup>2</sup> | ![예](../media/icons/yes-icon.png)  <sup>2</sup> | ![예](../media/icons/yes-icon.png)  <sup>2</sup> |
| [Azure Monitor의 메트릭](./monitor-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) |
| [블록 Blob에 대한 개체 복제](object-replication-overview.md) | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [페이지 Blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) | ![예](../media/icons/yes-icon.png) |![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [블록 Blob에 대한 지정 시간 복원(미리 보기)](point-in-time-restore-overview.md) | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [Blob에 대한 일시 삭제](./soft-delete-blob-overview.md) | ![예](../media/icons/yes-icon.png) | ![예 ](../media/icons/yes-icon.png) <sup>2</sup><sup>3</sup>      | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [컨테이너에 대한 일시 삭제](soft-delete-container-overview.md) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [정적 웹 사이트](storage-blob-static-website.md) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) |
| [스토리지 분석 로그 (클래식)](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png)  <sup>3</sup> | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [스토리지 분석 메트릭 (클래식)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |

<sup>1</sup> Data Lake Storage Gen2, NFS (네트워크 파일 시스템) 3.0 프로토콜 및 SFTP (SSH 파일 전송 프로토콜)는 계층 네임 스페이스가 사용 하도록 설정 된 저장소 계정이 필요 합니다.

<sup>2</sup>    기능은 미리 보기 수준에서 지원 됩니다.

<sup>3</sup> [Azure Data Lake Storage Gen2의 알려진 문제를](data-lake-storage-known-issues.md)참조 하세요. 이러한 문제는 계층 구조 네임 스페이스 기능이 사용 하도록 설정 된 모든 계정에 적용 됩니다.

## <a name="premium-block-blob-accounts"></a>Premium 블록 blob 계정

| 스토리지 기능 | Blob Storage(기본 지원) | Data Lake Storage Gen2 <sup>1</sup> | NFS 3.0 <sup>1</sup> | SFTP <sup>1</sup> |
|---------------|-------------------|---|---|--|
| [액세스 계층-보관](access-tiers-overview.md)  | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [액세스 계층-콜드](access-tiers-overview.md) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [액세스 계층-핫](access-tiers-overview.md) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [익명 퍼블릭 액세스](anonymous-read-access-configure.md) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [Azure Active Directory 보안](authorize-access-azure-active-directory.md) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [Blob 인벤토리](blob-inventory.md) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png)  <sup>2</sup> | ![예](../media/icons/yes-icon.png)  <sup>2</sup> | ![예](../media/icons/yes-icon.png)  <sup>2</sup> |
| [Blob 인덱스 태그](storage-manage-find-blobs.md) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [Blob 스냅샷](snapshots-overview.md) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png)  <sup>2</sup> | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [Blob Storage api](reference.md) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png)  <sup>3</sup> | ![예](../media/icons/yes-icon.png) <sup>3</sup>| ![예](../media/icons/yes-icon.png) <sup>3</sup> |
| [Blob Storage Azure CLI 명령](storage-quickstart-blobs-cli.md) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) |
| [Blob Storage 이벤트](storage-blob-event-overview.md) | ![예](../media/icons/yes-icon.png)  <sup>3</sup>  | ![예](../media/icons/yes-icon.png) <sup>3</sup>| ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [Blob Storage PowerShell 명령](storage-quickstart-blobs-powershell.md) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) |
| [Blob 버전 관리](versioning-overview.md) | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [Blobfuse](storage-how-to-mount-container-linux.md) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) |
| [변경 피드](storage-blob-change-feed.md) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [사용자 지정 도메인](storage-custom-domain-name.md) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png)  <sup>2</sup> | ![예](../media/icons/yes-icon.png)  <sup>2</sup> | ![예](../media/icons/yes-icon.png)  <sup>2</sup> |
| [고객 관리형 계정 장애 조치(failover)](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [고객 관리 키 (암호화)](../common/customer-managed-keys-overview.md?toc=/azure/storage/blobs/toc.json)  | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [고객이 제공한 키 (암호화)](encryption-customer-provided-keys.md) | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [데이터 중복성 옵션](../common/storage-redundancy.md?toc=/azure/storage/blobs/toc.json) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) |
| [암호화 범위](encryption-scope-overview.md) | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [변경 불가능한 스토리지](immutable-storage-overview.md) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png)  <sup>2</sup> | ![예](../media/icons/yes-icon.png)  <sup>2</sup> | ![예](../media/icons/yes-icon.png)  <sup>2</sup> |
| [수명 주기 관리를 위한 마지막 액세스 시간 추적](lifecycle-management-overview.md#move-data-based-on-last-accessed-time) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) | ![예](../media/icons/yes-icon.png) |
| [수명 주기 관리 정책(Blob 삭제)](./lifecycle-management-overview.md) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) |
| [수명 주기 관리 정책(계층화)](./lifecycle-management-overview.md) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [Azure Monitor 로그인](./monitor-blob-storage.md) | ![예](../media/icons/yes-icon.png)  <sup>2</sup> | ![예](../media/icons/yes-icon.png)  <sup>2</sup> | ![예](../media/icons/yes-icon.png)  <sup>2</sup> | ![예](../media/icons/yes-icon.png)  <sup>2</sup> |
| [Azure Monitor의 메트릭](./monitor-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png)  <sup>2</sup> | ![예](../media/icons/yes-icon.png)  <sup>2</sup> | ![예](../media/icons/yes-icon.png)  <sup>2</sup> |
| [블록 Blob에 대한 개체 복제](object-replication-overview.md) | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [페이지 Blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [블록 Blob에 대한 지정 시간 복원(미리 보기)](point-in-time-restore-overview.md) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [Blob에 대한 일시 삭제](./soft-delete-blob-overview.md)   | ![예](../media/icons/yes-icon.png) | ![예 ](../media/icons/yes-icon.png) <sup>2</sup><sup>3</sup>      | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [컨테이너에 대한 일시 삭제](soft-delete-container-overview.md) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| [정적 웹 사이트](storage-blob-static-website.md) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) |
| [Storage Analytics 로그(클래식)](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![예](../media/icons/yes-icon.png) | ![예 ](../media/icons/yes-icon.png) <sup>2</sup><sup>3</sup>     | ![아니요](../media/icons/no-icon.png)| ![아니요](../media/icons/no-icon.png) |
| [Storage Analytics 메트릭(클래식)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |

<sup>1</sup> Data Lake Storage Gen2, NFS(네트워크 파일 시스템) 3.0 프로토콜 및 SFTP(SSH 파일 전송 프로토콜) 지원은 모두 계층 구조 네임스페이스를 사용하도록 설정된 스토리지 계정이 필요합니다.

<sup>2</sup>    기능은 미리 보기 수준에서 지원됩니다.

<sup>3</sup> [Azure Data Lake Storage Gen2의 알려진 문제를 참조하세요.](data-lake-storage-known-issues.md) 이러한 문제는 계층 구조 네임스페이스 기능을 사용하도록 설정된 모든 계정에 적용됩니다.

## <a name="see-also"></a>참고 항목

- [Azure Data Lake Storage Gen2에서 알려진 문제](data-lake-storage-known-issues.md)

- [Azure Blob Storage의 NFS(네트워크 파일 시스템) 3.0 프로토콜 지원의 알려진 문제](network-file-system-protocol-known-issues.md)

- [Azure Blob Storage SFTP(SSH 파일 전송 프로토콜) 지원과 관련된 알려진 문제(미리 보기)](secure-file-transfer-protocol-known-issues.md)
