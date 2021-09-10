---
author: mattchenderson
ms.service: app-service-web
ms.topic: include
ms.date: 06/11/2021
ms.author: mahender
ms.openlocfilehash: 9b05a4d16cbf6d3b9677c894bffb6e47018b5cff
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122262880"
---
[Azure AD를 사용하여 요청에 권한을 부여](../articles/storage/blobs/authorize-access-azure-active-directory.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)하도록 Azure Blob 스토리지를 구성할 수 있습니다. 즉, 만료된 SAS 키를 생성하는 대신 애플리케이션의 [관리 ID](../articles/app-service/overview-managed-identity.md)를 사용할 수 있습니다. 기본적으로 앱의 시스템 할당 ID가 사용됩니다. 사용자 할당 ID를 지정하려는 경우 `WEBSITE_RUN_FROM_PACKAGE_BLOB_MI_RESOURCE_ID` 앱 설정을 해당 ID의 리소스 ID로 설정할 수 있습니다. 이 설정은 "SystemAssigned"를 값으로 수락할 수 있지만 이는 설정을 완전히 생략하는 것과 동일합니다.

ID를 사용하여 패키지를 가져올 수 있도록 설정하려면 다음을 수행합니다.

1. [Blob이 프라이빗 액세스용으로 구성](../articles/storage/blobs/anonymous-read-access-configure.md#set-the-public-access-level-for-a-container)되었는지 확인합니다.

1. ID에 패키지 Blob에 대한 범위가 있는 [Storage Blob 데이터 읽기 권한자](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader) 역할을 부여합니다. 역할 할당을 만드는 방법에 대한 자세한 내용은 [Blob 데이터에 대한 액세스에 Azure 역할 할당](../articles/storage/blobs/assign-azure-role-data-access.md)을 참조하세요.

1. `WEBSITE_RUN_FROM_PACKAGE` 애플리케이션 설정을 패키지의 Blob URL로 설정합니다. 이는 "https://{storage-account-name}.blob.core.windows.net/{container-name}/{path-to-package}" 또는 이와 유사한 형식일 수 있습니다.