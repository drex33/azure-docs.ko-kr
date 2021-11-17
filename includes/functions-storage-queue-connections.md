---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: 965a2abb8a56894efc700cea75097526daf4e544
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132530223"
---
## <a name="connections"></a>Connections

`connection`속성은 앱이 Azure 큐에 연결 되는 방법을 지정 하는 환경 구성에 대 한 참조입니다. 다음과 같이 지정할 수 있습니다.

- [연결 문자열](#connection-string) 을 포함 하는 응용 프로그램 설정의 이름입니다.
- 여러 응용 프로그램 설정에 대 한 공유 접두사의 이름으로, [id 기반 연결](#identity-based-connections)을 정의 합니다.

구성 된 값이 단일 설정과 정확히 일치 하 고 다른 설정에 대 한 접두사 일치 이면 정확히 일치 하는 항목이 사용 됩니다.

### <a name="connection-string"></a>연결 문자열

연결 문자열을 얻으려면 [저장소 계정 액세스 키 관리](../articles/storage/common/storage-account-keys-manage.md)에 표시 된 단계를 따르세요.

이 연결 문자열은 `connection` 바인딩 구성의 속성에 지정 된 값과 일치 하는 이름을 가진 응용 프로그램 설정에 저장 해야 합니다.

앱 설정 이름이 "AzureWebJobs"로 시작하는 경우 여기에서 이름의 나머지만을 지정할 수 있습니다. 예를 들어 `connection`을 "MyStorage"로 설정한 경우 함수 런타임 기능은 "AzureWebJobsMyStorage"라는 앱 설정을 찾습니다. `connection`을 비워 두면 함수 런타임 기능은 `AzureWebJobsStorage`라는 앱 설정에서 기본 스토리지 연결 문자열을 사용합니다.

### <a name="identity-based-connections"></a>ID 기반 연결

암호를 사용 하 여 연결 문자열을 사용 하는 대신 [버전 4.x 이상의 확장](../articles/azure-functions/functions-bindings-storage-queue.md#storage-extension-5x-and-higher)을 사용 하는 경우 앱에서 [Azure Active Directory id](../articles/active-directory/fundamentals/active-directory-whatis.md)를 사용 하도록 할 수 있습니다. 이렇게 하려면 `connection` 트리거 및 바인딩 구성의 속성에 매핑되는 일반적인 접두사 아래에 설정을 정의 합니다.

이 모드에서 확장에는 다음 속성이 필요 합니다.

| 속성                  | 환경 변수 템플릿                       |설명                                | 예제 값 |
|---------------------------|-----------------------------------------------------|--------------------------------------------|---------|
| 큐 서비스 URI | `<CONNECTION_NAME_PREFIX>__queueServiceUri`<sup>1</sup>  | HTTPS 체계를 사용 하 여 연결 하는 큐 서비스의 데이터 평면 URI입니다. | https://<storage_account_name>. queue.core.windows.net |

<sup>1</sup> 은 `<CONNECTION_NAME_PREFIX>__serviceUri` 별칭으로 사용할 수 있습니다. 두 양식을 모두 제공 하면 `queueServiceUri` 양식이 사용 됩니다. `serviceUri`전체 연결 구성을 blob, 큐 및/또는 테이블에서 사용 하는 경우에는이 양식을 사용할 수 없습니다.

추가 속성을 설정 하 여 연결을 사용자 지정할 수 있습니다. [Id 기반 연결에 대 한 공용 속성을](../articles/azure-functions/functions-reference.md#common-properties-for-identity-based-connections)참조 하세요.

[!INCLUDE [functions-identity-based-connections-configuration](./functions-identity-based-connections-configuration.md)]

[!INCLUDE [functions-queue-permissions](./functions-queue-permissions.md)]