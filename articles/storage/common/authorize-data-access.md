---
title: 데이터 작업 권한 부여
titleSuffix: Azure Storage
description: Azure Storage 데이터에 대 한 액세스 권한을 부여 하는 다양 한 방법에 대해 알아봅니다. Azure Storage는 Azure Active Directory, 공유 키 인증 또는 SAS (공유 액세스 서명)를 사용한 권한 부여를 지원 하 고 blob에 대 한 익명 액세스도 지원 합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/18/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 911db718fd7c88501006f89a549a371cd12adef4
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130178185"
---
# <a name="authorize-access-to-data-in-azure-storage"></a>Azure Storage의 데이터에 대한 액세스 권한 부여

저장소 계정의 데이터에 액세스할 때마다 클라이언트 응용 프로그램은 HTTP/HTTPS를 통해 Azure Storage를 요청 합니다. 기본적으로 Azure Storage의 모든 리소스에는 보안이 설정 되어 있으며 보안 리소스에 대 한 모든 요청에는 권한이 있어야 합니다. 권한 부여는 클라이언트 응용 프로그램에 저장소 계정의 데이터에 액세스할 수 있는 적절 한 권한이 있는지 확인 합니다.

다음 표에서는 데이터에 대 한 액세스 권한을 부여 하기 위해 Azure Storage에서 제공 하는 옵션을 설명 합니다.

| Azure 아티팩트 | 공유 키(스토리지 계정 키) | 공유 액세스 서명(SAS) | Azure AD(Azure Active Directory) | 온-프레미스 Active Directory Domain Services | 익명 공용 읽기 액세스 |
|--|--|--|--|--|--|
| Azure Blob | [지원됨](/rest/api/storageservices/authorize-with-shared-key/) | [지원됨](storage-sas-overview.md) | [지원됨](../blobs/authorize-access-azure-active-directory.md) | 지원되지 않음 | [지원됨](../blobs/anonymous-read-access-configure.md) |
| Azure Files(SMB) | [지원됨](/rest/api/storageservices/authorize-with-shared-key/) | 지원되지 않음 | [AAD 도메인 서비스에서만 지원됨](../files/storage-files-active-directory-overview.md) | [지원됨, 자격 증명을 Azure AD에 동기화해야 함](../files/storage-files-active-directory-overview.md) | 지원되지 않음 |
| Azure Files(REST) | [지원됨](/rest/api/storageservices/authorize-with-shared-key/) | [지원됨](storage-sas-overview.md) | 지원되지 않음 | 지원되지 않음 | 지원되지 않음 |
| Azure 큐 | [지원됨](/rest/api/storageservices/authorize-with-shared-key/) | [지원됨](storage-sas-overview.md) | [지원됨](../queues/authorize-access-azure-active-directory.md) | 지원되지 않음 | 지원되지 않음 |
| Azure Tables | [지원됨](/rest/api/storageservices/authorize-with-shared-key/) | [지원됨](storage-sas-overview.md) | [지원됨](../tables/authorize-access-azure-active-directory.md)(미리 보기) | 지원되지 않음 | 지원되지 않음 |

각 권한 부여 옵션은 아래에 간략하게 설명되어 있습니다.

- blob, 큐 및 테이블 리소스에 대 한 요청을 승인 하는 **Azure Active Directory (Azure AD) 통합** 최적의 보안과 사용 편의성을 위해 가능한 경우 Azure AD 자격 증명을 사용 하 여 데이터에 대 한 요청에 권한을 부여 하는 것이 좋습니다. Azure AD 통합에 대 한 자세한 내용은 [Azure Storage 데이터에 대 한 액세스 권한 부여](authorize-data-access.md)를 참조 하세요.

    Azure RBAC (역할 기반 액세스 제어)를 사용 하 여 저장소 계정의 blob, 큐 및 테이블 리소스에 대 한 보안 주체의 사용 권한을 관리할 수 있습니다. 또한 Azure 특성 기반 액세스 제어 (ABAC)를 사용 하 여 blob 리소스에 대 한 Azure 역할 할당에 조건을 추가할 수 있습니다. RBAC에 대 한 자세한 내용은 [azure 역할 기반 액세스 제어 (AZURE RBAC) 란?](../../role-based-access-control/overview.md)을 참조 하세요. ABAC에 대 한 자세한 내용은 [azure attribute based access control (azure ABAC) 이란?을 참조 하세요. (미리 보기)](../../role-based-access-control/conditions-overview.md).

