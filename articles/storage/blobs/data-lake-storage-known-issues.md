---
title: Azure Data Lake Storage Gen2에서 알려진 문제
description: Azure Data Lake Storage Gen2의 알려진 문제와 제한 사항에 대해 알아봅니다.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/14/2021
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 6a612621ef316e7d4e7c248968b3cc87c04b85ae
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130046265"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2에서 알려진 문제

이 문서에서는 계층 구조 네임 스페이스 기능이 사용 하도록 설정 된 계정의 제한 사항 및 알려진 문제에 대해 설명 합니다.

> [!NOTE]
> 이 문서에서 설명 하는 기능 중 일부는 NFS (네트워크 파일 시스템) 3.0 지원을 사용할 수 있는 계정에서 지원 되지 않을 수 있습니다. 다양 한 기능을 사용 하는 경우 기능 지원의 영향을 보여 주는 표를 보려면 [Azure Storage 계정의 Blob Storage 기능 지원](storage-feature-support-in-storage-accounts.md)을 참조 하세요.

## <a name="supported-blob-storage-features"></a>지원되는 Blob 스토리지 기능

점점 더 많은 수의 Blob Storage 기능이 계층 구조 네임스페이스가 있는 계정에서 작동합니다. 전체 목록은 [Azure Data Lake Storage Gen2에서 사용할 수 있는 Blob Storage 기능](./storage-feature-support-in-storage-accounts.md)을 참조하세요.

## <a name="supported-azure-service-integrations"></a>지원되는 Azure 서비스 통합

Azure Data Lake Storage Gen2는 데이터를 수집하고, 분석을 수행하고, 시각적 표현을 만드는 데 사용할 수 있는 여러 Azure 서비스를 지원합니다. 지원되는 Azure 서비스 목록은 [Azure Data Lake Storage Gen2를 지원하는 Azure 서비스](data-lake-storage-supported-azure-services.md)를 참조하세요.

자세한 내용은 [Azure Data Lake Storage Gen2를 지 원하는 Azure 서비스](data-lake-storage-supported-azure-services.md)를 참조 하세요.

## <a name="supported-open-source-platforms"></a>지원되는 오픈 소스 플랫폼

Data Lake Storage Gen2는 몇 가지 오픈 소스 플랫폼에서 지원합니다. 전체 목록은 [Azure Data Lake Storage Gen2를 지원하는 오픈 소스 플랫폼](data-lake-storage-supported-open-source-platforms.md)을 참조하세요.

자세한 내용은 [Azure Data Lake Storage Gen2를 지 원하는 오픈 소스 플랫폼](data-lake-storage-supported-open-source-platforms.md)을 참조 하세요.

## <a name="blob-storage-apis"></a>Blob Storage API

Data Lake Storage Gen2 api, NFS 3.0 및 Blob api는 동일한 데이터에 대해 작동할 수 있습니다.

이 섹션에서는 동일한 데이터에 대해 작동 하는 blob api, NFS 3.0 및 Data Lake Storage Gen2 api를 사용 하는 경우의 문제 및 제한 사항에 대해 설명 합니다.

- blob api, NFS 3.0 및 Data Lake Storage api를 사용 하 여 파일의 동일한 인스턴스에 쓸 수는 없습니다. Data Lake Storage Gen2 또는 api 또는 NFS 3.0를 사용 하 여 파일에 쓰는 경우 해당 파일의 블록이 [Get Block List](/rest/api/storageservices/get-block-list) blob API에 대 한 호출에 표시 되지 않습니다. 오직 덮어쓰기를 사용할 때만 허용됩니다. 0-자르기 옵션을 사용 하 여 API 또는 NFS 3.0를 사용 하 여 파일/b s i s를 덮어쓸 수 있습니다.

- 구분 기호를 지정하지 않고 [Blob 나열](/rest/api/storageservices/list-blobs) 작업을 사용하면 결과에 디렉터리와 Blob이 모두 포함됩니다. 구분 기호를 사용하려면 슬래시(`/`)만 사용합니다. 이것이 유일하게 지원되는 구분 기호입니다.

- [Blob 삭제](/rest/api/storageservices/delete-blob) API를 사용하여 디렉터리를 삭제하면 디렉터리가 비어있는 경우에만 삭제됩니다. 따라서, Blob API 디렉터리 삭제는 재귀적으로 사용할 수 없습니다.

다음과 같은 Blob REST API는 지원되지 않습니다.

- [Blob 배치(페이지)](/rest/api/storageservices/put-blob)
- [페이지 배치](/rest/api/storageservices/put-page)
- [페이지 범위 가져오기](/rest/api/storageservices/get-page-ranges)
- [Blob 증분 복사](/rest/api/storageservices/incremental-copy-blob)
- [URL에서 페이지 배치](/rest/api/storageservices/put-page-from-url)

