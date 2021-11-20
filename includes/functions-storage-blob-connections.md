---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: 07496f2b334eeafcff0d1ab6e709f6ce5460a29b
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132875422"
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

비밀이 있는 연결 문자열을 사용하는 대신 [버전 5.x 이상 확장](../articles/azure-functions/functions-bindings-storage-blob.md#storage-extension-5x-and-higher)를 사용하는 경우 앱에서 Azure Active Directory [ID를](../articles/active-directory/fundamentals/active-directory-whatis.md)사용하도록 할 수 있습니다. 이렇게 하려면 트리거 및 바인딩 구성의 속성에 매핑되는 공통 접두사 아래에 설정을 `connection` 정의합니다.

이 모드에서는 확장에 다음 속성이 필요합니다.

| 속성                  | 환경 변수 템플릿                       | 설명                                | 예제 값 |
|---------------------------|-----------------------------------------------------|--------------------------------------------|---------|
| Blob Service URI | `<CONNECTION_NAME_PREFIX>__serviceUri`<sup>1</sup>  | HTTPS 체계를 사용하여 연결 중인 Blob Service의 데이터 평면 URI입니다. | https://<storage_account_name>.blob.core.windows.net |

<sup>1은</sup> `<CONNECTION_NAME_PREFIX>__blobServiceUri` 별칭으로 사용할 수 있습니다. Blob 트리거에서 연결 구성을 사용하는 경우 `blobServiceUri` 도 함께 사용해야 `queueServiceUri` 합니다. 아래 내용을 참조하세요.

연결을 사용자 지정하기 위해 추가 속성을 설정할 수 있습니다. [ID 기반 연결에 대한 공용 속성을 참조하세요.](../articles/azure-functions/functions-reference.md#common-properties-for-identity-based-connections)

`serviceUri`전체 연결 구성을 Blob, 큐 및/또는 테이블에서 사용할 경우에는 폼을 사용할 수 없습니다. URI 자체는 Blob service만 지정할 수 있습니다. 또는 각 서비스에 대한 URI를 제공하여 단일 연결을 사용할 수 있습니다. 두 버전이 모두 제공되면 다중 서비스 양식이 사용됩니다. 대신 여러 서비스에 대한 연결을 구성하려면 `<CONNECTION_NAME_PREFIX>__serviceUri` 다음을 설정합니다.

| 속성                  | 환경 변수 템플릿                       | 설명                                | 예제 값 |
|---------------------------|-----------------------------------------------------|--------------------------------------------|---------|
| Blob Service URI | `<CONNECTION_NAME_PREFIX>__blobServiceUri` | HTTPS 체계를 사용하여 연결 중인 Blob Service의 데이터 평면 URI입니다. | https://<storage_account_name>.blob.core.windows.net |
| 큐 서비스 URI(Blob 트리거 <sup>2에</sup>**필요)**  | `<CONNECTION_NAME_PREFIX>__queueServiceUri` | HTTPS 체계를 사용하는 큐 서비스의 데이터 평면 URI입니다. 이 값은 Blob 트리거에만 필요합니다. | https://<storage_account_name>.queue.core.windows.net |

<sup>2</sup> 기본적으로 Blob 트리거는 내부적으로 Azure 큐를 사용합니다. `serviceUri`양식에서 `AzureWebJobsStorage` 연결이 사용됩니다. 그러나 를 지정할 `blobServiceUri` 때는 큐 서비스 URI도 와 함께 제공해야 `queueServiceUri` 합니다. Blob 서비스와 동일한 스토리지 계정의 서비스를 사용하는 것이 좋습니다. 또한 Storage [큐 데이터 기여자와](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor)같은 역할을 할당하여 트리거가 구성된 큐 서비스에서 메시지를 읽고 쓸 수 있는지 확인해야 합니다. 

[!INCLUDE [functions-identity-based-connections-configuration](./functions-identity-based-connections-configuration.md)]

[!INCLUDE [functions-blob-permissions](./functions-blob-permissions.md)]