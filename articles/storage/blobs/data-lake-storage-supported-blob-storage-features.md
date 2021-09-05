---
title: Azure Data Lake Storage Gen2에서 사용할 수 있는 Blob Storage 기능 | Microsoft Docs
description: Azure Data Lake Storage Gen2에서 사용할 수 있는 Blob Storage 기능에 대해 알아봅니다.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 06/29/2021
ms.author: normesta
ms.openlocfilehash: 234f3ae60aaf389b347ce8378c889cdeb61e0738
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535806"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2에서 사용할 수 있는 Blob Storage 기능

[진단 로깅](../common/storage-analytics-logging.md), [액세스 계층](storage-blob-storage-tiers.md) 및 [Blob Storage 수명 주기 관리 정책](storage-lifecycle-management-concepts.md)과 같은 Blob Storage 기능은 이제 계층 구조 네임스페이스가 있는 계정으로 작동합니다. 따라서 이러한 기능에 대한 액세스를 잃지 않고 Blob 스토리지 계정에서 계층 구조 네임스페이스를 사용하도록 설정할 수 있습니다.

다음 표에는 Azure Data Lake Storage Gen2에서 사용할 수 있는 Blob Storage 기능이 나열되어 있습니다. 이러한 표에 표시되는 항목은 이후 지원이 계속 확장됨에 따라 변경될 수 있습니다. 기능의 미리 보기 상태와 관련된 특정 문제에 대한 자세한 내용은 [알려진 문제](data-lake-storage-known-issues.md) 문서를 참조하세요.

## <a name="supported-blob-storage-features"></a>지원되는 Blob 스토리지 기능

다음 표에서는 Data Lake Storage Gen2에서 각 Blob 스토리지 기능이 지원되는 방식을 보여줍니다. 표준 및 [프리미엄 성능](premium-tier-for-data-lake-storage.md) 계층에 대한 열이 있습니다. 

