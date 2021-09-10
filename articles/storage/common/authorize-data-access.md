---
title: 데이터 작업 권한 부여
titleSuffix: Azure Storage
description: Azure Active Directory, 공유 키 인증 또는 SAS(공유 액세스 서명)를 포함하여 Azure Storage에 대한 액세스 권한을 부여하는 다양한 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 9896cdaf82ca508c5e6a1a444564403ef0f7e1eb
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113765456"
---
# <a name="authorize-access-to-data-in-azure-storage"></a>Azure Storage의 데이터에 대한 액세스 권한 부여

스토리지 계정의 데이터에 액세스할 때마다 클라이언트에서 HTTP/HTTPS를 통해 Azure Storage에 요청합니다. 보안 리소스에 대한 모든 요청은 권한을 부여받아 클라이언트에서 데이터 액세스에 필요한 권한을 갖도록 해야 합니다.

다음 표에서는 리소스에 대한 액세스 권한을 부여하기 위해 Azure Storage에서 제공하는 옵션을 설명합니다.

| Azure 아티팩트 | 공유 키(스토리지 계정 키) | 공유 액세스 서명(SAS) | Azure AD(Azure Active Directory) | 온-프레미스 Active Directory Domain Services | 익명 공용 읽기 액세스 |
|--|--|--|--|--|--|
| Azure Blob | [지원됨](/rest/api/storageservices/authorize-with-shared-key/) | [지원됨](storage-sas-overview.md) | [지원됨](authorize-data-access.md) | 지원되지 않음 | [지원됨](../blobs/anonymous-read-access-configure.md) |
| Azure Files(SMB) | [지원됨](/rest/api/storageservices/authorize-with-shared-key/) | 지원되지 않음 | [AAD 도메인 서비스에서만 지원됨](../files/storage-files-active-directory-overview.md) | [지원됨, 자격 증명을 Azure AD에 동기화해야 함](../files/storage-files-active-directory-overview.md) | 지원되지 않음 |
| Azure Files(REST) | [지원됨](/rest/api/storageservices/authorize-with-shared-key/) | [지원됨](storage-sas-overview.md) | 지원되지 않음 | 지원되지 않음 | 지원되지 않음 |
| Azure 큐 | [지원됨](/rest/api/storageservices/authorize-with-shared-key/) | [지원됨](storage-sas-overview.md) | [지원됨](authorize-data-access.md) | 지원되지 않음 | 지원되지 않음 |
| Azure Tables | [지원됨](/rest/api/storageservices/authorize-with-shared-key/) | [지원됨](storage-sas-overview.md) | [지원됨](../tables/authorize-access-azure-active-directory.md)(미리 보기) | 지원되지 않음 | 지원되지 않음 |

각 권한 부여 옵션은 아래에 간략하게 설명되어 있습니다.

- Blob, 큐 및 테이블에 대한 **Azure AD(Azure Active Directory) 통합**. Azure는 스토리지 계정의 리소스에 대한 클라이언트 액세스를 제어하는 Azure RBAC(Azure 역할 기반 액세스 제어)를 제공합니다. Microsoft는 최적의 보안과 사용 편의성을 위해 가능한 경우 Azure AD를 사용할 것을 권장합니다. Azure AD 통합에 관한 자세한 내용은 [Azure Storage의 데이터에 대한 액세스 권한 부여](authorize-data-access.md)를 참조하세요.

- Azure Files에 대한 **Azure AD DS(Azure Active Directory Domain Services) 인증**. Azure Files는 Azure AD DS를 통해 SMB(서버 메시지 블록)에 대한 ID 기반 권한 부여를 지원합니다. Azure RBAC를 사용하여 스토리지 계정의 Azure Files 리소스에 대한 클라이언트의 액세스를 세부적으로 제어할 수 있습니다. 도메인 서비스를 사용하는 Azure Files 인증에 대한 자세한 내용은 [개요](../files/storage-files-active-directory-overview.md)를 참조하세요.

- Azure Files에 대한 **온-프레미스 Active Directory Domain Services(AD DS 또는 온-프레미스 AD DS) 인증** . Azure Files는 AD DS를 통해 SMB에 대한 ID 기반 권한 부여를 지원합니다. AD DS 환경은 온-프레미스 머신 또는 Azure VM에서 호스팅될 수 있습니다. 파일에 대한 SMB 액세스는 온-프레미스 또는 Azure의 도메인 조인 머신에서 AD DS 자격 증명을 사용하여 지원됩니다. 공유 수준 액세스 제어를 위한 Azure RBAC와 디렉터리/파일 수준 사용 권한 적용을 위한 NTFS DACL의 조합을 사용할 수 있습니다. 도메인 서비스를 사용하는 Azure Files 인증에 대한 자세한 내용은 [개요](../files/storage-files-active-directory-overview.md)를 참조하세요.

- Blob, 파일, 큐 및 테이블에 대한 **공유 키 권한 부여**. 공유 키를 사용하는 클라이언트는 스토리지 계정 액세스 키를 사용하여 서명된 모든 요청에 헤더를 전달합니다. 자세한 내용은 [공유 키를 사용하여 권한 부여](/rest/api/storageservices/authorize-with-shared-key/)를 참조하세요.

- Blob, 파일, 큐 및 테이블에 대한 **공유 액세스 서명**. SAS(공유 액세스 서명)는 스토리지 계정의 리소스에 대해 제한적으로 위임된 권한을 제공합니다. 서명이 유효한 시간 간격 또는 부여되는 권한에 제약 조건을 추가하면 액세스를 유연하게 관리할 수 있습니다. 자세한 내용은 [SAS(공유 액세스 서명) 사용](storage-sas-overview.md)을 참조하세요.
- 컨테이너 및 Blob에 대한 **익명 공용 읽기 액세스**. 권한 부여가 필요하지 않습니다. 자세한 내용은 [컨테이너 및 Blob에 대한 익명 읽기 권한 관리](../blobs/anonymous-read-access-configure.md)를 참조하세요.  

## <a name="next-steps"></a>다음 단계

- [Azure Storage의 데이터에 대한 액세스 권한 부여](authorize-data-access.md)
- [공유 키로 권한 부여](/rest/api/storageservices/authorize-with-shared-key/)
- [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](storage-sas-overview.md)