비관리형 VM 디스크는 계층 구조 네임스페이스가 있는 계정에서 지원되지 않습니다. 스토리지 계정에서 계층 구조 네임스페이스를 활성화하려면, 계층 구조 네임스페이스 기능을 활성화하지 않은 스토리지 계정에 비관리형 VM 디스크를 배치합니다.

<a id="api-scope-data-lake-client-library"></a>

## <a name="support-for-setting-access-control-lists-acls-recursively"></a>ACL(액세스 제어 목록)을 재귀적으로 설정하기 위한 지원

ACL 변경 내용을 상위 디렉터리에서 하위 항목으로 재귀적으로 적용하는 기능을 일반적으로 사용할 수 있습니다. 이 기능은 현재 릴리스에서는 Azure Storage 탐색기, PowerShell, Azure CLI 및 .NET, Java 및 Python SDK를 사용하여 ACL 변경 내용을 적용할 수 있습니다. Azure Portal에서는 아직 지원되지 않습니다.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>ACL(액세스 제어 목록) 및 익명 읽기 액세스

컨테이너에 [익명 읽기 액세스](./anonymous-read-access-configure.md)가 부여된 경우 ACL은 해당 컨테이너 또는 해당 컨테이너의 파일에 영향을 주지 않습니다.  이는 읽기 요청에만 영향을 줍니다.  쓰기 요청은 Acl을 계속 인식 합니다.

<a id="known-issues-tools"></a>

## <a name="azcopy"></a>AzCopy

최신 버전의 AzCopy만 사용하십시오([AzCopy v10](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)).  AzCopy v8.1과 같은 이전 버전의 AzCopy는 지원되지 않습니다.

<a id="storage-explorer"></a>

## <a name="azure-storage-explorer"></a>Azure Storage Explorer

버전 `1.6.0` 이상에서만 사용 가능합니다.

<a id="explorer-in-portal"></a>

## <a name="storage-explorer-in-the-azure-portal"></a>Azure Portal의 Storage Explorer

ACL은 아직 지원되지 않습니다.

<a id="third-party-apps"></a>

## <a name="third-party-applications"></a>타사 애플리케이션

REST API를 사용하는 제3자 애플리케이션은 Data Lake Storage Gen2를 사용하는 경우 계속 사용 가능합니다. BLOB API를 호출하는 애플리케이션은 일반적으로 작동합니다.

## <a name="storage-analytics-logs-classic"></a>Storage 분석 로그 (클래식)

보존 기간 설정은 아직 지원되지 않지만 Azure Storage Explorer, REST 또는 SDK처럼 지원되는 도구를 사용하여 로그를 수동으로 삭제할 수 있습니다.

## <a name="windows-azure-storage-blob-wasb-driver"></a>Windows Azure Storage Blob (WASB) 드라이버

현재 Blob API와만 작동하도록 설계된 WASB 드라이버는 몇 가지 일반적인 시나리오에서 문제가 발생합니다. 특히 계층적 네임 스페이스 사용 저장소 계정에 대 한 클라이언트 인 경우 Data Lake Storage에서 다중 프로토콜 액세스를 사용해도 이 문제는 완화되지 않습니다.

계층적 네임 스페이스 사용 저장소 계정에 대 한 클라이언트로 WASB 드라이버를 사용 하는 것은 지원 되지 않습니다. 대신 Hadoop 환경에서 [Azure Blob 파일 시스템 (ABFS)](data-lake-storage-abfs-driver.md) 드라이버를 사용 하는 것이 좋습니다. 온-프레미스 Hadoop 환경에서 Hadoop branch-3 이전 버전으로 마이그레이션하려는 경우에는 귀하와 귀사에 올바른 경로를 사용하여 연락할 수 있도록 Azure 지원 티켓을 열어 주시기 바랍니다.

## <a name="soft-delete-for-blobs-capability-currently-in-preview"></a>Blob 기능 일시 삭제(현재 미리 보기)

일시 삭제 된 파일 또는 디렉터리의 부모 디렉터리 이름을 바꾸면 일시 삭제 된 항목이 Azure Portal에 올바르게 표시 되지 않을 수 있습니다. 이러한 경우 [PowerShell](soft-delete-blob-manage.md?tabs=dotnet#restore-soft-deleted-blobs-and-directories-by-using-powershell) 또는 [Azure CLI](soft-delete-blob-manage.md?tabs=dotnet#restore-soft-deleted-blobs-and-directories-by-using-azure-cli)를 사용하여 일시 삭제된 항목을 나열하고 복원할 수 있습니다.

## <a name="events"></a>이벤트

계정에 이벤트 구독이 있는 경우 보조 끝점에서 읽기 작업을 수행 하면 오류가 발생 합니다. 이 문제를 해결 하려면 이벤트 구독을 제거 합니다.

> [!TIP]
> 보조 끝점에 대 한 읽기 액세스는 읽기 액세스 지역 중복 저장소 (RA-GRS) 또는 읽기 액세스 지역 중복 저장소 (RA-GZRS)를 사용 하도록 설정한 경우에만 사용할 수 있습니다.