|Blob Storage 기능 |Standard |Premium |관련 문서 |
|---------------|-------------------|---|
|핫 액세스 계층|일반 공급|지원되지 않음|[Azure Blob Storage: 핫, 쿨 및 보관 스토리지 계층](storage-blob-storage-tiers.md)|
|쿨 액세스 계층|일반 공급|지원되지 않음|[Azure Blob Storage: 핫, 쿨 및 보관 스토리지 계층](storage-blob-storage-tiers.md)|
|이벤트|일반 공급|일반 공급|[Blob Storage 이벤트에 응답](storage-blob-event-overview.md)|
|메트릭(클래식)|일반 공급|일반 공급|[Azure Storage 분석 메트릭(클래식)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Monitor의 메트릭|일반 공급|미리 보기|[Azure Monitor의 Azure Storage 메트릭](./monitor-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Blob Storage PowerShell 명령|일반 공급|일반 공급|[빠른 시작: PowerShell을 사용하여 Blob 업로드, 다운로드 및 나열](storage-quickstart-blobs-powershell.md)|
|Blob Storage Azure CLI 명령|일반 공급|일반 공급|[빠른 시작: Azure CLI를 사용하여 Blob 생성, 다운로드 및 나열](storage-quickstart-blobs-cli.md)|
|Blob Storage API|일반 공급|일반 공급|[빠른 시작: .NET용 Azure Blob Storage 클라이언트 라이브러리 v12](storage-quickstart-blobs-dotnet.md)<br>[빠른 시작: Java v12 SDK로 Blob 관리](storage-quickstart-blobs-java.md)<br>[빠른 시작: Python v12 SDK로 Blob 관리](storage-quickstart-blobs-python.md)<br>[빠른 시작: Node.js에서 JavaScript v12 SDK를 사용하여 Blob 관리](storage-quickstart-blobs-nodejs.md)|
|고객 관리형 키|일반 공급|일반 공급|[Azure Storage 암호화용 고객 관리형 키](../common/customer-managed-keys-overview.md?toc=/azure/storage/blobs/toc.json)|
|진단 로그|일반 공급|미리 보기 |[Azure Storage 분석 로깅](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|보관 액세스 계층|일반 공급|지원되지 않음|[Azure Blob Storage: 핫, 쿨 및 보관 스토리지 계층](storage-blob-storage-tiers.md)|
|수명 주기 관리 정책(계층화)|일반 공급|아직 지원되지 않음|[Azure Blob Storage 수명 주기 관리](storage-lifecycle-management-concepts.md)|
|수명 주기 관리 정책(Blob 삭제)|일반 공급|일반 공급|[Azure Blob Storage 수명 주기 관리](storage-lifecycle-management-concepts.md)|
|컨테이너 일시 삭제|일반 공급|일반 공급|[컨테이너에 대한 일시 삭제](soft-delete-container-overview.md)|
|Azure Monitor 로그인|미리 보기 |미리 보기|[Azure Storage 모니터링](./monitor-blob-storage.md)|
|스냅샷|미리 보기|미리 보기|[Blob 스냅샷](snapshots-overview.md)|
|정적 웹 사이트|일반 공급<div role="complementary" aria-labelledby="preview-form"></div>|일반 공급<div role="complementary" aria-labelledby="preview-form"></div>|[Azure Storage에서 정적 웹 사이트 호스팅](storage-blob-static-website.md)|
|변경 불가능한 스토리지|미리 보기<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|미리 보기<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|[비즈니스에 중요한 BLOB 데이터를 변경이 불가능한 스토리지에 저장](immutable-storage-overview.md)|
|Azure Storage 인벤토리|미리 보기|미리 보기|[Azure Storage 인벤토리를 사용하여 Blob 데이터 관리(미리 보기)](blob-inventory.md)|
|사용자 지정 도메인|미리 보기<div role="complementary" aria-labelledby="preview-form-1"><sup>1</sup></div>|미리 보기<div role="complementary" aria-labelledby="preview-form-1"><sup>1</sup></div>|[Azure Blob Storage 엔드포인트에 사용자 지정 도메인 매핑](storage-custom-domain-name.md)|
|Blob 일시 삭제|미리 보기|미리 보기|[Blob에 대한 일시 삭제](./soft-delete-blob-overview.md)|
|Blobfuse|일반 공급|일반 공급|[blobfuse를 사용하여 Blob Storage를 파일 시스템으로 탑재하는 방법](storage-how-to-mount-container-linux.md)|
|익명 퍼블릭 액세스 |일반 공급|일반 공급| [컨테이너 및 Blob에 대한 익명 공용 읽기 액세스 구성](anonymous-read-access-configure.md)을 참조하세요.|
|고객 관리형 계정 장애 조치(failover)|아직 지원되지 않음|아직 지원되지 않음|[재해 복구 및 계정 장애 조치(failover)](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|고객 제공 키|아직 지원되지 않음|아직 지원되지 않음|[Blob 스토리지에 대한 요청 시 암호화 키 제공](encryption-customer-provided-keys.md)|
|암호화 범위|아직 지원되지 않음|아직 지원되지 않음|[암호화 범위 만들기 및 관리](encryption-scope-manage.md)|
|변경 피드|아직 지원되지 않음|아직 지원되지 않음|[Azure Blob Storage의 변경 피드 지원](storage-blob-change-feed.md)|
|개체 복제|아직 지원되지 않음|아직 지원되지 않음|[블록 Blob에 대한 개체 복제 구성](object-replication-configure.md)|
|Blob 버전 관리|아직 지원되지 않음|아직 지원되지 않음|[Blob 버전 관리 설정 및 관리](versioning-enable.md)|
|지정 시간 복원|아직 지원되지 않음|아직 지원되지 않음|[블록 Blob에 대한 지정 시간 복원(미리 보기)](point-in-time-restore-overview.md)|
|Blob 인덱스 태그|아직 지원되지 않음|아직 지원되지 않음|[Blob 인덱스 태그를 사용하여 Azure Blob 데이터 관리 및 찾기](storage-manage-find-blobs.md)|

<div id="preview-form-2"><sup>1</sup> 사용자 지정 도메인 이름은 Blob 서비스 또는 정적 웹 사이트 엔드포인트에만 매핑될 수 있습니다. Data Lake 스토리지 엔드포인트는 지원되지 않습니다(</a>).  </div>

## <a name="see-also"></a>참고 항목

- [Azure Data Lake Storage Gen2에서 알려진 문제](data-lake-storage-known-issues.md)
- [Azure Data Lake Storage Gen2를 지원하는 Azure 서비스](data-lake-storage-supported-azure-services.md)
- [Azure Data Lake Storage Gen2를 지원하는 오픈 소스 플랫폼](data-lake-storage-supported-open-source-platforms.md)
- [Azure Data Lake Storage에 대한 다중 프로토콜 액세스](data-lake-storage-multi-protocol-access.md)
