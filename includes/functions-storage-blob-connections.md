---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: 5fc17f7a4a5220ebcfb05b179c3ee75ba077e5a4
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132529597"
---
## <a name="connections"></a>Connections

`connection`속성은 앱이 Azure Blob에 연결하는 방법을 지정하는 환경 구성에 대한 참조입니다. 다음을 지정할 수 있습니다.

- [연결 문자열을](#connection-string) 포함하는 애플리케이션 설정의 이름
- [ID 기반 연결을](#identity-based-connections)정의하는 여러 애플리케이션 설정에 대한 공유 접두사 이름입니다.

구성된 값이 단일 설정과 정확히 일치하고 다른 설정에 대한 접두사 일치인 경우 정확한 일치가 사용됩니다.

### <a name="connection-string"></a>연결 문자열

연결 문자열을 가져오려면 스토리지 계정 액세스 키 관리에 표시된 단계를 [수행합니다.](../articles/storage/common/storage-account-keys-manage.md) 연결 문자열은 [Blob Storage 계정](../articles/storage/common/storage-account-overview.md#types-of-storage-accounts)이 아닌 범용 스토리지 계정의 문자열이어야 합니다.

이 연결 문자열은 바인딩 구성의 속성에 지정된 값과 일치하는 이름을 가진 애플리케이션 설정에 저장되어야 `connection` 합니다.

앱 설정 이름이 "AzureWebJobs"로 시작하는 경우 여기에서 이름의 나머지만을 지정할 수 있습니다. 예를 들어 `connection`을 "MyStorage"로 설정한 경우 함수 런타임 기능은 "AzureWebJobsMyStorage"라는 앱 설정을 찾습니다. `connection`을 비워 두면 함수 런타임 기능은 `AzureWebJobsStorage`라는 앱 설정에서 기본 스토리지 연결 문자열을 사용합니다.

### <a name="identity-based-connections"></a>ID 기반 연결

비밀이 있는 연결 문자열을 사용하는 대신 [버전 5.x 이상 확장](../articles/azure-functions/functions-bindings-storage-blob.md#storage-extension-5x-and-higher)를 사용하는 경우 앱에서 Azure Active Directory [ID](../articles/active-directory/fundamentals/active-directory-whatis.md)를 사용하도록 할 수 있습니다. 이렇게 하려면 트리거 및 바인딩 구성의 속성에 매핑되는 공통 접두사 아래에 설정을 `connection` 정의합니다.

이 모드에서는 확장에 다음 속성이 필요합니다.

| 속성                  | 환경 변수 템플릿                       | 설명                                | 예제 값 |
|---------------------------|-----------------------------------------------------|--------------------------------------------|---------|
| Blob Service URI | `<CONNECTION_NAME_PREFIX>__blobServiceUri`<sup>1</sup>  | HTTPS 체계를 사용하여 연결 중인 Blob Service의 데이터 평면 URI입니다. | https://<storage_account_name>.blob.core.windows.net |

<sup>1은</sup> `<CONNECTION_NAME_PREFIX>__serviceUri` 별칭으로 사용할 수 있습니다. 두 별칭이 모두 제공되면 `blobServiceUri` 양식이 사용됩니다. `serviceUri`전체 연결 구성을 Blob, 큐 및/또는 테이블에서 사용할 경우에는 폼을 사용할 수 없습니다.

> [!NOTE]
> 기본적으로 Blob 트리거는 내부적으로 Azure 큐를 사용합니다. `<CONNECTION_NAME_PREFIX>__queueServiceUri` 을 지정할 수도 있지만 기본 동작이 없으면 "AzureWebJobsStorage"로 정의된 연결을 사용하는 것입니다. 트리거는 이러한 큐에 사용할 연결에 [Storage 큐 데이터 기여자가](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor) 필요합니다.

연결을 사용자 지정하기 위해 추가 속성을 설정할 수 있습니다. [ID 기반 연결에 대한 공용 속성을 참조하세요.](../articles/azure-functions/functions-reference.md#common-properties-for-identity-based-connections)

[!INCLUDE [functions-identity-based-connections-configuration](./functions-identity-based-connections-configuration.md)]

[!INCLUDE [functions-blob-permissions](./functions-blob-permissions.md)]