- Azure Files에 대한 **Azure AD DS(Azure Active Directory Domain Services) 인증**. Azure Files는 Azure AD DS를 통해 SMB(서버 메시지 블록)에 대한 ID 기반 권한 부여를 지원합니다. Azure RBAC를 사용하여 스토리지 계정의 Azure Files 리소스에 대한 클라이언트의 액세스를 세부적으로 제어할 수 있습니다. 도메인 서비스를 사용 하 여 인증을 Azure Files 하는 방법에 대 한 자세한 내용은 [개요](../files/storage-files-active-directory-overview.md)를 참조 하세요.

- Azure Files에 대한 **온-프레미스 Active Directory Domain Services(AD DS 또는 온-프레미스 AD DS) 인증** . Azure Files는 AD DS를 통해 SMB에 대한 ID 기반 권한 부여를 지원합니다. AD DS 환경은 온-프레미스 머신 또는 Azure VM에서 호스팅될 수 있습니다. 파일에 대한 SMB 액세스는 온-프레미스 또는 Azure의 도메인 조인 머신에서 AD DS 자격 증명을 사용하여 지원됩니다. 공유 수준 액세스 제어를 위한 Azure RBAC와 디렉터리/파일 수준 사용 권한 적용을 위한 NTFS DACL의 조합을 사용할 수 있습니다. 도메인 서비스를 사용 하 여 인증을 Azure Files 하는 방법에 대 한 자세한 내용은 [개요](../files/storage-files-active-directory-overview.md)를 참조 하세요.

- Blob, 파일, 큐 및 테이블에 대한 **공유 키 권한 부여**. 공유 키를 사용하는 클라이언트는 스토리지 계정 액세스 키를 사용하여 서명된 모든 요청에 헤더를 전달합니다. 자세한 내용은 [공유 키를 사용하여 권한 부여](/rest/api/storageservices/authorize-with-shared-key/)를 참조하세요.

    저장소 계정에 대 한 공유 키 인증을 허용 하지 않을 수 있습니다. 공유 키 권한 부여를 허용 하지 않는 경우 클라이언트는 Azure AD를 사용 하 여 해당 저장소 계정의 데이터에 대 한 요청을 승인 해야 합니다. 자세한 내용은 [Azure Storage 계정에 대한 공유 키 권한 부여 방지](shared-key-authorization-prevent.md)를 참조하세요.

- Blob, 파일, 큐 및 테이블에 대한 **공유 액세스 서명**. SAS(공유 액세스 서명)는 스토리지 계정의 리소스에 대해 제한적으로 위임된 권한을 제공합니다. 서명이 유효한 시간 간격 또는 부여되는 권한에 제약 조건을 추가하면 액세스를 유연하게 관리할 수 있습니다. 자세한 내용은 [SAS(공유 액세스 서명) 사용](storage-sas-overview.md)을 참조하세요.

- 컨테이너 및 Blob에 대한 **익명 공용 읽기 액세스**. 익명 액세스가 구성 되 면 클라이언트는 권한 부여 없이 blob 데이터를 읽을 수 있습니다. 자세한 내용은 [컨테이너 및 Blob에 대한 익명 읽기 권한 관리](../blobs/anonymous-read-access-configure.md)를 참조하세요.

    저장소 계정에 대 한 익명 공용 읽기 액세스를 허용 하지 않을 수 있습니다. 익명 공용 읽기 액세스가 허용 되지 않는 경우 사용자는 익명 액세스를 사용 하도록 컨테이너를 구성할 수 없으며 모든 요청에 권한이 부여 되어야 합니다. 자세한 내용은 [컨테이너 및 Blob에 대한 익명 퍼블릭 읽기 권한 방지](../blobs/anonymous-read-access-prevent.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Storage의 데이터에 대한 액세스 권한 부여](authorize-data-access.md)
- [공유 키로 권한 부여](/rest/api/storageservices/authorize-with-shared-key/)
- [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](storage-sas-overview.md)
        