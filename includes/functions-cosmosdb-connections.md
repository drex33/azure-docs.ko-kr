---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: 204e2b629740bba53154f3a43fa4e723e3a73248
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129994347"
---
## <a name="connections"></a>Connections

`connectionStringSetting` / `connection` 및 `leaseConnectionStringSetting` / `leaseConnection` 속성은 앱이 Azure Cosmos DB에 연결하는 방법을 지정하는 환경 구성에 대한 참조입니다. 다음을 지정할 수 있습니다.

- [연결 문자열을](#connection-string) 포함하는 애플리케이션 설정의 이름
- ID [기반 연결을](#identity-based-connections)정의하는 여러 애플리케이션 설정에 대한 공유 접두사 이름입니다. 이 옵션은 `connection` `leaseConnection` 버전 [4.x 이상 확장의]및 버전에서만 사용할 수 있습니다.

구성된 값이 단일 설정과 정확히 일치하고 다른 설정에 대한 접두사 일치인 경우 정확한 일치가 사용됩니다.

### <a name="connection-string"></a>연결 문자열

데이터베이스 계정에 대한 연결 문자열은 바인딩 구성의 연결 속성에 지정된 값과 일치하는 이름으로 애플리케이션 설정에 저장되어야 합니다.

### <a name="identity-based-connections"></a>ID 기반 연결

비밀이 있는 연결 문자열을 사용하는 대신 [버전 4.x 이상 확장]를 사용하는 경우 앱에서 Azure Active Directory [ID를](../articles/active-directory/fundamentals/active-directory-whatis.md)사용하도록 할 수 있습니다. 이렇게 하려면 트리거 및 바인딩 구성의 연결 속성에 매핑되는 공통 접두사 아래에 설정을 정의합니다.

이 모드에서는 확장에 다음 속성이 필요합니다.

| 속성                  | 환경 변수 템플릿                       | 설명                                | 예제 값                                        |
|---------------------------|-----------------------------------------------------|--------------------------------------------|------------------------------------------------|
| 계정 엔드포인트 | `<CONNECTION_NAME_PREFIX>__accountEndpoint` | Azure Cosmos DB 계정 엔드포인트 URI입니다. | <database_account_name>.documents.azure.com |

연결을 사용자 지정하기 위해 추가 속성을 설정할 수 있습니다. [ID 기반 연결에 대한 공용 속성을 참조하세요.](../articles/azure-functions/functions-reference.md#common-properties-for-identity-based-connections)

[!INCLUDE [functions-identity-based-connections-configuration](./functions-identity-based-connections-configuration.md)]

[!INCLUDE [functions-cosmos-permissions](./functions-cosmos-permissions.md)]

[버전 4.x 이상 확장]: ../articles/azure-functions/functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher
