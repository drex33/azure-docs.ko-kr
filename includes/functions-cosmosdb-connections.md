---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 11/12/2021
ms.author: mahender
ms.openlocfilehash: 4a78d64e2a66f5d3025407b6d7d7907d085b1fe6
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132529919"
---
## <a name="connections"></a>Connections

`connectionStringSetting` / `connection` 및 `leaseConnectionStringSetting` / `leaseConnection` 속성은 앱이 Azure Cosmos DB에 연결 하는 방법을 지정 하는 환경 구성에 대 한 참조입니다. 다음과 같이 지정할 수 있습니다.

- [연결 문자열](#connection-string) 을 포함 하는 응용 프로그램 설정의 이름입니다.
- 여러 응용 프로그램 설정에 대 한 공유 접두사의 이름으로, [id 기반 연결](#identity-based-connections)을 정의 합니다. 이 옵션은 `connection` `leaseConnection` 버전 4.x [이상의 확장]에서 및 버전에만 사용할 수 있습니다.

구성 된 값이 단일 설정과 정확히 일치 하 고 다른 설정에 대 한 접두사 일치 이면 정확히 일치 하는 항목이 사용 됩니다.

### <a name="connection-string"></a>연결 문자열

데이터베이스 계정에 대 한 연결 문자열은 바인딩 구성의 연결 속성에 지정 된 값과 일치 하는 이름의 응용 프로그램 설정에 저장 되어야 합니다.

### <a name="identity-based-connections"></a>ID 기반 연결

암호를 사용 하 여 연결 문자열을 사용 하는 대신 [버전 4.x 이상의 확장]을 사용 하는 경우 앱에서 [Azure Active Directory id](../articles/active-directory/fundamentals/active-directory-whatis.md)를 사용 하도록 할 수 있습니다. 이렇게 하려면 트리거 및 바인딩 구성의 연결 속성에 매핑되는 일반적인 접두사 아래에 설정을 정의 합니다.

이 모드에서 확장에는 다음 속성이 필요 합니다.

| 속성                  | 환경 변수 템플릿                       | 설명                                | 예제 값                                        |
|---------------------------|-----------------------------------------------------|--------------------------------------------|------------------------------------------------|
| 계정 끝점 | `<CONNECTION_NAME_PREFIX>__accountEndpoint` | Azure Cosmos DB 계정 끝점 URI입니다. | https://<database_account_name>. documents.azure.com:443/ |

추가 속성을 설정 하 여 연결을 사용자 지정할 수 있습니다. [Id 기반 연결에 대 한 공용 속성을](../articles/azure-functions/functions-reference.md#common-properties-for-identity-based-connections)참조 하세요.

[!INCLUDE [functions-identity-based-connections-configuration](./functions-identity-based-connections-configuration.md)]

[!INCLUDE [functions-cosmos-permissions](./functions-cosmos-permissions.md)]

[버전 4.x 이상 확장]: ../articles/azure-functions/functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher
