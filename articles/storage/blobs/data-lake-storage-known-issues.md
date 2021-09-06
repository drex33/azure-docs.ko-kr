---
title: Azure Data Lake Storage Gen2에서 알려진 문제 | Microsoft Docs
description: Azure Data Lake Storage Gen2의 알려진 문제와 제한 사항에 대해 알아봅니다.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 6fc966fc61893fb05185a15e7149e8a132aecb04
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2021
ms.locfileid: "113125891"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2에서 알려진 문제

이 문서에서는 Azure Data Lake Storage Gen2의 알려진 문제와 제한 사항에 대해 설명합니다.

## <a name="supported-blob-storage-features"></a>지원되는 Blob 스토리지 기능

점점 더 많은 수의 Blob Storage 기능이 계층 구조 네임스페이스가 있는 계정에서 작동합니다. 전체 목록은 [Azure Data Lake Storage Gen2에서 사용할 수 있는 Blob Storage 기능](data-lake-storage-supported-blob-storage-features.md)을 참조하세요.

## <a name="supported-azure-service-integrations"></a>지원되는 Azure 서비스 통합

Azure Data Lake Storage Gen2는 데이터를 수집하고, 분석을 수행하고, 시각적 표현을 만드는 데 사용할 수 있는 여러 Azure 서비스를 지원합니다. 지원되는 Azure 서비스 목록은 [Azure Data Lake Storage Gen2를 지원하는 Azure 서비스](data-lake-storage-supported-azure-services.md)를 참조하세요.

[Azure Data Lake Storage Gen2를 지원하는 Azure 서비스](data-lake-storage-supported-azure-services.md)를 참조하세요.

## <a name="supported-open-source-platforms"></a>지원되는 오픈 소스 플랫폼

Data Lake Storage Gen2는 몇 가지 오픈 소스 플랫폼에서 지원합니다. 전체 목록은 [Azure Data Lake Storage Gen2를 지원하는 오픈 소스 플랫폼](data-lake-storage-supported-open-source-platforms.md)을 참조하세요.

[Azure Data Lake Storage Gen2를 지원하는 오픈 소스 플랫폼](data-lake-storage-supported-open-source-platforms.md)을 참조하세요.

## <a name="blob-storage-apis"></a>Blob Storage API

Blob API와 Data Lake Storage Gen2 API는 동일한 데이터에서 작업할 수 있습니다.

이 섹션에서는 Blob API와 Data Lake Storage Gen2 API를 사용하여 동일한 데이터에서 작업하는 경우의 문제점과 제한 사항에 대해 설명합니다.

* BLOB API 및 Data Lake Storage API를 사용하여 한 파일의 동일한 인스턴스에 작성할 수는 없습니다. Data Lake Storage Gen2 API를 사용하여 파일에 쓰면 해당 파일의 블록은 [블록 목록 가져오기](/rest/api/storageservices/get-block-list) Blob API에 대한 호출에 보이지 않습니다. 오직 덮어쓰기를 사용할 때만 허용됩니다. API를 사용하여 파일/BLOB을 덮어쓰면 됩니다.

* 구분 기호를 지정하지 않고 [Blob 나열](/rest/api/storageservices/list-blobs) 작업을 사용하면 결과에 디렉터리와 Blob이 모두 포함됩니다. 구분 기호를 사용하려면 슬래시(`/`)만 사용합니다. 이것이 유일하게 지원되는 구분 기호입니다.

* [Blob 삭제](/rest/api/storageservices/delete-blob) API를 사용하여 디렉터리를 삭제하면 디렉터리가 비어있는 경우에만 삭제됩니다. 따라서, Blob API 디렉터리 삭제는 재귀적으로 사용할 수 없습니다.

다음과 같은 Blob REST API는 지원되지 않습니다.

* [Blob 배치(페이지)](/rest/api/storageservices/put-blob)
* [페이지 배치](/rest/api/storageservices/put-page)
* [페이지 범위 가져오기](/rest/api/storageservices/get-page-ranges)
* [Blob 증분 복사](/rest/api/storageservices/incremental-copy-blob)
* [URL에서 페이지 배치](/rest/api/storageservices/put-page-from-url)
* [블록 목록 배치](/rest/api/storageservices/put-block-list)

비관리형 VM 디스크는 계층 구조 네임스페이스가 있는 계정에서 지원되지 않습니다. 스토리지 계정에서 계층 구조 네임스페이스를 활성화하려면, 계층 구조 네임스페이스 기능을 활성화하지 않은 스토리지 계정에 비관리형 VM 디스크를 배치합니다.

<a id="api-scope-data-lake-client-library"></a>

## <a name="support-for-setting-access-control-lists-acls-recursively"></a>ACL(액세스 제어 목록)을 재귀적으로 설정하기 위한 지원

ACL 변경 내용을 상위 디렉터리에서 하위 항목으로 재귀적으로 적용하는 기능을 일반적으로 사용할 수 있습니다. 이 기능은 현재 릴리스에서는 Azure Storage 탐색기, PowerShell, Azure CLI 및 .NET, Java 및 Python SDK를 사용하여 ACL 변경 내용을 적용할 수 있습니다. Azure Portal에서는 아직 지원되지 않습니다.

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

REST API를 사용하는 제3자 애플리케이션은 Data Lake Storage Gen2를 사용하는 경우 계속 사용 가능합니다.
BLOB API를 호출하는 애플리케이션은 일반적으로 작동합니다.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>ACL(액세스 제어 목록) 및 익명 읽기 액세스

컨테이너에 [익명 읽기 액세스](./anonymous-read-access-configure.md)가 부여된 경우 ACL은 해당 컨테이너 또는 해당 컨테이너의 파일에 영향을 주지 않습니다.

## <a name="diagnostic-logs"></a>진단 로그

보존 기간 설정은 아직 지원되지 않지만 Azure Storage Explorer, REST 또는 SDK처럼 지원되는 도구를 사용하여 로그를 수동으로 삭제할 수 있습니다.

## <a name="lifecycle-management-policies-with-premium-tier-for-azure-data-lake-storage"></a>Azure Data Lake Storage 프리미엄 계층 수명 주기 관리 정책

프리미엄 계층에 저장된 데이터는 핫, 쿨, 그리고 보관 계층 간에 이동할 수 없습니다. 그러나 프리미엄 계층에서 다른 계정의 핫 액세스 계층으로 데이터를 복사할 수 있습니다.

## <a name="windows-azure-storage-blob-wasb-driver-unsupported-with-data-lake-storage-gen2"></a>WASB(Windows Azure Storage Blob) 드라이버(Data Lake Storage Gen2에서 지원되지 않음)

현재 Blob API와만 작동하도록 설계된 WASB 드라이버는 몇 가지 일반적인 시나리오에서 문제가 발생합니다. 계층 구조 네임스페이스가 활성화된 스토리지 계정에 대한 클라이언트인 경우에는 특히 그렇습니다. Data Lake Storage에서 다중 프로토콜 액세스를 사용해도 이 문제는 완화되지 않습니다. 

당분간은(아마도 가까운 미래에는), WASB 드라이버를 클라이언트로 사용하는 고객에게 계층 구조 네임스페이스가 활성화된 스토리지 계정을 지원하지 못합니다. 그 대신 Hadoop 환경에서 [ABFS(Azure Blob File System)](data-lake-storage-abfs-driver.md) 드라이버를 사용하는 것이 좋습니다. 온-프레미스 Hadoop 환경에서 Hadoop branch-3 이전 버전으로 마이그레이션하려는 경우에는 귀하와 귀사에 올바른 경로를 사용하여 연락할 수 있도록 Azure 지원 티켓을 열어 주시기 바랍니다.

## <a name="soft-delete-for-blobs-capability-currently-in-preview"></a>Blob 기능 일시 삭제(현재 미리 보기)

일시 삭제된 파일 또는 디렉터리의 부모 디렉터리 이름이 변경되면 일시 삭제된 항목이 Azure Portal에 올바르게 표시되지 않을 수 있습니다. 이러한 경우 [PowerShell](soft-delete-blob-manage.md?tabs=dotnet#restore-soft-deleted-blobs-and-directories-by-using-powershell) 또는 [Azure CLI](soft-delete-blob-manage.md?tabs=dotnet#restore-soft-deleted-blobs-and-directories-by-using-azure-cli)를 사용하여 일시 삭제된 항목을 나열하고 복원할 수 있습니다. 
